# Sliding-in a Full Page Panel with CSS and jQuery
In this blog post I show how to use CSS and jQuery to slide-in a panel full page on top of another and then slide back in the original content.

You can see a running version of the code presented in this blog post [here](https://hectorcorrea.com/demos/slide-in-panel-sample.html).  This demo page starts with a panel showing some part of the Lorem Ipsum text. When you click on the text a new panel displaying the Bacon Ipsum text slides-in on top of the previous text.  Clicking on the new text slides the Lorem Ipsum back again. Nothing  fancy but enough to show the slide-in functionality in a full page panel.


## The HTML

The HTML used in this example is basically composed of two HTML divs. The first div (called `mainDiv`) includes the Lorem Ipsum text while the second div (called `galleryDiv`) includes the Bacon Ipsum.

```code
<div id="mainDiv">Lorem Impsum text goes here</div>

<div id="galleryDiv">Bacon ipsum text goes here</div>
```


## The CSS

The CSS to style these two divs is shown below. As you can see they are just plain divs using fixed positioning. The main difference between both styles is the background color (the `mainDiv` is beige and the `galleryDiv` is a tone of red.) Notice that initially the `mainDiv` is set to use all the real state (height and width are set to 100%).

```code
#mainDiv {
  background-color:#FFD39B;
  position:fixed;
  left:0px;                    
  right:0px;
  bottom:0px;
  height:100%;
  width:100%;
  overflow-y:scroll;
}
 
#galleryDiv{
  background-color:#FF4040;
  position:fixed;
  left:0px;
  right:0px;
  bottom:0px;
  overflow-y:scroll; 
}
```

## The jQuery Code

The basics of the jQuery code to slide-in the one panel on top of another were taken from the excellent [Sliding Panel Photo Wall Gallery with jQuery](http://tympanus.net/codrops/2010/05/14/sliding-panel-photo-wall-gallery-with-jquery/) example by Mary Lou at Codrops. I’ve simplified the code to make it easier to see what it takes to slide-in content on top of another back and forth.

When the page first loads we force the `mainDiv` to be visible and the `galleryDiv` invisible.

```code
// Start with the mainDiv in full view...
$('#mainDiv').css({'height':'100%'}); 
$('#mainDiv').show();
    
// ...and the galleryDiv collapsed.
$('#galleryDiv').css({'height':'0px'}); 
$('#galleryDiv').hide();
```

The next thing we do is to wire the `mainDiv` so that when the user clicks on it we slide-in the `galleryDiv`. The code for this is very simple too:

```code
// When clicking on the mainDiv...
$('#mainDiv').bind('click', function() {
      
  // ...send this div to the back...
  $('#mainDiv').css({'z-index':'0'});  
      
  // ...bring the galleryDiv to the front...
  $('#galleryDiv:hidden').show();
  $('#galleryDiv').css({'z-index':'100'});
      
  // ...resize it...
  $('#galleryDiv').animate({'height':'100%'},speed,function(){
    // ...and collapse the mainDiv
    $('#mainDiv').css({'height':'0px'}); 
  }); // animate
                
}); // bind click
```

This code does the following:
* Pushes the mainDiv to the back by setting its z-index to zero.
* Pops the galleryDiv to the front by setting its z-index to something greater than zero, in our case to 100.
* Sets the height of the galleryDiv to 100% using an animation (so that the slide-in effect is noticeable.)
* Finally we collapse the mainDiv by setting its height to zero. This allows us to animate it when we slide-in back again.

The code to wire the `galleryDiv` to do the reverse is also very simple:

```code
// When clicking on the galleryDiv...
$('#galleryDiv').bind('click', function() {
 
  // ...send this div to the back...
  $('#galleryDiv').css({'z-index':'0'});
      
  // ...bring the mainDiv to the front...
  $('#mainDiv').css({'z-index':'100'});
 
  // ...resize it...
  $('#mainDiv').animate({'height':'100%'},speed,function(){
    // ...and collapse the galleryDiv
    $('#galleryDiv').css({'height':'0px'}); 
  }); // animate
 
}); // bind click
```


## And that’s it

With just a few of lines of code we can easily slide-in content in our web pages. You can see this code running in [this link](https://hectorcorrea.com/demos/slide-in-panel-sample.html). The page is self contained. View source and grab the code if you like it.