# Pruebas End-to-End (E2E)

{% hint style="info" %}
Esta sección actualmente es un Trabajo en Progreso (WIP).
{% endhint %}

Esta sección describe cómo los desarrolladores probarán el código relacionado con las pruebas e2e.

## Stack de Pruebas

Todas nuestras pruebas de IU DEBEN realizarse usando [Cypress](https://www.cypress.io/) como framework principal de pruebas con [Synpress](https://github.com/Synthetixio/synpress) para soporte de wallet. El código de prueba DEBE escribirse usando [Typescript](https://www.typescriptlang.org/) y ejecutarse usando [ts-jest](https://github.com/kulshekhar/ts-jest) para tener soporte de verificación de tipos.

## Estructura de Directorios

Las nuevas pruebas DEBEN colocarse en un directorio `tests/e2e`, en la ruta raíz. Los archivos se nombrarán según el flujo que estarán probando, es decir, si el archivo de pruebas e2e estará probando el flujo de `Publicación de Colección`, el nombre del archivo DEBE ser `collection-publication.spec.cy.ts`.

## Con Qué Frecuencia Se Ejecutarán

Programadas vía GitHub Actions. Frecuencia por definir.

## Qué Probar

* Las interacciones de usuario más relevantes en las dApps (Publicaciones de Items, Compras de NFTs, etc.)

## Cómo Probar

Todas nuestras pruebas DEBEN seguir el estilo semi-estructurado de **Given-When-Then** (GWT).

El siguiente ejemplo:

1. **Given** un usuario visita `https://example.cypress.io`
2. **When** hace clic en el enlace etiquetado como `type`
3. Y escribe "fake@email.com" en el input `[data-testid="action-email"]`
4. **Then** la URL debe incluir `/commands/actions`
5. Y el input `[data-testid="action-email"]` tiene "fake@email.com" como su valor

Debe escribirse como:

```jsx
describe('Given a user visits https://example.cypress.io', () => {
  beforeEach(() => {
    cy.visit('https://example.cypress.io')
  })

  describe('when clicks the link labeled type', () => {
    beforeEach(() => {
      cy.contains('type').click()
    })

    describe('and type an email into the action-email input', () => {
      beforeEach(() => {
        // Get an input, type into it
        cy.get('.action-email').type('fake@email.com')
      })

      it('Then the url should include /commands/actions', () => {
        // Should be on a new URL which
        // includes '/commands/actions'
        cy.url().should('include', '/commands/actions')
      })

      it('and the action-email input value should contain the typed email', () => {
        //  Verify that the value has been updated
        cy.get('.action-email').should('have.value', 'fake@email.com')
      })
    })
  })
})
```

## Probar Contratos

Para probar smart contracts, referirse a la documentación de Estándares Solidity.
