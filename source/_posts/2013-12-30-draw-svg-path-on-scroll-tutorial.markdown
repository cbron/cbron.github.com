---
layout: post
title: "draw svg path on scroll tutorial"
date: 2013-12-30 12:40
comments: true
categories: 
---

The "[Russia Left Behind](http://www.nytimes.com/newsgraphics/2013/10/13/russia/?ref=global-home)" article a few months ago and fell in love with it, especially the two maps. 
I will quickly go over one way to draw a line on user scroll, although this way is different from the way the New York Times did it. From the looks of it they used [TopoJSON](https://github.com/mbostock/topojson).

[Demo](http://www.calebbron.com/stuff/svg.html)

There's two pieces in building this line. First is creating the SVG and second is drawing it on an html page. 

### SVG 

I used illustrator but I believe inkscape would work as well. All you need is the `<svg>` code. Inside Illustrator use the pen tool to draw a line or shape. The tough part is making sure that all the points stay part of a single path, which results in a single `<svg>` block. When your done hit "save as" and change the type to .svg then click save and click "Show SVG Code", or just save it and open it in a code editor. You should be able to extract a single code block, mine looks like this: 

{% codeblock lang:xml %}
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" width="800px" height="400px" viewBox="0 200 800 400" enable-background="new 0 200 800 400" xml:space="preserve">
<g id="Layer_2">
  <g>
    <g>
      <path fill="none" stroke="#E01F26" stroke-width="4" stroke-linecap="round" stroke-linejoin="round" d="M329.435,255.494
        c125.977,108.275,37.064,167.75,73.58,263.252"/>
    </g>
  </g>
</g>
</svg>
{% endcodeblock %}


### HTML/jQuery

Paste your code into an html document with some spacing around it so you can scroll, then include jquery. Here's the scrolling code: 

{% codeblock lang:js %}

//On scroll call the draw function
$(window).scroll(function() {
    drawLines();
});

//If you have more than one SVG per page this will pick it up
function drawLines(){
    $.each($("path"), function(i, val){
      var line = val;
      drawLine($(this), line);
    });
}

//draw the line
function drawLine(container, line){
	var length = 0;
	var pathLength = line.getTotalLength();
	var distanceFromTop = container.offset().top - $(window).scrollTop();
	var percentDone = 1 - (distanceFromTop / $(window).height());
	length = percentDone * pathLength;
	line.style.strokeDasharray = [length,pathLength].join(' ');
	console.log("strokeDasharray: "+[length,pathLength].join(' '));
}
{% endcodeblock %}

The basic idea in the drawLine() function is to calculate the percentage of distance the container is from the top of the viewport and then draw the line based on that percentage. The `line.style.strokeDasharray` is where the real magic happens. 

Now everytime you scroll, the new height percentage is caluclated and that amount of the line is drawn onto the svg. 

Tada!

From here its easy to play with the actual SVG code and change the colors or distance of the path. 

### Resources

[http://stackoverflow.com/questions/14275249/how-can-i-animate-a-progressive-drawing-of-svg-path](http://stackoverflow.com/a/14282391/552036)