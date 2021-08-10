---
title: 'Make Your Roam Theme Your Own With These CSS Snippets'
layout: post
---

There are a lot of amazing themes for [Roam Research](https://roamresearch.com/), many of which come with custom CSS enhancements in addition to changing the default colors. But to fully customize your experience you sometimes want to add custom CSS to an existing theme (or your own theme). Here's a collection of CSS snippets that can help make your Roam CSS theme really feel like your own. 

* TOC
{:toc}

# Setup

To use the snippets, you'll need to set up [custom CSS](https://forum.roamresearch.com/t/awesome-css-for-beginners-and-intermediates/593/7) for your database if you haven't already. I recommend using the [Stylus Chrome Extension](https://chrome.google.com/webstore/detail/stylus/clngdbkpkpeebahjckkjfobafhncgmne?hl=en), but you can also use the native [[roam/css]] page. Using a fully fledged theme in addition to these snippets is optional.

# Custom Tags 

One of the best ways to customize your Roam CSS is custom tags. You can use the custom tags to indicate a type of note, to help visually organize your daily notes, etc. For example, I use custom tags as part of my daily notes template:

<img src="/images/rmdaily.png" width="60%">

This styling includes emoji that are placed before the tag (using the `::before` pseudo class), and then additional styling for the text itself. You can also style tags based on phrases that they start with, end with, or contain. This tag style was created and popularized by [Maggie Appleton](https://maggieappleton.com/paintingroam). Here's the relevant CSS snippet ([source](https://github.com/theianjones/roam-research-themes/blob/master/mappletons.css) for first two selectors):


```css
/* add content before a tag (change yourTagHere to the tag)
   you can also add content ::after */
span.rm-page-ref[data-tag="yourTagHere"]::before  {
    content: "💭 "
}

/* style tag text */
span.rm-page-ref[data-tag="yourTagHere"]{
    color: #31b350 !important;
    padding: 3px 7px;
    line-height: 2em;
    font-weight: 600;
}

/* style tag starting with text 
   you can substitute $= for "ends with" and *= for "includes" */
span.rm-page-ref[data-tag^="Evergreen"]{
    color: #25883d !important;
    font-weight: 600;
}
```
# Custom Pages 

Pages and tags in Roam are functionally equivalent, but are styled differently. I style pages for specific namespaces using `data-link-title^=`, and dates using `data-link-title$="2020"`. As with tags, you can add content before and after each page. Here are some examples:

 <img src="/images/rmpages.png" width="60%">

And here's the css ([source](https://github.com/jmharris903/Railscast-for-Roam-Research-Theme) for first two selectors):
```css
/* add content before a page (change yourTagHere to the tag)
   you can also add content ::after */
span[data-link-title="yourTagHere"]::before  {
    content: "💭 "
}

/* style tag text */
span[data-link-title="yourTagHere"]{
    color: #31b350 !important;
    padding: 3px 7px;
    line-height: 2em;
    font-weight: 600;
}

/* add a calendar to all dates (pages that end w/ 2020...)
   you can substitute $= for "ends with" and *= for "includes" */
span[data-link-title$="2020"] .rm-page-ref-link-color::before{
    content: "🗓 ";
}
```

# Turn page brackets off 

While you can toggle brackets in Roam on and off using Ctrl-c then Ctrl-b, you can also disable them in CSS ([source](https://github.com/theianjones/roam-research-themes/blob/master/mappletons.css)):

```css
.rm-page-ref-brackets {
    display: none !important;
}
```

# Pretty Block References

While the default block reference styling is very clear, Maggie Appleton created a prettier version of block references ([source](https://github.com/theianjones/roam-research-themes/blob/master/mappletons.css)):

<img src="/images/rmref.png" width="60%">

```css
/* add vertical line before block reference*/
.rm-block-ref::before {
    content: '';
    display: inline-block;
    width: 2px;
    border-radius: 40px;
    height: 12px;
    background: #ff913c;
    margin-right: 8px;
}

/* turn off underlining */ 
.rm-block-ref{
    border-bottom: none;
}
```
# Round checkbox

Some people like round checkboxes for their to dos in Roam; now you can have that with this short snippet! (thanks to David on [Roam Slack](https://roamresearch.slack.com/join/shared_invite/zt-ft43m4mo-DDXHHaguKCIEgU09P1ittA#/))

<img src="/images/rmcheckbox.png" width="40%">

```css
.checkmark {
  border-radius: 50% !important;
}
```

# Styling aliases and links

In addition to page refs with brackets `[[]]`, you can use aliases for external links, links to blocks, and links to pages (`[]()`). This CSS snippet allows you to style each of these different types of links, including links for specific Roam databases (thanks to Steven Salka on [Roam Slack](https://roamresearch.slack.com/join/shared_invite/zt-ft43m4mo-DDXHHaguKCIEgU09P1ittA#/)):

```css
/* style external, block, and page aliases 
   move to their own block to alter individually */
a.rm-alias-external, a.rm-alias-block, a.rm-alias-page {
  
}

/* style links to specific Roam DB (change db_name) */
a[title^="url: https://roamresearch.com/#/app/db_name/"]{
  
}
```

# Customize Queries 

Queries are a powerful component of Roam, but they have a lot of extra context that is not always necessary (for example, when using queries for to do lists or project management). [Jeff Harris](https://github.com/jmharris903/Railscast-for-Roam-Research-Theme) and [Matt Goldenberg](http://mattgoldenberg.net/2020/05/18/todoist-to-roam-research-advanced-task-management-in-roam/) have developed custom CSS to hide different parts of the query, including the title, breadcrumbs, and query string:

```css

/* RR change: MINIMIZE QUERIES: add any one of the following tags before the beginning of your query (in the same block):

    #min-title = hides the page reference link / page title
    #min-con = hides the contextual reference information (breadcrumbs)
    #minimal = hides both the title and the context
    #min-q = hides the query string, similar to legacy behavior
    #min-all = hides everything — title, context, and query string

inspired by Matt Goldenberg */

[data-tag="minimal"], 
[data-tag="minimal"] + .rm-query .rm-title-arrow-wrapper,
[data-tag="minimal"] + .rm-query .zoom-mentions-view {
  display:none!important; /* hide page reference (title) and mention context (breadcrumbs) */
}
[data-tag="min-title"], [data-tag="min-title"] + .rm-query .rm-title-arrow-wrapper {
display:none!important; /* hide page reference (title) */
}
[data-tag="min-con"], [data-tag="min-con"] + .rm-query .zoom-mentions-view {
  display:none !important;  /* hide mention context (breadcrumbs) */
}
[data-tag="min-q"], 
[data-tag="min-q"] + .rm-query .rm-query-title {
  display:none !important;  /* hide the query string */
}
[data-tag="min-all"], 
[data-tag="min-all"] + .rm-query .zoom-mentions-view,
[data-tag="min-all"] + .rm-query .rm-title-arrow-wrapper,
[data-tag="min-all"] + .rm-query .rm-query-title {
  display:none !important;  /* hide everything */
}
[data-tag="minimal"] + .rm-query .rm-query-title::after,
[data-tag="min-title"] + .rm-query .rm-query-title::after,
[data-tag="min-con"] + .rm-query .rm-query-title::after{
  content: " #minimal" /* add a tag to the query string to indicate this query has been minimized */
}
```

# Writing Your Own Snippets

My advice for writing your own CSS snippets is to take a little time to complete this [Basic CSS Tutorial](https://www.khanacademy.org/computing/computer-programming/html-css/intro-to-css/pt/css-selecting-by-class), and then start poking around in Roam using the [inspector](https://developers.google.com/web/tools/chrome-devtools/css). The tutorial is helpful because once you identify the class you'd like to modify you may still need help figuring out how to write the appropriate selector. You can also browse around in existing Roam themes and mix and match what works for you.

# Resources

Here's a more complete set of references I've found helpful:

- Learning CSS:
    - [Basic CSS Tutorial](https://www.khanacademy.org/computing/computer-programming/html-css/intro-to-css/pt/css-selecting-by-class) (Khan Academy)
    - [Get Started With Viewing And Changing CSS](https://developers.google.com/web/tools/chrome-devtools/css) (for Chrome, similar in other browsers)
    - [Painting Roam](https://maggieappleton.com/paintingroam)
    - [CSS Cheat Sheet](https://htmlcheatsheet.com/css/)
- Roam themes:
    - [RoamCult Themes](https://roamresearch.com/#/app/help/page/fJRcVITNY)
    - [Roam Themes](https://roamthemes.io/)
    - [Roam theme tester](https://gist.github.com/kipply/a42c66b5b962d07b5f4bc37812c21e00)
- Help with Roam CSS:
    - [Roam Slack](https://roamresearch.slack.com/join/shared_invite/zt-ft43m4mo-DDXHHaguKCIEgU09P1ittA#/) (join the #css-themes channel)
    - [Roam Forum](https://forum.roamresearch.com)

Let me know on [twitter](https://twitter.com/maggied) or in Roam slack/the forums if there are cool snippets I've missed, or more you'd like to see.
