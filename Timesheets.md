# What is the timesheet

It is a support standard for 'SMIL' (Synchronized Multimedia Integration Language).
SMIL got separated as standalone standards that is now getting implemented using javascript.
As the name indicates it can be used for triggering actions based on time and user input.
Time is either relative or derived from external media i.e. audio or video. 

# How to include

Timesheet can be included in XHTML or HTML documents:
* HTML : via link - cannot be inlined only external
* XHTML : via namespace (xmlns) - can be inlined or external

http://www.w3.org/TR/timesheets/#smilTimesheetsNS-Elements-Timesheet

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
is exclusive by definition (__seq__ displays each it's item after other = show first, hide first, show next, ...).
For slide purpose we usually need to keep items visible while displaying next.

Voila here it is.

```xml
<timesheet>
      <par>
            <item select=".Bullet" beginInc="3s" />
      <par>
</timesheet>
```

Well as you can see it is a bit cheating and fast forward to __par__.
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
Let us first start with that element because for use case described above we need
also the thumbnail index which has to loaded at once.
Thus __par__ is the appropriate element here.

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

Certainly the same could be also achieved by __#name-id__ (__id__ attribute value) reference.
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

Comming back to slide bullets show.

```xml
<timesheet>
      <par>
            <item select=".Bullet" beginInc="3s">
                  <set select=".Bullet" attributeName="color" to="rgb(0,0,255)" dur="1s" />
            <item>
      <par>
</timesheet>
```

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

```html
<audio id="talk">
    <source type="audio/mp3" src="data/git-pre-commit.mp3" />
    <source type="audio/mp4" src="data/git-pre-commit.m4a" />
    <source type="audio/ogg" src="data/git-pre-commit.ogg" />
</audio>
```

```html
<audio id="talk" autoplay preload>
    <source type="audio/mp3" src="data/git-pre-commit.mp3" />
    <source type="audio/mp4" src="data/git-pre-commit.m4a" />
    <source type="audio/ogg" src="data/git-pre-commit.ogg" />
</audio>
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
