---
title: "Comments are live!"
categories: tech
tags:
  - diy-tech
  - staticman
  - comments
---

You will now hopefully see that the comments feature is now live on all my posts!

I'll admit this took me more effort than I expected. I did probably pick the hardest option for the greatest learning opportunity.

Today I'm just giving a quick summary of the solutions I reviewed and why I picked [staticman](https://staticman.net/). I plan to do a more detailed post on the staticman solution another day, as one of the main challenges I found was a lack of documentation relevant to my scenario.

## Options considered

I use Jekyll and minimal mistakes theme, so the two main resources I considered where:
1. What [minimal-mistakes supports out of the box](https://mmistakes.github.io/minimal-mistakes/docs/configuration/#comments)
2. This [blog post from Ben Fedidat](https://fedidat.com/530-blog-comments/). There are many such, but he did a good job summarising and referencing further sources.

This gave me a view of:
- Disqus
- Discourse
- Facebook comments
- [Staticman](https://staticman.net/)
- [Utterances](https://utteranc.es/)

For me, the solution criteria that lead to my decision were:
- **Alignment to my current hosting solution**. i.e. GitHub pages, with Jekyll static content  
- **How users are authenticated**. As many have a proprietary tie-in to something that excludes some users or forces them to sign-up
- **Privacy of the users**. I like transparency and privacy by design, and some of these services such as Disqus will track more than users may know or freely offer which is a practice I dislike.
- **Who owns the content**. As part of my 
<!-- https://matthunt1984.github.io/tags/ convert to URL after fixing tags--> #diy-tech I'm trying to own as much of the infrastructure and content as I can. Mainly for learning reasons but also for transparency, privacy and cost.

### Disqus
As called out by the post from Ben, there are a number of issues with Disqus that went against most of my criteria.

### Discourse
Although open-source, it requires a break-out from the site to contribute and therefore breaks the user journey I didn't really like, and also increases the effort for people to comment immediately.

### Facebook Comments
So most readers probably DO have facebook logins, but on the privacy front Facebook has a 'need to know' basis from me since watching the [social dilemna](https://www.thesocialdilemma.com/) (worth a watch if you've not seen it).

### Utterances
Utterances was my second choice. It's open-source, gives you full control of content, and worked with my theme. The main concession was a requirement of a GitHub account which isn't a tough barrier for a tech blog but I had hoped to do more than just tech on here longer term, and although the content is in Github it's not as portable as staticman which converts it to markdown. I still think one is worth a look.

## Conclusion: staticman

In the end, staticman won out for these key reasons:
- Open-source, and fully 'on me' server-side solution. I also liked the challenge this would give.
- Specifically targetted to static-site uses cases, and has other use cases than just comments potentially
- Has lots of configuration options around moderation, and working with other tools, so I felt that it could meet current and future needs
- Doesn't enforce authentication if not required. I'm going to start with this off, and then likely add a captcha to prevent spam.
- Directly supported by my Jekyll theme

It's still too early to give more feedback on how successful it has been once setup, but I can post an update later.

I would state upfront that getting this working, self-hosted, was more complexity and effort than I expected, and the documentation isn't great so was a slow/debuggy process. I'll post the detail on that later - but in the meantime, I would say this is **not for those that want a quick and easy option**!

Please feel free to leave a comment and say hi on this page!