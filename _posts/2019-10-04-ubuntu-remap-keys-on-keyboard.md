---
title: "Ubuntu - remap keys on keyboard"
date: "2019-10-04"
categories: 
  - "installation"
tags: 
  - "keyboard-layout"
  - "ubuntu"
---

I have apple keyboard and default layout is not good enough. The less and greater character appear on the wrong key, by default there are on the key above left tab key but I need them down on the second key in the second row, just before "y" character.

This change is from the **Ubuntu 18.04** version.

Open keyboard definition for my country (si) from the /usr/share/X11/xkb/symbols folder.

sudo gedit /usr/share/X11/xkb/symbols/si

**Change the "cedilla , diaeresis" to "less, greater" and re-login.**

Original file content:

default  partial alphanumeric\_keys
 xkb\_symbols "basic" {
 `include "rs(latin)" name[Group1]="Slovenian"; key <TLDE> { type[Group1]="TWO_LEVEL", [ **cedilla, diaeresis** ] };`
 };
 partial alphanumeric\_keys
 xkb\_symbols "us" {
 `include "rs(latinyz)" name[Group1]= "Slovenian (US, with Slovenian letters)"; key <TLDE> { type[Group1]="TWO_LEVEL", [ **cedilla, diaeresis** ] };`
 };
 partial alphanumeric\_keys
 xkb\_symbols "alternatequotes" {
 `include "rs(latinalternatequotes)" name[Group1]= "Slovenian (with guillemets)"; key <TLDE> { type[Group1]="TWO_LEVEL", [ **cedilla, diaeresis** ] };`
 };

After the change :

default  partial alphanumeric\_keys
 xkb\_symbols "basic" {
 `include "rs(latin)" name[Group1]="Slovenian"; key <TLDE> { type[Group1]="TWO_LEVEL", [ **less, greater** ] };`
 };
 partial alphanumeric\_keys
 xkb\_symbols "us" {
 `include "rs(latinyz)" name[Group1]= "Slovenian (US, with Slovenian letters)"; key <TLDE> { type[Group1]="TWO_LEVEL", [ **less, greater** ] };`
 };
 partial alphanumeric\_keys
 xkb\_symbols "alternatequotes" {
 `include "rs(latinalternatequotes)" name[Group1]= "Slovenian (with guillemets)"; key <TLDE> { type[Group1]="TWO_LEVEL", [ **less, greater** ] };`
 };
