---
title: 'New Digital Garden'
layout: post
---

I've always wanted to keep an active blog, but despite having this aspiration for many years and revisiting it over and over, I've realized that my ideal way of sharing information might take a different form. For the next few weeks I am experimenting with a [digital garden](/garden), focusing on curating pages and resources rather than writing discretized updates. Follow along there if you'd like to see what I'm up to. It took a little while longer to setup than I would have liked, so I'm sharing how I set it up to save others a headache.

## Setting up the digital garden

I started by checking out a [guide](https://nesslabs.com/digital-garden-set-up) on setting up a digital garden on Ness Labs. Since I already have Jekyll and Github pages working together on this website, I decided to narrow my options to either this [digital garden theme](https://digital-garden-jekyll-template.netlify.app/) or [Simply Jekyll](https://simply-jekyll.netlify.app/posts/introduction-to-simply-jekyll). I thought the former was prettier so I decided to go with that. 

## Digital Garden Template

In order to set up the template, I needed to install Jekyll as I got a new computer. For some reason I was having a hard time getting the Ruby dev environment set up correctly. Eventually I found that people across the internet were having similar issues, and installing Ruby using [rbenv](https://github.com/orta/cocoapods-keys/issues/198#issuecomment-510909030) fixed it for me. 

Once Ruby was ready, I could bundle the Jekyll template following the instructions [here](https://maximevaillancourt.com/blog/setting-up-your-own-digital-garden-with-jekyll). Instead of deploying to Netlify, I wanted to use Github pages. However, I quickly ran into issues with Github pages not building. I commented out the graph feature in the template to get Github pages to build, but then realized that custom plugins (required for bi-directional linking in the digital garden) aren't allowed to run on Github pages. Instead, I created a second Github repository that just stored the `_site` folder from the digital garden (i.e. all the HTML files), and then committed [that](https://github.com/maggiedelano/garden).

## Editing Markdown

Part of this project was motivated by my desire to try out [Obsidian](https://obsidian.md/) to see how it might complement my use of [Roam Research](https://roamresearch.com/). To use Obsidian with the digital garden, open the `_notes` folder as a new vault. The digital garden supports page links using double brackets [[]] which is very convenient. There are some markdown files in the default template outside the `_notes` folder, which you can also open in Obsidian or another text editor. If you have any issues, it could be with page titles. Check out [this post](https://refinedmind.co/obsidian-jekyll-workflow) for ideas.


## Faster Deployment

One of the reasons I haven't been great about keeping this website and blog up to date is that I find even the friction of writing the files, building the site to verify them, and then committing the code to be a lot of overhead. If it's been a while, I even forget how to build the site and have to look it up again. To address this I wrote some [keyboard maestro](https://www.keyboardmaestro.com/main/) macro that expands to `bundle exec jekyll serve`. I also wrote a shell script that automatically commits and pushes the site to Github. This dramatically decreases the overhead, so I hope to spend more time [tending](/garden). 

``` zsh
#!/bin/zsh

git add .
git commit -m $1
git push

```

To use the script, put this in a file with a `.sh` file extension. Use `chmod 755 <yourfile>.sh` to make it executable. Then type `./<yourfile>.sh "your commit message"` to commit. You will probably need to enter your credentials once but can then store it for the future. 