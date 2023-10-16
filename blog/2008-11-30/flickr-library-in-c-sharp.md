# Flickr Library in C#
The last few days I've been working on a sample C# library to access Flickr pictures using Flickr's own API. This library is a light-weight C# class that provides two basic features: (1) retrieve the list of photosets in your account and (2) retrieve information about the photos in a photoset.

Using this class you can retrieve the photosets in your Flickr account and display them in a combobox with a few lines of code like these:

```code
Flickr flickr = new Flickr("YourApiKey", "YourUserID");
List<PhotoSetInfo> photosets = flickr.GetPhotoSets();
cboPhotoSets.DataSource = photosets;
cboPhotoSets.DataTextField = "Title";
cboPhotoSets.DataValueField = "PhotoSetID";
cboPhotoSets.DataBind();
```

Once you select a photoset you can retrieve information about the photos inside it (like title and URL) with the following code. In this example I am binding the list of photos to an ASP.NET datalist control to display them:

```code
Flickr flickr = new Flickr("YourApiKey", "YourUserID");
List<PhotoInfo> photos = flickr.GetPhotosInSet(cboPhotoSets.SelectedValue);
listPhotos.DataSource = photos;
listPhotos.DataBind();
lblPhotosMsg.Text = (photos.Count == 0) ? "No photos in this photoset" : "";
```

The Flickr API supports multiple requests and response formats. I am using the REST format for requests and responses in my FlickrLib library. A typical Flickr REST request looks like this:

```
http://api.flickr.com/services/rest/?method=methodName&amp;param1=value1&amp;param2=value2
```

where `methodName` is the name of the method to call, for example `flickr.photosets.getList` to return a list of photosets.

REST responses that Flickr provide are XML documents with the pertinent information for the request. For the `flickr.photosets.getList` method the response would be an XML document with a photosets node and several photoset children nodes. These XML responses are very easy to parse with LINQ to XML and turn them into C# strong-typed objects (like `PhotoSetInfo` and `PhotoInfo`) which then can be bound to .NET classes. That's exactly what the FlickrLib library does, it provides a wrapper to the Flickr API functionality so that you can easily consume it from your .NET applications.


### References
You can download the C# source code for this class from [here](https://hectorcorrea.com/downloads/FlickrLibDemo.zip). 

For information about the Flick API visit [http://www.flickr.com/services/api/](http://www.flickr.com/services/api/) there is a lot of information there on what the API provides and how to use it. There are tons of Flickr features that the API provide that you might find useful and that my little class does not expose.

You will need to obtain an API key from Flickr in order to use their API through this class or any other class. The API key is free but it is needed so that Flickr can prevent a single user from submitting too many requests to their site and take it down.
