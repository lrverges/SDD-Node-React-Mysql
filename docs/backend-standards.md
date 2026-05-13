---
description: Backend development standards, best practices, and conventions for the LTI Node.js/TypeScript/Express application including Domain-Driven Design, SOLID principles, architecture patterns, API design, and testing practices
globs: ["backend/src/**/*.ts", "backend/prisma/**/*.{prisma,ts}", "backend/jest.config.js", "backend/tsconfig.json", "backend/serverless.yml", "backend/package.json"]
alwaysApply: true
---

# Backend Project Standards and Best Practices

## Table of Contents

- [Overview](#overview)
- [Technology Stack](#technology-stack)
  - [Core Technologies](#core-technologies)
  - [Database & ORM](#database--orm)
  - [Testing Framework](#testing-framework)
  - [Development Tools](#development-tools)
- [Architecture Overview](#architecture-overview)
  - [Domain-Driven Design (DDD)](#domain-driven-design-ddd)
  - [Layered Architecture](#layered-architecture)
  - [Project Structure](#project-structure)
- [Domain-Driven Design Principles](#domain-driven-design-principles)
  - [Entities](#entities)
  - [Value Objects](#value-objects)
  - [Aggregates](#aggregates)
  - [Repositories](#repositories)
  - [Domain Services](#domain-services)
  - [Additional Recommendations](#additional-recommendations)
- [SOLID and DRY Principles](#solid-and-dry-principles)
  - [Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
  - [Open/Closed Principle (OCP)](#openclosed-principle-ocp)
  - [Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
  - [Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
  - [Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)
  - [DRY (Don't Repeat Yourself)](#dry-dont-repeat-yourself)
- [Coding Standards](#coding-standards)
  - [Language and Naming Conventions](#language-and-naming-conventions)
  - [TypeScript Usage](#typescript-usage)
  - [Error Handling](#error-handling)
  - [Validation Patterns](#validation-patterns)
  - [Logging Standards](#logging-standards)
- [API Design Standards](#api-design-standards)
  - [REST Endpoints](#rest-endpoints)
  - [Request/Response Patterns](#requestresponse-patterns)
  - [Error Response Format](#error-response-format)
  - [CORS Configuration](#cors-configuration)
- [Database Patterns](#database-patterns)
  - [Prisma Schema](#prisma-schema)
  - [Migrations](#migrations)
  - [Repository Pattern](#repository-pattern)
- [Testing Standards](#testing-standards)
  - [Unit Testing](#unit-testing)
  - [Integration Testing](#integration-testing)
  - [Test Coverage Requirements](#test-coverage-requirements)
  - [Mocking Standards](#mocking-standards)
- [Performance Best Practices](#performance-best-practices)
  - [Database Query Optimization](#database-query-optimization)
  - [Async/Await Patterns](#asyncawait-patterns)
  - [Error Handling Performance](#error-handling-performance)
- [Security Best Practices](#security-best-practices)
  - [Input Validation](#input-validation)
  - [Environment Variables](#environment-variables)
  - [Dependency Injection](#dependency-injection)
- [Development Workflow](#development-workflow)
  - [Git Workflow](#git-workflow)
  - [Development Scripts](#development-scripts)
  - [Code Quality](#code-quality)
- [Serverless Deployment](#serverless-deployment)
  - [AWS Lambda Configuration](#aws-lambda-configuration)
  - [Serverless Framework](#serverless-framework)

---

## Overview

Este documento describe las mejores prácticas, convenciones y estándares utilizados en la aplicación backend de LTI. El backend sigue los principios de Domain-Driven Design (DDD) e implementa una arquitectura por capas para asegurar la consistencia del código, mantenibilidad y escalabilidad.

## Stack Tecnológico

### Tecnologías Core
- **Node.js**: Entorno de ejecución
- **TypeScript**: Desarrollo con tipado seguro en modo estricto
- **Express.js**: Framework de aplicación web
- **Prisma**: ORM moderno para acceso a base de datos

### Base de Datos y ORM
- **MySQL**: Base de datos relacional (contenedor Docker)
- **Prisma Client**: Cliente de base de datos con tipado seguro
- **Prisma Migrate**: Herramienta de migración de base de datos

### Framework de Pruebas
- **Jest**: Framework de pruebas con soporte para TypeScript
- **Umbral de Cobertura**: 90% para ramas, funciones, líneas y sentencias
- **Ubicación de Pruebas**: Directorios `__tests__` y archivos `.test.ts`

### Herramientas de Desarrollo
- **ESLint**: Linting de código
- **TypeScript Compiler**: Verificación de tipos y compilación
- **Serverless Framework**: Soporte para despliegue en AWS Lambda


## Vista General de la Arquitectura

### Domain-Driven Design (DDD)

Domain-Driven Design es una metodología que se enfoca en modelar el software de acuerdo con la lógica de negocio y el conocimiento del dominio. Al centrar el desarrollo en una comprensión profunda del dominio, DDD facilita la creación de sistemas complejos.

**Beneficios:**
- **Comunicación Mejorada**: Promueve un lenguaje común entre desarrolladores y expertos del dominio, mejorando la comunicación y reduciendo errores de interpretación.
- **Modelos de Dominio Claros**: Ayuda a construir modelos que reflejan con precisión las reglas y procesos de negocio.
- **Alta Mantenibilidad**: Al dividir el sistema en subdominios, facilita el mantenimiento y la evolución del software.

### Arquitectura por Capas (Layered Architecture)

El backend sigue una arquitectura DDD por capas:

**Capa de Presentación** (`src/presentation/`)
- Los controladores manejan las peticiones/respuestas HTTP.
- Las rutas definen los endpoints de la API.
- Los controladores usan servicios de la capa de Aplicación.

**Capa de Aplicación** (`src/application/`)
- Los servicios contienen la lógica de negocio y la orquestación.
- El validador maneja la validación de entradas.
- Los servicios usan repositorios de la capa de Dominio.

**Capa de Dominio** (`src/domain/`)
- Los modelos definen las entidades principales del negocio (Candidate, Position, Application, Interview, etc.).
- Las interfaces de los repositorios definen los contratos de acceso a datos.
- Lógica de negocio pura sin dependencias externas.

**Capa de Infraestructura** (implícita)
- El ORM Prisma maneja las operaciones de la base de datos.
- Las implementaciones de los repositorios (vía Prisma) satisfacen las interfaces del dominio.

### Estructura del Proyecto

```
backend/
├── src/
│   ├── domain/
│   │   ├── models/          # Entidades de dominio
│   │   └── repositories/    # Interfaces de repositorio
│   ├── application/
│   │   ├── services/        # Servicios de lógica de negocio
│   │   └── validator.ts     # Validación de entradas
│   ├── presentation/
│   │   └── controllers/     # Manejadores de peticiones HTTP
│   ├── infrastructure/
│   │   ├── logger.ts        # Utilidades de logging
│   │   └── prismaClient.ts  # Configuración del cliente de Prisma
│   ├── routes/              # Definiciones de rutas de Express
│   ├── middleware/          # Middleware de Express
│   ├── index.ts             # Punto de entrada de la aplicación
│   └── lambda.ts            # Manejador de AWS Lambda
├── prisma/
│   ├── schema.prisma        # Esquema de la base de datos
│   └── migrations/          # Migraciones de la base de datos
├── test-utils/
│   ├── builders/            # Constructores de datos de prueba (builders)
│   └── mocks/               # Helpers para mocks
├── jest.config.js           # Configuración de Jest
├── tsconfig.json            # Configuración de TypeScript
├── serverless.yml           # Configuración de Serverless Framework
└── package.json             # Dependencias y scripts
```

## Principios de Domain-Driven Design

### Entidades

Las entidades son objetos con una identidad distinta que persiste a lo largo del tiempo.

**Antes:**
```typescript
// Anteriormente, los datos del candidato podrían haberse manejado como un objeto JSON simple sin métodos.
const candidate = {
    id: 1,
    firstName: 'John',
    lastName: 'Doe',
    email: 'john.doe@example.com'
};
```

**Después:**
```typescript
export class Candidate {
    id?: number;
    firstName: string;
    lastName: string;
    email: string;
    
    // Constructor y métodos que encapsulan la lógica de negocio
    constructor(data: any) {
        this.id = data.id;
        this.firstName = data.firstName;
        this.lastName = data.lastName;
        this.email = data.email;
    }
}
```

**Explicación**: `Candidate` es una entidad porque tiene un identificador único (`id`) que lo distingue de otros candidatos, incluso si otras propiedades son idénticas.

**Mejor Práctica**: Las entidades deben encapsular la lógica de negocio relacionada con su concepto de dominio y mantener la consistencia de su estado interno.

### Objetos de Valor (Value Objects)

Los Objetos de Valor describen aspectos del dominio sin identidad conceptual. Se definen por sus atributos en lugar de por un identificador.

**Antes:**
```typescript
// Manejo de información de educación como un objeto simple
const education = {
    institution: 'University',
    degree: 'Bachelor',
    startDate: '2010-01-01',
    endDate: '2014-01-01'
};
```

**Después:**
```typescript
export class Education {
    institution: string;
    title: string;
    startDate: Date;
    endDate?: Date;
    
    constructor(data: any) {
        this.institution = data.institution;
        this.title = data.title;
        this.startDate = new Date(data.startDate);
        this.endDate = data.endDate ? new Date(data.endDate) : undefined;
    }
}
```

**Explicación**: `Education` puede considerarse un Objeto de Valor en algunos contextos, ya que describe la educación de un candidato sin necesidad de un identificador único. Sin embargo, en el modelo actual, se le ha asignado un id, lo que podría contradecir la definición pura de un Objeto de Valor en DDD.

**Recomendación**: Clases como `Education` y `WorkExperience` actualmente tienen identificadores únicos, clasificándolas como entidades. En muchos casos, estas podrían tratarse como Objetos de Valor dentro del contexto de un agregado `Candidate`. Considera eliminar los identificadores únicos de las clases que deberían ser Objetos de Valor, o incorporarlas como parte del documento de Candidate si se usa una base de datos NoSQL.

### Agregados (Aggregates)

Los Agregados son conjuntos de objetos que deben tratarse como una unidad. Tienen una entidad raíz que hace cumplir las invariantes y los límites de consistencia.

**Antes:**
```typescript
// Los datos del candidato y de educación se manejan por separado
const candidate = { id: 1, name: 'John Doe' };
const educations = [{ candidateId: 1, institution: 'University' }];
```

**Después:**
```typescript
export class Candidate {
    id?: number;
    firstName: string;
    lastName: string;
    email: string;
    educations: Education[];
    
    constructor(data: any) {
        this.id = data.id;
        this.firstName = data.firstName;
        this.lastName = data.lastName;
        this.email = data.email;
        this.educations = data.educations?.map(edu => new Education(edu)) || [];
    }
}
```

**Explicación**: `Candidate` actúa como una raíz de agregado (aggregate root) que contiene `Education`, `WorkExperience`, `Resume` y `Application`. `Candidate` es la raíz del agregado, ya que las otras entidades solo tienen sentido en relación con un candidato.

**Recomendación**: Los agregados deben diseñarse cuidadosamente para asegurar que todas las operaciones dentro del límite del agregado mantengan la consistencia. Las operaciones que afectan a `Education` y `WorkExperience` deben manejarse a través de la raíz del agregado, `Candidate`, para mantener la integridad y el encapsulamiento.

### Repositorios (Repositories)

Los Repositorios proporcionan interfaces para acceder a agregados y entidades, encapsulando la lógica de acceso a datos.

**Antes:**
```typescript
// Acceso directo a la base de datos sin abstracción
function getCandidateById(id: number) {
    return database.query('SELECT * FROM candidates WHERE id = ?', [id]);
}
```

**Después:**
```typescript
export interface ICandidateRepository {
    findById(id: number): Promise<Candidate | null>;
    save(candidate: Candidate): Promise<Candidate>;
    findA    async save(candidate: Candidate): Promise<Candidate> {
        // Implementación con Prisma
    }
}
```

**Explicación**: `CandidateRepository` proporciona una interfaz clara para acceder a los datos de los candidatos, encapsulando la lógica de acceso a datos.

**Recomendación**: 
- Desarrollar interfaces de repositorio completas para cada entidad y agregado, asegurando que todas las interacciones de base de datos para esas entidades pasen por el repositorio.
- Implementar métodos de repositorio que manejen colecciones de entidades, como listas de Candidatos, que puedan filtrarse o modificarse en bloque.
- Usar inyección de dependencias para inyectar el cliente de Prisma en los repositorios.

### Servicios de Dominio (Domain Services)

Los Servicios de Dominio contienen lógica de negocio que no pertenece naturalmente a una entidad u objeto de valor.

**Antes:**
```typescript
// Funciones sueltas para manejar la lógica de negocio
function calculateAge(candidate: any): number {
    const today = new Date();
    const birthDate = new Date(candidate.birthDate);
    let age = today.getFullYear() - birthDate.getFullYear();
    const m = today.getMonth() - birthDate.getMonth();
    if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) {
        age--;
    }
    return age;
}
```

**Después:**
```typescript
export class CandidateService {
    static calculateAge(candidate: Candidate): number {
        const today = new Date();
        const birthDate = new Date(candidate.birthDate);
        let age = today.getFullYear() - birthDate.getFullYear();
        const m = today.getMonth() - birthDate.getMonth();
        if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) {
            age--;
        }
        return age;
    }
}
```

**Explicación**: `CandidateService` encapsula la lógica de negocio relacionada con los candidatos, como calcular la edad, proporcionando un punto centralizado y coherente para manejar estas operaciones.

### Recomendaciones Adicionales

**Uso de Factorías (Factories)**

Las factorías son útiles en DDD para encapsular la lógica de creación de objetos complejos, asegurando que todos los objetos creados cumplan con las reglas del dominio desde el momento de su creación.

**Recomendación**: Implementar factorías para la creación de entidades y agregados, especialmente aquellos que son complejos y requieren una configuración inicial específica que cumpla con las reglas de negocio.

**Mejora en el Modelado de Relaciones**

Las relaciones entre entidades y agregados deben ser claras y consistentes con las reglas de negocio.

**Recomendación**: Revisar y posiblemente rediseñar las relaciones entre entidades para asegurar que reflejen con precisión las necesidades y reglas del dominio. Esto puede incluir eliminar relaciones innecesarias o agregar nuevas relaciones que faciliten las operaciones de negocio.

**Integración de Eventos de Dominio (Domain Events)**

Los eventos de dominio son una parte importante de DDD y pueden usarse para manejar efectos secundarios de las operaciones del dominio de manera desacoplada.

**Recomendación**: Implementar un sistema de eventos de dominio que permita a las entidades y agregados publicar eventos que otros componentes del sistema puedan manejar sin estar fuemente acoplados a las entidades que los generan.

## Principios SOLID y DRY

### Principios SOLID

Los principios SOLID son cinco principios de diseño orientado a objetos que ayudan a crear sistemas más comprensibles, flexibles y mantenibles.

#### Principio de Responsabilidad Única (SRP)

Cada clase debe tener una sola responsabilidad o motivo para cambiar.

**Antes:**
```typescript
// Un método que maneja múltiples responsabilidades: validación y almacenamiento de datos
function processCandidate(candidate: any) {
    if (!candidate.email.includes('@')) {
        console.error('Invalid email');
        return;
    }
    database.save(candidate);
    console.log('Candidate saved');
}
```

**Después:**
```typescript
export class Candidate {
    // La clase ahora solo maneja la lógica relacionada con el candidato
    validateEmail(): void {
        if (!this.email.includes('@')) {d email');
        return;
    }
    database.save(candidate);
    console.log('Candidate saved');
}
```

**After:**
```typescript
export class Candidate {
    // The class now only handles logic related to the candidate
    validateEmail(): void {
        if (!this.email.includes('@')) {
            throw new Error('Invalid email');
        }
    }
}

export class CandidateRepository {
    async save(candidate: Candidate): Promise<Candidate> {
        candidate.validateEmail();
        return await prisma.candidate.create({ data: candidate });
    }
}
```

**Explicación**: La clase `Candidate` ahora tiene métodos separados para la validación, mientras que el repositorio maneja la persistencia de datos, cumpliendo con el principio de responsabilidad única.

**Observación**: La clase `Candidate` en `backend/src/domain/models/Candidate.ts` maneja tanto la lógica de negocio como la lógica de acceso a datos.

**Recomendación**: Separar la lógica de acceso a datos en una capa de repositorio para adherirse más estrechamente a SRP.

#### Principio de Abierto/Cerrado (OCP)

Las entidades de software deben estar abiertas para la extensión pero cerradas para la modificación.

**Antes:**
```typescript
// Modificación directa de la clase para agregar funcionalidad
class Candidate {
    saveToDatabase() {
        // código para guardar en la base de datos
    }
    // Para agregar nueva funcionalidad, modificamos la clase directamente
    sendEmail() {
        // código para enviar un correo electrónico
    }
}
```

**Después:**
```typescript
export class Candidate {
    saveToDatabase() {
        // código para guardar en la base de datos
    }
}

// Extender la funcionalidad sin modificar la clase existente
class CandidateWithEmail extends Candidate {
    sendEmail() {
        // código para enviar un correo electrónico
    }
}
```

**Explicación**: La funcionalidad de envío de correo electrónico se extiende en una subclase, manteniendo la clase original cerrada para modificaciones pero abierta para extensiones.

**Observación**: La función `addCandidate` en `backend/src/application/services/candidateService.ts` instancia directamente las clases `Candidate`, `Education`, `WorkExperience` y `Resume`.

**Recomendación**: Usar métodos factoría (factory methods) para crear instancias, permitiendo una extensión más fácil sin modificar el código existente.

#### Principio de Sustitución de Liskov (LSP)

Los objetos de una clase derivada deben poder reemplazarse por objetos de la clase base sin alterar la funcionalidad del programa.

**Antes:**
```typescript
// Subclase que no puede reemplazar completamente a su clase base
class TemporaryCandidate extends Candidate {
    saveToDatabase() {
        throw new Error("Temporary candidates can't be saved.");
    }
}
```

**Después:**
```typescript
class TemporaryCandidate extends Candidate {
    saveToDatabase() {
        // Implementación adecuada que permite el manejo temporal
        console.log("Manejado temporalmente");
        // Alternativa: Guardar en almacenamiento temporal
    }
}
```

**Explicación**: `TemporaryCandidate` ahora proporciona una implementación adecuada que respeta el contrato de la clase base, permitiendo la sustitución sin errores.

**Observación**: Actualmente, no hay herencia en uso donde se pueda violar LSP. El proyecto usa composición sobre herencia, lo que generalmente apoya LSP.

**Recomendación**: Continuar usando composición para evitar violaciones de LSP y asegurar que cualquier estructura de herencia futura permita que las clases derivadas sustituyan a sus clases base sin alterar cómo funciona el programa.

#### Principio de Segregación de Interfaces (ISP)

Muchas interfaces específicas son mejores que una sola interfaz general.

**Antes:**
```typescript
// Una interfaz grande que los clientes pequeños no usan completamente
interface CandidateOperations {
    save(): void;
    validate(): void;
    sendEmail(): void;
    generateReport(): void;
}
```

**Después:**
```typescript
interface SaveOperation {
    save(): void;
}

interface EmailOperations {
    sendEmail(): void;
}

interface ReportOperations {
    generateReport(): void;
}

class Candidate implements SaveOperation, EmailOperations {
    save() {
        // implementación
    }
    
    sendEmail() {
        // implementación
    }
}
```

**Explicación**: Las interfaces se segregan en operaciones más pequeñas, permitiendo que las clases implementen solo las interfaces que necesitan.

**Observación**: El proyecto actualmente no usa interfaces de TypeScript de manera extensiva para hacer cumplir contratos para las clases.

**Recomendación**: Definir interfaces más granulares para las clases de servicio para asegurar que solo implementen los métodos que necesitan.

#### Principio de Inversión de Dependencias (DIP)

Los módulos de alto nivel no deben depender de módulos de bajo nivel; ambos deben depender de abstracciones.

**Antes:**
```typescript
// Dependencia directa de una implementación concreta
class Candidate {
    private database = new PrismaClient();
    
    save() {
        this.database.candidate.create({ data: this });
    }
}
```

**Después:**
```typescript
interface Database {
    save(candidate: Candidate): Promise<Candidate>;
}

class Candidate {
    private database: Database;
    
    constructor(database: Database) {
        this.database = database;
    }
    
    async save(): Promise<Candidate> {
        return await this.database.save(this);
    }
}
```

**Explicación**: `Candidate` ahora depende de una abstracción (Database), no de una implementación concreta, lo que facilita la flexibilidad y las pruebas de código.

**Observación**: Clases como `Candidate` dependen directamente del `PrismaClient` concreto para las operaciones de base de datos.

**Recomendación**: Usar inyección de dependencias para invertir la dependencia, confiando en abstracciones en lugar de implementaciones concretas. Inyectar `PrismaClient` a través del constructor o un método setter.

### DRY (Don't Repeat Yourself)

El principio DRY se enfoca en reducir la duplicación en el código. Cada pieza de conocimiento debe tener una representación única, inequívoca y autoritativa dentro de un sistema.

**Antes:**
```typescript
// Código repetido para validar correos electrónicos en múltiples funciones
function saveCandidate(candidate: Candidate) {
    if (!candidate.email.includes('@')) {
        throw new Error('Invalid email');
    }
    // lógica de guardado
}

function updateCandidate(candidate: Candidate) {
    if (!candidate.email.includes('@')) {
        throw new Error('Invalid email');
    }
    // lógica de actualización
}
```

**Explicación**: La validación de correo electrónico se centraliza en un único método `validateEmail`, eliminando la duplicación de código en las funciones de guardado y actualización.

**Observación**: Los métodos para guardar entidades como `Candidate`, `Education`, `WorkExperience` y `Resume` contienen lógica repetitiva para manejar las operaciones de la base de datos.

**Recomendación**: Abstraer la lógica común de operaciones de base de datos en una función o clase reutilizable.

## Estándares de Codificación

### Convenciones de Nombres

- **Nombres de Variables**: Usa camelCase para variables y funciones (ej. `candidateId`, `findCandidateById`).
- **Nombres de Clases**: Usa PascalCase para clases e interfaces (ej. `Candidate`, `CandidateRepository`).
- **Nombres de Constantes**: Usa UPPER_SNAKE_CASE para constantes (ej. `MAX_CANDIDATES_PER_PAGE`).
- **Nombres de Tipos**: Usa PascalCase para tipos e interfaces (ej. `CandidateData`, `ICandidateRepository`).
- **Nombres de Archivos**: Usa camelCase para nombres de archivos (ej. `candidateService.ts`, `candidateController.ts`).

**Ejemplos:**

```typescript
// Bueno: Código en inglés con comentarios en español
export class CandidateRepository {
    async findById(candidateId: number): Promise<Candidate | null> {
        // Buscar candidato por ID en la base de datos
        const candidate = await this.prisma.candidate.findUnique({
            where: { id: candidateId }
        });
        return candidate ? new Candidate(candidate) : null;
    }
}

// Evitar: Nombres o comentarios que no estén en inglés
export class RepositorioCandidato {
    async buscarPorId(idCandidato: number): Promise<Candidato | null> {
        // Buscar candidato por ID en la base de datos
        const candidato = await this.prisma.candidate.findUnique({
            where: { id: idCandidato }
        });
        return candidato ? new Candidato(candidato) : null;
    }
}
```

**Mensajes de Error y Logs:**

```typescript
// Bueno: Mensajes de error en inglés
throw new NotFoundError('Candidate not found with the provided ID');
logger.error('Failed to create candidate', { error: error.message });

// Evitar: Mensajes que no estén en inglés en el código
throw new NotFoundError('Candidato no encontrado con el ID proporcionado');
logger.error('Error al crear candidato', { error: error.message });
```

### Uso de TypeScript

- **Modo Estricto**: Habilita siempre el modo estricto en `tsconfig.json`.
- **Definiciones de Tipos**: Usa tipos explícitos para los parámetros de función y los valores de retorno.
- **Interfaces**: Define interfaces para estructuras de datos complejas.
- **Evitar `any`**: Usa `unknown` o tipos específicos en lugar de `any` cuando sea posible.

```typescript
// Bueno: Tipos explícitos
async function findCandidateById(id: number): Promise<Candidate | null> {
    // implementación
}

// Evitar: Usar any
function processData(data: any): any {
    // implementación
}
```

### Manejo de Errores

- **Clases de Error Personalizadas**: Crea clases de error específicas del dominio.
- **Middleware de Errores**: Usa middleware de errores global para respuestas de error consistentes.
- **Mensajes de Error**: Proporciona mensajes de error descriptivos para la depuración.

```typescript
export class NotFoundError extends Error {
    constructor(message: string) {
        super(message);
        this.name = 'NotFoundError';
    }
}

// En el controlador
try {
    const candidate = await candidateService.findById(id);
    if (!candidate) {
        throw new NotFoundError('Candidate not found');
    }
    res.json(candidate);
} catch (error) {
    next(error);
}
```

### Patrones de Validación

- **Validación de Entradas**: Valida todas las entradas en la capa de aplicación.
- **Usar Módulo Validador**: Centraliza la lógica de validación en `src/application/validator.ts`.
- **Validar Antes de Procesar**: Valida siempre antes de ejecutar la lógica de negocio.

```typescript
import { validateCandidateData } from '../application/validator';

export async function addCandidate(req: Request, res: Response, next: NextFunction) {
    try {
        const validatedData = validateCandidateData(req.body);
        const candidate = await candidateService.create(validatedData);
        res.status(201).json(candidate);
    } catch (error) {
        next(error);
    }
}
```

### Estándares de Logging

- **Usar Clase Logger**: Usa el logger centralizado de `src/infrastructure/logger.ts`.
- **Niveles de Log**: Usa los niveles de log apropiados (info, error, warn, debug).
- **Logging Estructurado**: Incluye contexto relevante en los mensajes de log.

```typescript
import { Logger } from '../infrastructure/logger';

const logger = new Logger();

logger.info('Candidate created', { candidateId: candidate.id });
logger.error('Failed to create candidate', { error: error.message });
```

## API Design Standards

### REST Endpoints

- **RESTful Naming**: Use RESTful conventions for endpoint naming
- **HTTP Methods**: Use appropriate HTTP methods (GET, POST, PUT, DELETE, PATCH)
- **Resource-Based URLs**: URLs should represent resources, not actions

```typescript
GET    /candidates          // List candidates
GET    /candidates/:id      // Get candidate by ID
POST   /candidates          // Create new candidate
PUT    /candidates/:id      // Update candidate
DELETE /candidates/:id      // Eliminar candidato
```

### Patrones de Petición/Respuesta (Request/Response Patterns)

- **Formato JSON**: Usa JSON para los cuerpos de petición y respuesta.
- **Estructura Consistente**: Mantén una estructura de respuesta consistente en todos los endpoints.
- **Códigos de Estado**: Usa los códigos de estado HTTP apropiados.

```typescript
// Respuesta de éxito
{
    "success": true,
    "data": { ... },
    "message": "Operation completed successfully"
}

// Respuesta de error
{
    "success": false,
    "error": {
        "message": "Error description",
        "code": "ERROR_CODE"
    }
}
```

### Formato de Respuesta de Error

- **Formato Consistente**: Todos los errores deben seguir la misma estructura de respuesta.
- **Códigos de Error**: Usa códigos de error significativos para diferentes tipos de error.
- **Códigos de Estado HTTP**: Mapea los errores a los códigos de estado HTTP apropiados.

```typescript
// 400 Bad Request
{
    "success": false,
    "error": {
        "message": "Validation failed",
        "code": "VALIDATION_ERROR",
        "details": [ ... ]
    }
}

// 404 Not Found
{
    "success": false,
    "error": {
        "message": "Resource not found",
        "code": "NOT_FOUND"
    }
}
```

### Configuración de CORS

- **Habilitar CORS**: Configura CORS para permitir el origen del frontend.
- **Configuración Segura**: Permite solo orígenes específicos en producción.
- **Credenciales**: Configura el manejo de credenciales de manera apropiada.

```typescript
import cors from 'cors';

const corsOptions = {
    origin: process.env.FRONTEND_URL || 'http://localhost:3000',
    credentials: true
};

app.use(cors(corsOptions));
```

## Patrones de Base de Datos

### Esquema de Prisma (Prisma Schema)

- **Fuente Única de Verdad**: `prisma/schema.prisma` es la fuente única de verdad para la estructura de la base de datos.
- **Relaciones**: Define las relaciones usando las relaciones de Prisma.
- **Convenciones de Nombres**: Usa convenciones de nombres consistentes (camelCase para campos, PascalCase para modelos).

### Migraciones

- **Control de Versiones**: Todos los cambios en la base de datos deben estar controlados por versiones a través de migraciones.
- **Nombres de Migraciones**: Usa nombres descriptivos para las migraciones.
- **Revisar Migraciones**: Revisa los archivos de migración antes de aplicarlos.

```bash
# Crear migración
npx prisma migrate dev --name nombre_descriptivo_de_migracion

# Aplicar migraciones en producción
npx prisma migrate deploy
```

### Patrón Repositorio (Repository Pattern)

- **Interfaces de Repositorio**: Define las interfaces de repositorio en la capa de dominio.
- **Implementación de Prisma**: Implementa los repositorios usando Prisma en la capa de infraestructura.
- **Inyección de Dependencias**: Inyecta el cliente de Prisma en los repositorios.

```typescript
// Interfaz de la capa de dominio
export interface ICandidateRepository {
    findById(id: number): Promise<Candidate | null>;
    save(candidate: Candidate): Promise<Candidate>;
}

// Implementación de la capa de infraestructura
export class CandidateRepository implements ICandidateRepository {
    constructor(private prisma: PrismaClient) {}
    
    async findById(id: number): Promise<Candidate | null> {
        const data = await this.prisma.candidate.findUnique({ where: { id } });
        return data ? new Candidate(data) : null;
    }
}
```

## Estándares de Pruebas (Testing Standards)

El proyecto tiene requisitos estrictos para la calidad del código y la mantenibilidad. Estos son los estándares de pruebas unitarias y mejores prácticas que deben aplicarse. 

### Estructura de Archivos de Prueba
- Usa nombres de archivo de prueba descriptivos: `[nombreComponente].test.ts`
- Coloca los archivos de prueba junto al código fuente que prueban
- Usa Jest como framework de pruebas con soporte para TypeScript
- Mantén un umbral de cobertura del 90% para ramas, funciones, líneas y sentencias


### Patrón de Organización de Pruebas
Plantilla:
```typescript
describe('[NombreComponente] - [nombreMetodo]', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  describe('should_[comportamiento_esperado]_when_[condicion]', () => {
    it('should [caso de prueba específico]', async () => {
      // Arrange
      // Act  
      // Assert
    });
  });
});
```

Ejemplo real:
```typescript
describe('CandidateService - findById', () => {
    beforeEach(() => {
        jest.clearAllMocks();
    });

    it('debería retornar el candidato cuando se encuentre', async () => {
        // Arrange
        const candidateId = 1;
        const mockCandidate = new Candidate({ id: 1, firstName: 'John' });
        (CandidateRepository.findById as jest.Mock).mockResolvedValue(mockCandidate);

        // Act
        const result = await candidateService.findById(candidateId);

        // Assert
        expect(result).toEqual(mockCandidate);
        expect(CandidateRepository.findById).toHaveBeenCalledWith(candidateId);
    });
});
```



### Convención de Nombres para Casos de Prueba
- Usa nombres descriptivos basados en el comportamiento: `should_[comportamiento_esperado]_when_[condicion]`
- Agrupa los casos de prueba relacionados bajo bloques `describe` descriptivos
- Usa snake_case para los bloques describe y camelCase para las pruebas individuales

### Estructura de la Prueba (Patrón AAA)
Sigue siempre el patrón Arrange-Act-Assert (Preparar-Actuar-Verificar):
```typescript
it('debería actualizar el estado del candidato exitosamente cuando se proporcionan datos válidos', async () => {
  // Arrange - Configurar datos de prueba y mocks
  const candidateId = 1;
  const applicationId = 1;
  const newInterviewStep = 2;
  
  // Act - Ejecutar la función bajo prueba
  const result = await updateCandidateStage(candidateId, applicationId, newInterviewStep);
  
  // Assert - Verificar el comportamiento esperado
  expect(result).toEqual(expectedResult);
});
```

Patrón de aserciones:
- Usa matchers específicos: `toHaveBeenCalledWith()`, `toHaveBeenCalledTimes()`
- Verifica tanto las operaciones exitosas como las condiciones de error
- Verifica que los mocks fueron llamados con los parámetros correctos
- Realiza aserciones sobre los valores de retorno y los efectos secundarios








### Estándares de Mocking

- Haz mock de todas las dependencias externas (modelos, servicios, clientes de base de datos).
- Haz mock de las capas de repositorio en las pruebas de servicio.
- Haz mock de las capas de servicio en las pruebas de controlador.
- Usa `jest.mock()` en la parte superior de los archivos de prueba para el mocking a nivel de módulo.
- Crea instancias de mock con estructuras de datos realistas.
- Limpia todos los mocks en `beforeEach()` para asegurar el aislamiento de las pruebas.


### Requisitos de Cobertura de Pruebas

- **Cobertura de pruebas exhaustiva**: Incluye estas categorías de prueba para cada función:
  1. **Pruebas de Camino Feliz (Happy Path)**: Entradas válidas que producen las salidas esperadas.
  2. **Pruebas de Manejo de Errores**: Entradas inválidas, datos faltantes, errores de base de datos.
  3. **Casos Límite (Edge Cases)**: Valores límite, entradas null/undefined, datos vacíos.
  4. **Pruebas de Validación**: Validación de entradas, cumplimiento de reglas de negocio.
  5. **Puntos de Integración**: Llamadas a servicios externos, operaciones de base de datos.

- **Umbral**: 90% para ramas, funciones, líneas y sentencias.
- **Reportes de Cobertura**: Genera reportes de cobertura con `npm run test:coverage`.
- **Archivos de Cobertura**: Los reportes de cobertura en el directorio `coverage/` deben agregar la fecha, como `YYYYMMDD-backend-coverage.md`.


### Pruebas de Errores
- Prueba tanto los errores esperados como los inesperados.
- Verifica que los mensajes de error sean descriptivos y útiles.
- Prueba la propagación de errores a través de las capas de servicio.
- Asegura los códigos de estado HTTP correctos en las pruebas de controlador.

### Especificaciones para Pruebas de Controlador
- Haz mock de la capa de servicio completamente.
- Prueba el manejo de peticiones/respuestas HTTP.
- Verifica el análisis (parsing) y validación de parámetros.
- Prueba el formato de respuesta de error.
- Usa mocks realistas de Request/Response de Express.

### Especificaciones para Pruebas de Servicio
- Haz mock de los modelos de dominio y repositorios.
- Prueba la lógica de negocio en aislamiento.
- Verifica la transformación y validación de datos.
- Prueba el manejo de errores y casos límite.
- Haz mock de las dependencias externas (Prisma, validadores).

### Pruebas de Base de Datos
- Haz mock del cliente de Prisma y de todas las operaciones de base de datos.
- Prueba tanto las operaciones de base de datos exitosas como las fallidas.
- Verifica las consultas y parámetros correctos de la base de datos.
- Prueba el manejo de transacciones y escenarios de rollback.

### Pruebas Asíncronas
- Usa siempre `async/await` para operaciones asíncronas.
- Usa `Promise.allSettled()` para probar operaciones concurrentes.
- Maneja adecuadamente los rechazos de promesas en las pruebas.
- Prueba escenarios de tiempo de espera (timeout) cuando aplique.

### Gestión de Datos de Prueba
- Usa funciones factoría (factory functions) para crear datos de prueba.
- Mantén los datos de prueba consistentes y realistas.
- Evita valores hardcodeados en múltiples lugares.
- Usa datos de prueba significativos que reflejen escenarios del mundo real.

### Pruebas de Integración

- **Pruebas de Controlador**: Prueba el manejo de peticiones/respuestas HTTP.
- **Pruebas de Base de Datos**: Prueba las implementaciones de repositorio con la base de datos.
- **Flujo End-to-End**: Prueba flujos completos de petición.


### Estándares de Calidad de Código

#### Uso de TypeScript
- Usa tipado estricto para todos los parámetros de prueba y valores de retorno.
- Define interfaces adecuadas para los datos de mock.
- Usa aserciones de tipo con moderación y con la justificación adecuada.
- Aprovecha el sistema de tipos de TypeScript para una mejor confiabilidad de las pruebas.

#### Documentación
- Escribe nombres de prueba claros y descriptivos que expliquen el escenario.
- Agrega comentarios para configuraciones de prueba complejas.
- Documenta cualquier condición de prueba especial o caso límite.
- Mantén el código de prueba tan legible como el código de producción.

#### Consideraciones de Rendimiento
- Mantén las pruebas rápidas y enfocadas.
- Evita operaciones asíncronas innecesarias en las pruebas.
- Usa estrategias de mock adecuadas para evitar I/O real.
- Agrupa pruebas relacionadas para minimizar la sobrecarga de configuración/desmontaje (setup/teardown).

### Integración con el Flujo de Desarrollo
- Ejecuta las pruebas antes de cada commit.
- Asegura que todas las pruebas pasen antes de fusionar (merge).
- Usa desarrollo guiado por pruebas (TDD) cuando sea apropiado.
- Actualiza las pruebas cuando modifiques funcionalidad existente.

### Anti-Patrones Comunes a Evitar
- No pruebes detalles de implementación, prueba el comportamiento.
- No crees configuraciones de prueba excesivamente complejas.
- No ignores las pruebas fallidas ni omitas los escenarios de error.
- No uses conexiones reales a la base de datos en pruebas unitarias.
- No crees pruebas que dependan de servicios externos.
- No escribas pruebas que estén demasiado acopladas a la implementación.


## Mejores Prácticas de Rendimiento

### Optimización de Consultas de Base de Datos

- **Seleccionar Campos Específicos**: Selecciona solo los campos que se necesitan.
- **Usar Índices**: Asegura índices de base de datos adecuados para los campos consultados con frecuencia.
- **Evitar Consultas N+1**: Usa el `include` de Prisma para obtener datos relacionados de manera eficiente.

```typescript
// Bueno: Obtener datos relacionados de manera eficiente
const candidate = await prisma.candidate.findUnique({
    where: { id },
    include: {
        educations: true,
        workExperiences: true
    }
});

// Evitar: Consultas N+1
const candidate = await prisma.candidate.findUnique({ where: { id } });
const educations = await prisma.education.findMany({ where: { candidateId: id } });
```

### Patrones Async/Await

- **Usar Siempre Async/Await**: Usa async/await en lugar de cadenas de promesas.
- **Manejo de Errores**: Maneja adecuadamente los errores en operaciones asíncronas.
- **Operaciones Paralelas**: Usa `Promise.all()` para operaciones paralelas cuando sea apropiado.

```typescript
// Bueno: Operaciones paralelas
const [candidates, positions] = await Promise.all([
    candidateService.findAll(),
    positionService.findAll()
]);
```

### Rendimiento en el Manejo de Errores

- **Retornos Tempranos (Early Returns)**: Retorna temprano para evitar procesamiento innecesario.
- **Propagación de Errores**: Deja que los errores se propaguen naturalmente a través de la pila de llamadas.
- **Evitar Sobre-Envoltura**: No envuelvas los errores innecesariamente.

## Mejores Prácticas de Seguridad

### Validación de Entradas

- **Validar Todas las Entradas**: Valida todas las entradas de usuario antes de procesarlas.
- **Sanitizar Datos**: Sanitiza los datos para prevenir ataques de inyección.
- **Verificación de Tipos**: Usa TypeScript y validación para asegurar la seguridad de tipos.

### Variables de Entorno

- **Nunca Hagas Commit de Secretos**: Nunca hagas commit de archivos `.env` o secretos al control de versiones.
- **Usar Variables de Entorno**: Usa variables de entorno para la configuración.
- **Validar Entorno**: Valida las variables de entorno requeridas al iniciar.

```typescript
// Validar variables de entorno requeridas
const requiredEnvVars = ['DATABASE_URL', 'PORT'];
requiredEnvVars.forEach(varName => {
    if (!process.env[varName]) {
        throw new Error(`Missing required environment variable: ${varName}`);
    }
});
```

### Inyección de Dependencias

- **Inyectar Cliente de Prisma**: Inyecta el cliente de Prisma a través de middleware de Express.
- **Evitar Estado Global**: Evita el estado global para las conexiones de base de datos.
- **Testabilidad**: Usa la inyección de dependencias para mejorar la testabilidad.

```typescript
// Middleware para inyectar el cliente de Prisma
app.use((req: Request, res: Response, next: NextFunction) => {
    req.prisma = prisma;
    next();
});

// Uso en controladores
export async function getCandidate(req: Request, res: Response) {
    const candidate = await req.prisma.candidate.findUnique({
        where: { id: req.params.id }
    });
    res.json(candidate);
}
```

## Flujo de Desarrollo (Development Workflow)

### Flujo de Git

- **Ramas de Características (Feature Branches)**: Desarrolla características en ramas separadas usando nombres claros y descriptivos para permitir el trabajo en paralelo y evitar conflictos o colisiones.
- **Commits Descriptivos**: Escribe mensajes de commit descriptivos en español.
- **Revisión de Código**: Revisión de código antes de fusionar.
- **Ramas Pequeñas**: Mantén las ramas pequeñas y enfocadas.

### Scripts de Desarrollo

```bash
npm run dev          # Servidor de desarrollo con recarga en vivo (hot reload)
npm run build        # Construir para producción
npm test             # Ejecutar pruebas
npm run test:coverage # Ejecutar pruebas con cobertura
npm run prisma:generate  # Generar cliente de Prisma
npx prisma migrate dev   # Crear y aplicar migración
npx prisma db seed       # Poblar la base de datos (seed)
```

### Calidad de Código

- **Validación de ESLint**: Ejecuta ESLint antes de los commits.
- **Compilación de TypeScript**: Asegura que TypeScript compile sin errores.
- **Todas las Pruebas Pasando**: Asegura que todas las pruebas pasen antes del despliegue.
- **Revisión de Código**: Revisa el código para el cumplimiento de los estándares.

## Despliegue Serverless

### Configuración de AWS Lambda

- **Manejador Lambda**: El punto de entrada es `src/lambda.ts`.
- **Serverless HTTP**: Usa `serverless-http` para envolver la aplicación Express.
- **Environment Variables**: Configura las variables de entorno en `serverless.yml`.

### Framework Serverless

- **Archivo de Configuración**: `serverless.yml` define la configuración de Lambda.
- **Comando de Construcción**: Usa `npm run build:lambda` para construcciones de Lambda.
- **Despliegue**: Despliega usando el CLI de Serverless Framework.

```typescript
// lambda.ts
import { APIGatewayProxyEvent, APIGatewayProxyResult, Context } from 'aws-lambda';
import serverless from 'serverless-http';
import { app } from './index';

const serverlessHandler = serverless(app);

export const handler = async (
  event: APIGatewayProxyEvent,
  context: Context
): Promise<APIGatewayProxyResult> => {
  context.callbackWaitsForEmptyEventLoop = false;
  return await serverlessHandler(event, context) as APIGatewayProxyResult;
};
```

Este documento sirve como base para mantener la calidad y consistencia del código en la aplicación backend de LTI. Todos los miembros del equipo deben seguir estas prácticas para asegurar un código base mantenible, escalable y testeable.
