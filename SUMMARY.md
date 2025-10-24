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

## Group 1

* [Decentraland APIs](group-1/decentraland-apis/README.md)
  * [Realm Provider](group-1/decentraland-apis/realm-provider/README.md)
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
  * ```yaml
    type: builtin:openapi
    props:
      models: true
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: worlds-content-server
    ```
