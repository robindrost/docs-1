# Livery Portals

Both the Livery Video & Interaction solution are fully manageable by their own online portals. The portals can be used together or as separate solutions. 

Livery Video: https://video-encoder-director.playtotv.com/

Livery Interactive: https://livery-videoextension-{customer}-director.playtotv.com/

## Livery Video Portal

The Livery customers portal allows our customers to manage their streams. The portal comes with default settings, and if requested the Livery team is able to customize the setup according to the specific needs of the customer(s), making more features available.

The core video features are accessible via a direct API implementation. Allowing our customers to merge the Livery Video solution in their own platform. More information about the API implementation can be found in the [API section.](customer-api.md 'api-calls')

### Create a stream

Additional streams can be added via the “+Stream” button in the top right corner of the portal. The required information needs to be added in the panel. 

![Add Stream](portal/add_stream.png)


|Field|Description|
|-----|-----------|
|Name|The stream "Name" is used as reference in the system.|
|Type|Determines if the stream will use the cloud or on-prem encoder.|
|Region|Optimises the stream settings according to the main region of the viewers. When a required region is not included, select the second best location and/or contact the Livery team for support.| 
|Video Quality|The Video quality can be set by selecting one or multiple video bitrates. The name of the bitrates contains the information about the configuration of the video quality. The players will use the ABR algorithm when multiple bitrates have been selected. The name of the video quality is set according the following logic: {"P" for Portrait or "L" for Landscape}_{Bitrate (1000K)}_{Resolution (720p)}_{Codec}_{Frame rate (25FPS)}.|
|Audio Quality|The Audio quality can be set by selecting one audio bitrate.|


### Stream Overview

All streams connected to the account are listed in the stream overview. The basic information is listed in the row of a stream. Additional information and settings can be found in the menu on the right side of the row. 
The StreamID is used to link a stream to a player implementation. More information about the players and the integration of the player SDK’s can be found on [docs.liveryvideo.com] (index.md 'Main page')

The ID of the linked encoders is visible in the row. The cloud encoder ID only shows while streaming. The on-prem encoders, when linked, are always visible. An on-prem encoder, when available, can be linked to a stream via the “Add Encoder to Stream” option in the stream menu.  

The Livery Console shows information about a cloud stream. For example the ingest url (SRT or RTMP) can be copied and pasted in your streaming program (OBS/Wirecast/etc). Please see [Mixer Soft- and Hardware section](encoder.md 'Main page') for more info. Once you start streaming from there a cloud instance will be provisioned for your stream. This can take some timem 20-30 seconds when no cloud available cloud encoders are actie. Once the encoder is provisioned and streaming, a red bubble with the text Streaming will appear which indicates the stream is live.
When the ingest to the cloud encoder has been stopped, the could encoder shutters down. 

The Livery platform knows 3 stream phases; Pre, Live and Post broadcast. The selected phase determines the behaviour of the integrated player. 

|Phase|Description|
|-----|-----------|
|Pre|The player does not actively checks if the stream is active, the viewers will not see a loading indicator and no stream data is collected. The players will show the Pre-Stream-Poster Image when set.|
|Live|The player actively checks if a stream is active, the viewers might see a loading indicator when the player checks for the stream. The players collect stream data, when in the Live phase. The players will show the Live-Stream-Poster Image when set.|
|Post|The player does not actively checks if the stream is active, the viewers will not see a loading indicator and no stream data is collected. The players will show the Post-Stream-Poster Image when set.|


The Stream Phases dropdown allows you to change the phase of the stream. Please contact the Livery team if you want to use the Stream Phase feature and the option is not listed in the portal. 

?> A phase change takes about 20-60 seconds to become effective for all viewers.

![Streams](portal/stream_line_item.png)

### Encoder Overview
One or more encoders can be linked to a customer account. The encoders are listed in the encoder overview. Encoders can be linked or unlinked to a stream, this allows the customer to rotate a single on-prem encoder between multiple streams.

![Encoders](portal/encoder_line_item.png)

One or more Encoders can be linked to a stream to create a redundant setup. The proper role need to be defined for each encoder. The Livery Platform works with 3 encoder roles.

|Role|Description|
|-----|-----------|
|Main|The main encoder for the stream, the majority of the user will be connected with the main encoder.|
|Backup|The backup encoder for the stream. When the player(s) detect a possible issue it will switch to the backup encoder.|
|Standby|The players will not connect to an encoder in “Standby”. It is important to prevent a situation where a standby encoders is linked to a stream.|


