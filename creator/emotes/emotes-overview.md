---
description: An overview of emotes NFTs for Decentraland
---

# Emotes Overview

\
&#x20; const profile = Math.ceil(Math.random() \* 120)\
&#x20; const emotes = \['clap', 'dab', 'dance', 'fashion', 'fashion-2', 'fashion-3','fashion-4', 'love', 'money', 'fist-pump', 'head-explode']\
function emote() {\
&#x20; return emotes\[Math.floor(Math.random() \* emotes.length)]\
}\
document.getElementById("emote-preview").src = "https://wearable-preview.decentraland.org/?profile=default"+profile+"\&emote="+emote()+"\&transparentBackground\&loop=true"\
\
&#x20; function changeProfile() {\
document.getElementById("emote-preview").contentWindow.postMessage({\
&#x20; type: 'update',\
&#x20; payload: { options: {\
&#x20;   profile: \`default${Math.ceil(Math.random() \* 120)}\`\
&#x20; } }\
},'\*')\
return false\
&#x20; }\
\
&#x20; function changeEmote() {\
document.getElementById("emote-preview").contentWindow.postMessage({\
&#x20; type: 'update',\
&#x20; payload: { options: {\
&#x20;   emote: emote()\
&#x20; } }\
},'\*')\
return false\
&#x20; }

Change avatar ↺ - Change emote ↺

Emotes are animation sequences for avatars’ skeleton bones, which are defined in a transport file, usually in `.glb`, or `.gltf` formats. An emote can include sound, or even an animated 3d geometry attached to it. For more information check [Adding Props and Sounds to your Emotes](https://github.com/decentraland/docs-creator/blob/main/creator/emotes/props-and-sounds/README.md).

There are a selection of free default Emotes that are available to any user, but Decentraland also supports the creation and use of custom Emotes that are represented by non-fungible tokens ( NFTs). This allows a finite amount of different Emotes to be created, or minted, on the blockchain, similar to **\[Wearables]\(\{{< ref "/content/creator/wearables-and-emotes/wearables/wearables-overview.md" >\}})**.

By default, Decentraland Emotes are minted on the Polygon/Matic sidechain so users can mint, buy, sell, or transfer items without having to pay gas fees (the DAO covers these costs as voted on in [**this Proposal**](https://governance.decentraland.org/proposal/?id=548aa0c0-d51a-11ec-b521-2f98ffa6ccb0)).

Emotes are organized into different categories depending on what a Creator thinks best describes what an Emote does. The available categories are:

* Dance
* Stunt
* Greetings
* Fun
* Poses
* Reactions
* Horror
* Miscellaneous
