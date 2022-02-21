---
layout: post
title: Website Revamp!
image: /images/mkd_header.png
tags:
- website
---

Greetings from my new website! I had a little time this weekend, and to distract myself from the hellhole that is 2020, I decided to port my previous site (which was mostly handwritten in HTML with a header and some organization using [bootstrap](https://getbootstrap.com/ "bootstrap") and hosted on Swarthmore's servers) to [Jekyll](https://jekyllrb.com/ "Jekyll"), with hosting on [GitHub Pages](https://pages.github.com/ "GitHub Pages"). I wanted to be able to combine my website and former tumblr blog into one, and Jekyll seemed like an easy way to do that. I considered porting to [Gatsby](https://www.gatsbyjs.org/ "Gatsby"), but I know nothing about [React](https://reactjs.org/ "React") or [GraphQL](https://graphql.org/ "GraphQL") and learning seemed like much more than a weekend project. As it was, I had "fun" trying to figure out how to vertically align my photo and my contact information on the main page, while also being responsive / looking good on both desktop and mobile, so it seems 12 year old software is advanced enough for my limited web programming skills. But hey, I did finally learn the basics of CSS. Here's a tour of the changes I made.

<!--* TOC
//{:toc}-->

# Jekyll and Pages Setup

I followed the [Jekyll Installation Guide](https://jekyllrb.com/docs/installation/) to get started. There were a few minor hiccups getting things to work on macOS; I occasionally had to use `sudo` to execute certain lines of code, which made certain pages unwritable by my user. I fixed this using `chmod -R 775 jekyllSite` (assuming `jekyllSite` is the name of the folder with my Jekyll installation in it).

Once the base Jekyll blog was setup, I moved on to configuration for [GitHub pages](https://pages.github.com/ "GitHub pages").  GitHub pages has two different hosting modes: user/organization pages and project pages. They need to be setup slightly differently. To use the user page, I renamed my repo (previously called "website") to "maggiedelano.github.io", moved my old website completely out of the repository, and moved my Jekyll install into the root of the repository. GitHub pages then automatically served my Jekyll installation from the `master` branch. You can check that GitHub pages is serving by checking the repository settings on GitHub (it's toward the bottom).

# Porting Tumblr Blog

To port my blog, I ran the following line on the command line while in the root Jekyll directory for the new blog:

``` ruby

$ ruby -r rubygems -e 'require "jekyll-import";
    JekyllImport::Importers::Tumblr.run({
      "url"            => "https://maggiedelano.tumblr.com",
      "format"         => "md", # or "md"
      "grab_images"    => true,  # whether to download images as well.
      "add_highlights" => false,  # whether to wrap code blocks (indented 4 spaces) in a Liquid "highlight" tag
      "rewrite_urls"   => true   # whether to write pages that redirect from the old Tumblr paths to the new Jekyll paths
    })'
```

There are many other importers available [here](https://import.jekyllrb.com/ "Jekyll Importers").

# Moving Blog Posts to blog/

The Jekyll default theme is [Minima](https://github.com/jekyll/minima "Minima").[^2] This theme displays the blog posts on the home page by default, with the option to add some content at the top before the blog posts begin using the _index.md_ or _index.html_ files in the root directory. I decided to change the layout for the home page and instead moved the code that produces the blog posts to its own file called _blog.md_. Minima then automatically created a link to the blog in the header.

[^2]: The Minima files on GitHub are the latest under development, but by default my Jekyll installation came with an earlier version (2.5.1). I had to run `bundle info --path minima` to find the files actually being used locally by Jekyll.

To change the layout for the home page, I had to add a new _\_layouts/home.html_ file. I ran `bundle info --path minima` to find where Minima's default files are, and copied _home.html_ to _\_layouts/home.html_, removed the content related to blog posts, and saved the file. Any files in the same directory structure as the original Minima gem will automatically override anything inside the Minima gem when serving. (This does mean updating the theme will require more work later). Here's the new blog page contents:[^1]

[^1]: Fun side note: I had to wrap the `<html>` tags in `{ % raw % } ... { % endraw % }` so that the Jekyll [Liquid](https://jekyllrb.com/docs/liquid/ "Liquid") parser would output the raw text and not all the blog posts on my site.

``` html
---
layout: default
title: Blog
permalink: /blog/
---
{% raw %} <html>
  {%- if site.posts.size > 0 -%}
    <h2 class="post-list-heading">{{ page.list_title | default: "Posts" }}</h2>
    <ul class="post-list">
      {%- for post in site.posts -%}
      <li>
        {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
        <span class="post-meta">{{ post.date | date: date_format }}</span>
        <h3>
          <a class="post-link" href="{{ post.url | relative_url }}">
            {{ post.title | escape }}
          </a>
        </h3>
        {%- if site.show_excerpts -%}
          {{ post.excerpt }}
        {%- endif -%}
      </li>
      {%- endfor -%}
    </ul>
  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></p>
  {%- endif -%}
<html>{% endraw %}
```

I used the _default_ layout instead of _page_ layout so it doesn't put the page title at the top. The last thing I did was enable excerpts to show on the blog page, which can be done by adding the line `show_excerpts: true` to the _\_config.yml_ page.

# Fun with Flexbox

One thing that continues to surprise me about web programming is how much of a pain it is to vertically align things and also be responsive. For example, consider the header on my webpage:

![Header of maggiedelano.com](/images/mkd_header.png)

I wanted a way to ensure that my face was vertically aligned with the text on the right. A relatively easy way to do this is with a table; unfortunately, when these elements are put into a table and viewed on mobile the image of my face shrinks to be tiny so that the full table can be displayed in the much narrower space. My solution ultimately was to use [flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/ "flexbox"), and size the header so that it is small enough to display on a phone will a small screen. (I could probably have also experimented with flex items, but this works well enough for now).

Setting this up required writing some custom HTML and css.[^3] Since this is a small website, I hard-coded `<div>`s for the whole block (`all\_together`), and then one each for the headshot and text so that I could write custom CSS for them.[^4] 

[^3]: I finally learned how CSS actually works by completing the CSS chapters of the Khan Academy [Intro to HTML/CSS: Making Webpages](https://www.khanacademy.org/computing/computer-programming/html-css "Intro to HTML/CSS: Making Webpages") course.

[^4]: Writing custom CSS for the Minima version I am using requires copying _\_sass/minima.scss_ and then the contents of the _\_sass/minima/_ folder and then adding or editing accordingly.

``` html
<html>
    <div id="all_together">
        <div id="headshot">
            <img src="MKD_headshot.jpg" width="300">
        </div>
        <div id="text">
            <h2>Maggie Delano</h2>
            Assistant Professor <br>
            Engineering Department <br>
            Swarthmore College <br>
            <br>
            Office: Singer 233 <br>
            Email: <a href="mailto:mdelano1@swarthmore.edu">mdelano1@swarthmore.edu</a> <br>
            Phone: 610-328-8295 <br>
            <br>
            Fall 2020 Office Hours: TBD
        </div>
    </div>
</html>
```

Here's the part I didn't know how to do when I built my last website: vertical alignment with flexbox! This code was added to _\_sass/minima/\_layout.scss_.

``` css
#all_together{
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  margin-bottom: 25px;
}   

#headshot {
  width: 300px;
}

#text {
  @include relative-font-size(0.9375);
  margin-right: 75px;
}
```

Flexbox has configuration settings for the parent container (in this case `#all_together`) and also for the individual items. In this case, I only needed to add CSS to the parent container. I chose the `flex-direction` to be a row and for it to wrap via `flex-wrap`; this way my headshot and contact information would show one after the other (headshot first) on a smaller page. `align-items` aligns everything to the center vertically. Lastly, I added a bottom margin to give things a little breathing room. For `#headshot`, I just ensured that the width was max 300px, and for `#text`, I used the same font size as the rest of the page and added a right margin to get it to line up the way I wanted in the page.

# Adding a Publications Page

Once everything else was set up, adding a publications page was easy: I just wrote the whole thing in markdown and linked to my CV. Hurray!

# Custom Domain

My last step was to set up GitHub pages to use a custom domain (www.maggiedelano.com). Fortunately following the [instructions](https://docs.github.com/en/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site "GitHub custom domain instructions") went smoothly, though I also added a [301 redirect](https://support.google.com/webmasters/answer/93633?hl=en) from maggiedelano.com to www.maggiedelano.com on my host provider ([Namecheap](https://www.namecheap.com/ "Namecheap")) because just typing maggiedelano.com in the browser without it wasn't working anymore.

And then I was done (zzz).

# Footnotes

<style>
.center-image
{
    margin: 0 auto;
    display: block;
}
</style>