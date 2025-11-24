# Guía de Migración UI1 a UI2

Esta guía cubre el proceso de migrar componentes desde UI1 (`decentraland-ui`) a UI2 (`decentraland-ui2`).

## Cuándo Migrar

Las migraciones pueden comenzar por tres razones válidas:

### 1. Mejora Técnica

Quieres migrar un componente para:
* Mejor soporte de theming
* Tipos TypeScript mejorados
* Consistencia con otros componentes UI2
* Optimización de performance
* Mejoras de accesibilidad

### 2. Actualización de un Componente

* Un componente UI1 necesita ser actualizado
* Todavía no hay un equivalente UI2
* **Requisito**: Créalo en UI2 primero, luego úsalo

### 3. Necesidades del Proyecto

* El componente se usará en un proyecto nuevo o existente
* Hay tiempo disponible para migrarlo apropiadamente
* Los recursos del proyecto permiten una migración completa

{% hint style="warning" %}
**No** migres componentes "solo porque sí." Cada migración debe tener una justificación clara de negocio o técnica.
{% endhint %}

---

## Proceso de Migración

### Paso 1: Planificación

Antes de comenzar la migración:

1. **Identifica dependencias**
   * ¿Qué otros componentes usa?
   * ¿Qué proyectos lo usan actualmente?
   * ¿Hay cambios breaking planeados?

2. **Revisa el uso actual**
   * ¿Cuántos proyectos usan este componente?
   * ¿Qué props se usan más comúnmente?
   * ¿Hay problemas conocidos?

3. **Define alcance**
   * ¿Será una migración 1:1?
   * ¿Hay mejoras planeadas?
   * ¿Cuál es el timeline?

### Paso 2: Crear Componente UI2

Sigue la guía de [Custom Components](custom-components.md) para componentes candidatos UI2.

**Requisitos:**

* Usa sintaxis de objetos para styled-components
* Usa solo valores del theme (sin valores arbitrarios)
* Agrega stories comprehensivos de Storybook
* Escribe tests completos
* Documenta todos los props y comportamientos

**Estructura de ejemplo:**

```
ui2/src/components/Button/
├── Button.tsx
├── Button.styles.ts
├── Button.stories.tsx
├── Button.test.tsx
├── types.ts
├── index.ts
└── README.md
```

### Paso 3: Mantener Compatibilidad

El componente UI2 **DEBE** exponer los mismos props y comportamientos que la versión UI1.

#### Mismos Props

```tsx
// Button UI1
interface ButtonProps {
  primary?: boolean;
  size?: 'small' | 'medium' | 'large';
  onClick?: () => void;
  disabled?: boolean;
  children: React.ReactNode;
}

// Button UI2 - DEBE soportar mismos props
interface ButtonProps {
  primary?: boolean;
  size?: 'small' | 'medium' | 'large';
  onClick?: () => void;
  disabled?: boolean;
  children: React.ReactNode;
  // Puede agregar nuevos props opcionales
  variant?: 'text' | 'outlined' | 'contained';
}
```

#### Cambios Retrocompatibles

Si necesitas cambiar o agregar props:

1. **Primero**, agrega los nuevos props a UI1 como opcionales
2. **Luego**, migra los consumidores de UI1 para usar los nuevos props
3. **Finalmente**, crea el componente UI2 con la nueva API

```tsx
// Paso 1: Agregar prop opcional a UI1
interface ButtonProps {
  primary?: boolean;
  // Nuevo prop opcional
  variant?: 'primary' | 'secondary';
}

// Paso 2: Actualizar implementación UI1
export function Button({ primary, variant = primary ? 'primary' : 'secondary' }: ButtonProps) {
  // Usa variant en lugar de primary internamente
}

// Paso 3: Crear UI2 con nueva API
interface ButtonProps {
  // variant es ahora el prop principal
  variant?: 'primary' | 'secondary';
  // Mantener primary por compatibilidad, marcar como deprecated
  /** @deprecated Usa variant en su lugar */
  primary?: boolean;
}
```

### Paso 4: Deprecar Componente UI1

Agrega un aviso de deprecación al componente UI1:

```tsx
/**
 * @deprecated Este componente ha sido migrado a UI2.
 * Importa desde 'decentraland-ui2' en su lugar:
 * 
 * ```tsx
 * import { Button } from 'decentraland-ui2';
 * ```
 * 
 * Ve guía de migración: https://docs.decentraland.org/contributor-guides/web-ui-standards/migration
 */
export function Button(props: ButtonProps) {
  // ... implementación existente
}
```

### Paso 5: Adopción Gradual

No fuerces la migración inmediata. Permite adopción gradual:

1. **Publica** componente UI2
2. **Documenta** el camino de migración
3. **Actualiza** nuevos proyectos para usar UI2
4. **Migra** proyectos existentes oportunísticamente
5. **Planea** eventual remoción de UI1 (con aviso)

