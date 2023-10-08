# Goodbye Gmail
One of the advantages of owning my web domain is that I can host my website and email anywhere I like. This allows me to switch web or mail providers without having to change my domain or email address. This week I *moved my email from being hosted at Gmail to be hosted at a different provider* and deleted my Google account entirely. This blog post elaborates on why I did and I how I went about it.


## Background
I hosted my email for `hector@hectorcorrea.com` with Gmail for many years. I don't remember well how I hosted it before that, I have a vague recollection of hosting it on an Exchange server at home at some point and later on using the built-in email service that my web hosting company provided.

When I first switched to Gmail to host my email in 2007 I remember I was really happy with the hands-off approach that Gmail afforded me and I was particularly impressed with the spam filter that they provided. Overnight I went from having to worry about spam to not having to worry about it at all. Back then *I knew* Google was analyzing the text of my email in order to detect spam and it was well known that they were using this analysis to provide better advertising via the little ads at the top of the page. I was OK with that.

As time went on, I became a little concerned on how much of my information I was sharing with Google: email, search history, Google Documents, calendars, and so on. A few years ago I started using [DuckDuckGo](https://duckduckgo.com/) as my default search engine and switched to [FireFox](https://www.mozilla.org/) as my default browser in at attempt to reduce how much of my identity I share with Google in the form of search and browse history but I didn't do much else. 

Last year when Google accidentally started [blocking people's access](https://www.blog.google/products/docs/protecting-our-google-docs-and-drive-users/) to their own Google Documents was a wake up call for me. This incident highlighted how much Google is working on *analyzing the content and context* of all the data that they host for me "for free". One thing is knowing that Google is parsing the text looking for words like "software development" to detect that I probably want advertising on software development books, but knowing that their analysis is becoming smarter at making connections with all the data that *I was storing at their servers* made me uneasy.

Reading [Algorithms of Oppression](https://nyupress.org/books/9781479837243/) by Safiya Umoja Noble earlier this year was another wake up call. Noble makes it clear that at its core, Google is an advertising company, not a search company. With this perspective I became more concerned about all the data that I was giving Google to store for me. Is it worth it?

The last straw for me was the recent addition of [smart reply](https://www.blog.google/products/gmail/save-time-with-smart-reply-in-gmail/) buttons on their email service. All of a sudden when I get an email Gmail suggests a few potential quick replies that I could use. These replies are contextual, Gmail gives different suggestions depending on the content of the email. In one email it will suggest "It's working", "Success", and "Got it!" and in another "OK, I'll take a look", "Will do", "Thanks, I'll check them out". That was a clear indication on how deep into the content of my email Google is going. I was OK with Google doing some *word matching* with my information but at one point they crossed the line into a more deep and personal analysis of my email. I was not comfortable with this.


## Webmail providers

A few weeks ago I started looking for alternatives for hosting my email outside of Gmail. One of the things that became clear to me is that if I don't want companies to dig into my email *I will need to pay for email hosting* (what a concept, right?). I didn't think any of the major free email providers like Outlook and Yahoo were going to be any different from Gmail when it comes to the privacy of my information. As the saying goes [if you're not paying for it, you become the product](https://www.forbes.com/sites/marketshare/2012/03/05/if-youre-not-paying-for-it-you-become-the-product/). For a service that I depend on as much as I do, paying for email sounds reasonable to me, even though I have never paid for it before. 

I found a good reference of [major webmail providers](https://en.wikipedia.org/wiki/Comparison_of_webmail_providers) in Wikipedia and I looked at several of the options. In the end I was between ProtonMail and FastMail.

[ProtonMail](https://protonmail.com/) has gotten a lot of of publicity recently because they have a strong focus on privacy, they store email encrypted in a way that not even they can read it. The downside of this approach is that you cannot check your email with some of the standard Mail apps, for example it does not work with the built-in Mail application on the iPhone, instead you must use their own Mail application which supports passing the encryption keys.

For my needs I found that [FastMail](https://www.fastmail.com/) was good enough. They might not be as strong on privacy as ProtonMail but I trust that they won't be mining my information the way Google does. And they are reasonable priced, the entry level plan is $30 per year. 


## Making the switch to FastMail

Before making the switch to a new provider I downloaded all my data from Google. I am thankful for the [Google Takeout](https://en.wikipedia.org/wiki/Google_Takeout) option to do this.

Then I opened an account with [FastMail](https://www.fastmail.com/) and test drove their service for a few days (you can do this for free for 30 days). Once I was comfortable with their service and offerings I chose a paid plan. I am using the Standard plan ($50 per year) because that's the one that allows for custom domains. I love how clear the pricing is and that you can pay month by month without a long term contract.

After that I updated the MX records of my domain to point to the FastMail servers. This was the most stressful part of the process for me. I had not updated the MX records in my domain in more than 10 years and I wanted to be sure I got this right. FastMail provides [great instructions](https://www.fastmail.com/help/receive/domains.html?u=f6d1409f) to do this, they even have a built-in check to tell you whether you did it right.

Once I changed my MX records and configured my domain information on FastMail I started getting email sent to `hector@hectorcorrea.com` on the FastMail server within 15 minutes or so. Pretty painless. 

Giving that spam filtering was one of the big gains that Gmail gave me in 2007 I was a bit curious to see how this was going to work with FastMail in 2018. So far, so good. I think this is an area where most email providers have made huge improvements and are good enough by now without much fanfare. 


## Life without a Google account

In my case I did not import my email from Gmail into my FastMail account. I decided that I really don't need 11 years worth of email and I can afford to start fresh. I did a quick scan of the more than 12 thousand emails in my Gmail account and, although it was a nice trip down memory lane, it was not worth bringing over. If I really need something I can always go back to the Google Takeout file that I downloaded and get it from there, but I would be surprised if I do.

After a few days running on FastMail without hiccups I deleted my Google account. I could have deleted my email only and keep my Google account but I decided to delete my account entirely and see what life without a Google account feels like. ~~Actually, I still have an extra Google account (like everybody else) that I use for junk email subscriptions, but I am not counting that since that account does not store much information and I am never logged into it.~~ (**Update January/2019**: That other account is also gone now.) 

One of the things that I didn't think through before deleting my Google account was that my StackOverflow account was linked to Google for authentication. When I tried to login to StackOverflow it failed. Luckily for me StackOverflow has an option to set a password on my account and login without Google authentication.


## Calendars, contacts, and documents

One of the things that I had to reevaluate once I deleted my Google account was where and how to manage Calendars and Contacts. For calendar I ended using the Calendars that Apple offers via iCloud. I was able to connect the iCloud calendar to my FastMail account relatively easily.

I am still not sure what I am going to do for Contacts. I use the contacts from my Phone for phone calls and texting, but for email I am still not sure. I never liked the Contacts app that comes with Gmail, but I loved how I never had to worry much about email addresses since Google (for better or for worse) remembered every person that I emailed to. I could just import contacts on my FastMail account but I haven't done this.

Google Docs is another part of the puzzle that I have not figured out how to replace yet. It was nice to store a few personal documents and spreadsheets on Google Drive and have read-write access to them from my work or home computers. I hardly ever accessed these documents from my phone, though. It's possible that I won't need to replace this with anything. Time will tell.


## The long tail
Of course there is a long list of other Google products that I used over time that I had completely forgotten about and that I will start noticing as time goes on. 

Today I was reminded about Google Analytics, which I used on my personal site. This is one of those things that I don't have access anymore since I deleted my Google account and that information does not come in the Google Takeout. This is not a big loss for me, I mostly had Google Analytics turned on as way for me to learn how to use it and try different features in my personal site without having to mess with sites at work. In the future I will probably use a log analyzer like [GoAccess](https://goaccess.io/) to monitor traffic to my site. 

I also used to subscribe to a few Google Groups using my Google account. I will need to resubscribe somehow if I want to post to them. Again, not a big deal to me. 

## A year later... 
**Update February/2020**: It's been more than a year that I made the switch and I thought it might be a good idea to update this post with some of my experiences since I made the switch in September of 2018. 

Overall I am very pleased I made the switch to Fastmail and I have not missed Gmail at all. The email service that Fastmail provides is top notch and so is their availability and spam filtering. Although I did get a bit more spam in the first couple of weeks after I switched, the spam eventually stopped. I suspect this is the time that it took the spam detection algorithm at Fastmail to "learn" what I consider spam.

The integration of Fastmail with the Apple Calendar was not as easy as I had hoped for, but once configured it works as well as Google Calendars did. 

I should clarify that although I don't have a *personal* Google account anymore, I do have one at work, so I am not in the dark about what the Google products do, since I use them everyday at work.


