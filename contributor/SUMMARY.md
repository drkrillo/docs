# Table of contents

* [Welcome Contributor](README.md)

## Introduction

* [Welcome](introduction/welcome.md)
* [Architecture](introduction/architecture.md)

## Architecture

* [Overview](architecture/architecture.md)
* [Client Architecture](architecture/client.md)
* [Backend Services](architecture/services.md)
* [Catalyst Network](architecture/catalyst.md)
* [Infrastructure](architecture/infrastructure.md)

## Authentication

* [Authentication Chain](authentication/authchain.md)
* [Signed Fetch](authentication/signed-fetch.md)

## Catalyst

* [Glossary](catalyst/glossary.md)
* [Realm Selection](catalyst/realm-selection.md)

## Communications

* [Overview](communications/overview.md)
* [Archipelago](communications/archipelago.md)
* [MAIN Realm](communications/main-realm.md)
* [Messages](communications/messages.md)
* [Transports](communications/transports.md)
* [LiveKit](transport-types/livekit.md)
* [Websocket](transport-types/websocket.md)
* [Offline](transport-types/offline.md)
* [Simulator](transport-types/simulator.md)
* [Signed Login](transport-types/signed-login.md)

## Content

* [Overview](content/overview.md)
* [Entities](content/entities.md)
* [File System](content/file-system.md)
* [Pointers](content/pointers.md)
* [Collections](content/collections.md)
* [Snapshots](content/snapshots.md)
* [Scenes](entity-types/scenes.md)
* [Profiles](entity-types/profiles.md)
* [Wearables](entity-types/wearables.md)
* [Emotes](entity-types/emotes.md)
* [Stores](entity-types/stores.md)
* [Outfits](entity-types/outfits.md)

## Scenes

* [Overview](runtime/overview.md)
* [CLI Examples](practice/cli-examples.md)
* [Python Examples](practice/python-examples.md)
* [Globals](runtime/globals.md)
* [Execution](runtime/execution.md)
* [Permissions](runtime/permissions.md)
* [Basic Entities](runtime/basic-entities.md)
* [Basic Components](runtime/basic-components.md)
* [Engine API](runtime-modules/engine-api.md)
* [Restricted Actions](runtime-modules/restricted-actions.md)
* [Players](runtime-modules/players.md)
* [User Identity](runtime-modules/user-identity.md)
* [Signed Fetch](runtime-modules/signed-fetch.md)
* [Runtime](runtime-modules/runtime.md)
* [Component Creation](components/component-creation.md)

## Social Service

* [Overview](social-service/overview.md)
* [JavaScript Client](social-service/js-client.md)
* [Login](social-service/login.md)
* [Get Friends](social-service/get-friends.md)
* [Put Friends](social-service/put-friends.md)
* [Websocket](social-service/ws.md)

## Tutorials

* [Getting Started](tutorials/getting-started.md)
* [How to Run a Catalyst Node](tutorials/how-to-run-a-catalyst.md)
* [How to Create a New Entity Type](tutorials/how-to-create-new-entity-type.md)

## Contributor Guides

* [Overview](contributor-guides/contributor-guides.md)
* [API Documentation](contributor-guides/api-documentation.md)
* [Testing Standards](contributor-guides/testing-standards/README.md)
  * [Writing Tests](contributor-guides/testing-standards/writing-tests.md)
  * [Testing UIs](contributor-guides/testing-standards/testing-uis.md)
  * [Testing Services (WKC)](contributor-guides/testing-standards/testing-services-wkc.md)
  * [Testing Services (Deprecated)](contributor-guides/testing-standards/testing-services-deprecated.md)
  * [Testing E2E](contributor-guides/testing-standards/testing-e2e.md)
* [Well-Known Components](contributor-guides/well-known-components/README.md)
  * [Components](contributor-guides/well-known-components/components.md)
  * [Adapters](contributor-guides/well-known-components/adapters.md)
  * [Logic Components](contributor-guides/well-known-components/logic-components.md)
  * [Controllers](contributor-guides/well-known-components/controllers.md)
* [UI Standards](contributor-guides/ui-standards/README.md)
  * [Store Setup](contributor-guides/ui-standards/store-setup.md)
  * [RTK Query](contributor-guides/ui-standards/rtk-query.md)
  * [State Management](contributor-guides/ui-standards/state-management.md)
  * [Component Patterns](contributor-guides/ui-standards/component-patterns.md)
  * [Web3 Integration](contributor-guides/ui-standards/web3-integration.md)
  * [Testing & Performance](contributor-guides/ui-standards/testing-and-performance.md)
* [Web UI Standards](contributor-guides/web-ui-standards/README.md)
  * [Process Overview](contributor-guides/web-ui-standards/process-overview.md)
  * [Custom Components](contributor-guides/web-ui-standards/custom-components.md)
  * [Styling & Theming](contributor-guides/web-ui-standards/styling-and-theming.md)
  * [Migration Guide](contributor-guides/web-ui-standards/migration.md)

## APIs

* [Realm Provider](apis/realm-provider/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: realm-provider-api
    ```
* [Comms Gatekeeper](apis/comms-gatekeeper/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: comms-gatekeeper-api
    ```
* [Archipelago Workers](apis/archipelago-workers/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: archipelago-workers-api
    ```
* [Social Service](apis/social-service/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: social-service-api
    ```
* [Catalyst](apis/catalyst/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: catalyst
    ```
* [Lamb2](apis/lamb2/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: lambdas
    ```
* [Worlds](apis/worlds/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: worlds-content-server
    ```
* [Asset Bundle Registry](apis/asset-bundle-registry/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: asset-bundle-registry-api
    ```
* [Events Notifier](apis/events-notifier/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: events-notifier-api
    ```
* [Badges](apis/badges/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: badges-api
    ```
* [Credits Server](apis/credits-server/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: credits-server-api
    ```
* [Notifications Workers](apis/notifications-workers/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: notifications-inbox-api
    ```
* [Auth Server](apis/auth-server/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: auth-server-api
    ```
* [Marketplace Server](apis/marketplace-server/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: marketplace-server-api
    ```
* [Events](apis/events/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: events
    ```
* [Places](apis/places/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: places
    ```
* [Rewards](apis/rewards/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: referral-api
    ```
* [Camera reel](apis/camera-reel/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: camera-reel-service-api
    ```
* [Atlas Server](apis/atlas-server/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: atlas-service
    ```
* [Exploration Games](apis/exploration-games/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: exploration-games-api
    ```
