# Hydra Connect 2
![Hydra Connect ballroom](https://hectorcorrea.com/images/hydraconnect2.jpg) 

This is my first time attending a [Hydra Connect](https://wiki.duraspace.org/display/hydra/Hydra+Connect+2+-+Fall+2014) and I was very impressed with the overall experience. From what I understand there were about 150 attendees representing 47 different institutions.

The event had a good mix of workshops, lightning talks, tutorials, sessions, un-conference sessions, and working groups meetings. Below are my notes and thoughts from some of the sessions that I attended.


## Geodata in Blacklight and Hydra
The first session that I attended was on a project that Stanford is working to make use of geographical information via Blacklight. The name of the project is "Geo Blacklight" and it allows users to perform searches by selecting a bounding box in a map. Once an area has been selected different kind of data/documents can be shown about that particular area, this allows researches to narrow down the list of documents that they need to review without having to download and open them individually.

Stanford is using an "overlap ratio" to rate search GIS results. 

Bess Sadler made the point that we should be thinking on

> how are libraries going to curate geo-spatial data, 
> there seems to be a changing culture around this data and the metadata are still in flux

An area that is still is open for development is the mix of text and geo searches. For example, if I type "cleveland" I might be light to see results for documents related to Cleveland even if these documents don't have the word "Cleveland" in them but the geodata on them points to an area inside Cleveland.

Geo Blacklight is a full app (like Blacklight.) There was a mention of a "Blacklight maps" plugin that is also available to plot points on a map. They are two different things, though.

They are using Solr to do the geo-filtering. Solr provides the concept of bounding box to select by a square area and "geofilt" to filter by a circle. More information here: [https://wiki.apache.org/solr/SpatialSearch](https://wiki.apache.org/solr/SpatialSearch) 

Geo Blacklight uses Leaflet, a JavaScript plug-in, to handle the map interactions on the page: [http://leafletjs.com/](http://leafletjs.com/)

Beth and Jack Reed also mentioned "OpenGeo Suite" which is a server product to handle caching, multiple formats (shape file, KML, json, pdf, JPEG), and other server side requirements. [http://boundlessgeo.com/solutions/opengeo-suite/](http://boundlessgeo.com/solutions/opengeo-suite/)


## RDF and Hydra
In this session Tom Johnson gave a hands-on tutorial on ActiveTriples, a Ruby gem to work with RDF triples. The source code that we used during the presentation is available here: [https://gist.github.com/no-reply/fce0771254a3a25817dd#file-walkthrough-rb](https://gist.github.com/no-reply/fce0771254a3a25817dd#file-walkthrough-rb) 

ActiveTriples abstracts most of the RDF syntax and wraps it on Ruby objects so that you can work with them without much regard of what RDF store the data came from or will end up in. ActiveTriples does not connect to Fedora for example, it just handles the objects in memory. This is a really neat gem. I believe the way Tom described it was along the lines of 

> ActiveTriples is a projection on the underlying RDF graph

The source code for this gem can be found here: [https://github.com/no-reply/ActiveTriples](https://github.com/no-reply/ActiveTriples)

## Fedora 4 and Hydra Test Drive
In this session Esmé Cowles gave a quick hands-on tutorial on how to connect to Fedora 4 from Ruby using Active Fedora 4. The code he used during the tutorial can be found here: [https://github.com/escowles/testdrive](https://github.com/escowles/testdrive)

ActiveFedora 4 builds on top of ActiveTriples. Whereas ActiveTiples abstracts RDF into Ruby objects, ActiveFedora provides the operations so that these objects behave like ActiveRecord classes in a regular Rails application. Later on I learned that there is another piece to the puzzle called LDP client that makes the HTTP connection to Fedora.

![ActiveFedora, LDP, and ActiveTriples diagram](https://hectorcorrea.com/images/activefedora_diag.jpg) 

The fact that the Fedora API is "based loosely around the Linked Data Platform [LDP] 1.0 Architecture" is clearly indicated on the [Fedora REST API documentation](https://wiki.duraspace.org/display/FF/RESTful+HTTP+API) but it didn't mean much to me until I heard it in context at the conference. You can find more about LDP here: [http://www.w3.org/TR/ldp/](http://www.w3.org/TR/ldp).


## What does Fedora 4 do for the Hydra stack?

Although I have plenty of experience on relational databases and am somewhat comfortable with some of the NoSQL databases, I am total n00b when it comes to Fedora, so I was really curious to attend this presentation in which Andrew Woods (the tech lead for the Fedora project) and Esmé Cowles presented on why is Fedora a good option for the needs of the Hydra community.

Andrew talked about how "Fedora shares sensibilities" with the Hydra community and provides a solid base (institutions, users, and developers). [slides](https://wiki.duraspace.org/download/attachments/63408008/F4-Hydra-Connect-stack-2014-10-02.pdf). 

Some of the features of Fedora 4 are that are particularly interesting for Hydra are the ability to handle large files, scalability, versioning, and durable storage. 

Andrew also talked about Fedora 4's "Native Linked Data" feature and how it can be used to handle inter-connected repositories with shared content and vocabulary. I am not particularly thrilled with this concept. I think the idea of sharing data at the database level is attractive at the beginning but does not scale well over time, at least that has been my experience in the world of relational databases. Personally, I think we should focus on sharing data via services and not via databases.  [Update: I guess somebody can make the case that Fedora is acting as a "service" by supporting LDP, but I still think of Fedora as a storage repository. Not sure about this yet, ...food for thought.] 

Some of the areas that are still under development for Fedora 4 are transactions, asynchronous storage (e.g. to access a remote store in AWS), and a shared index with Solr.


## Hydra on top of Fedora 4

Adam Wead and Justin Coyne gave a couple of presentations on the topic of migrating Hydra applications from Fedora 3 to Fedora 4. There are several conceptual changes in Fedora 4. For example, in Fedora 3 we have one DigitalObject with many datastreams, these datastreams might have properties, content or both. In Fedora 4 all items are "Nodes" but some of them are of subtype "DataStreamNode". DatastreamNodes can have properties, content, or both. In addition, the concept of disseminators in Fedora 3 is now gone in Fedora 4. The RELS-EXT datastream is also gone in Fedora 4.

There is significant progress by many developers from many institutions on updating the Hydra stack to work with Fedora 4. We currently have an "alpha" version of Sufia using ActiveFedora 8. [slides](http://jcoyne.github.io/hydra-on-top-of-fedora4.html#slide-25)

Adam pointed out that we are now going to "take advantage of the native RDF in Fedora 4" and provided a code example on how this looks like [slides](http://awead.github.io/presentations/fedora-migrate/#/8) He also talked about some of the areas that we are still working on like Versions and Relationships.


## Metadata, RDF, linked data

Given that I am new to the world of libraries and the tools and technologies used by librarians and archivists I made a point of attending several sessions on metadata, RDF, and linked data. Most of the sessions were way over my head, but interesting as I immerse myself on this domain.

In one of the sessions Karen Estlund talked about the difficulties of creating metadata that is both human and machine readable, or that can be entered by humans or machines. She indicated that libraries should feel free to create their own ontologies when needed but strongly encouraged people to use URIs rather than strings as predicates.

I found very interesting the wide range of topics that were discussed in these sessions. For example, I was surprised to realize that there are still discussions on how to represent ordered lists in the RDF world, a problem that pretty the rest of the non-RDF world has already solved. On the other hand, some of the RDF topics where on big and difficult topics like access rights in a distributed environment, see [http://www.w3.org/wiki/WebAccessControl](http://www.w3.org/wiki/WebAccessControl).  As I said before, a very wide range of topics.

I was glad to hear that the book that I am currently reading was recommended as a good resource to learn RDF: [Semantic Web for the Working Ontologist](http://workingontologist.org) by Dean Allemang and Jim Hendler.