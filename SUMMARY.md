# Table of contents

## Getting Started

* [Overview](README.md)

## API Reference

* [Social Service API](api-reference/social-service-api/README.md)
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
* [Comms Gatekeeper API](api-reference/comms-gatekeeper-api/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: false
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: comms-gatekeeper-api
    ```
* ```yaml
  type: builtin:openapi
  props:
    models: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: worlds-content-server
  ```

***

* [Realm Provider API](realm-provider-api/README.md)
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
