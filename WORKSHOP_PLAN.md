# Angular Providers Workshop - Complete Plan (60 minutes)

## Workshop Overview
**Duration:** 60 minutes  
**Project:** Task Management System with Advanced Provider Patterns  
**Goal:** Master Angular's Dependency Injection system through practical examples

---

## Timeline & Structure

### 1. Introduction (5 minutes)
- What are Providers?
- The Dependency Injection System
- Why Providers Matter

### 2. Basic Provider Concepts (10 minutes)
- Provider Types Overview
- Root vs Feature Module Providers
- Live Demo: Basic Service Registration

### 3. Architectural Patterns (10 minutes)
- Service Hierarchies
- Injection Tokens
- Factory Providers
- Live Demo: Implementing Multi-Level Services

### 4. Practical Application - Task Manager (15 minutes)
- Building the Demo App
- Implementing Real-World Patterns:
    - API Service with HTTP Interceptors
    - State Management Service
    - Feature-Specific Providers
    - Configuration Services

### 5. Advanced Patterns (12 minutes)
- Multi-Providers (Plugin Architecture)
- forwardRef and Circular Dependencies
- Value Providers vs Factory Providers
- useExisting for Aliasing
- Live Demo: Plugin System Implementation

### 6. Best Practices & Anti-Patterns (5 minutes)
- Good vs Bad Provider Registration
- Performance Considerations
- Testing Strategies
- Code Examples from Production

### 7. Q&A and Wrap-up (3 minutes)

---

## Detailed Content

### Part 1: Introduction (5 min)

**Key Points:**
- Providers are recipes that tell Angular how to create dependencies
- Core of Angular's DI system
- Enable loose coupling, testability, and modularity

**Quick Example:**
```typescript
// Without DI
class MyComponent {
  service = new MyService(); // Tight coupling
}

// With DI
class MyComponent {
  constructor(private service: MyService) {} // Loose coupling
}
```

---

### Part 2: Basic Provider Concepts (10 min)

**Provider Types:**

1. **Class Provider (useClass)**
```typescript
{ provide: MyService, useClass: MyService }
// Shorthand: providers: [MyService]
```

2. **Value Provider (useValue)**
```typescript
{ provide: API_URL, useValue: 'https://api.example.com' }
```

3. **Factory Provider (useFactory)**
```typescript
{
  provide: ConfigService,
  useFactory: (http: HttpClient) => new ConfigService(http),
  deps: [HttpClient]
}
```

4. **Existing Provider (useExisting)**
```typescript
{ provide: NewLogger, useExisting: Logger }
```

**Scope Levels:**
- `providedIn: 'root'` - Application-wide singleton
- `providedIn: 'platform'` - Shared across apps
- `providedIn: 'any'` - Each lazy-loaded module gets instance
- Module providers - Feature-specific instances

**Live Demo:** Show Task Service registration variations

---

### Part 3: Architectural Patterns (10 min)

**1. Service Hierarchies**
```typescript
@Injectable({ providedIn: 'root' })
export class GlobalTaskService {}

@Component({
  providers: [LocalTaskService] // Component-level
})
export class TaskComponent {}
```

**2. Injection Tokens**
```typescript
export const API_CONFIG = new InjectionToken<ApiConfig>('api.config');

providers: [
  { provide: API_CONFIG, useValue: { baseUrl: '...' } }
]
```

**3. Factory Patterns**
```typescript
export function taskServiceFactory(config: AppConfig) {
  return config.useLocalStorage 
    ? new LocalTaskService() 
    : new RemoteTaskService();
}
```

**Live Demo:** Build configuration-based service selection

---

### Part 4: Practical Application - Task Manager (15 min)

**App Features:**
- Create/Read/Update/Delete Tasks
- Filter by Status
- Priority Management
- Local Storage + API Simulation
- Notification System

**Provider Architecture:**

1. **Core Services (Root Level)**
    - TaskService (State Management)
    - ApiService (HTTP Operations)
    - NotificationService (User Feedback)

2. **Feature Services (Component Level)**
    - TaskFilterService (Per-component filtering)
    - TaskValidationService (Form validation)

3. **Configuration**
    - APP_CONFIG token (Application settings)
    - STORAGE_CONFIG token (Storage preferences)

**Code Walkthrough:**
- Show how services interact
- Demonstrate provider scope impact
- Live modifications and hot reload

---

### Part 5: Advanced Patterns (12 min)

**1. Multi-Providers (Plugin Architecture)**
```typescript
export const TASK_VALIDATOR = 
  new InjectionToken<TaskValidator>('task.validator');

providers: [
  { provide: TASK_VALIDATOR, useClass: LengthValidator, multi: true },
  { provide: TASK_VALIDATOR, useClass: PriorityValidator, multi: true }
]

// Inject all validators
constructor(@Inject(TASK_VALIDATOR) private validators: TaskValidator[]) {}
```

**2. forwardRef (Circular Dependencies)**
```typescript
@Injectable()
export class ParentService {
  constructor(@Inject(forwardRef(() => ChildService)) private child) {}
}
```

**3. Value vs Factory Providers**
```typescript
// Value: Immediate, for constants
{ provide: CONFIG, useValue: { debug: true } }

// Factory: Lazy, for computed values
{ 
  provide: CONFIG, 
  useFactory: () => ({ 
    debug: environment.production === false 
  })
}
```

**4. useExisting (Aliasing)**
```typescript
// Useful for legacy code migration
{ provide: OldTaskService, useExisting: TaskService }
```

**Live Demo:** Implement validator plugin system in Task Manager

---

### Part 6: Best Practices & Anti-Patterns (5 min)

**✅ GOOD PRACTICES:**

