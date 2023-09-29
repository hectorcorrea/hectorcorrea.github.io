# DotWiki 2.0
I've posted a new version of the DotWiki that I introduced back in 2004 in this [CODE magazine article](http://www.codemag.com/Article/0403081). This new version 2.0 is a complete rewrite using C# and it has several new features and a more attractive look and feel. Security is probably the most important new feature since it had been by far the most commonly requested feature since I released the original version in 2002.

Previous versions of the DotWiki had been updates to the original VB.NET code and I felt that a full rewrite was in order to clean up the code, remove unused features, reflect what I've learn since the original release, and add a few more features. Since I've been doing exclusively C# for the last few years I took the plunge and rewrote it in C#.

Security is implemented using the ASP.NET Membership provider. It's amazing how easy was to secure the site using the built-in features for membership for creating new users, resetting your password, logging in, logging out, and such.

This new version also uses Rockford Lhotka's [CSLA](http://www.lhotka.net/cslanet/Default.aspx) business objects framework. The business objects to read and write data are typical CSLA business objects. You don't need to know much (if anything) about CSLA to understand the code and yet it helps me tremendously to keep the code in logical tiers.

This version still uses the [FCKeditor](http://www.fckeditor.net/) to provide a what  you see is what you get (WYSIWYG) experience to the user. I updated the code to use version 2.6 which supports the Safari browser. 

I am continuously amazed on how much free stuff there is for ASP.NET applications and how good it is. CSLA is a fantastic middle tier business object framework and is open source. The FCKeditor integrates seamlessly with ASP.NET and comes with all the JavaScript source code. The ASP.NET Membership provider is part of the .NET framework since version 2.0 and the design starter kits for ASP.NET applications have always been free.

The DotWiki itself is open source and I hope developers find the new version as useful (or more!) as previous versions. 

**Update February/2015** I no longer maintain or support the code for the DotWiki. However the code is still available for download at the following links: 

* [Latest source code](https://hectorcorrea.com/downloads/dotwiki.zip) (C#)
* [Previous version](https://hectorcorrea.com/downloads/dotwiki_vs2005.zip) (VB.NET) 
