---
description: Frontend development standards, best practices, and conventions for the LTI React application including component patterns, state management, UI/UX guidelines, and testing practices
globs: ["frontend/src/**/*.{js,jsx,ts,tsx}", "frontend/cypress/**/*.{ts,js}", "frontend/tsconfig.json", "frontend/cypress.config.ts", "frontend/package.json"]
alwaysApply: true
---

# Frontend Project Configuration and Best Practices

## Table of Contents

- [Overview](#overview)
- [Technology Stack](#technology-stack)
  - [Core Technologies](#core-technologies)
  - [UI Framework](#ui-framework)
  - [State Management & Data Flow](#state-management--data-flow)
  - [Testing Framework](#testing-framework)
  - [Development Tools](#development-tools)
- [Project Structure](#project-structure)
- [Coding Standards](#coding-standards)
  - [Language and Naming Conventions](#language-and-naming-conventions)
  - [Component Conventions](#component-conventions)
  - [State Management](#state-management)
  - [Service Layer Architecture](#service-layer-architecture)
- [UI/UX Standards](#uiux-standards)
  - [Bootstrap Integration](#bootstrap-integration)
  - [Form Handling](#form-handling)
  - [Navigation Patterns](#navigation-patterns)
  - [Accessibility](#accessibility)
- [Testing Standards](#testing-standards)
  - [End-to-End Testing with Cypress](#end-to-end-testing-with-cypress)
  - [Test Organization](#test-organization)
- [Configuration Standards](#configuration-standards)
  - [TypeScript Configuration](#typescript-configuration)
  - [ESLint Configuration](#eslint-configuration)
  - [Environment Configuration](#environment-configuration)
- [Performance Best Practices](#performance-best-practices)
  - [Component Optimization](#component-optimization)
  - [Bundle Optimization](#bundle-optimization)
  - [API Efficiency](#api-efficiency)
- [Development Workflow](#development-workflow)
  - [Git Workflow](#git-workflow)
  - [Development Scripts](#development-scripts)
  - [Code Quality](#code-quality)
- [Migration Strategy](#migration-strategy)
  - [TypeScript Migration](#typescript-migration)
  - [Component Modernization](#component-modernization)

---

## Vista General

Este documento describe las mejores prácticas, convenciones y estándares utilizados en la aplicación frontend de LTI. Estas prácticas aseguran la consistencia del código, la mantenibilidad y una experiencia de desarrollo óptima.

## Stack Tecnológico

### Tecnologías Core
- **React 18.3.1**: React moderno con componentes funcionales y hooks
- **TypeScript 4.9.5**: Para seguridad de tipos y mejor experiencia de desarrollo
- **Create React App 5.0.1**: Herramientas de construcción y servidor de desarrollo
- **React Router DOM 6.23.1**: Enrutamiento y navegación en el lado del cliente

### Framework de UI
- **Bootstrap 5.3.3**: Framework de CSS para diseño responsivo
- **React Bootstrap 2.10.2**: Componentes de Bootstrap para React
- **React Bootstrap Icons 1.11.4**: Librería de iconos
- **React DatePicker 6.9.0**: Componentes de entrada de fecha

### Gestión de Estado y Flujo de Datos
- **React Hooks**: useState, useEffect para gestión de estado local
- **React Beautiful DND 13.1.1**: Funcionalidad de arrastrar y soltar
- **Axios**: Cliente HTTP para comunicación con la API

### Framework de Pruebas
- **Cypress 14.4.1**: Pruebas end-to-end
- **Jest**: Pruebas unitarias (vía Create React App)
- **React Testing Library**: Utilidades de prueba de componentes

### Herramientas de Desarrollo
- **ESLint**: Linting de código con reglas específicas de React
- **TypeScript**: Verificación estática de tipos
- **Web Vitals**: Monitoreo de rendimiento


## Estructura del Proyecto

```
frontend/
├── public/                 # Archivos estáticos
├── src/
│   ├── components/        # Componentes de UI reutilizables
│   ├── services/         # Capa de servicios de API
│   ├── pages/           # Componentes de página (organización futura)
│   ├── assets/          # Imágenes, fuentes, recursos estáticos
│   ├── App.js           # Componente principal de la aplicación
│   ├── index.tsx        # Punto de entrada de la aplicación
│   └── index.css        # Estilos globales
├── cypress/
│   └── e2e/            # Archivos de pruebas end-to-end
├── package.json         # Dependencias y scripts
├── tsconfig.json       # Configuración de TypeScript
└── cypress.config.ts   # Configuración de Cypress
```

## Estándares de Codificación

### Convenciones de Nombres

- **Nombres de Componentes**: Usa PascalCase para los componentes de React (ej. `CandidateCard`, `PositionDetails`, `RecruiterDashboard`).
- **Nombres de Variables**: Usa camelCase para variables y funciones (ej. `candidateId`, `handleSubmit`, `fetchPositions`).
- **Nombres de Constantes**: Usa UPPER_SNAKE_CASE para constantes (ej. `MAX_CANDIDATES_PER_PAGE`, `API_BASE_URL`).
- **Nombres de Tipos e Interfaces**: Usa PascalCase para tipos e interfaces (ej. `CandidateData`, `PositionProps`, `ICandidateService`).
- **Nombres de Archivos**: Usa PascalCase para archivos de componentes (ej. `CandidateCard.tsx`, `PositionDetails.tsx`) y camelCase para archivos de utilidades (ej. `candidateService.js`, `apiUtils.ts`).
- **Nombres de Clases CSS**: Usa kebab-case para clases CSS (ej. `candidate-card`, `position-details`).
- **Nombres de Hooks**: Usa camelCase comenzando con el prefijo "use" (ej. `useCandidate`, `usePositionData`, `useFormValidation`).

**Ejemplos:**

```typescript
// Bueno: Código en inglés con comentarios en español
import React, { useState, useEffect } from 'react';

type CandidateCardProps = {
    candidate: Candidate;
    index: number;
    onClick: (candidate: Candidate) => void;
};

const CandidateCard: React.FC<CandidateCardProps> = ({ candidate, index, onClick }) => {
    const [isLoading, setIsLoading] = useState(false);
    
    // Manejar el evento de clic en la tarjeta de candidato
    const handleCardClick = () => {
        onClick(candidate);
    };
    
    return (
        <div className="candidate-card" onClick={handleCardClick}>
            {/* JSX del componente */}
        </div>
    );
};

// Evitar: Nombres o comentarios que no estén en inglés
const TarjetaCandidato: React.FC<PropsTarjetaCandidato> = ({ candidato, indice, alHacerClic }) => {
    const [estaCargando, setEstaCargando] = useState(false);
    
    // Manejar evento de clic en la tarjeta de candidato
    const manejarClicTarjeta = () => {
        alHacerClic(candidato);
    };
    
    return (
        <div className="tarjeta-candidato" onClick={manejarClicTarjeta}>
            {/* JSX del componente */}
        </div>
    );
};
```

**Mensajes de Error y Console Logs:**

```typescript
// Bueno: Mensajes de error en inglés
catch (error) {
    console.error('Failed to fetch candidates:', error);
    setError('Unable to load candidates. Please try again later.');
}

// Evitar: Mensajes que no estén en inglés en el código
catch (error) {
    console.error('Error al obtener candidatos:', error);
    setError('No se pudieron cargar los candidatos. Por favor, inténtelo de nuevo más tarde.');
}
```

**Ejemplos de la Capa de Servicios:**

```typescript
// Bueno: Nombres en inglés en los servicios
export const candidateService = {
    getAllCandidates: async () => {
        try {
            const response = await axios.get(`${API_BASE_URL}/candidates`);
            return response.data;
        } catch (error) {
            console.error('Error fetching candidates:', error);
            throw error;
        }
    }
};

// Evitar: Nombres que no estén en inglés
export const servicioCandidatos = {
    obtenerTodosLosCandidatos: async () => {
        try {
            const respuesta = await axios.get(`${API_BASE_URL}/candidates`);
            return respuesta.data;
        } catch (error) {
            console.error('Error al obtener candidatos:', error);
            throw error;
        }
    }
};
```

### Convenciones de Componentes

#### Componentes Funcionales
- **Usa siempre componentes funcionales** con hooks en lugar de componentes de clase.
- Usa **TypeScript para nuevos componentes** cuando sea posible.
- Mantén **JavaScript para componentes legados** hasta la migración.

```typescript
// Preferido - Componente funcional en TypeScript
import React, { useState, useEffect } from 'react';

type Position = {
    id: number;
    title: string;
    status: 'Open' | 'Contratado' | 'Cerrado' | 'Borrador';
};

const Positions: React.FC = () => {
    const [positions, setPositions] = useState<Position[]>([]);
    // Lógica del componente
};
```

#### Props de Componentes
- **Define interfaces de TypeScript** para las props de los componentes cuando uses TypeScript.
- Usa **desestructuración** para las props.
- Incluye **valores por defecto** donde sea apropiado.

```typescript
type CandidateCardProps = {
    candidate: Candidate;
    index: number;
    onClick: (candidate: Candidate) => void;
};

const CandidateCard: React.FC<CandidateCardProps> = ({ candidate, index, onClick }) => {
    // Implementación del componente
};
```

### Gestión de Estado (State Management)

#### Estado Local con Hooks
- Usa **useState** para el estado a nivel de componente.
- Usa **useEffect** para efectos secundarios y obtención de datos.
- **Extrae hooks personalizados** para lógica de estado reutilizable.

```javascript
const [formData, setFormData] = useState({
    title: '',
    description: '',
    status: 'Borrador'
});

const handleInputChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({
        ...prev,
        [name]: value
    }));
};
```

#### Estados de Carga y Error
- **Maneja siempre los estados de carga** para operaciones asíncronas.
- **Implementa el manejo de errores** con mensajes amigables para el usuario.
- **Usa componentes Alert de React Bootstrap** para la retroalimentación.

```javascript
const [loading, setLoading] = useState(true);
const [error, setError] = useState('');
const [success, setSuccess] = useState('');

// En función asíncrona
try {
    setLoading(true);
    const data = await apiCall();
    setSuccess('Operation completed successfully');
} catch (error) {
    setError('Error message: ' + error.message);
} finally {
    setLoading(false);
}
```

### Arquitectura de la Capa de Servicios

#### Servicios de API
- **Centraliza las llamadas a la API** en archivos de servicio.
- Usa **axios** para peticiones HTTP.
- **Exporta objetos de servicio** con métodos agrupados.
- **Maneja los errores a nivel de servicio** cuando sea apropiado.

```javascript
import axios from 'axios';

const API_BASE_URL = 'http://localhost:3010';

export const positionService = {
    getAllPositions: async () => {
        try {
            const response = await axios.get(`${API_BASE_URL}/positions`);
            return response.data;
        } catch (error) {
            console.error('Error fetching positions:', error);
            throw error;
        }
    },
    
    updatePosition: async (id, positionData) => {
        try {
            const response = await axios.put(`${API_BASE_URL}/positions/${id}`, positionData);
            return response.data;
        } catch (error) {
            console.error('Error updating position:', error);
            throw error;
        }
    }
};
```

## Estándares de UI/UX

### Integración de Bootstrap
- Usa **componentes de React Bootstrap** en lugar de Bootstrap plano.
- **Importa el CSS de Bootstrap** en el componente principal App.
- Sigue el **sistema de rejilla responsivo de Bootstrap** (Container, Row, Col).

```javascript
import { Container, Row, Col, Card, Button, Form, Alert } from 'react-bootstrap';
```

### Manejo de Formularios
- Usa **componentes controlados** para las entradas de formulario.
- Implementa **validación en tiempo real** donde sea apropiado.
- **Deshabilita los botones de envío** durante el envío del formulario.
- **Limpia el estado del formulario** después de un envío exitoso.

```javascript
<Form onSubmit={handleSubmit}>
    <Form.Group className="mb-3">
        <Form.Label>Title *</Form.Label>
        <Form.Control
            type="text"
            name="title"
            value={formData.title}
            onChange={handleInputChange}
            required
        />
    </Form.Group>
    <Button type="submit" disabled={saving}>
        {saving ? 'Saving...' : 'Save'}
    </Button>
</Form>
```

### Patrones de Navegación
- Usa **React Router** para toda la navegación.
- **Implementa migas de pan (breadcrumbs)** con navegación hacia atrás.
- Usa **navegación programática** con el hook `useNavigate`.

```javascript
import { useNavigate } from 'react-router-dom';

const navigate = useNavigate();

// Ejemplos de navegación
<Button variant="link" onClick={() => navigate('/')}>
    ← Back to Dashboard
</Button>
```

### Accesibilidad
- Incluye atributos **aria-label** para elementos interactivos.
- Usa elementos **HTML semánticos**.
- Asegura soporte para **navegación por teclado**.
- Proporciona **texto alternativo** para las imágenes.

```javascript
<Form.Control 
    type="text" 
    placeholder="Search by title" 
    aria-label="Search positions by title"
/>
```

## Estándares de Pruebas (Testing Standards)

### Pruebas End-to-End con Cypress
- **Prueba los flujos de trabajo del usuario** en lugar de los detalles de implementación.
- Usa atributos **data-testid** para una selección de elementos confiable.
- **Organiza las pruebas por característica** (`candidates.cy.ts`, `positions.cy.ts`).
- **Incluye pruebas de API** junto con las pruebas de UI.

```typescript
describe('Positions API - Update', () => {
    beforeEach(() => {
        cy.window().then((win) => {
            win.localStorage.clear();
        });
    });

    it('debería actualizar una posición exitosamente', () => {
        const updateData = {
            title: 'Updated Test Position',
            status: 'Open'
        };

        cy.request({
            method: 'PUT',
            url: `${API_URL}/positions/${testPositionId}`,
            body: updateData
        }).then((response) => {
            expect(response.status).to.eq(200);
            expect(response.body.data.title).to.eq(updateData.title);
        });
    });
});
```

### Organización de Pruebas
- **Agrupa pruebas relacionadas** con bloques `describe`.
- **Usa nombres de prueba descriptivos** que expliquen el comportamiento esperado.
- **Prueba tanto escenarios de éxito como de error**.
- **Incluye casos límite** y pruebas de validación.

## Estándares de Configuración

### Configuración de TypeScript
- Habilita el **modo estricto** para la verificación de tipos.
- Usa **mapeo de rutas** con "@/*" para importaciones más limpias.
- Incluye **tanto tipos de Cypress como de Node**.
- Configura el **objetivo ES5** para una compatibilidad más amplia.

```json
{
    "compilerOptions": {
        "strict": true,
        "baseUrl": ".",
        "paths": {
            "@/*": ["src/*"]
        },
        "types": ["cypress", "node"]
    }
}
```

### Configuración de ESLint
- Extiende la configuración de **React App**.
- Incluye **reglas de Jest** para pruebas.
- **Formateo automático de código** y detección de errores.
- **Estilo de código consistente** en todo el proyecto.

### Configuración del Entorno
- Usa **variables de entorno** para las URLs de la API.
- **Configuraciones separadas** para desarrollo y producción.
- **Configura Cypress** con ajustes específicos del entorno.

```javascript
// cypress.config.ts
export default defineConfig({
    e2e: {
        baseUrl: 'http://localhost:3000',
        env: {
            API_URL: 'http://localhost:3010'
        }
    }
});
```

## Mejores Prácticas de Rendimiento

### Optimización de Componentes
- **Carga perezosa (Lazy load)** de componentes cuando sea apropiado.
- **Memoriza cálculos costosos** con `useMemo`.
- **Evita re-renders innecesarios** con `useCallback`.
- **Extrae lógica reutilizable** en hooks personalizados.

### Optimización del Bundle
- **Tree shaking** habilitado a través de Create React App.
- **División de código (Code splitting)** a nivel de ruta.
- **Optimiza imágenes** y recursos estáticos.
- **Monitorea el tamaño del bundle** con herramientas de construcción.

### Eficiencia de la API
- **Implementa un manejo de errores adecuado** para las peticiones de red.
- **Cachea respuestas de la API** donde sea apropiado.
- **Usa estados de carga** para mejorar el rendimiento percibido.
- **Agrupa llamadas a la API** cuando sea posible.

## Flujo de Desarrollo (Development Workflow)

- **Ramas de Características (Feature Branches)**: Desarrolla características en ramas separadas, agregando el sufijo descriptivo "-frontend" para permitir el trabajo en paralelo y evitar conflictos o colisiones.
- **Commits Descriptivos**: Escribe mensajes de commit descriptivos en español.
- **Revisión de Código**: Revisión de código antes de fusionar.
- **Ramas Pequeñas**: Mantén las ramas pequeñas y enfocadas.

### Scripts de Desarrollo
```bash
npm start          # Servidor de desarrollo
npm test           # Ejecutar pruebas unitarias
npm run build      # Construcción para producción
npm run cypress:open    # Abrir el ejecutor de pruebas de Cypress
npm run cypress:run     # Ejecutar pruebas de Cypress en modo headless
```

### Calidad de Código
- **Validación de ESLint** antes de los commits.
- **Compilación de TypeScript** sin errores.
- **Todas las Pruebas Pasando** antes del despliegue.
- **Monitoreo de rendimiento** con Web Vitals.

## Estrategia de Migración

### Migración a TypeScript
- **Migración gradual** de JavaScript a TypeScript.
- **Nuevos componentes en TypeScript** por defecto.
- **Mantener componentes de JavaScript existentes** hasta la refactorización planeada.
- **Agregar tipos incrementalmente** al código existente.

### Modernización de Componentes
- **Componentes funcionales** sobre componentes de clase.
- **Hooks** en lugar de métodos de ciclo de vida.
- Componentes de **React Bootstrap** para consistencia.
- Principios de **diseño responsivo** en todo momento.

Este documento sirve como base para mantener la calidad y consistencia del código en la aplicación frontend de LTI. Todos los miembros del equipo deben seguir estas prácticas para asegurar un código base mantenible y escalable.
