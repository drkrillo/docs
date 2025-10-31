# Table of contents

* [Welcome to Decentraland](README.md)

## Architecture

* [Overview](architecture/README.md)
* [Client Architecture](architecture/client.md)
* [Backend Services](architecture/services.md)
* [Catalyst Network](architecture/catalyst.md)
* [Infrastructure](architecture/infrastructure.md)

## Developer Standards

* [Overview](developer-standards/README.md)
* [API Documentation](developer-standards/api-documentation.md)
* [Testing Standards](developer-standards/testing-standards/README.md)
  * [Writing Tests](developer-standards/testing-standards/writing-tests.md)
  * [Testing UIs](developer-standards/testing-standards/testing-uis.md)
  * [Testing Services (WKC)](developer-standards/testing-standards/testing-services-wkc.md)
  * [Testing Services (Deprecated)](developer-standards/testing-standards/testing-services-deprecated.md)
  * [Testing E2E](developer-standards/testing-standards/testing-e2e.md)
* [Well-Known Components](developer-standards/well-known-components/README.md)
  * [Components](developer-standards/well-known-components/components.md)
  * [Adapters](developer-standards/well-known-components/adapters.md)
  * [Logic Components](developer-standards/well-known-components/logic-components.md)
  * [Controllers](developer-standards/well-known-components/controllers.md)
* [UI Standards](developer-standards/ui-standards/README.md)
  * [Store Setup](developer-standards/ui-standards/store-setup.md)
  * [RTK Query](developer-standards/ui-standards/rtk-query.md)
  * [State Management](developer-standards/ui-standards/state-management.md)
  * [Component Patterns](developer-standards/ui-standards/component-patterns.md)
  * [Web3 Integration](developer-standards/ui-standards/web3-integration.md)
  * [Testing & Performance](developer-standards/ui-standards/testing-and-performance.md)
* [Web UI Standards](developer-standards/web-ui-standards/README.md)
  * [Process Overview](developer-standards/web-ui-standards/process-overview.md)
  * [Custom Components](developer-standards/web-ui-standards/custom-components.md)
  * [Styling & Theming](developer-standards/web-ui-standards/styling-and-theming.md)
  * [Migration Guide](developer-standards/web-ui-standards/migration.md)

## APIs

* [Realm Provider](apis/realm-provider/README.md)
  * ```yaml
    props:
      models: true
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
      models: true
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
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: social-service-api
    ```
* [Asset Bundle Registry](apis/asset-bundle-registry/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: false
      downloadLink: false
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: asset-bundle-registry-api
    ```
* [Badges](apis/badges/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: auth-server-api
    ```
* [Notifications Workers](apis/notifications-workers/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: auth-server-api
    ```
* [Auth Server](apis/auth-server/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
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
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: marketplace-server-api
    ```
* [Credits Server](apis/credits-server/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: credits-server-api
    ```
* [Worlds](apis/worlds/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: worlds-content-server
    ```
* [Places](apis/places/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: places
    ```
* [Camera reel](apis/camera-reel/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: camera-reel-service-api
    ```
* [Catalyst](apis/catalyst/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: catalyst
    ```
* [Atlas Server](apis/atlas-server/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: atlas-service
    ```
* [Events Notifier](apis/events-notifier/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: events-notifier-api
    ```
* [Events](apis/events/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: events
    ```
* [Exploration Games](apis/exploration-games/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: exploration-games-api
    ```
* [Rewards](apis/rewards/README.md)
  * ```yaml
    props:
      models: false
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: referral-api
    ```