---

## Ejemplos de Migración

### Ejemplo 1: Componente Simple

Migrando un componente básico `Card`:

#### Versión UI1

```tsx
// decentraland-ui/src/components/Card/Card.tsx
import React from 'react';
import './Card.css';

export interface CardProps {
  className?: string;
  children: React.ReactNode;
}

export function Card({ className, children }: CardProps) {
  return (
    <div className={`dcl-card ${className || ''}`}>
      {children}
    </div>
  );
}
```

#### Versión UI2

```tsx
// decentraland-ui2/src/components/Card/Card.tsx
import { styled } from '@mui/material/styles';

export interface CardProps {
  className?: string;
  children: React.ReactNode;
}

const StyledCard = styled('div')(({ theme }) => ({
  backgroundColor: theme.palette.background.paper,
  borderRadius: theme.shape.borderRadius,
  padding: theme.spacing(2),
  boxShadow: theme.shadows[1],
  
  [theme.breakpoints.down('sm')]: {
    padding: theme.spacing(1),
  },
}));

export function Card({ className, children }: CardProps) {
  return (
    <StyledCard className={className}>
      {children}
    </StyledCard>
  );
}
```

### Ejemplo 2: Componente con Variantes

Migrando un `Button` con variantes:

#### Versión UI1

```tsx
// UI1
import './Button.css';

interface ButtonProps {
  primary?: boolean;
  secondary?: boolean;
  size?: 'small' | 'medium' | 'large';
}

export function Button({ primary, secondary, size = 'medium', ...props }: ButtonProps) {
  const classes = [
    'dcl-button',
    primary && 'primary',
    secondary && 'secondary',
    `size-${size}`,
  ].filter(Boolean).join(' ');
  
  return <button className={classes} {...props} />;
}
```

#### Versión UI2

```tsx
// UI2
import { styled } from '@mui/material/styles';

interface ButtonProps {
  /** @deprecated Usa variant="contained" en su lugar */
  primary?: boolean;
  /** @deprecated Usa variant="outlined" en su lugar */
  secondary?: boolean;
  variant?: 'text' | 'outlined' | 'contained';
  size?: 'small' | 'medium' | 'large';
}

const StyledButton = styled('button')<ButtonProps>(({ theme, variant = 'contained', size = 'medium' }) => {
  const sizes = {
    small: theme.spacing(0.5, 1),
    medium: theme.spacing(1, 2),
    large: theme.spacing(1.5, 3),
  };
  
  const variants = {
    text: {
      backgroundColor: 'transparent',
      color: theme.palette.primary.main,
    },
    outlined: {
      backgroundColor: 'transparent',
      color: theme.palette.primary.main,
      border: `1px solid ${theme.palette.primary.main}`,
    },
    contained: {
      backgroundColor: theme.palette.primary.main,
      color: theme.palette.primary.contrastText,
    },
  };
  
  return {
    padding: sizes[size],
    borderRadius: theme.shape.borderRadius,
    border: 'none',
    cursor: 'pointer',
    ...variants[variant],
    
    '&:hover': {
      opacity: 0.9,
    },
    
    '&:disabled': {
      opacity: 0.5,
      cursor: 'not-allowed',
    },
  };
});

export function Button({ 
  primary, 
  secondary, 
  variant, 
  ...props 
}: ButtonProps) {
  // Maneja props deprecated
  const actualVariant = variant || 
    (primary ? 'contained' : secondary ? 'outlined' : 'text');
  
  return <StyledButton variant={actualVariant} {...props} />;
}
```

---

## Checklist de Migración

Usa este checklist para cada migración de componente:

### Fase de Planificación

- [ ]  Identifica todos los proyectos usando el componente
- [ ]  Documenta props y comportamientos actuales
- [ ]  Define alcance y timeline de migración
- [ ]  Obtén aprobación de stakeholders

### Fase de Implementación

- [ ]  Crea componente UI2 siguiendo estándares
- [ ]  Mantén compatibilidad de props
- [ ]  Usa sintaxis de objetos para styling
- [ ]  Usa solo valores del theme
- [ ]  Implementa todos los estados (idle, hover, focus, disabled, error)
- [ ]  Agrega stories comprehensivos de Storybook
- [ ]  Escribe tests unitarios
- [ ]  Documenta funcionalidades de accesibilidad

### Fase de Deprecación

- [ ]  Agrega aviso de deprecación al componente UI1
- [ ]  Actualiza documentación de UI1
- [ ]  Crea guía de migración para consumidores
- [ ]  Publica componente UI2

### Fase de Adopción

