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

```xml
<timesheet>
      <seq>
            <item select=".Slide" dur="5s" />
      </seq>
</timesheet>
```

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

### par

Element __par__ defines a simple time grouping in which multiple elements can play at the same time.
Default value of ___begin___ of child element of __par__ is 0 (zero) which means that it starts right after
the __par__ becomes active. The whole element default duration is defined as ___endSync="last"___ in other words
it is long as the longest child element in par
(Cite: _The implicit duration ends with the last active end of the child elements._).

### excl

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
