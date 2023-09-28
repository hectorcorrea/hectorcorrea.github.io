`debugQuery` is a parameter that we can pass to Solr when submitting a query, with this parameter Solr will include extra information in the response that is useful to troubleshoot a variety of issues in Solr queries. The information returned is helpful to diagnose:

* *how* is Solr interpreting a search query
* why a particular document was *included* (or not) in the result set
* why some documents are *ranked higher* than others

The `debugQuery` parameter is available in the Solr Admin screen

![Solr Admin](https://hectorcorrea.com/images/solr_debug_query.jpg)

or we can pass `debugQuery=on` in the URL like we would any other parameter:

```
curl 'http://localhost:8983/solr/your-core/select?debugQuery=on&q=*'
```

When we pass `debugQuery=on` in the request, the Solr response will include an extra `debug` attribute with information about the raw query that Solr received, how Solr parsed the query, an explanation about how each of the documents in the result were scored, and a few other pieces of information. The `debug` node in the response looks more or less like this:

```
"debug":{
  "rawquerystring":"*",
  "querystring":"*",
  "parsedquery":"...",
  "parsedquery_toString":"...",
  "explain":{
    ...
  }
}
```

# parsedquery - what Solr does with our query
Let's say that we do a very simple query in Solr: `q=blue`. On a brand new Solr core with no customizations, the debug output will show something like this:

```
"debug":{
    "rawquerystring":"blue",
    "querystring":"blue",
    "parsedquery":"_text_:blue",
    "parsedquery_toString":"_text_:blue",
    "explain":{},
    "QParser":"LuceneQParser",
```

notice that the `rawquerystring` is the search term "blue" that we passed but the `parsedquery` indicates that Solr is searching on the `_text_` field. Notice also that Solr is using the default `LuceneQParser` parser.

Now let's say that instead of the brand new core with no customizations we are searching against a Solr core that has been configured in `solrconfig.xml` to use a few default query fields (`qf`) and the eDisMax parser, in that case the output of the same query might look like this:

```
"debug":{
    "rawquerystring":"blue",
    "querystring":"blue",
    "parsedquery":"+DisjunctionMaxQuery(((title_txt:blue)^10.0 | author_txt:blue))",
    "parsedquery_toString":"+((title_txt:blue)^10.0 | author_txt:blue)",
    "explain":{},
    "QParser":"ExtendedDismaxQParser",
```

notice that although the `rawquerystring` is still "blue", in this case the `parsedquery` value clearly tells us that Solr is searching in the `title_txt` field (with a boost factor of 10) and the `author_txt` field. We can also see that it's using the `ExtendedDismaxQParser` (aka `eDisMax`) parser rather than the standard Lucene parser.

The `debugQuery` parameter also helps when working with **multi-words searches**. Let's say that I run a search for `q=title_txt:blue sky`, the output of the `debugQuery` would indicate the following:

```
"debug":{
    "rawquerystring":"title_txt:blue sky",
    "querystring":"title_txt:blue sky",
    "parsedquery":"title_txt:blue _text_:sky",
    "parsedquery_toString":"title_txt:blue _text_:sky",
    "explain":{},
    "QParser":"LuceneQParser",
```

notice that the `parsedquery` shows that Solr searched for "blue" in the `title_txt` field but it searched for "sky" in the `_text_` field, certainly not what we wanted, but this is what Solr parsed because we did not wrap the search terms in quotes.

If we wrap our search terms in quotes: `q=title_txt:"blue sky"` Solr will produce a `parsedquery` like the one shown below, which is what we wanted:

```
"debug":{
    "rawquerystring":"title_txt:\"blue sky\"",
    "querystring":"title_txt:\"blue sky\"",
    "parsedquery":"PhraseQuery(title_txt:\"blue sky\")",
    "parsedquery_toString":"title_txt:\"blue sky\"",
    "explain":{},
    "QParser":"LuceneQParser",
```


The `parsedquery` value is also useful to see how Solr is **treating our search terms**. Let's say that instead of searching on a `title_txt` field we search on a field named `title_txt_en` (on a standard Solr configuration fields ending with `txt_en` are run through transformations specific for the English language). In this case the result of a search for `q=title_txt_en:running` will provide the following debug output:

```
"debug":{
    "rawquerystring":"title_txt_en:running",
    "querystring":"title_txt_en:running",
    "parsedquery":"+title_txt_en:run",
    "parsedquery_toString":"+title_txt_en:run",
    "explain":{},
    "QParser":"ExtendedDismaxQParser",
```

notice how the `parsedquery` show us that Solr is searching for the stem of the search term (i.e. "run" rather than "running") in this case because the field `title_txt_en` has specific English language transformations applied.

## explain - how each document is scored

Another value that Solr includes in the response is the `explain` property. This property has one node (for each of the documents that were returned in the result set) with information explaining how the score for these documents was calculated. For example the result might look like this:

```
"debug":{
    "rawquerystring":"title_txt_en:blue",
    "querystring":"title_txt_en:blue",
    "parsedquery":"+title_txt_en:blue",
    "parsedquery_toString":"+title_txt_en:blue",
    "explain":{
      "00012393":"a ton of text goes here",
      "00002135":"a ton of text goes here",
      "00011405":"a ton of text goes here",
      "00008465":"a ton of text goes here",
      "00011495":"a ton of text goes here",
      "00022192":"a ton of text goes here",
      "00009343":"a ton of text goes here",
      "00010956":"a ton of text goes here",
      "00011520":"a ton of text goes here",
      "00009331":"a ton of text goes here"
    }
```

For brevity I've omitted the actual data that is returned for each document (and just indicated "a ton of text goes here") but the actual data looks more or less like this *for each* of the document ids:

```
3.8011298 = weight(title_txt_en:blue in 5842) [SchemaSimilarity], result of:
  3.8011298 = score(freq=1.0), computed as boost &ast; idf &ast; tf from:
      6.1900153 = idf, computed as log(1 + (N - n + 0.5) / (n + 0.5)) from:
            20 = n, number of documents containing term
                  10000 = N, total number of documents with field
                      0.61407435 = tf, computed as freq / (freq + k1 &ast; (1 - b + b &ast; dl / avgdl)) from:
                            1.0 = freq, occurrences of term within document
                                  1.2 = k1, term saturation parameter
                                        0.75 = b, length normalization parameter
                                              2.0 = dl, length of field
                                                    5.48 = avgdl, average length of field
```

As you can see, the information in this section is pretty hard to decipher, but it is important to highlight that it is has a wealth of information if you are trying to understand why a given document was scored the way it was. I blogged about the details of the `explain` information in [Understanding scoring of documents in Solr](https://library.brown.edu/create/digitaltechnologies/understanding-scoring-of-documents-in-solr/) if you want more details about it.

The default format for this information is a very long line-delimited string. Newer versions of Solr have an additional parameter `debug.explain.structured=true` that allows us to request the information in JSON (rather than line-delimited) that is more suitable for parsing.

## debugQuery or debug
According to [Solr's documentation](https://solr.apache.org/guide/8_9/common-query-parameters.html#debug-parameter) the `debugQuery` parameter is now just `debug` and we can pass several options to indicate what kind of debug output we are interested: query, timing, results, or all. The original `debugQuery=on` is preserved for backwards compatibility and it's equivalent to `debug=all`

## explainOther - score a document that we are interested in
Newer versions of Solr also provide an additional [explainOther](https://solr.apache.org/guide/8_9/common-query-parameters.html#explainother-parameter) parameter that we can include in our request to force Solr to include additional documents and score them *even if they were not returned in the original query*. 

For example, let's say that we a run a query that gives us almost all the results that we are interested in, but one document in particular (the one with id "00000747") was not included in the first 10 documents even though it meets the criteria `title_txt_en:blue`. In this case we can pass Solr `explainOther=id:00000747` in our query to force this document to be included and scored in the debug output. The output will be something like this:

```
"debug":{
    "rawquerystring":"title_txt_en:blue",
    "querystring":"title_txt_en:blue",
    "parsedquery":"+title_txt_en:blue",
    "parsedquery_toString":"+title_txt_en:blue",
    "explain":{
      "00012393":"a ton of text goes here",
      "00002135":"a ton of text goes here",
      "00011405":"a ton of text goes here",
      "00008465":"a ton of text goes here",
      "00011495":"a ton of text goes here",
      "00022192":"a ton of text goes here",
      "00009343":"a ton of text goes here",
      "00010956":"a ton of text goes here",
      "00011520":"a ton of text goes here",
      "00009331":"a ton of text goes here"
    },
    "otherQuery":"id:00000747",
    "explainOther":{ "ton of infomation here about document 00000747" },
    "QParser":"ExtendedDismaxQParser",

```

Notice that the document with id "00000747" was not in the first set of ids in the `explain` section and therefore was not scored, but because we included `explainOther=id:00000747` in our *request* the document will be scored and included in the `explainOther` node in the response.
