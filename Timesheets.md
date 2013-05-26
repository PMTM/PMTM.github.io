# What is the timesheet

It is a support standard for 'SMIL' (Synchronized Multimedia Integration Language).
SMIL got separated as standalone standards that is now getting implemented using javascript.
As the name indicates it can be used for triggering actions based on time and user input.
Time is either relative or derived from external media i.e. audio or video. 

# How to include

Timesheet can be included in XHTML or HTML documents:
* HTML : via link - cannot be inlined only external
* XHTML : via namespace (xmlns) - can be inlined or external

* [W3C Timesheets std](http://www.w3.org/TR/timesheets/)
* [Timesheets engine FI](https://mediatech.aalto.fi/~pv/timesheets/)
* [Timesheet.js at InriAlpes.fr](http://wam.inrialpes.fr/timesheets/)
[![DocEng 2011: Timesheets - When SMIL Meets HTML5 and CSS3](http://img.youtube.com/vi/VKxDB4NHWdQ/0.jpg)](http://www.youtube.com/watch?v=VKxDB4NHWdQ)
* [DocEng 2011: Timesheets - When SMIL Meets HTML5 and CSS3](http://www.youtube.com/watch?v=VKxDB4NHWdQ)
* [MarkDown Cheetsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#wiki-code)


# Elements of the language

This language is pretty simple having just a few elements with bunch of attributes.

## timesheet

Top level element that has following next level elemets that actually controls the show.

### seq

Elements __seq__ defines a sequence of child elements that are played one after the other.
That is probably enough to start with. Following example just show simple sequence of
slides that are displayed for 5 seconds each. After that time next slide is displayed.

```xml
<timesheet>
      <seq>
            <item select="#Slide1" dur="5s" />
            <item select="#Slide2" dur="5s" />
            <item select="#Slide3" dur="5s" />
      </seq>
</timesheet>
```

Though the above example is pretty simple it can be futher simplified.
Such demand could justified by deriving the display order just from order inside
the original document (HTML/XHTML) thus swapping of two slides does not require
change of any label or modification of timesheet item elemets.

On the other hand when using __#name-id__ style selectors the change of order within slides is 
controlled just by the timesheet list. Anyway managing more complex sequences and
media synchronization, i.e. multilevel setups like slide and sub-slide animations
or interactions, would still require use of __#name-id__ (which is __id__ atribute
of particular element).

After all the most siplified slideshow can look like this:

```xml
<timesheet>
      <seq>
            <item select=".Slide" dur="5s" />
      </seq>
</timesheet>
```

Comming back to multilevel synchronization which is realized in this case by __seq__
embedded within __item__ of another __seq__ documents the hierarchy.
The below shown example will display each slide for 7 seconds and within the individual
slide bullets are shown one after the other in 1 second intervals.

```xml
<timesheet>
      <seq>
            <item select="#Slide1" dur="7s">
                  <seq>
                        <item select=".Bullet" dur="1s" /> 
                  </seq>
            </item>
            <item select="#Slide2" dur="7s">
                  <seq>
                        <item select=".Bullet" dur="1s" /> 
                  </seq>
            </item>
            <item select="#Slide3" dur="7s">
                  <seq>
                        <item select=".Bullet" dur="1s" /> 
                  </seq>
            </item>
      </seq>
</timesheet>
```

If you are control freak, then id references gives you untimate control over the presentation.

```xml
<timesheet>
      <seq>
            <item select="#Slide1" dur="7s">
                  <seq>
                        <item select="#Bullet1_1" dur="1s" /> 
                        <item select="#Bullet1_2" dur="1s" /> 
                        <item select="#Bullet1_3" dur="1s" /> 
                        <item select="#Bullet1_4" dur="1s" /> 
                  </seq>
            </item>
            ...
      </seq>
</timesheet>
```

As you can see the two above mentioned way to describe in slide animations (aka ___selections___)
is exclusive by definition (element __seq__ displays each it's item after other = show first, hide first, show next, ...).
For slide purpose we usually need to keep items visible while displaying next.

Voila here it is.

```xml
<timesheet>
      <seq>
            <item select="#Slide1" dur="7s">
                  <par>
                        <item select=".Bullet" beginInc="3s" />
                  </par>
            </item>
            ...
      </seq>
</timesheet>
```

Well as you can see it is a bit of _cheating_ and fast forward to __par__ element.
So please hold on to the next section.

So far everything was driven by the time. Which is good for non-interactive presentation
while interactive sessions like _lecture_ like sessions might require more controls then this.

Let us imagine with simple click to next.

This is the simpliest way to make presentation driven by left and right arrow.
Using this you are unable to select random slide. The only thing you can do is to traverse
the presentation in natural order forth and back.

```xml
<timesheet>
      <seq prev="accesskey(Left)" next="accesskey(Right)">
            <item select=".Slide" />
      </seq>
</timesheet>
```

After all you can add home and end buttons, but still hanving strict order.

```xml
<timesheet>
      <seq first="accesskey(Home)" prev="accesskey(Left)" next="accesskey(Right)" last="accesskey(End)">
            <item select=".Slide" />
      </seq>
</timesheet>
```

When we raise demand for having out of order slide selection then the element __seq__
becomes too limiting with it's strict order.
For that we need actually a little bit more than __seq__ element.

The element that gives us the power is __excl__ (short from exclusive).
In fact is was derived from another element __par__ (short for parallel).
Let us first start with __par__ element because for our use case  we need
also the thumbnail index which has to loaded at once. Later on we will add
the __excl__ element to display only the necessary slide when it's thumbnail
is clicked.

### par

Element __par__ defines a simple time grouping in which multiple elements can play at the same time.
Default value of ___begin___ of child element of __par__ is 0 (zero) which means that it starts right after
the __par__ becomes active. The whole element default duration is defined as ___endSync="last"___ in other words
it is long as the longest child element in par
(Cite: _The implicit duration ends with the last active end of the child elements._).

The actual code below simply loads all thumbnails at once
(selector __.Thumbnail__ is a CSS class that refers to all its instances).

```xml
<timesheet>
      <par>
            <item select=".Thumbnail" />
      </par>
</timesheet>
```

Certainly the same could be also achieved by __#name-id__ (refering to __id__ attribute value) reference.
Longer but you can be more specific.

```xml
<timesheet>
      <par>
            <item select="#Thumbnail1" />
            <item select="#Thumbnail2" />
            <item select="#Thumbnail3" />
            <item select="#Thumbnail4" />
            <item select="#Thumbnail5" />
      </par>
</timesheet>
```

Comming back to slide bullets showing one after the other but keeping the previous visible.
The example below actually does that as well as highliting bullet for a certain period of time (1s).

```xml
<timesheet>
      <seq> <!-- Slide sequence -->
            <item select="#Slide1" dur="7s"> <!-- individual slide -->
                  <par> <!-- all items on a single slide -->
                        <item select=".Bullet" beginInc="3s"> <!-- item on a slide here a bullet(s) -->
                              <set select=".Bullet" attributeName="color" to="rgb(0,0,255)" dur="1s" />
                              <!-- shat to do with individual bullet -->
                        <item>
                  <par>
            </item>
            ...
      </seq>
</timesheet>
```

You can see one new element __set__ which is used for animation efect, actually here changing color.


### excl

This elements is used for loading

Thumbnail and main view, where 

```xml
<timesheet>
      <par>
            <excl>
                  <item select=".Image" indexStart="1" begin="index(Thumbnail).click" />
            </excl>
            <par>
                  <item select=".Thumbnail" />
            </par>
      </par>
</timesheet>
```

### item

This is original document reference.

#### select

__Select__ is actually the refernce that this item operates on.
When __item__ element is about to be displayed engine actually request the rendering engine to
render that particular element usually __div__.

#### beginInc

When __select__ attribute matches multiple elements then this attribute shifts the __begin__ time
of each matching element by its value.

```xml
<timesheet>
      <par>
            <item select=".Bullet" beginInc="1s" />
      </par>
</timesheet>
```

In practise, the above example is equal to:

```xml
<timesheet>
      <par>
            <item select="#Bullet1_1" begin="0s" />
            <item select="#Bullet1_2" begin="1s" />
            <item select="#Bullet1_3" begin="2s" />
            <item select="#Bullet1_4" begin="3s" />
      </par>
</timesheet>
```

Assuming that there are just __#Bullet1_1__, __#Bullet1_2__, __#Bullet1_3__ and __#Bullet1_4__.

Or the same applied to __seq__ element:

```xml
<timesheet>
      <seq>
            <item select=".Bullet" beginInc="1s" />
      </seq>
</timesheet>
```

Again expands into:

```xml
<timesheet>
      <seq>
            <item select="#Bullet1_1" begin="0s" />
            <item select="#Bullet1_2" begin="1s" />
            <item select="#Bullet1_3" begin="2s" />
            <item select="#Bullet1_4" begin="3s" />
      </seq>
</timesheet>
```

The only difference is how rendering engine is reuested to display items/elements.
In __par__ case all the items are displayed at the same time (reflowing as necessary).
While in __seq__ case items are displayed one by one exclusively.

## Attributes

__Attributes__ present in __item__ elements instruct the engine to sync with time.

### begin

Defines when particular item should start in __seq__ or __par__ container.

### dur

Obviously duration for how long the item shall be visible.

### end

Complimentary to the ___begin___ and ___dur___, defines when the item ends it selection
again in __seq__ or __par__ (for __par__ the end does not make much sense because the duration
of whole __par__ is defined by the last active element thus only when this value
is the last time value in whole __par__).

### fill

Extends the current element beyond the active duration by freezing the final state of the element.
Depends on media used, mostly applies only to the video element.

### endSync

Sets the explicit end of this element is synchronizes to the other element end.

endSync = ( "first" | "last" | "all" | "media" | Id-value | SMIL-1-Id-value )

* first - when the first sub-element ends
* last - when the last sub-element ends
* all - when all sub-elememts has ended, indefinite sub-element keeps this element from finishing
* media - when linked media ends
* Id-value - particular sub-element
* SMILL-1-Id-Value - ...

### repeatCount

How many times shall the container be repeated.

### repeatDur

For how long the container shall keep running

The following attributes are used to control use of envents and is applicable only for __excl__ element.
All of them are events (click, keypress, ...).

### first
### prev
### next
### last

## Animation

Used for driving changes on screen, style: color, position, transparency.
Can be also done by javascript or CSS (1,2 or 3) transformations including 3D.

### animate
### set
### animateColor
### animateMotion

# Advanced example #

This is an example shows synchronization with external media (audion).

The first listing is the SMIL Timesheet:

```xml
<timesheet xmlns="http://www.w3.org/ns/SMIL">
  <excl dur="2:51" mediaSync="#talk">
    <item select="#slide01" begin="00:00"/>
    <item select="#slide02" begin="00:09"/>
    <item select="#slide03" begin="00:30"/>
    <item select="#slide04" begin="00:50"/>
    <item select="#slide05" begin="01:10"/>
    <item select="#slide06" begin="01:30"/>
    <item select="#slide07" begin="01:55"/>
    <item select="#slide08" begin="02:30"/>
  </excl>
</timesheet>
```

Actual element in HTML that refers to audio that is played and the presentation is
synchronized to it.

```html
...
<audio id="talk" autoplay preload>
    <source type="audio/mp3" src="data/git-pre-commit.mp3" />
    <source type="audio/mp4" src="data/git-pre-commit.m4a" />
    <source type="audio/ogg" src="data/git-pre-commit.ogg" />
</audio>
...
```


```xml
<timesheet xmlns="http://www.w3.org/ns/SMIL">
  <excl timeAction="intrinsic" dur="2:51"
      mediaSync="#talk" controls="#timeController" navigation="arrows; click;">
    <item select="#slide01" begin="00:00"/>
    <item select="#slide02" begin="00:09"/>
    <item select="#slide03" begin="00:30"/>
    <item select="#slide04" begin="00:50"/>
    <item select="#slide05" begin="01:10"/>
    <item select="#slide06" begin="01:30"/>
    <item select="#slide07" begin="01:55"/>
    <item select="#slide08" begin="02:30"/>
  </excl>
</timesheet>
```


__All that put together__

Following code is simple picture gallery that is driven by clicking on thumbnails.
The thumbnails are shown in parallel in order to show them to the user.
Preload feature is not used.
___Begin___ attribute defines trigger to display each individual large image.
__Excl__ element ensures that large pictures are displayed exclusively.

```xml
<timesheet>
  <par>
    <excl>
      <item select="#Image1" begin="Thumbnail1.click" />
      <item select="#Image2" begin="Thumbnail2.click" />
      <item select="#Image3" begin="Thumbnail3.click" />
      <item select="#Image4" begin="Thumbnail4.click" />
      <item select="#Image5" begin="Thumbnail5.click" />
    </excl>
    <par>
      <item select="#Thumbnail1" />
      <item select="#Thumbnail2" />
      <item select="#Thumbnail3" />
      <item select="#Thumbnail4" />
      <item select="#Thumbnail5" />
    </par>
  </par>
</timesheet>
```
