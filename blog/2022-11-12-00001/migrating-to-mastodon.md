# Migrating to Mastodon
Mastodon is an open source project with the goal of providing a platform to create decentralized social networks. 

> Mastodon started in 2016 as an open-source project by Eugen Rochko, who, as an avid user since 2008, was dissatisfied with the state and direction of Twitter.
> 
> Believing that instant global communications were too crucial for modern society to belong to a single commercial company, he sought to build a user-friendly microblogging product that would not belong to any central authority, but remain practical for everyday use. - [https://joinmastodon.org/about](https://joinmastodon.org/about)

The goal of creating a decentralized social media platform is something that aligns with my idea of the web, but my previous two attempts at joining Mastodon were disappointing since I had a hard time finding people to follow - the site felt like an empty warehouse with little to offer. But this month, with the massive [Twitter Migration](https://www.msn.com/en-us/news/technology/considering-joining-the-twitter-migration-check-out-these-platform-alternatives/ar-AA13NZlw), it looks like things are starting to turn around for Mastodon and the amount of people posting interesting stuff has grown exponentially and that makes me optimistic about it.

Mastodon is just one of many open source projects that promotes and participate in this idea of a federated universe ([Fediverse](https://en.wikipedia.org/wiki/Fediverse)), in which users from different products can communicate with each other (here is a [very short video](https://framatube.org/w/4294a720-f263-4ea4-9392-cf9cea4d5277) that explains the basic idea). Again, conceptually this is how I would like our use of the web to evolve, but in practice this is very hard to achieve and we, collectively as a society, tend to revert back to centralized and proprietary solutions (e.g. in email, source control, and social networks) -- Martin Fowler [elaborates on this rather eloquently](https://martinfowler.com/articles/exploring-mastodon.html#what-if-twitter-goes-myspace). Let's hope this time a federated online universe prevails for social networks.


## Web Interface
I use Mastodon exclusively through the website. I find the website to be responsive and works well both on desktop and mobile browsers. The Mastodon website keeps track of three different timelines: Home, Local, and Federated. 

* The **Home** timeline displays posts from people that I follow. This is where I spend most of my time. 
* The **Local** timeline shows displays posts from people in the Mastodon instance that my account lives in (in my case the `mastodon.social` instance)
* The **Federated** timeline displays post from other instances. 

In general I only care about the **Home** timeline since that displays the posts from people that *I am following*. This timeline also include posts boosted (retweeted in Twitter lingo) by the people that I follow. One nice thing about Mastodon is that timelines are *always* in chronological order and there is no advertising, so my Home timeline is predictable and with the stuff from people that I decided to follow.

I browse the **Local** timeline from time to time to see if there are other people posting things that I might be interested in and that I might want to follow. But rarely I find something that I really care. 

The **Federated** timeline is like drinking from the fire hose, and I never know what I am going to find out. For the most part is just uninteresting stuff for me, so I don't browse it very often.

I did changed a few of the defaults settings for my profile on the website, but they were very minimal:
* The default theme on Mastodon seems to be *dark* (i.e. black background) and I switched mine to *light* (i.e. white background).
* I also turned on *slow mode*. By default the site refreshes the timeline as new posts come along and I found the timeline was jumping too much. With *slow mode* the timeline does not automatically refresh and instead I see a banner at the top that tells me "there are X new items", I click on that banner when I want to see what's new.
* There is also an *Advanced Web Interface* in which you can view more than one timeline at the same time via three different columns. I think it was off by default on my instance (thank goodness), but I seem to remember that it was on by default in one of the instances that I tried before and I did not like it. I prefer the simplicity of a single timeline at a time.


## Building a network on Mastodon
Most of my use of Mastodon (as it was with Twitter) is to find interesting stuff to read, I use it mostly as a glorified RSS reader. I don't use Mastodon to engage in deep conversations. I mean I engage in conversations here and there, but the large majority of the time I am browsing to see what's good to read in the form of posts, blogs, articles, or references to other sources. 

Since a lot of the people that I used to follow on Twitter have moved to Mastodon this time around I am finding easy to build a network of people to follow. One thing to notice is that, because Mastodon is decentralized, people have their accounts in many different Mastodon instances. This makes the process of finding people to follow a bit more challenging that it was on Twitter where everybody lives on the same instance. For example, my Mastodon account is hosted in `mastodon.social` but I follow people that have their accounts on other Mastodon instances like `code4lib.social`, `hachyderm.io`, and `digipres.club` to name a few. However, once you know somebody's account (regardless of what instance they are on) you can follow them and interact with them as if they were in your instance. This work well in practice because I never just look for somebody by name to see if they are on Mastodon, usually I see their Mastodon handle somewhere and after that I follow them.

My process to find people has been rather ad-hoc. I usually see who others are following and check out those accounts to see if they have stuff that I find interesting. I know there are tools to help migrate your network from Twitter to Mastodon, but I just been doing it manually. I suspect the Local timeline might be a good way to find people with similar interests *if I were to have an account on an instance with a more narrow focus* but since I am on a giant all-purpose kind of instance that does not work for me.

As far as posting is concerned, I have posted a few toots so far, and as with Twitter [my posts tend to revolve around software development](https://mastodon.social/@hectorjcorrea). I was never a big fan of adding hashtags in Twitter but it seems that in Mastodon they are a bit more important given the distributed nature of the fediverse -- I believe the search feature only searches within your instance but it can find posts from other instances if they have hashtags because those are federated...or something like that.


## Overall first impressions
As I said before, I use Mastodon very similar to the way I used Twitter: to read what other people are posting, and as such it has been a good place to be now that people are moving to it and actively posting. 

One thing that is very nice with Mastodon is that there is no advertising. It is really nice to only see content in my timeline and not a single ad. 

I mentioned before that timelines in Mastodon are always chronological. This is because there is no algorithm behind the scenes trying to guess what you should to see in order to promote more engagement and attract advertisers. One great benefit of this approach is that it is easy to find a post that you know you read before in your timeline. That post will be in the exact same spot chronologically and surrounded by the same posts as last time next time you are looking for it. I was always frustrated when I could not find a previous post in Twitter because Twitter kept changing what was around such post. 

Many Mastodon instances, including the one my account is located at (`mastodon.social`) struggled earlier this month to keep up with the influx of new users. The sites were at times unavailable, very slow to respond, or delayed to show content. This is understandable give how large the Twitter Migration has been. As far as I can tell things are back to normal and sites are stable again. It is important to remember that each Mastodon instance is hosted by somebody (a person or an organization) and not by a single giant corporation like Twitter or Facebook. My instance is hosted by [Mastodon gGmbH](https://mastodon.social/about) a non-profit in Germany but other instances are hosted by individuals. 

One of the principles of Mastodon is that you should be able to migrate to your account from one instance to another. There are even options in the web interface to help you accomplish this. From what I understand this is not a perfect process, but even with that, the tooling is already better than any of the proprietary social networks.


## Privacy and code of conduct
Each Mastodon instance is independent of each other and each of them has its own code of conduct. This means that when you open an account in a given instance you are trusting somebody (again it might be a single individual or an organization) with your data and agreeing to follow the code of conduct that they enforce. 

In theory this is a better model than a giant corporation hosting everybody's data and one single set of rules, but in practice is not a perfect model as noted by [Ro](https://ubiqueros.com/notes/97fl43rhnu) and highlighted by [Chanda Prescod-Weinstein's experience](https://twitter.com/IBJIYONGI/status/1590462702983720962). 

I am still wondering how this is going to work out in the long term, as [Sarah T. Roberts](https://www.behindthescreen-book.com/) points out, content moderation is a hard problem that does not have a tech-only solution. In the meantime I've deactivated my Twitter account and you can find me on Mastodon at [@hectorjcorrea@mastodon.social](https://mastodon.social/@hectorjcorrea). 

