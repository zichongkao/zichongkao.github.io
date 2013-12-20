---
layout: post
title:  "The Pathologies of Big Data [Article Review]"
date:   2013-10-02 02:54:29
categories: data_science
---

Everyone’s been going on about the benefits of Big Data. I thought it’d be refreshing to learn about misuse of Big Data.

I came across this article written by <a href="http://www.crunchbase.com/person/adam-jacobs-4">Adam Jacobs</a> in 2009. His background is in neuroscience and he appears to be currently the chief scientist at <a href="http://www.1010data.com/about-us/company-overview/">1010data</a>, a cloud-based analytics company. It turns out that the title of the article is a bit misleading. Rather than discussing the misuse or pitfalls of big data, it highlights the challenges of analyzing big data – only the second most common theme, after “Why Big Data is Good”.

Nevertheless, Adam made some noteworthy observations:

1. Big Data is often big only because of it’s temporal aspect
-------------------------------------------------------------
Adam points out that it isn’t easy for data to get big – the basic census data for all 6.75 billion people on the planet can be stored on a 100 GB disk. Whether you are observing or using observations from each of these 6.75 billion people, the data remains small enough to fit on a standard commodity hard drive.

If you think about it, it’s a smart move to use the number of people as a point of reference. We could quite easily classify most of the things we study into data about people or data about the natural world. Data about people scales according to the number of people there are. What about data about the natural world? Say the weather? There are roughly 10^44 molecules of air in the Earth’s atmosphere. Surely, data about the positions and trajectories of all the molecule would constitute big data. However, Adam points out that we can only collect as much information as we have sensors. Today, if we were to have every man, woman and child on Earth put down what they were doing, go build a sensor, position it somewhere and hook it up to a server, we would only collect 6.75 billion data points. Simply put, we are limited by sensors, and sensors are limited by people.

Now, the whole example above is pretty silly because we rarely use sensors just once. It wouldn’t cost us much more to have each of the sensors log data once a second for a day. This would immediately put us in the petabyte range. That’s a million gigabytes. The point is, data gets big much more easily when it has a temporal aspect to it.

2. Traditional relational databases aren’t suited for handling temporal data
----------------------------------------------------------------------------
Since Big Data often has a temporal aspect, it is important that our storage solution be able to represent the inherent temporal ordering. However, Adam points out that traditional relational databases do not recognize ordering on rows within each table.

This is problematic for a couple of reasons. With temporal data, we typically want to perform sequential reads. If data is stored in order, we can make use of locality in accessing disk or memory to do this efficiently. However, since traditional relational databases do not recognize ordering, we are forced to use random disk access which may be up to 5 orders of magnitude slower! Furthermore, even after accessing the data we need, we require more resources to perform a sort on the data. As such, traditional relational databases are doubly penalized when handling temporal data.

Though I don’t know much about the Hadoop/ HDFS framework, it seems like it partitioning would deal with inherent temporal ordering in data. Partitioning is basically what Hadoop does to separate tables for storage as separate files in HDFS. From what I’ve seen, data is normally partitioned based on time. For instance, a single day or single hour’s data may be stored in a single file. If we want to run jobs for a series of days, we use a scheduler like Oozie to query the metastore for the location of the corresponding file, and access the files directly. This does away with the need for sorting (though map-reduce jobs require a sort, which is not strictly for temporal ordering). A caveat is that it doesn’t help with exploiting locality. Though one might argue that since this is a distributed system, we shouldn’t have been expecting to leverage locality anyway.

3. What is Big Data
-------------------
Adam also takes a stab at defining big data. According to him, big data is ”data whose size forces us to look beyond the tried-and-true methods that are prevalent at that time.” That’s a pretty toothless definition if you ask me.  What’s missing is a description of features of the data that make existing methods ineffective. Without this, the definition remains very unhelpful! It doesn’t clarify the nature of the problem, nor does it focus our efforts on critical impediments. All it does is paint Big Data as this elusive blob on the horizon, ever amorphous, ever unattainable.

You can read the full article here: <a href="http://cacm.acm.org/magazines/2009/8/34493-the-pathologies-of-big-data/fulltext">http://cacm.acm.org/magazines/2009/8/34493-the-pathologies-of-big-data/fulltext</a>