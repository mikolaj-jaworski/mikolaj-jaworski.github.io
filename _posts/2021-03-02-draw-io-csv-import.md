---
layout: post
title: Creating DRAW.IO diagrams using CSV files
subtitle:
tags: [diagrams, draw.io]
comments: false
---
### Background
I needed to create graph for PowerBI Service resources for easy resources and users mapping. I was looking for a way to create DRAW.IO diagrams programmatically and I came across CSV graph definition which can be imported to [DRAW.IO](https://app.diagrams.net/).
I found it pretty nice feature, so I just wanted to summarize what I learnt.

### Creating CSV file
The most convenient way of creating such file is doing it with Excel, which would be easier to handle by non-coders (optionally we can automate CSV creation using Python and, in this case, PowerBI API). Also, we can leverage additional tables with static parameters which would be used in our input table later:

| type | fill | image |
| ---- | ---- | ----- |
|Workspace|	#009999|	https://img.icons8.com/carbon-copy/100/000000/power-plant.png|
|DataFlow	|#ff99ff	|https://img.icons8.com/ios/50/000000/database-import.png|
|DataSet	|#00ccff	|https://img.icons8.com/ios/50/000000/database.png|
|Report	|#99ffcc	|https://img.icons8.com/ios/50/000000/business-report.png|
|Dashboard|	#ffffcc	|https://img.icons8.com/dotty/80/000000/dashboard.png|
|User|	#ffffff	|https://img.icons8.com/windows/32/000000/user-male-circle.png|

So the final result may look like this, which can be converted to DRAW.IO's format by using TEXTJOIN function (instead of saving as csv and replacing semicolons):

|Object|ObjectType|Parent|ParentType|Relatives|fill|image|
|------|----------|------|----------|---------|----|-----|
|A1    |Workspace |		 |		    |         |#009999	|https://img.icons8.com/carbon-copy/100/000000/power-plant.png|
|B1	|DataSet|	A1|	Workspace|	us1,us2|	#00ccff|	https://img.icons8.com/ios/50/000000/database.png|
|C1	|DataSet|	A1|	Workspace|		|#00ccff|	https://img.icons8.com/ios/50/000000/database.png|
|D1	|Report	|B1	|DataSet	|us1,us2	|#99ffcc	|https://img.icons8.com/ios/50/000000/business-report.png|
|E1	|DataFlow|	A1|	Workspace|	|#ff99ff|	https://img.icons8.com/ios/50/000000/database-import.png|
|F1	|Dashboard	|D1	|Report|		|#ffffcc	|https://img.icons8.com/dotty/80/000000/dashboard.png|
|us1	|User|	||			|#ffffff	|https://img.icons8.com/windows/32/000000/user-male-circle.png|
|us2	|User|  ||			|#ffffff	|https://img.icons8.com/windows/32/000000/user-male-circle.png|

### Import to DRAW.IO
Having our CSV file, we can import it in *Arrange/Insert/Advanced/CSV* form. It should be filled with some sample code and many explanations, which I replaced by even simplier version of it:

```
# label: %Object%<br><i>%ObjectType%</i>
#
# style: label;image=%image%;whiteSpace=wrap;html=1;rounded=1;fillColor=%fill%;
#
# parentstyle: swimlane;whiteSpace=wrap;html=1;childLayout=stackLayout;horizontal=1;horizontalStack=0;resizeParent=1;resizeLast=0;collapsible=1;
#
# namespace: csvimport-
#
# connect: {"from": "Parent", "to": "Object", "invert": true, \
#          "style": "curved=1;endArrow=blockThin;endFill=1;fontSize=11;"}
# connect: {"from": "Relative", "to": "Object", "style": "curved=1;fontSize=11;"}
#
# ignore: image,fill
#
## ---- CSV ----
Object,ObjectType,Parent,ParentType,Relative,fill,image
A1,Workspace,,,,#009999,https://img.icons8.com/carbon-copy/100/000000/power-plant.png
B1,DataSet,A1,Workspace,"us1,us2",#00ccff,https://img.icons8.com/ios/50/000000/database.png
C1,DataSet,A1,Workspace,,#00ccff,https://img.icons8.com/ios/50/000000/database.png
D1,Report,B1,DataSet,"us1,us2",#99ffcc,https://img.icons8.com/ios/50/000000/business-report.png
E1,DataFlow,A1,Workspace,,#ff99ff,https://img.icons8.com/ios/50/000000/database-import.png
F1,Dashboard,D1,Report,,#ffffcc,https://img.icons8.com/dotty/80/000000/dashboard.png
us1,User,,,,#ffffff,https://img.icons8.com/windows/32/000000/user-male-circle.png
us2,User,,,,#ffffff,https://img.icons8.com/windows/32/000000/user-male-circle.png
```

### Exploring complex diagrams
Unfortunately, during CSV import no containers, pools or lanes could be defined, so we need to do it by ourselves. However, if our graph is quite complex, it can be explored in the browser.
When publishing (*File/Publish/Link* - available only in the browser app), we need to add *&p=ex* somewhere in the URL. This would allow user to interact with the graph and eventually make it more readable.

### Sources
- [Automatically create draw.io diagrams from CSV files](https://drawio-app.com/automatically-create-draw-io-diagrams-from-csv-files/)
- [Examples: Import from CSV to draw.io diagrams](https://drawio-app.com/import-from-csv-to-drawio/)
- [Animation and Automatic Layout: Explore Complex Diagrams](https://drawio-app.com/animation-and-automatic-layout-explore-complex-diagrams/)

Thanks for reading!