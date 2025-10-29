# Table of contents

* [Welcome to Decentraland](README.md)

## Developer Standards

* [Overview](developer-standards/README.md)
* [Testing Standards](developer-standards/testing-standards/README.md)
  * [Writing Tests](developer-standards/testing-standards/writing-tests.md)
  * [Testing UIs](developer-standards/testing-standards/testing-uis.md)
  * [Testing Services (WKC)](developer-standards/testing-standards/testing-services-wkc.md)
  * [Testing Services (Deprecated)](developer-standards/testing-standards/testing-services-deprecated.md)
  * [Testing E2E](developer-standards/testing-standards/testing-e2e.md)

## APIs

* [Realm Provider](apis/realm-provider/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: true
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: realm-provider-api
    ```
* [Comms Gatekeeper](apis/comms-gatekeeper/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: true
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: comms-gatekeeper-api
    ```
* [Archipelago Workers](apis/archipelago-workers/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: false
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: archipelago-workers-api
    ```
* [Social Service](apis/social-service/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: false
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
          spec: auth-server-api
    ```
* [Badges](apis/badges/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: false
      downloadLink: false
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: auth-server-api
    ```
* [Notifications Workers](apis/notifications-workers/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: false
      downloadLink: false
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: auth-server-api
    ```
* [Auth Server](apis/auth-server/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: false
      downloadLink: false
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: auth-server-api
    ```
