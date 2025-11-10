
# Architecture

The general architecture of Decentraland can be divided into three main components:

* [The Catalyst network](architecture.md#catalyst): distributed peer servers that host content and provide the core APIs.
* [The World Explorer](architecture.md#explorer): app for players to log into Decentraland and explore the land.
* [The CLI](architecture.md#cli): a command-line interface for creators to develop and deploy content.

The interactive diagram below captures the most important sub-components of the protocol, and can take you to the relevant documentation to learn more.

{% hint style="info" %}
**Note**: The interactive architecture diagram from the original documentation has been preserved, but may require adaptation for Gitbook. You can view the [Foundation's architectural diagram](https://github.com/decentraland/documentation/blob/main/static/images/contributor/architecture.png) for reference.
{% endhint %}

## Catalyst <a href="#catalyst" id="catalyst"></a>

A Catalyst is a node in the Decentraland network that provides an array of different services, including access to content, peer-to-peer communications, blockchain inspection and various utility endpoints.

You can inspect the state of the network using the [Catalyst Monitor](https://decentraland.github.io/catalyst-monitor), and host your own node by becoming a [Catalyst owner](https://github.com/decentraland/catalyst-owner).

**Subsystems:**

* [Content service](architecture.md#catalyst-content)
* [Lambda service](architecture.md#catalyst-lambdas)
* [Comms service](architecture.md#catalyst-archipelago)
* [Backend-for-frontend service](architecture.md#catalyst-bff)

**Learn more:**

* Subsystem sections listed above
* [Catalyst repository](https://github.com/decentraland/catalyst)

### Content Service <a href="#catalyst-content" id="catalyst-content"></a>

The content service, provided by all Catalysts, allows access to the distributed file-system. With this API, clients can query content indices, retrieve files, deploy entities and download periodic snapshots.

**Learn more:**

* [Content system documentation](../../content/overview.md)
* [Content API reference](https://decentraland.github.io/catalyst-api-specs/#tag/Content-Server)
* [Catalyst repository](https://github.com/decentraland/catalyst)

### Lambda Service <a href="#catalyst-lambdas" id="catalyst-lambdas"></a>

The lambda service, provided by all Catalysts, offers a set of utility endpoints that allow clients to retrieve and validate commonly required data.

Some of the endpoints require access to blockchain state to verify the ownership of referenced assets. In these cases, [The Graph](https://thegraph.com/hosted-service/subgraph/decentraland/collections-matic-mainnet) is used to query the blockchain.

**Learn more:**

* [Lambdas API reference](https://decentraland.github.io/catalyst-api-specs/#tag/Lambdas)
* [Catalyst repository](https://github.com/decentraland/catalyst)

### Comms Service <a href="#catalyst-archipelago" id="catalyst-archipelago"></a>

The _archipelago_ service, provided by all Catalysts, groups players into dynamic clusters called _islands_, defined by proximity. It automatically balances the number of players on each island, and reassigns them as they travel the world to enable efficient peer-to-peer communications (such as text and voice chat).

The service is based on a generic transport layer that can be backed by different real-time systems in different areas, according to the configuration of each Catalyst.

**Learn more:**

* [Archipelago repository](https://github.com/decentraland/archipelago-service)
* [Comms API reference](https://decentraland.github.io/catalyst-api-specs/#tag/Comms)

### Backend for Frontend Service (BFF) <a href="#catalyst-bff" id="catalyst-bff"></a>

This service was created to resolve client needs to enable faster development of new features without breaking the existing APIs. In the Catalyst context, it's used for the communications between peers connected to the client, its main responsibility is to manage the P2P signaling.

**Learn more:**

* [BFF repository](https://github.com/decentraland/explorer-bff)

## World Explorer <a href="#explorer" id="explorer"></a>

The World Explorer is the iconic client of the Decentraland protocol, an application for players to enter the metaverse, explore it and enjoy the experiences created by others.

The reference implementation of a fully-featured World Explorer is the [Decentraland Foundation's](https://github.com/decentraland/explorer) project.

**Subsystems:**

* [Content system](architecture.md#explorer-content)
* [Game engine](architecture.md#explorer-engine)
* [Scene runtime](architecture.md#explorer-runtime)
* [Comms system](architecture.md#explorer-comms)

**Learn more:**

* [Decentraland Foundation Explorer](architecture.md#fwe)

## Content system <a href="#explorer-content" id="explorer-content"></a>

The client-side counterpart to the [content service](architecture.md#catalyst-content) of the Catalyst network.

It downloads and caches content files, and can update assets such as a player's profile on behalf of the user.

**Learn more:**

* [Content documentation](../../content/overview.md)

### Game engine <a href="#explorer-engine" id="explorer-engine"></a>

The graphics rendering and interactive functions of the World Explorer are provided by a game engine, such as Unity.

**Subsystems:**

* [Rendering system](architecture.md#explorer-renderer)
* [State system](architecture.md#explorer-state)
* [Input system](architecture.md#explorer-input)

#### Rendering System <a href="#explorer-renderer" id="explorer-renderer"></a>

This subsystem receives instructions from scenes running in the World Explorer and renders the 3D environment.

Each World Explorer implements this system using the capabilities of the chosen game engine.

#### State System <a href="#explorer-state" id="explorer-state"></a>

The game engine gives life to all entities and components created across scenes, synchronizing a globally maintained state with the individually handled state of each scene.

**Learn more:**

* [Runtime documentation](../../runtime/overview.md)

#### Input System <a href="#explorer-input" id="explorer-input"></a>

The input system handles user interaction, such as the player's keyboard, mouse or controller. Events are then routed to the components that subscribed to them, including interactive entities and UI displays.

**Learn more:**

* [Components documentation](../../runtime/components.md)

## Scene Runtime <a href="#explorer-runtime" id="explorer-runtime"></a>

The scene runtime is the sandboxed environment where community-developed scenes can run in isolation, without interfering with one another or directly accessing security-sensitive system functions.

**Learn more**

* [Runtime documentation](../../runtime/overview.md)
* [Foundation Explorer runtime](architecture.md#fwe-runtime)

#### Runtime Library <a href="#explorer-library" id="explorer-library"></a>

The World Explorer's scene runtime provides a module library with commonly required functionality and access to methods that require authorization to reach beyond the sandbox.

**Learn more:**

* [Runtime documentation](../../runtime/overview.md)
* [Foundation Explorer runtime](architecture.md#fwe-runtime)

#### Scenes <a href="#explorer-scenes" id="explorer-scenes"></a>

Scenes are community-developed bundles of behavior and assets, and offer the various experiences players can enjoy in Decentraland.

**Learn more:**

* [Scene protocol documentation](../../content/entity-types/scenes.md)
* Scene creator documentation (available in docs-creator project)
* [Foundation Explorer scene system](architecture.md#fwe-scene)

### Comms System <a href="#explorer-comms" id="explorer-comms"></a>

The client-side counterpart to the [archipelago comms service](architecture.md#catalyst-archipelago) of the Catalyst network.

The World Explorer's comms system can connect to the different peer-to-peer communication networks supported by Decentraland in order to provide text and voice chat functionality for players, and to exchange messages between clients.

**Learn more:**

* [Foundation Explorer comms system](architecture.md#fwe-comms)

## CLI <a href="#cli" id="cli"></a>

The `dcl` [command-line interface](https://github.com/decentraland/cli) is the principal tool to assist content creators in the development of scenes and other projects.

Among other capabilities, it can bootstrap environments, run and visualize scenes, test them and deploy them to the content network.

**Learn more:**

* CLI documentation (available in docs-creator project)
* [CLI repository](https://github.com/decentraland/cli)
* [Examples and Tutorials](https://studios.decentraland.org/resources?sdk_version=SDK7)

***

## Foundation's World Explorer <a href="#fwe" id="fwe"></a>

The web browser implementation of a World Explorer is the fully-featured version developed and maintained by the Decentraland Foundation. It leverages the entire set of features offered by the protocol, and is the reference codebase for community projects.

See the Foundation's [architectural diagram](https://github.com/decentraland/documentation/blob/main/static/images/contributor/architecture.png) to get an overview of the actual implementation components, as used in practice.

**Learn more**:

* [Explorer repository](https://github.com/decentraland/explorer)
* [Kernel repository](https://github.com/decentraland/kernel)
* [Client comms repository](https://github.com/decentraland/catalyst-comms-peer)

### Catalyst Client <a href="#fwe-catalyst-client" id="fwe-catalyst-client"></a>

This client [library](https://github.com/decentraland/catalyst-client) can be used to interact with Decentraland's Catalyst servers. You can both fetch data, or deploy new entities to the server you specify.

**Learn more:**

* [Catalyst client repository](https://github.com/decentraland/catalyst-client)

### Peer Library <a href="#fwe-peer-library" id="fwe-peer-library"></a>

The [Peer Library](https://github.com/decentraland/catalyst-comms-peer) manages Websocket connections for WebRTC signaling, Islands, Notifications and Location Data and WebRTC Connections for peers positions, scene bus, global chat and voice chat (private chat goes through the Matrix Synapse Server and the Matrix Client).

**Learn more**:

* [Client comms repository](https://github.com/decentraland/catalyst-comms-peer)

### Kernel - Voice Chat Module <a href="#fwe-voice-chat" id="fwe-voice-chat"></a>

This [Module](https://github.com/decentraland/explorer/tree/af59463dd3882516874c86bc926726bc557d5184/kernel/packages/voice-chat-codec) is the codec to hook WebAudio & Worklets to comms

### Kernel - Client Comms <a href="#fwe-client-comms" id="fwe-client-comms"></a>

Client-side module to interact with the Decentraland network comms system.

**Learn more:**

* [Comms package repository](https://github.com/decentraland/explorer/tree/df1d30412dcd1a94d933171a39796837aedc87a1/kernel/packages/shared/comms)

### Kernel - Scene Loader System <a href="#fwe-scene-loader" id="fwe-scene-loader"></a>

[Module](https://github.com/decentraland/explorer/tree/df1d30412dcd1a94d933171a39796837aedc87a1/kernel/packages/decentraland-loader) that loads and unloads the scenes/parcels based on user position.

### Kernel - Scene <a href="#fwe-scene" id="fwe-scene"></a>

High level [wrapper](https://github.com/decentraland/explorer/blob/af59463dd3882516874c86bc926726bc557d5184/kernel/packages/unity-interface/UnityScene.ts#L19) around the runtime scene

### Kernel - Avatar Scene <a href="#fwe-avatar-scene" id="fwe-avatar-scene"></a>

It is a regular Decentraland [Scene](https://github.com/decentraland/explorer/blob/af59463dd3882516874c86bc926726bc557d5184/kernel/packages/ui/avatar/avatarSystem.ts), it has the size of the world. And it renders the avatars using the SDK

### Matrix Client <a href="#fwe-matrix" id="fwe-matrix"></a>

The Matrix client can be used to provide interaction between Decentraland's users, providing the ability to send private messages and add people as friends.

**Learn more:**

* [Foundation Matrix Client](https://github.com/decentraland/matrix-client)

### Synapse <a href="#fwe-synapse" id="fwe-synapse"></a>

[Synapse](https://matrix.org/docs/projects/server/synapse) server is an implementation of the [Matrix Protocol](https://matrix.org/), created for secure, decentralized communications. In the Foundation's explorer, it is used to manage private chats between peers and friends.

## Explorer Website <a href="#fwe-website" id="fwe-website"></a>

The React application to load the Kernel and Renderer.

**Learn more:**

* [Foundation website repository](https://github.com/decentraland/explorer-website)

### Kernel - Runtime <a href="#fwe-runtime" id="fwe-runtime"></a>

The [Runtime](https://github.com/decentraland/explorer/blob/df1d30412dcd1a94d933171a39796837aedc87a1/kernel/packages/scene-system/sdk/SceneRuntime.ts) handles SDK bindings and messaging with the Scene in Kernel

**Learn more:**

* [Runtime documentation](../../runtime/overview.md)
* [Runtime repository](https://github.com/decentraland/explorer/blob/df1d30412dcd1a94d933171a39796837aedc87a1/kernel/packages/scene-system/sdk/SceneRuntime.ts)

### Compiler Bundle

#### AMD

This [Module](https://github.com/decentraland/js-sdk-toolchain/tree/c648dcabc0ac1aade3cf143769f7e7f67ffba95b/packages/%40dcl/amd) manages loading of RPC modules to interact with different components of Decentraland

**Repository:** https://github.com/decentraland/js-sdk-toolchain

#### ECS

The public library to interact with Decentraland. Sometimes people refers to the ECS as "The SDK"

#### User Code

The user generated code is part of the bundle of the Scenes

### NATS

NATS is a message broker that enables the data exchange and communication between services. This is also a building block for future developments and will enable an easy way to connect services using subject-based messaging. In the context of the communication services architecture, it is used to communicate the BFF, Archipelago and LiveKit.

**Learn more:** https://nats.io/

### LiveKit

LiveKit is an open source project that provides scalable, multi-user conferencing over WebRTC. Instead of doing a P2P network, peers are connected to a [Selective Forwarding Unit](https://github.com/decentraland/comms3-livekit-transport) (SFU) in charge of managing message relay and different quality aspects of the communication. This will be the added infrastructure in order to provide high-performance/high-quality communications between crowds on designated scenes.

**Learn more:** [https://livekit.io/](https://livekit.io/)

**Repository:** [https://github.com/decentraland/livekit-adapter](https://github.com/decentraland/livekit-adapter)

### Nginx

[Nginx](https://nginx.org/en/docs/) is the reverse proxy used to route traffic to the Catalysts Services.

**Repository:** https://github.com/decentraland/catalyst-owner
