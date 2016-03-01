---
layout: post
title:  "Making a RESTFUL API with Yii 2.0"
date:   2016-02-24 10:23:43 -0600
categories: yii
---
Intro:

RESTFUL APIs are a modern way to serve data from your database for your app to consume.  They expose your data via JSON or XML so you can separate your database queries in the backend from the action in the frontend.

Steps:

## Download and install Yii 2 into an api subdirectory

Go to http://www.yiiframework.com/download/

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
	 'components' =>
		 [ 'urlManager' => [
			 'enablePrettyUrl' => true,
			 'showScriptName' => false,
			 'enableStrictParsing' => false,
			 'rules' => [
				 // ...
			 ],
		 ],
	 ],
	]

## Create a relational database structure
## Setup database config file
## Use Gii to make the RESTFUL controllers
## Edit the Controller
* Make it restful
* Customize the controller to change default actions
** (i.e. pagination / sorting)
* ## Edit the Model
* Define relations
* Show expanded fields
Example / demo
* Show how to write the URL to include expanded fields
