# Table of contents

* [Welcome to Decentraland](README.md)

## Architecture

* [Overview](architecture/README.md)
* [Client Architecture](architecture/client.md)
* [Backend Services](architecture/services.md)
* [Catalyst Network](architecture/catalyst.md)
* [Infrastructure](architecture/infrastructure.md)

## Development Standards

* [Overview](development-standards/README.md)
* [API Documentation](development-standards/api-documentation.md)
* [Testing Standards](development-standards/testing-standards/README.md)
  * [Writing Tests](development-standards/testing-standards/writing-tests.md)
  * [Testing UIs](development-standards/testing-standards/testing-uis.md)
  * [Testing Services (WKC)](development-standards/testing-standards/testing-services-wkc.md)
  * [Testing Services (Deprecated)](development-standards/testing-standards/testing-services-deprecated.md)
  * [Testing E2E](development-standards/testing-standards/testing-e2e.md)
* [Well-Known Components](development-standards/well-known-components/README.md)
  * [Components](development-standards/well-known-components/components.md)
  * [Adapters](development-standards/well-known-components/adapters.md)
  * [Logic Components](development-standards/well-known-components/logic-components.md)
  * [Controllers](development-standards/well-known-components/controllers.md)
* [UI Standards](development-standards/ui-standards/README.md)
  * [Store Setup](development-standards/ui-standards/store-setup.md)
  * [RTK Query](development-standards/ui-standards/rtk-query.md)
  * [State Management](development-standards/ui-standards/state-management.md)
  * [Component Patterns](development-standards/ui-standards/component-patterns.md)
  * [Web3 Integration](development-standards/ui-standards/web3-integration.md)
  * [Testing & Performance](development-standards/ui-standards/testing-and-performance.md)
* [Web UI Standards](development-standards/web-ui-standards/README.md)
  * [Process Overview](development-standards/web-ui-standards/process-overview.md)
  * [Custom Components](development-standards/web-ui-standards/custom-components.md)
  * [Styling & Theming](development-standards/web-ui-standards/styling-and-theming.md)
  * [Migration Guide](development-standards/web-ui-standards/migration.md)

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
* [Lamb2](apis/lamb2/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: false
      downloadLink: false
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
      downloadLink: false
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
      downloadLink: false
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
      downloadLink: false
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
      downloadLink: false
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
      downloadLink: false
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
      downloadLink: false
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