- [ ]  Actualiza nuevos proyectos para usar UI2
- [ ]  Crea PRs de migración para proyectos existentes
- [ ]  Monitorea problemas
- [ ]  Recolecta feedback
- [ ]  Planea timeline de remoción de UI1

---

## Patrones Comunes de Migración

### CSS a Styled Components

```tsx
// UI1: Archivo CSS
.dcl-card {
  background: #fff;
  padding: 16px;
  border-radius: 8px;
}

// UI2: Styled component
const Card = styled('div')(({ theme }) => ({
  backgroundColor: theme.palette.background.paper,
  padding: theme.spacing(2),
  borderRadius: theme.shape.borderRadius,
}));
```

### Class Names a Props

```tsx
// UI1: Variantes basadas en clases
<Button className={primary ? 'primary' : 'secondary'} />

// UI2: Variantes basadas en props
<Button variant={primary ? 'contained' : 'outlined'} />
```

### Valores Fijos a Theme

```tsx
// UI1: Valores fijos
const styles = {
  color: '#333',
  fontSize: '14px',
  padding: '8px 16px',
};

// UI2: Valores del theme
const Component = styled('div')(({ theme }) => ({
  color: theme.palette.text.primary,
  fontSize: theme.typography.body2.fontSize,
  padding: theme.spacing(1, 2),
}));
```

---

## Cambios Breaking

A veces los cambios breaking son necesarios. Manéjalos cuidadosamente:

### Cuándo los Cambios Breaking Son Aceptables

* Correcciones de seguridad
* Bugs críticos
* Actualizaciones de versión mayor
* Remoción de funcionalidades deprecated (con aviso)

### Cómo Manejar Cambios Breaking

1. **Anuncia temprano** - Comunica cambios con anticipación
2. **Proporciona camino de migración** - Documenta cómo actualizar
3. **Version bump** - Sigue semantic versioning
4. **Período de deprecación** - Da tiempo para migrar
5. **Codemods** - Proporciona herramientas de migración automatizada si es posible

### Ejemplo: Remover Props Deprecated

```tsx
// Versión 1.0: Introduce nueva API, depreca antigua
interface ButtonProps {
  /** @deprecated Usa variant="contained" en su lugar */
  primary?: boolean;
  variant?: 'text' | 'outlined' | 'contained';
}

// Versión 1.5: Advierte sobre remoción
interface ButtonProps {
  /** @deprecated Se removerá en 2.0. Usa variant en su lugar */
  primary?: boolean;
  variant?: 'text' | 'outlined' | 'contained';
}

// Versión 2.0: Remueve prop deprecated
interface ButtonProps {
  variant?: 'text' | 'outlined' | 'contained';
}
```

---

## Testing de Migración

Asegura que los componentes migrados funcionen correctamente:

### Testing de Regresión Visual

Compara componentes UI1 y UI2 visualmente:

```tsx
// Story de Storybook para comparación
export const ComparisonStory: Story = {
  render: () => (
    <div style={{ display: 'flex', gap: '2rem' }}>
      <div>
        <h3>UI1</h3>
        <UI1Button primary>Click Me</UI1Button>
      </div>
      <div>
        <h3>UI2</h3>
        <UI2Button variant="contained">Click Me</UI2Button>
      </div>
    </div>
  ),
};
```

### Testing de Comportamiento

Asegura que los props funcionen de la misma manera:

```tsx
describe('Button migration', () => {
  it('should handle primary prop (deprecated) the same as variant="contained"', () => {
    const { container: ui1 } = render(<UI1Button primary>Test</UI1Button>);
    const { container: ui2 } = render(<UI2Button primary>Test</UI2Button>);
    
    // Compara output renderizado
    expect(ui1.textContent).toBe(ui2.textContent);
  });
});
```

---

## Actualizaciones de Documentación

Después de la migración, actualiza la documentación:

### Actualizar Documentos del Componente UI1

```markdown
# Button (UI1 - Deprecated)

> ⚠️ **Este componente ha sido migrado a UI2.**
> Ve la [documentación del Button UI2](../ui2/button) para la nueva versión.

Este componente está deprecated y será removido en una versión futura.
Por favor migra a UI2.

## Guía de Migración

Ve [Guía de Migración](./migration) para detalles.
```

### Crear Documentos del Componente UI2

```markdown
# Button (UI2)

Componente de botón moderno con soporte completo de theme.

## Migración desde UI1

Si estás migrando desde UI1:

- Prop `primary` → `variant="contained"`
- Prop `secondary` → `variant="outlined"`
- Clases CSS → styled-components

Ve la [Guía de Migración](./migration) completa para detalles.
```

---

## Próximos Pasos

* Revisa [Custom Components](custom-components.md) para crear componentes UI2
* Ve [Styling & Theming](styling-and-theming.md) para estándares de styling
* Verifica [Process Overview](process-overview.md) para el flujo de trabajo completo
