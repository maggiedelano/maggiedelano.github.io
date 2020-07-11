---
layout: post
title: The little things you needed to know about Altium
date: '2014-02-09T22:51:09-05:00'
tags:
- phd
- altium
- ee
- pcb
tumblr_url: https://maggiedelano.tumblr.com/post/76186995705/the-little-things-you-needed-to-know-about-altium
---
I decided to switch from [Eagle](http://www.cadsoftusa.com/) to [Altium](http://www.altium.com/) for the next PCB I am designing for my PhD. While Altium is definitely a more professional tool, I found the startup time for my first design to be a bit longer than I would have liked. Below is a list of a few little things that seem simple in theory, but were non-intuitive for me to figure out the first time around (even with the help of google and the tutorial pdfs and videos).

- **Where are all the components???** - in the vault! Which is found in View \> Workspace Panels \> System \> Vaults
- **Displaying Resistor/Capacitor values** - not sure how to do it by default, but you can double click on a component after you’ve placed it to turn the “value” parameter on
- **Place a wire in schematic (shortcut!)&nbsp;**- the ‘p’ button brings up the place menu and the 'w’ button places a wire
- **Why are there “?” everywhere???&nbsp;** - you have to set up the designators! Find it under Tools \> Annotate schematics
- **Why does Altium complain about floating sources, mismatched I/O pins, etc.?&nbsp;** - The default connection matrix is a bit unintuitive, and won’t always recognize capacitively coupled connections. You can adjust it in Project \> Project Options \> Connection Matrix
- **I am placing a multi-part component but only see the first part. Help!&nbsp;** - When you place the part, right click first and then select the first part, then after placement it will allow you to place the remaining parts.
- **OK, I’m ready to layout my PCB. Now what?&nbsp;** - Be sure you annotate your schematics so there are no components with “?”. Create a new PCB file in your project. Then, from a schematic view, hit Design \> Update PCB Document. Your components should be imported into your new PCB file.
- **How do I start routing interactively?&nbsp;** - 'p’ for place, then ’t’ to start routing interactively.
- **How do I update my design rules?** - right click in PCB document \> Design \> Design Rules
- **Help my ground plane is obstructing my view of everything!** - Change the transparency! Right click on the layers below, select layer transparency \> signal layers and adjust the polygon transparency.

I haven’t actually exported my gerbers yet, but I’ll add a section for that when I do.

Other helpful stuff: [This](http://www.johnstowers.co.nz/blog/pages/altium-designer-tips.html)&nbsp;article was really helpful for finding shortcuts quickly. [This](http://wiki.altium.com/spaces/flyingpdf/pdfpageexport.action?pageId=3080293)&nbsp;PDF was helpful for creating my first library component. [These](https://code.google.com/p/altium-designer-addons/downloads/detail?name=Libs_RRutledge%202013-01-30.zip&can=2&q=) libraries are useful for extra connectors and miscellaneous things not found in the Vault.