### Stream settings

The settings linked to a stream are managed in the stream setting. Any change in the stream settings are automatically picked up by the players. It might take up-to 60 seconds before a change becomes available for all viewers.

|Field|Description|
|-----|-----------|
|Name|The stream "Name" is used as reference in the system.|
|Target Latency|The Livery Platform adjusts glass-to-glass latency based on the set target latency.|
|Poster image|Each phase can have it's own poster image, shown when the stream loads or when no stream is active. The poster image can be set by uploading an image or adding an image UR. An image with the same ratio as the stream settings is recommended.|
|Interactive|The interactive URL load a HTML/JavaScript element in the interactive layer of the player.
|Pre-roll|The preroll is an short video clip loaded before the player loads the live stream. A MPEG-4 video file with the following specifications is recommended: 15 seconds video clip, 1080p, 4Mbps video and 128Kbps audio.|
|Fit|The Fit value determine how the video object fits the container: <br> - Contain, The content is scaled to maintain its aspect ratio while fitting within the container. <br> - Cover, The content is sized to maintain its aspect ratio while filling the entire container. <br> - Fill, The content is sized to fill the container. The entire object will completely fill the container.|
|Domain Whitelisting|One or more domains can be whitelisted when domain whitelisting is enabled. Only players hosted on these domains will be able to watch the stream. If this field is empty, any player can watch the stream.|
|Player configuration|The player settings determine the availability of the different player functions. Each of the settings can be turned ON or OFF. The following settings are available; <br> - Unbranded, determines if the Livery branding is shown in the players. <br> - Casting determines if the Google Cast and Apple Airplay features are accessible to the end-user. <br> - Error determines if the errors are shown in the players. <br> - Fullscreen, determines if the fullscreen button is shown in the players. <br> - Mute determines if the mute/unmute button is shown in the players. <br> - Pip determines if the picture-in-picture mode is allowed. <br> - Quality determines if the quality selection is shown in the players. <br> - Autoplay determines if the video automatically starts when the player is loaded. It is not always possible to Autoplay unmuted, due to browser restrictions.|

![Edit Stream](portal/stream_settings.png)

### Encoder settings

The settings linked to one or more Encoders are managed in the encoder setting. The encoder settings determine how the incoming video is encoded and transcoded. A stream needs to be stopped before any of the encoder settings can be changed. 

|Field|Description|
|-----|-----------|
|Video Quality|The Video quality can be set by selecting one or multiple video bitrates. The name of the bitrates contains the information about the configuration of the video quality. The players will use the ABR algorithm when multiple bitrates have been selected.|
|Audio Quality|The Audio quality can be set by selecting one audio bitrate.|
|Segment Size|Determines the size in seconds of the video segments.|
|Crop Method|The "Crop Method" determines how the encoder is allowed to crop the video content to meet the set resolution. This is especially used when the ingest is in landscape and the stream in portrait. When manual is selected an X/Y offset is to be entered, calculated for the top left corner.|

### Stream History

The stream history which can be accessed via the top right menu, contains a list of all streaming moments. The overview contains a basic list with details to provide insights in each of the streaming moments. When a redundant setup with multiple encoders is configured, each encoder is listed as a separate streaming moment. 

### Statistics Dashboard

The statistics dashboard contains an overview of basic statistics, which are updated every 60 seconds. A customer is able to see his statistics for the past 14 days. Please contact the Livery team for specific stats, which are not listed in the dashboard. 

Besides the dashboard, customers can also access the statistics via an API integration. Please see the GetAnalytics section in the [API section](customer-api.md 'GetAnalytics') for more info.

<!--  # Interactive Portal
 TBA
##Create broadcast
TBA
##Broadcast interactions
TBA
--> 

# Livery Portals Revision History
## 1.0.43
- Map improvements stats dashboard. 

# Livery Portals Q&A

### **How to link an on-prem Encoder to a different stream?**

*One or more on-prem encoders could be linked to a stream. To unlink an encoder from a stream you need to select “Remove Encoder” from the stream menu (accessible via the 3 dotted button on the right side of the stream). When the encoder is successfully removed the encoder ID is no longer listed in the Encoders column.*

*You can now add the removed encoder to a different stream, by clicking “Add Encoder to stream” from the stream menu. You need to select the specific encoder from the dropdown menu.*

*All encoders (linked or unlinked) are listed in the encoder overview tab.*

