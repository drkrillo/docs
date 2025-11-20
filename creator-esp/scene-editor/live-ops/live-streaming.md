---
description: Stream live video into your scene using the Video Screen and Admin Tools.
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/oPnXBby9S6MrsW83Y9qZ/scene-editor/live-ops/live-streaming
---

# Live Streaming

Use the **Video Screen** smart item together with the **Scene Admin** smart item to stream live video into your scene.

### Requirements

* A scene with at least one [Video Screen](../../../creator/scene-editor/interactivity/video-screen.md) smart item.
* The [Scene Admin](../../../creator/scene-editor/live-ops/scene-admin.md) smart item linked to your video screens.
* Streaming software that can output to an RTMP endpoint (e.g. [OBS](https://obsproject.com/), [XSplit](https://www.xsplit.com/), [StreamYard](https://streamyard.com/)).

### Configure the scene

1.  Add a **Video Screen** smart item to your scene.

    ![](../../.gitbook/assets/video-player-item.png)
2.  Add a **Scene Admin** smart item and enable the **Video Screens** section. Select each screen from the dropdown and give it a friendly name for the admin UI.

    ![](../../.gitbook/assets/multi-video-setup.png)
3.  Publish your scene (World or Genesis City) and enter as a user with admin permissions.

    ![](../../.gitbook/assets/publish-button.png)

### Get stream credentials

1.  Open the Admin UI in the scene (top‑right icon).

    ![](../../.gitbook/assets/admin-icon.png)
2.  In the **Video** tab, switch to **Live** and click **Get Stream Key**.

    ![](../../.gitbook/assets/get-key.png)
3.  Copy the **RTMP Server** and **Stream Key** into your streaming software.

    ![](../../.gitbook/assets/OBS-configuration.png)

{% hint style="danger" %}
**❗Warning**: Only one person can stream to a scene at a time. When finished streaming, click **Stop Streaming** in your software to free the channel.
{% endhint %}

### Start and control the stream

1. Start streaming from your software.
2.  In the Admin UI, click **Activate** to show the stream in the scene.

    ![](../../.gitbook/assets/activate.png)

Notes:

* Streaming works in Worlds and Genesis City, with no audience limits on the scene side.
* If you add multiple Video Screens, mute all but one to avoid audio artifacts.

### Stream keys

Stream keys are generated per scene and are valid for 4 days (96 hours). A single live session can run up to 4 hours continuously.

![](../../.gitbook/assets/live-stream-settings.png)

* Click **Reset Stream Key** to revoke the current key and issue a new one. Ongoing streams will stop.
* Each scene has its own streaming address and key. Admins can share the key with external streamers.
* Only one stream can be active per scene at a time; starting a new one will overwrite the current stream.

{% hint style="danger" %}
**❗Warning**: Treat stream keys as secrets. Reset the key between presenters if needed.
{% endhint %}

### Streaming from other providers

You can also stream using third‑party infrastructure by configuring the Video Screen to **Video URL** and pasting a stream URL.

* The URL must be `https` and CORS‑enabled by the provider (YouTube and similar sites won’t allow direct playback). See [About External Streaming](../../../creator/sdk7/media/video-playing.md#about-external-streaming).
* Managed providers include [Vimeo](https://vimeo.com/), [Livepeer Studio](https://livepeer.studio/) and [Serraform](https://serraform.gitbook.io/streaming-docs/guides/decentraland-playback).
* Tips for encoder setup: [Setting up OBS for successful streaming](../../../creator/sdk7/media/video-playing.md#setting-up-obs-for-successful-streaming).
