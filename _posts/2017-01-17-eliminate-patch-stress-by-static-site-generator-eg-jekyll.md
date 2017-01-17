---
layout: post
title: Eliminate CMS Patch Stress by Using a Static Site Generator like Jekyll
date: 2017-01-17 21:22:00.000000000 +01:00
type: post
published: true
status: publish
categories:
- general
meta:
author:
  display_name: Martin Stut
---
During the years, I have set up and/or maintained a number of websites for christian organizations and individuals. But I don't have the resources to be ready 24/7 to apply security patches to their content management systems (CMS). So I need an architecture that doesn't need security patches, which means to have **only static files, no executable code, on the public web server**. Hand editing HTML files stops being cool after applying the third theme change to a dozen of pages each. This is where **static content management systems**, a.k.a. site generators, come in. My current favourite is [Jekyll](http://www.jekyllrb.com), which I'm featuring in this post.

## CMS Patch Stress

### Todays Internet is a Shark Pool

Everywhere hackers are lurking to take over web sites that happen to run on vulnerable content management systems (CMS). There are even specialized search engines helping them to find their few thousand potential victims among the millions of all websites. 

### Example: Drupalgeddon

The event that showed me how bad things really are, was "Drupalgeddon" in 2014. On October 15 at 4:02pm UTC (just after 5 p.m. in Central Europe, potentially just after the good guys closed their e-mail clients for the day), the Drupal Security Team published [SA-CORE-2014-005 - Drupal core - SQL injection](https://www.drupal.org/SA-CORE-2014-005):  
> "A vulnerability in this [database abstraction] API allows an attacker to send specially crafted requests resulting in arbitrary SQL execution. Depending on the content of the requests this can lead to privilege escalation, arbitrary PHP execution, or other attacks. This vulnerability can be exploited by anonymous users."

Because a patch was published, the bad guys immediately knew how to exploit this security hole. With Drupal being a widely used content management system, the hackers saw a huge prey, so within a few hours they developed an exploit and attacked a huge number of websites.

On Oct 29th, two weeks after the vulnerability and the patch was announced, the Drupal Security Team published 
[Drupal Core - Highly Critical - Public Service announcement - PSA-2014-003](https://www.drupal.org/PSA-2014-003), containing a sentence that, in my view, redefined the bar for web maintainers: "You should proceed under the **assumption that every Drupal 7 website was compromised unless updated or patched** before Oct 15th, 11pm UTC, that is **7 hours after the announcement**." (emphasis mine). So, unless you had updated your website before midnight (Central Europe), you had to assume it was hacked.

### Duty of Website Maintainers

This means that, if you are responsible for the security of a website driven by a dynamic content management system, you must provide for responding (patching the CMS) to a sufficiently important announcement within less than 7 hours, no matter what the local time in your part of the world is.

In 20 years of website building, I have seen too many sites hacked to ignore this issue.

### Potential Ways Out of CMS Patch Stress

If you absolutely need a dynamic CMS, running executable code on the pulic web server, and you don't have the ressources to do timely patching 24/7, **someone else needs to do it**.

* **Paying some IT specialist** to do the updates for you. This usually means big bucks, possibly as high as 50 € per month, e.g. an hour's time of that IT specialist for applying each monthly security patch.
* Using an **ad supported service**. This is the business model of blogger.com or the free tier of wordpress.com. The service provider adds ads and all sorts of tracking to your blog and thus turns you and your readers into a product being sold to advertisers. I personally want to get out of this, which is my main reason for moving this blog away from wordpress.com .
* Use a **paid mass-standard service** that does not add ads and tracking. Some web hosters offer "X-CMS" hosting where the hoster does care for updates. This can be a viable option, as cost typically is reasonable at around 10 € per month, as long as you don't need the flexibility of plugins, because many plugins aren't supported, let alone updated, by the service provider - and updates are the main reason why you think of paying.
* Use an **architecture that does not have vulnerabilities**. See below.

## Static CMS as a Way Out

What is the problem with those CMS that execute code on the public web server? The server has executable code, that can be called by literally anyone in the world, just by requesting a URL. If this code contains a security hole, e.g. by doing Bad Things when maltreated with sufficiently strange parameters, then the website has lost.

But unless you want to hand-edit all HTML pages, you do need some code to generate the data that will be sent to the visitor's browser. An effective solution is to **keep that code away from the public web server**, running it on a separate development machine. This is the idea of static CMS, also known as static site generators.

The security of such websites still relies on FTP etc. being hardened, so hackers can't mess with your static files, but this easier than securing a CMS. Usually this is your shared web-hoster's job, who should be knowing what he is doing (not all do, unfortunately).


### Basic Principle

* generator/compiler
* runs on development machine
* theme defined by template files
* content written in separate files, one per page
* needs a templating system to merge content and theme, perform a little logic such as including variables, files, "if", "previous", "next", ...
* generates a set of **static** files, ready to be uploaded to the web server.

=> no executable code on the web server, only on the development machine.


### Advantages of static CMS

* no urgent security patches needed, because there is no code on the public web server that could be exploited
* very fast site serving, because serving static files consumes very little server ressources
* works well on low cost shared hosting accounts
* backup of web server data always exists: the development machine
* version control, e.g. by Git, of content and theme, because its all text files.
* works like software development, thus is attractive to certain groups of IT people, because it *is* software development

### Limitations of Static CMS

These are essentially the limitations of a static-only website, no matter how the static files are generated.

* **No way to process and respond to user input**, e.g. when running a forum or a web-shop. But there are limited ways out:
  * mailto: link, possibly JavaScript-protecting the address, directed to a provider with good spam handling. This can be sufficient for a contact form or low volume blog comments.
  * Outsource form processing to a hardened server/service. The form itself can be on the static website, only the processing (form action target) needs to be on a code-running site.
  * Outsource sub-functions, such as blog discussions, to a specialized, potentially paid, service. A widely used example is Disqus (which has its own issues I won't elaborate here).
* **Content can of course only be edited on the development machine.** But does your project *really* need editing on the web? I have seen many web projects where the original request was to enable editing by several users, but in reality, during the lifetime of the site, only the webmaster/developer ever edited content. As a service providing person you could even sell this as an advantage like "you just need to tell me your changes and I'll take care of everything". No lengthy CMS training to appliance type end users.
  * Potential way out: use a file synchronization service, such as NextCloud or Dropbox, to share the project files with the editors. Then the editors can add/edit Markdown text files themselves. They just need to call you to trigger the build/upload process once they have polished the content.
* **Setting up the system and editing content requires development-like capabilities**, so this is not for end users. But setting up an instance of a CMS isn't either. You just can't compete with the ease of setup of services like blogger.com (a.k.a. Google) or wordpress.com.


## Example Static CMS: Jekyll

Product website: https://jekyllrb.com/

### Origin, Popularity

* Github offers Jekyll for GitHub Pages.
* https://www.staticgen.com/ lists Jekyll as the most popular open-source static site generator, with about twice as many stars as number two (Hugo).

Given that broad support, I decided to create my small business website with Jekyll instead of my 1998 homegrown generator based on M4 and GAWK.

### Technical Foundation

* Ruby
* makes heavy use of Ruby gems
* Liquid templating engine
* SCSS CSS preprocessor included
* Markdown content processing - of course HTML is acceptable too.
* Special feature: Jekyll is blog-aware, with much of its code designed to cater for a blog, including categories, tags etc.

### Quick Start - Try It Yourself

Copied from the Quick-start Instructions on https://jekyllrb.com/docs/quickstart/

Do this on what could become your web site development machine.

1. Install [Ruby](https://www.ruby-lang.org/) (at least version 2, newer is better). Do include the development headers/tools. This is easier on Mac and Linux than on Windows. For details see https://jekyllrb.com/docs/installation/#requirements/ and its links.
2. Install Jekyll and Bundler gems through RubyGems:  
<code>~ $ gem install jekyll bundler</code>
3. Create a new Jekyll site at ./myblog:  
<code>~ $ jekyll new myblog</code>
4. Change into your new directory:  
<code>~ $ cd myblog</code>
5. Build the site on the preview server:  
<code>~/myblog $ bundle exec jekyll serve</code>
6. Now browse to http://localhost:4000

### Directory Layout

This is a quick rundown. For details see https://jekyllrb.com/docs/structure/ .

_config.yml
: settings and variables for the entire site (e.g. title, key, permalink structure) and the build process. Variables set here are available in the site.* object hierarchy.

index.md or index.html or any other *.md or *.html file
: page content files, containing YAML front matter and thus processed by the template engine (as opposed to being copied verbatim to the output).

_layouts
: html of page templates. Merges <code>{</code><code>{content}}</code> and files from _includes. Can contain Liquid commands and variables, including <code>{</code><code>%include filename%}</code>. Usually you need very few layouts, mostly just default.html and post.html .

_includes
: html snippets to be included by the <code>{</code><code>%include filename%}</code> directive. Can contain Liquid commands and variables, including <code>{</code><code>%include filename%}</code>.

_data
: general purpose YAML structured data sets. Many sites use it for defining the navigation hierarchy, but there could also be lists of pictures for an image gallery, products of a shop, ... . For each file, an object named site.data.filename with rich substructure is available.

_posts
: Blog posts source. Files must be named YYYY-MM-DD-title.MARKUP . MARKUP typically is md (Markdown) or html.

_site
: reserved for the generated output. Never ever edit anything here, because it will be overwritten by the next build.

Most other files and directories (all except files starting with . and directories starting with _) are copied to _site upon build. Whether or not any given file is processed by Liquid depends on whether or not it has YAML front matter, essentially a (possibly empty) collection of metadata. On most content pages you'll want to have at least a title attribute, while e.g. CSS files and images should stay as they are. Also index.md or index.html is processed according to this rule.

### Example: stut-it.net

You can look at the [full source code of this web site at GitHub](https://github.com/martinstut/stut-it-website).

#### _layouts

default.html
: template for pages

post.html
: template, very similar to default.html, for blog posts

#### _includes

head.html
: HTML head section occuring on each page, including title, referencing site.title and page.title .

header.html
: common HTML code of the top of the page, including logo etc. Refers to site.logofile, site.title, site.slogan, page.title

navi.html
: main sidebar navigation (for mobile sizes moved to the bottom by CSS). Essentially gets its data from _data/navimain.yaml .

lowernavi.html
: bottom-of-page navigation for previous/next page/post and legally required links. Gets most of its data from _data/navilower.yaml .

footer.html
: common end of page material, metadata.

#### _data

navimain.html
: List of pages for navigation, each page has url and menuname, optionally a set of subpages
navilower.html
: very similar to navimain.html, but for the bottom-of-page navigation

#### _posts

One source file per post, e.g. 2016-06-10-internationalizing-autoit-applications-by-something-similar-to-gnu-gettext.html

The initial set was created by a [conversion tool](https://import.jekyllrb.com/docs/wordpressdotcom/) from a wordpress.com XML export. Later files, starting 2017, were hand edited. .md files in Markdown syntax are perfectly acceptable too.

#### assets, css, images

These are directories to be copied verbatim to the output (_site directory).

css/styles.scss is automatically processed, i.e. converted to css/styles.css, by the SCSS stylesheet preprocessor that is built into Jekyll.  
The webfonts are currently not used in the CSS, because Firefox in Windows renders them really ugly.

assets/ contains content-specific files such as screenshot images showing in blog posts.

images/ contains site-specific images such as the site (business) logo, a portrait foto etc.
