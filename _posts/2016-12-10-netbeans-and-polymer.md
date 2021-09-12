---
layout: post
title: "Netbeans and Polymer"
date: "2016-12-10"
categories: 
  - "java"
  - "javascript"
  - "programming"
tags: 
  - "netbeans"
  - "polymer"
---

## Polymer HTML code completion for Netbeans 8.2

Thanks to this [wonderful extension](https://github.com/ladariha/PolymerForNB) I can get rid of annoying errors in HTML editor when I edit polymer components in html files.

Because I use maven project type and netbeans , there is no ".nbproject" folder. In that case the "customs.json" file must be present in src/main/webapp folder.

[![2016-12-10-11_08_11](/assets/images/2016-12-10-11_08_11--300x240.png)](http://bisaga.com/blog/wp-content/uploads/2016/12/2016-12-10-11_08_11-.png)

### Recreate new version of customs.json

Because not all attributes are recognized by customs.json I will recreate it as explained in project documentation.

After downloading master zip file, you need to create "**dist**" folder under src folder and then run index.js file.

igorb@Pavilion ~/PolymerForNB-master/src
$ mkdir dist

igorb@Pavilion ~/PolymerForNB-master/src
$ node index.js

New copy of **customs.json** will be created in dist folder, just copy it to the webapp folder and that's it.

## Merge changes back to project file

Don't forget if you already used customization and added something, you need to merge old version with new one or you will lose your changes...

Well, it's not so simple, looks like after you add few changes to customs.json with HTML editor, the file (customs.json) changed very  dramatically. Looks like netbeans  reorganize whole structure or something.
