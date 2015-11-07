# Python Work

## MPEG-DASH Analysis

Given the following MPD:

```
<?xml version="1.0"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" profiles="urn:mpeg:dash:profile:full:2011" minBufferTime="PT1.5S">
    <!-- Ad -->
    <Period duration="PT30S">
        <BaseURL>ad/</BaseURL>
        <!-- Everything in one Adaptation Set -->
        <AdaptationSet mimeType="video/mp2t">
            <!-- 720p Representation at 3.2 Mbps -->
            <Representation id="720p" bandwidth="3200000" width="1280" height="720">
                <!-- Just use one segment, since the ad is only 30 seconds long -->
                <BaseURL>720p.ts</BaseURL>
                <SegmentBase>
                    <RepresentationIndex sourceURL="720p.sidx"/>
                </SegmentBase>
            </Representation>
            <!-- 1080p Representation at 6.8 Mbps -->
            <Representation id="1080p" bandwidth="6800000" width="1920" height="1080">
                <BaseURL>1080p.ts</BaseURL>
                <SegmentBase>
                    <RepresentationIndex sourceURL="1080p.sidx"/>
                </SegmentBase>
            </Representation>
        </AdaptationSet>
    </Period>
    <!-- Normal Content -->
    <Period duration="PT5M">
        <BaseURL>main/</BaseURL>
        <!-- Just the video -->
        <AdaptationSet mimeType="video/mp2t">
            <BaseURL>video/</BaseURL>
            <!-- 720p Representation at 3.2 Mbps -->
            <Representation id="720p" bandwidth="3200000" width="1280" height="720">
                <BaseURL>720p/</BaseURL>
                <!-- First, we'll just list all of the segments -->
                <!-- Timescale is "ticks per second", so each segment is 1 minute long -->
                <SegmentList timescale="90000" duration="5400000">
                    <RepresentationIndex sourceURL="representation-index.sidx"/>
                    <SegmentURL media="segment-1.ts"/>
                    <SegmentURL media="segment-2.ts"/>
                    <SegmentURL media="segment-3.ts"/>
                    <SegmentURL media="segment-4.ts"/>
                    <SegmentURL media="segment-5.ts"/>
                    <SegmentURL media="segment-6.ts"/>
                    <SegmentURL media="segment-7.ts"/>
                    <SegmentURL media="segment-8.ts"/>
                    <SegmentURL media="segment-9.ts"/>
                    <SegmentURL media="segment-10.ts"/>
                </SegmentList>
            </Representation>
            <!-- 1080p Representation at 6.8 Mbps -->
            <Representation id="1080p" bandwidth="6800000" width="1920" height="1080">
                <BaseURL>1080/</BaseURL>
                <!-- Since all of our segments have similar names, this time we'll use a SegmentTemplate -->
                <SegmentTemplate media="segment-$Number$.ts" timescale="90000">
                    <RepresentationIndex sourceURL="representation-index.sidx"/>
                    <!-- Let's add a SegmentTimeline so the client can easily see how many segments there are
                         -->
                    <SegmentTimeline>
                        <!-- This reads: Starting from time 0, there are 10 segments with a duration of
                             (5400000 / @timescale) seconds -->
                        <S t="0" r="10" d="5400000"/>
                    </SegmentTimeline>
                </SegmentTemplate>
            </Representation>
        </AdaptationSet>
        <!-- Just the audio -->
        <AdaptationSet mimeType="audio/mp2t">
            <BaseURL>audio/</BaseURL>
            <!-- We're just going to offer one audio representation, since audio bandwidth isn't very
                 important. -->
            <Representation id="audio" bandwidth="128000">
                <SegmentTemplate media="segment-$Number$.ts" timescale="90000">
                    <RepresentationIndex sourceURL="representation-index.sidx"/>
                    <SegmentTimeline>
                        <S t="0" r="10" d="5400000"/>
                    </SegmentTimeline>
                </SegmentTemplate>
            </Representation>
        </AdaptationSet>
    </Period>
</MPD>
```

### We must figure out:

* Is it a valid MPD file?
* How long is the entire presentation?
* How many segments are there in the MPD?
* How long, in seconds, is each segment?

#### **It is an MPD stream**

```
In [1]: import xml.etree.ElementTree as etree

In [2]: tree = etree.parse('dash_test.xml')

In [3]: root = tree.getroot()

In [4]: root
Out[4]: <Element '{urn:mpeg:dash:schema:mpd:2011}MPD' at 0x7f9170db8548>  # good enough for me!
```

#### **There are ten segments**

```
In [37]: for element in seg:
   ....:     print(element)
   ....:     if 'Segment' in str(element):
   ....:         print("HAYOO")
   ....:         
<Element '{urn:mpeg:dash:schema:mpd:2011}RepresentationIndex' at 0x7f9170db8cc8>
<Element '{urn:mpeg:dash:schema:mpd:2011}SegmentURL' at 0x7f9170db8d68>
HAYOO
<Element '{urn:mpeg:dash:schema:mpd:2011}SegmentURL' at 0x7f9170db8db8>
HAYOO
<Element '{urn:mpeg:dash:schema:mpd:2011}SegmentURL' at 0x7f9170db8e08>
HAYOO
<Element '{urn:mpeg:dash:schema:mpd:2011}SegmentURL' at 0x7f9170db8e58>
HAYOO
<Element '{urn:mpeg:dash:schema:mpd:2011}SegmentURL' at 0x7f9170db8ea8>
HAYOO
<Element '{urn:mpeg:dash:schema:mpd:2011}SegmentURL' at 0x7f9170db8ef8>
HAYOO
<Element '{urn:mpeg:dash:schema:mpd:2011}SegmentURL' at 0x7f9170db8f48>
HAYOO
<Element '{urn:mpeg:dash:schema:mpd:2011}SegmentURL' at 0x7f9170db8f98>
HAYOO
<Element '{urn:mpeg:dash:schema:mpd:2011}SegmentURL' at 0x7f9170dbe048>
HAYOO
<Element '{urn:mpeg:dash:schema:mpd:2011}SegmentURL' at 0x7f9170dbe098>
HAYOO

In [38]: seg_num = 0

In [39]: for element in seg:
   ....:     if 'Segment' in str(element):
   ....:         print("HAYOO")
   ....:         seg_num += 1
   ....:         
HAYOO
HAYOO
HAYOO
HAYOO
HAYOO
HAYOO
HAYOO
HAYOO
HAYOO
HAYOO

In [40]: seg_num
Out[40]: 10
```

#### Each Segment is Sixty Seconds in Length

```
In [49]: seg.attrib
Out[49]: {'duration': '5400000', 'timescale': '90000'}

In [50]: int(seg.attrib['duration']) / int(seg.attrib['timescale'])
Out[50]: 60.0
```

## Output a JSON report

```
In [51]: import json

In [52]: results = {}

In [53]: seg_length = int(seg.attrib['duration']) / int(seg.attrib['timescale'])

In [54]: total_dur = seg_length * seg_num

In [55]: total_dur
Out[55]: 600.0

In [56]: results['segment_length'] = seg_length

In [57]: results['duration'] = total_dur

In [58]: results['segment_count'] = seg_num

In [59]: results
Out[59]: {'duration': 600.0, 'segment_count': 10, 'segment_length': 60.0}

In [60]: json.dumps(results)
Out[60]: '{"duration": 600.0, "segment_length": 60.0, "segment_count": 10}'
```

## Ingest MPEG-DASH Content

**So far all attempts have been unsuccessful.**