---
layout: post
title:  "Source Control for Dungeon Masters"
date:   2019-07-08 20:34:29 -0600
categories: dungeons dragons source control github master management
permalink: /dnd/source-control-for-dungeon-masters
description: "How GitHub, Markdown, and Atom make me a better dungeon master"
---

As a Dungeon Master of two separate campaigns that take place in the same realm, organization is critical.
While just more than eighty years separate my parties, they do share players and a Discord server.
I've tried a variety of ways to keep everything straight, and I'm finally settling in on a system I like.
While it requires some git-centric know-how, the overhead is paid almost entirely upfront.

## Why Should I Use A Digital System?

Simply put, ["You can't _grep_ dead trees"](http://catb.org/~esr/jargon/html/D/documentation.html).
When you're in the middle of a session, it's very difficult and risky to rely upon paper notes.

1) It's obvious what you're doing, and, much like the clamor of dice behind the screen, can alert players to start meta-gaming.
2) It pulls away from the immersion of the session by taking time away from potentially critical moments
3) Paper can fall victim to spilt drinks.

On the other hand, digital searches are instantaneous, and provided you treat your digital assets well, impervious to single points of failure.
Plus, if you're truly addicted to the feel of pen and paper, they're also printable.
While there are many tangential benefits to organizing yourself digitally, the inability to lose things to carelessness and the efficiency of instant lookup are too good to pass up.
I still use paper for quick notes/decisions I have to make on the fly, but they're converted to bits as soon as I am able to.

## How Do I Get Started?

While _Dungeons & Dragons_ is a hobby popular for nerds like me, its recent growth has expanded far beyond the reach of software developer archetypes.
If you are familiar with Markdown and git, feel free to skim or skip ahead.
If you're new to or shaky with the above, continue on.

### Markdown

As you've browsed the web, you've more than likely encountered the letters html.
The _HyperText Markup Language_ contains all of the content you see on a webpage, along with the rules on how it should be formatted, where links should go, and information for browsers and search engines.
Your browser renders this code with help from stylesheets to display the useful, formatted information.
This is different from tools like _Microsoft Word_ that display the result of applying your formatting rules as you edit the information.
If you make a sentence bold, _Word_ will show you a bolded sentence and hide the information it uses to store that format.
In a markup language, you add special tags to indicate what special formatting rules you'd like to apply and the display engine will remove them as they're applied to text.

Markdown was built as a simple way to write html, and is clean enough to type by hand without having to know much about the underlying technology.
It's a very common format that's supported by WordPress, GitHub, and many forums.
Several publishing companies even use it to manage their content, and have [published guides for authors.](https://www.portent.com/blog/copywriting/content-strategy/content-with-github-markdown.htm)
If you'd like to learn the basics, there are many [great guides online.](https://www.markdownguide.org/getting-started/)
If you prefer to learn by trying, there are live edit websites (often called fiddles) for [trying it out.](https://dillinger.io/)
Here's what a block of one of my session recaps looks like in Markdown:

    Left without the cash needed to secure a room for the remainder of the night, Sif decides to earn some gold the old-fashioned way.
    He eventually finds Gadriel studying a large pile of maps by candlelight.
    In a very long, haphazardly assembled series of slurs, Sif brings the elf up to speed.

    1.  **Gadriel's Mineral Acquisitions** will be fully staffed after Aryn and Grok make the return trip with the rescued miners.
    2.  Sif watched Grok "smash gobbies" and thinks he's a hero deserving of a raise
    3.  "Some spider guys want you dead as fuck buddy"

And this is the rendered text:

Left without the cash needed to secure a room for the remainder of the night, Sif decides to earn some gold the old-fashioned way.
He eventually finds Gadriel studying a large pile of maps by candlelight.
In a very long, haphazardly assembled series of slurs, Sif brings the elf up to speed.

1. **Gadriel's Mineral Acquisitions** will be fully staffed after Aryn and Grok make the return trip with the rescued miners.
2. Sif watched Grok "smash gobbies" and thinks he's a hero deserving of a raise
3. "Some spider guys want you dead as fuck buddy"

As you can see, the formatting takes up little space compared to the written text, and lacks the portability issues of formats like _.doc_ and _.odf_
It's slim enough that I use Markdown for most of my digital note-taking, but in a very specific way.
As you may have noticed in the code block, each sentence takes its own line- which is ultimately changed and displayed as a sentence of a paragraph.
Markdown does not require this convention, but I personally use it to track edit history by sentence.
git, which we'll talk about soon, creates a change log by line edits, and a sentence is the smallest grain of detail I want a history for.
Additionally, it makes reading/writing much easier on a vertical monitor.

### git + GitHub

Most of us that grew up with computers during the haydays of wildly unreliable software know the mantra "Save early an often."
If you've ever lost a computer to an electrical failure or another physical catastrophe, you also know how quickly you can lose massive amounts of work.
Between git and GitHub, you can give yourself protection in both cases.
Obviously, you could store your notes on _Microsoft's OneDrive_ or _Google Drive_, but I prefer having a local and a remote copy.

1) The current search features within the remote solutions above don't give contextual results, unless everything is in a single file
2) Their support for large, single documents is lacking
3) Organizing each session into its own file helps convert plans into recaps

Git can be a little dense to learn at first, but there are a treasure trove of guides and how-to's online to get you started.
[This is one](https://rogerdudler.github.io/git-guide/) that can get you in the door quickly.
GitHub is a free-to-use remote repository for git.
It provides the long-term resiliency for your writing, and, if you set your repository up the right way, it can convert your session notes into web pages you can share with your players.
To do that, you'll need to spend some time configuring a repository, and [learning a little about Jekyll.](http://jmcglone.com/guides/github-pages/)
