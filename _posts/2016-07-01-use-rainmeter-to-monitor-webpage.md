---
layout: post
title: Use Rainmeter to Monitor Webpage
category: Rainmeter
tags: [rainmeter, ipopp]
---

<div class="message">
  Due to the length of the code. The complete code is at the end of this post.
</div>


As a System Engineer, I need to monitor the running status of our software.
This can be done by monitoring the webpage which publish the products of the
software. However it is annoying to open the same webpage multiple times
everyday. With the help of [Rainmeter](https://www.rainmeter.net/),
I can put the information of the webpate
on my desktop, and do not have to open the webpage manually again.

![IPOPP Products](/images/sat_product_desktop.PNG)

## The information page
<!--break-->
The web page
 shows the products(You need to click Today's Files in the page to show today's
 products). Every day I need to monitor how many files are listed in this
 webpage.

![Products Webpage](/images/sat_product_webpage.PNG)

## Rainmeter
[Rainmeter](https://www.rainmeter.net/) is a tool to put customized skins, RSS
feeds and weather information, etc on your desktop. There are many Rainmeter
themes online and you can easily customize those themes.


### Web Parser of Rainmeter

The most important part of this skin is the [web parser](https://docs.rainmeter.net/tips/webparser-lookahead-assertions-in-regexp/).
 Which parses the html
and get the list in the page. The
[time measure](https://docs.rainmeter.net/manual/measures/time/)
MeasureDate is used to get today is date and
to format the date to match the format in the URL.

The RegExp is used to extract the name of the file from the html. As the length
of the list is unknown. The same match returned multiple times to match all the
items in the list.

In the following code:
{% highlight ini %}
RegExp=(?siU)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)...
{% endhighlight  %}

#### Options
<table>
  <tr>
    <th>?</th>
    <th>The directives are for the overall expression </th>
  </tr>
  <tr>
    <th>S</th>
    <th> ignore line breaks and tabs</th>
  </tr>
  <tr>
    <th>i</th>
    <th>case insensitive</th>
  </tr>
  <tr>
    <th>U</th>
    <th>Ungreely, return only the first instance of the match on the search string</th>
  </tr>
</table>

#### Lookahead

To match unknown numbers of repeation, I have to use [lookahead assertion](https://docs.rainmeter.net/tips/webparser-lookahead-assertions-in-regexp/)
 in my
regular expression.  
`(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)`  
The `(?` at the beginning, which pairs the `)` at the end is an IF conditional
directive.   
The `(?=.*<a href='.*tiff'>)` is the lookahead assertion.

#### RainRegExp

To figure out the correct regular expression to use for web parser, you can
download a tool called [RainRegExp](https://docs.rainmeter.net/tips/webparser-debugging-regexp/#RainRegExp)

{% highlight ini %}

; get today's date to put into the url
[MeasureDate]
Measure=Time
Format= %Y-%m-%d

[MeasureParent]
Measure=Plugin
Plugin=WebParser
URL=#Station#[&MeasureDate]
RegExp=(?siU)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)...

{% endhighlight  %}


## Code

{% highlight ini %}
[Rainmeter]
Update=500
Author= Asurin

[Variables]
Station=http://imars.marine.usf.edu/legacy-names/index.php?area=a05&date=

;-------------------------------------------------------------
;-------------------------------------------------------------


; a line between title and the list
[MeterLine]
Meter=Image
ImageName=#@#Line.png
X=0
Y=35
W=200
H=3
AntiAlias=1
Group=Current

; transparent background
[MeterBackground]
Meter=Image
SolidColor=0,0,0,1
X=0
Y=0
W=200
H=280

;-------------------------------------------------------------
;-------------------------------------------------------------
; get today's date to put into the url
[MeasureDate]
Measure=Time
Format= %Y-%m-%d

[MeasureParent]
Measure=Plugin
Plugin=WebParser
URL=#Station#[&MeasureDate]
OnRefreshAction=[!Log "[MeasureDate]"]
RegExp=(?siU)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)(?(?=.*<a href='.*tiff'>).*<a href='.*tiff'>(.*).tiff</a><br />)
Debug=2

[MeasureChild1]
Measure=Plugin
Plugin=WebParser
URL=[MeasureParent]
StringIndex=1
Substitute="":"No Data!"

[MeasureChild2]
Measure=Plugin
Plugin=WebParser
URL=[MeasureParent]
StringIndex=2
Substitute="":"No Data!"

[MeasureChild3]
Measure=Plugin
Plugin=WebParser
URL=[MeasureParent]
StringIndex=3
Substitute="":"No Data!"

[MeasureChild4]
Measure=Plugin
Plugin=WebParser
URL=[MeasureParent]
StringIndex=4
Substitute="":"No Data!"

[MeasureChild5]
Measure=Plugin
Plugin=WebParser
URL=[MeasureParent]
StringIndex=5
Substitute="":"No Data!"

[MeasureChild6]
Measure=Plugin
Plugin=WebParser
URL=[MeasureParent]
StringIndex=6
Substitute="":"No Data!"

[MeasureChild7]
Measure=Plugin
Plugin=WebParser
URL=[MeasureParent]
StringIndex=7
Substitute="":"No Data!"

[MeasureChild8]
Measure=Plugin
Plugin=WebParser
URL=[MeasureParent]
StringIndex=8
Substitute="":"No Data!"

[MeasureChild9]
Measure=Plugin
Plugin=WebParser
URL=[MeasureParent]
StringIndex=9
Substitute="":"No Data!"

[MeasureChild10]
Measure=Plugin
Plugin=WebParser
URL=[MeasureParent]
StringIndex=10
Substitute="":"No Data!"

;-------------------------------------------------------------
;-------------------------------------------------------------

; title
[MeterTextNotes]
Meter=String
FontColor=255,255,255
FontSize=15
FontFace=Aovel Sans
StringAlign=Left
FontSize=10
X=50
Y=0
Text="Sat Products"
AntiAlias=1

[MeterData1]
Meter=String
MeasureName=MeasureChild1
X=0
Y=45
W=190
H=280
FontColor=255,255,255
FontSize=12
FontFace=Aovel Sans
StringAlign=Left
AntiAlias=1
ClipString=1

[MeterData2]
Meter=String
MeasureName=MeasureChild2
X=0
Y=60
W=190
H=280
FontColor=255,255,255
FontSize=12
FontFace=Aovel Sans
StringAlign=Left
AntiAlias=1
ClipString=1

[MeterData3]
Meter=String
MeasureName=MeasureChild3
X=0
Y=75
W=190
H=280
FontColor=255,255,255
FontSize=12
FontFace=Aovel Sans
StringAlign=Left
AntiAlias=1
ClipString=1

[MeterData4]
Meter=String
MeasureName=MeasureChild4
X=0
Y=90
W=190
H=280
FontColor=255,255,255
FontSize=12
FontFace=Aovel Sans
StringAlign=Left
AntiAlias=1
ClipString=1

[MeterData5]
Meter=String
MeasureName=MeasureChild5
X=0
Y=105
W=190
H=280
FontColor=255,255,255
FontSize=12
FontFace=Aovel Sans
StringAlign=Left
AntiAlias=1
ClipString=1

[MeterData6]
Meter=String
MeasureName=MeasureChild6
X=0
Y=120
W=190
H=280
FontColor=255,255,255
FontSize=12
FontFace=Aovel Sans
StringAlign=Left
AntiAlias=1
ClipString=1

[MeterData7]
Meter=String
MeasureName=MeasureChild7
X=0
Y=135
W=190
H=280
FontColor=255,255,255
FontSize=12
FontFace=Aovel Sans
StringAlign=Left
AntiAlias=1
ClipString=1

[MeterData8]
Meter=String
MeasureName=MeasureChild8
X=0
Y=150
W=190
H=280
FontColor=255,255,255
FontSize=12
FontFace=Aovel Sans
StringAlign=Left
AntiAlias=1
ClipString=1

[MeterData9]
Meter=String
MeasureName=MeasureChild9
X=0
Y=165
W=190
H=280
FontColor=255,255,255
FontSize=12
FontFace=Aovel Sans
StringAlign=Left
AntiAlias=1
ClipString=1

[MeterData10]
Meter=String
MeasureName=MeasureChild10
X=0
Y=180
W=190
H=280
FontColor=255,255,255
FontSize=12
FontFace=Aovel Sans
StringAlign=Left
AntiAlias=1
ClipString=1

[MeterData11]
Meter=String
MeasureName=MeasureDate
X=0
Y=200
W=190
H=280
FontColor=255,255,255
FontSize=12
FontFace=Aovel Sans
StringAlign=Left
AntiAlias=1
ClipString=1


{% endhighlight %}