1. **Use providedIn: 'root' for singletons**
```typescript
@Injectable({ providedIn: 'root' })
export class TaskService {}
```
*Why:* Tree-shakeable, no need to add to providers array

2. **Use Injection Tokens for configuration**
```typescript
export const API_CONFIG = new InjectionToken<ApiConfig>('api.config');
```
*Why:* Type-safe, clear dependency declaration

3. **Factory providers for conditional logic**
```typescript
{
  provide: StorageService,
  useFactory: (config: AppConfig) => 
    config.useCloud ? new CloudStorage() : new LocalStorage(),
  deps: [APP_CONFIG]
}
```
*Why:* Runtime flexibility, testable

4. **Component-level providers for isolated state**
```typescript
@Component({
  providers: [FilterService] // New instance per component
})
```
*Why:* Prevents state leakage between instances

**❌ ANTI-PATTERNS:**

1. **Manual instantiation in constructors**
```typescript
// BAD
constructor() {
  this.service = new TaskService();
}

// GOOD
constructor(private service: TaskService) {}
```

2. **Providing everything at root level**
```typescript
// BAD: Bloated root injector
@NgModule({
  providers: [
    FeatureService1, // Only used in one feature
    FeatureService2, // Only used in one feature
    ...
  ]
})

// GOOD: Feature-level provision
@NgModule({
  imports: [
    FeatureModule // Provides its own services
  ]
})
```

3. **Circular dependencies without forwardRef**
```typescript
// BAD: Will fail
export class ServiceA {
  constructor(private b: ServiceB) {}
}
export class ServiceB {
  constructor(private a: ServiceA) {} // Circular!
}

// GOOD: Use forwardRef or redesign
export class ServiceA {
  constructor(@Inject(forwardRef(() => ServiceB)) private b) {}
}
```

4. **Using string tokens instead of InjectionToken**
```typescript
// BAD: No type safety
{ provide: 'API_URL', useValue: 'https://...' }

// GOOD: Type-safe
export const API_URL = new InjectionToken<string>('api.url');
{ provide: API_URL, useValue: 'https://...' }
```

**Real Production Examples:**

*Example 1: Good Practice*
```typescript
// From our dashboard module
export const DASHBOARD_CONFIG = new InjectionToken<DashboardConfig>('dashboard.config');

@NgModule({
  providers: [
    {
      provide: DASHBOARD_CONFIG,
      useFactory: (auth: AuthService) => ({
        widgets: auth.user.permissions.includes('admin') 
          ? [...adminWidgets, ...userWidgets]
          : userWidgets
      }),
      deps: [AuthService]
    }
  ]
})
```

*Example 2: Multi-Provider for Extensibility*
```typescript
// From our plugin system
export const CHART_RENDERER = new InjectionToken<ChartRenderer>('chart.renderer');

// Core module
providers: [
  { provide: CHART_RENDERER, useClass: BarChartRenderer, multi: true },
  { provide: CHART_RENDERER, useClass: LineChartRenderer, multi: true }
]

// Plugin module can add more
providers: [
  { provide: CHART_RENDERER, useClass: CustomChartRenderer, multi: true }
]
```

---

### Part 7: Q&A and Wrap-up (3 min)

**Key Takeaways:**
1. Providers are the foundation of Angular's DI system
2. Choose the right scope: root, module, or component
3. Use Injection Tokens for configuration
4. Multi-providers enable plugin architectures
5. Factory providers add runtime flexibility

**Resources:**
- Demo Code: [GitHub Link]
- Angular DI Documentation
- Your team's coding standards

**Action Items:**
- Review the demo app code
- Identify opportunities in current projects
- Refactor one service using advanced patterns

---

## Demo App File Structure

```
task-manager-app/
├── src/
│   ├── app/
│   │   ├── core/
│   │   │   ├── services/
│   │   │   │   ├── task.service.ts
│   │   │   │   ├── api.service.ts
│   │   │   │   └── notification.service.ts
│   │   │   ├── interceptors/
│   │   │   │   └── api.interceptor.ts
│   │   │   └── tokens/
│   │   │       └── app.tokens.ts
│   │   ├── features/
│   │   │   └── tasks/
│   │   │       ├── components/
│   │   │       │   ├── task-list/
│   │   │       │   ├── task-form/
│   │   │       │   └── task-filter/
│   │   │       └── services/
│   │   │           ├── task-filter.service.ts
│   │   │           └── task-validator.service.ts
│   │   ├── shared/
│   │   │   ├── validators/
│   │   │   │   └── validator.interface.ts
│   │   │   └── models/
│   │   │       └── task.model.ts
│   │   └── app.config.ts
│   └── main.ts
```

---

## Workshop Materials Checklist

- [ ] PowerPoint Presentation (25 slides)
- [ ] Complete Demo Application Code
- [ ] Code Snippets Document
- [ ] Good vs Bad Examples Document
- [ ] Production Code Examples
- [ ] Exercise Challenges
- [ ] Quick Reference Guide
- [ ] Additional Resources List

---

## Follow-up Exercise Ideas

1. **Beginner:** Refactor a service to use providedIn: 'root'
2. **Intermediate:** Implement a configuration service with InjectionToken
3. **Advanced:** Create a plugin system using multi-providers
4. **Expert:** Build a dynamic module loader with custom providers

---

## Notes for Presenter

- Keep introduction brief, dive into code quickly
- Use live coding for all demos
- Show errors and how to fix them
- Encourage questions throughout
- Have backup slides for common questions
- Test all code examples before workshop
- Prepare production code examples that team will recognize
- Have VS Code ready with demo app open
- Use Angular DevTools to show dependency tree