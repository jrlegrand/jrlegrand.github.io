---
layout: post
title:  "Visualizing Prescription Drug Monitoring Program (PDMP) Data"
date:   2019-07-31 10:12:00 -0600
categories: informatics
---
As a former retail pharmacist of 5+ years, I always felt like the prescription drug monitoring program (PDMP) user interface was horrible.  The results of a search (at least in the state where I worked) were returned in a PDF document which didn't allow for any interaction (sorting / filtering / decision support / etc), prescriber and pharmacy information was hidden away at the bottom of the PDF and only referred to within the data as a strange code name, and trying to figure out whether a patient had red flags for prescription diversion took way more math and focus than it needed to.

This is an example of what the PDMP currently looks like in my state for a patient query (all of this is fake data, FYI).

![PDMP](https://raw.githubusercontent.com/jrlegrand/jrlegrand.github.io/master/img/fill-view/pdmp.jpg)

I always thought there had to be a better way, and wanted to mock-up some potential UI designs.  I started with some [Google charts](https://developers.google.com/chart/), but quickly had to abandon them and move toward custom SVG visualizations because nothing out of the box seemed to be working.  I ended up [building a functional prototype](https://legrand.io/fill-view/) using [ReactJS](https://reactjs.org/) and [D3.js](https://d3js.org/).

The main focus of my prototype is to display the data in a "timeline heatmap" format where it is easy to see where prescriptions overlap in terms of fill date and duration.  Granted, this could be misleading if the dosage changes or for several other reasons; however, it immediately calls attention to these periods of time.

![Heatmap](https://raw.githubusercontent.com/jrlegrand/jrlegrand.github.io/master/img/fill-view/heatmap.jpg)

I created options to change:
* Color - overlap or MME/day
* Granularity - drug class, generic, or strength
* Days - lookback days from 30 to 365 and several points inbetween

![Options](https://raw.githubusercontent.com/jrlegrand/jrlegrand.github.io/master/img/fill-view/options.jpg)

The secondary focus of my prototype is a sidebar where notifications or decision support could be baked in for common red flags.  It also would be a place to quickly check details on prescribers, pharmacies, and prescriptions -- as well as a way to quickly filter on any of these items.  Hovering over any provider or pharmacy highlights all the fills within the timeline heatmap.

![Overview](https://raw.githubusercontent.com/jrlegrand/jrlegrand.github.io/master/img/fill-view/overview.jpg)

When it all comes together, this is what it looks like.

![Fill View](https://raw.githubusercontent.com/jrlegrand/jrlegrand.github.io/master/img/fill-view/fillview.jpg)

I'll be the first to admit that I am playing with idealistic and fake medication history data, and there will undoubtedly be informatics challenges (i.e. free text meds / difficulty calculating days' supply / etc) if/whenever I get my hands on some actual de-identified patient PDMP data.  And I appreciate that I'm going about this a bit backwards by working on a visualization without having actual data.  I just wanted to see if I could create an intuitive user interface for dealing with this type of task with the eventual goal of integrating.  I can also see this being beneficial for visualizing patient compliance with non-opioid medications, which is why I named it something general like "Fill View" as opposed to something opioid-specific.

How quickly can you tell whether or not this patient is filling controlled substances early with the PDF versus the visualization?

Link to active prototype: [Fill View](https://legrand.io/fill-view/)

I don't think my prototype is the best solution -- nor do I think it is the first thing that needs to be fixed with PDMPs (I can get into that some other time).  However, I think that somehow visualizing this data and making it interactive without introducing unnecessary clicks or user interaction could potentially speed up the process of checking the PDMP and potentially change prescriber (and pharmacist) decision making behavior.
