---
layout: post
title:  "Making a RESTful API with Yii 2.0"
date:   2016-02-24 10:23:43 -0600
categories: yii
---
Intro:

RESTful APIs are a modern way to serve data from your database for your app to consume.  They expose your data via JSON or XML so you can separate your database queries in the backend from the action in the frontend.

Steps:

## Download and install Yii 2 into an api subdirectory

Go to [http://www.yiiframework.com/download/][yii download].

I recommend using the Composer method to install the basic application template.  Install Yii into an "api" subfolder of your app to keep it separate from the rest of your code.

## Customize .htaccess and web config to show pretty URLs

*Part one:* Create a new file in any basic text editor (i.e. Notepad) and fill it with the following code.

	RewriteEngine on
	# If a directory or a file exists, use it directly
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteCond %{REQUEST_FILENAME} !-d
	# Otherwise forward it to index.php
	RewriteRule . index.php

This file is important because it hides your "index.php" at the end of your URLs.

Save this file to your api directory as ".htaccess" -- you don't need anything before the period, and it shouldn't have any extension past the htaccess part.

*Part two:* configure Yii to show "pretty URLs" -- I'll explain what that means as we go along.

Ugly URLs look like `/index.php?r=post/view&id=100`
Pretty URLs look like `/index.php/post/100`
Prettier URLS look like `/post/100`

By default, Yii shows ugly URLs.  We need to make them prettier by opening up `/config/web.php` and changing `enablePrettyUrl` to true.

	[
	 'components' => [ 
	 	'urlManager' => [
			 'enablePrettyUrl' => true,
			 'showScriptName' => false,
			 'enableStrictParsing' => false,
			 'rules' => [
				'class' => 'yii\rest\UrlRule',
				'controller' => [
					'loinc',
					'loinc-panel',
				],
			 ],
		 ],
	 ],
	]

Not only did we make the URLs pretty, but we also added a rule that for the `loinc` and `loinc-panel` controllers, we should use a RESTful URL class.  I will go into how this is useful as we go on.

## Create a relational database structure

If you don't already have a database set up, follow the directions to install XAMPP and plug the following code into your SQL editor.

	DROP  TABLE IF EXISTS loinc;
	CREATE TABLE loinc (
	  loinc_num varchar(10) not null,
	  loinc_name varchar(255) default null,
	  common_rank integer default null,
	  panel_type varchar(50) default null,
	
	  primary key (loinc_num)
	
	) ENGINE=InnoDB DEFAULT CHARSET=utf8;

	DROP  TABLE IF EXISTS loinc_panels;
	CREATE TABLE loinc_panels (
	  parent_loinc_num varchar(10) not null,
	  loinc_num varchar(10) not null,
	  loinc_name varchar(255) default null,
	  common_rank integer default null,

	) ENGINE=InnoDB DEFAULT CHARSET=utf8;

We are going to make two databases - one for LOINC codes, and another for LOINC panels.  Since LOINC codes can be related to several LOINC panels, and LOINC panels can have only one parent LOINC code, we will need to define those relations later on.

## Setup database config file

## Use Gii to make the models

## Create the RESTFUL Controller

Type the following into Notepad and save it as `/controllers/LoincController.php`.

	namespace app\controllers;
	
	use yii\rest\ActiveController;
	
	class LoincController extends ActiveController
	{
	    public $modelClass = 'app\models\Loinc';
	}

Do the same thing for your LOINC panel model (repeat the above, but replace `Loinc` with `LoincPanel`).	So you should now have added two files to the controllers directory: `LoincController.php` and `LoincPanelController.php`.

This tiny file (along with the RESTful URL rule we applied earlier) gives you basically all the functionality of a RESTful API with minimal code.

* Customize the controller to change default actions
** (i.e. pagination / sorting)

## Edit the Model

By default, Yii uses the `id` field as the primary key, but in this case, we need to use `loinc_num`.  Add the following code to your Loinc model within the class function.

	public static function primaryKey()
	{
	return ['loinc_num'];
	}

Also, we need to define the relations between LOINC codes and LOINC panels.  We define a `hasMany` panel relationship with the `LoincPanel` model, and we link the two models based on a panel's `parent_loinc` being equal to its parent's `loinc_num`.

	public function extraFields()
	{
		return ['panel'];
	}
	
	public function getPanel()
	{
		return $this->hasMany(LoincPanel::className(), ['parent_loinc' => 'loinc_num']);
	}

In addition, by adding the `extraFields()` function, we allowed the URL request to return details about the individual `Loinc` items contained within each `LoincPanel`.  To use this functionality when making a request, you would add `expand=panel` to the URL.

## Example

www.example.com/api/v1/loinc (showing all LOINC codes)

	. . .
	
	<item>
		<loinc_num>24313-9</loinc_num>
		<loinc_name>Hepatitis 1996 panel - Serum</loinc_name>
		<component>Hepatitis 1996 panel</component>
		<common_test_rank>0</common_test_rank>
		<common_order_rank>0</common_order_rank>
		<panel_type>Panel</panel_type>
	</item>

	. . .
	
www.example.com/api/v1/loinc?expand=panel (showing details of the contents of each panel)

	. . . 
	
	<item>
		<loinc_num>24313-9</loinc_num>
		<loinc_name>Hepatitis 1996 panel - Serum</loinc_name>
		<component>Hepatitis 1996 panel</component>
		<common_test_rank>0</common_test_rank>
		<common_order_rank>0</common_order_rank>
		<panel_type>Panel</panel_type>
		<panel>
			<item>
				<parent_id>10149</parent_id>
				<parent_loinc>24313-9</parent_loinc>
				<parent_name>Hepatitis 1996 Pnl Ser</parent_name>
				<id>10150</id>
				<sequence>1</sequence>
				<loinc_num>22312-3</loinc_num>
				<loinc_name>HAV Ab Ser-aCnc</loinc_name>
				<loinc>
					<loinc_num>22312-3</loinc_num>
					<loinc_name>Hepatitis A virus Ab [Units/volume] in Serum</loinc_name>
					<component>Hepatitis A virus Ab</component>
					<common_test_rank>0</common_test_rank>
					<common_order_rank>0</common_order_rank>
					<panel_type/>
				</loinc>
			</item>
			
			. . . 
			
			<item>
				<parent_id>10149</parent_id>
				<parent_loinc>24313-9</parent_loinc>
				<parent_name>Hepatitis 1996 Pnl Ser</parent_name>
				<id>10154</id>
				<sequence>5</sequence>
				<loinc_num>22327-1</loinc_num>
				<loinc_name>HCV Ab Ser-aCnc</loinc_name>
				<loinc>
					<loinc_num>22327-1</loinc_num>
					<loinc_name>Hepatitis C virus Ab [Units/volume] in Serum</loinc_name>
					<component>Hepatitis C virus Ab</component>
					<common_test_rank>0</common_test_rank>
					<common_order_rank>0</common_order_rank>
					<panel_type/>
				</loinc>
			</item>
		</panel>
	</item>
	
	. . .
	
http://www.monitorx.org/api/v1/loinc/22327-1 (specific Loinc ID)

	<loinc_num>22327-1</loinc_num>
	<loinc_name>Hepatitis C virus Ab [Units/volume] in Serum</loinc_name>
	<component>Hepatitis C virus Ab</component>
	<common_test_rank>0</common_test_rank>
	<common_order_rank>0</common_order_rank>
	<panel_type/>

[yii download]: http://www.yiiframework.com/download/
