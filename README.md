# btc-media-payments
I am trying to develop a micro-payments scheme that delivers small amounts of Bitcoin directly to content creators whenever viewers consume their media. 

Please note: This project is in the purely-theoretical-completely-not-practical land of my own mind. While I welcome any and all input/collaboration, everything written beneath this statement is likely to change drastically, and often.

## Background

The idea behind this is that content viewers would be able to send micro donations to content creators on a **per-streaming-segment basis**. By this, I mean that each segmented chunk of the streaming media will be monetized. This way, only content that has been viewed by the user is monetized.

To verify that the viewer is consuming donation-friendly media, we will leverage the built-in encryption methodologies that are available in adaptive bitrate streaming technologies such as HLS and MPEG-DASH. Unique ID strings are provided by these technologies for each file segment served. This means that we can validate each segment's string against that content's database of ID strings.

Because each ID string comes from a new segment of the streaming media content, it would be prudent to have each ID string paired with its own individual BTC wallet. This way, content creators will know intricately how their content has been monetized.

#### License

Artists need to make a living, and licensing should not pose any legitimate restriction upon achieving such goals. I intend for this project to be as GNU-compliant as possible. Therefore, all software within this repository is GPLv2 - so go ahead and do whatever!

## Goals

* Generate a frameMD5 blueprint of an HLS/MPEG-DASH media stream and its contents
* Determine how many segments are in the stream, the segment length, and the overall duration of the content
* Determine if the stream is audio-only, video-only, or audio-visual.

## Software Requirements

To achieve this, we will need to make software.

#### Media Stream Analysis

First, we will need to devise a means to **analyze media content streams**. It would setup an array of wallets based upon the length, number, and qualities of the streaming content segments.

Running a valid HLS or MPEG-DASH (or other) segmented media stream through this software should yield:

* A unique MD5-style hash of the media file, along with verification that it is a unique work within the context of the other video content registered to the database.
* A report containing analysis of the media stream and file(s), including specific instructions such as number of wallets to make, etc.

#### Bitcoin Wallet Creation and Management

We will need to pass the instructions from the analysis program to a second program, which can **create and manage bitcoin wallets**. 

The instructions provided in the final stream report will indicate how many wallets to create based on the length of the segments. This wallet management program will need to create wallets based on these instructions, and keep track of which wallet belongs to which media segment in the file's overall timeline.

#### Viewer Client

Viewers who are participating in this micro-donation scheme I'm cooking up will be running a little daemonized applet which will keep track of micro-donations for viewers. They will provide the client a bitcoin wallet from which to grab a micro donation, and the amount to donate per segment. At the moment, a 10-second segment price of 0.0000005 BTC seems appropriate. For a five minute audio stream, that would total 0.000015 BTC, which equates to $0.015 (one and a half cents.) That's on par with what Spotify pays out in streaming royalties via a service such as TuneCore.

If a viewer deposited 1 BTC into the wallet connected to their client, they would be able to log over 1100 hours of content consumed. Of course, since all of this is voluntary, the donation amount will have to be up to the user, ultimately. However, I could see discounts being provided for streams that occur on off-peak hours of the day, donation rates that are scaled by the current total valuation of the connected wallet (akin to income-based price scaling), etc. 

Seeing as this is a donation-based initiative, it would be nice if users got a little something from using such a service. It would be worth exploring what cool features we could build into this client to make it truly worthwhile. I'm thinking a nice, organizable history of the media you've viewed and donated to, play counts and other historical data, suggested content based on viewing/listening habits... I could go on and on. I'm sure you get the picture.