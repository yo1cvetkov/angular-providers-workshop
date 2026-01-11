# Angular Providers Workshop

## 📋 Overview

This workshop provides everything you need to conduct a comprehensive 60-minute Angular Providers workshop. In includes:
- **Complete Demo Application** - Task Manager app demonstrating all provider patterns
- **Workshop Plan** - Detailed 60-minute timeline with talking points
- **Code Examples** - Comprehensive reference of all provider patterns
- **Production Examples** - Real-world code samples for discussion

## 🎯 Workshop Objectives

By the end of this workshop, your team will: 

1. Understand Angular's dependency injection system
2. Know when to use each provider type
3. Implement multi-provider patterns for extensibility
4. Apply proper provider scoping
5. Avoid common anti-patterns
6. Write testable, maintainable Angular services

## 📁 Package Structure

```
angular-providers-workshop/
├── README.md                          # This file
├── WORKSHOP_PLAN.md                   # Detailed 60-minute plan
├── PROVIDER_EXAMPLES.md               # Comprehensive code examples
├── app/                               # Demo application
│   ├── app.config.ts                 # App configuration with providers
│   ├── core/
│   │   ├── services/
│   │   │   ├── task.service.ts       # Root-level state management
│   │   │   ├── api.service.ts        # HTTP service
│   │   │   └── notification.service.ts # User feedback
│   │   ├── interceptors/
│   │   │   └── api.interceptor.ts    # HTTP interceptor
│   │   └── tokens/
│   │       └── app.tokens.ts         # InjectionToken examples
│   ├── features/
│   │   └── tasks/
│   │       ├── components/
│   │       │   └── task-list.component.ts # Component with providers
│   │       ├── services/
│   │       │   ├── task-filter.service.ts # Component-level service
│   │       │   └── task-validator.service.ts # Multi-provider consumer
│   │       └── tasks-feature.module.ts # Module with all patterns
│   └── shared/
│       ├── models/
│       │   └── task.model.ts         # Type definitions
│       └── validators/
│           └── validator.interface.ts # Multi-provider validators
└── presentation/
    └── [Presentation files to be generated]
```

## 🚀 Quick Start

### 1. Review the Workshop Plan

Start by reading `WORKSHOP_PLAN.md` to understand:
- The 60-minute timeline
- Key concepts to cover
- Demo flow
- Discussion points

### 2. Study the Demo Application

The demo application (`app/` directory) demonstrates:

#### ✅ Root-Level Providers
- `TaskService` - State management singleton
- `NotificationService` - User feedback system
- `ApiService` - HTTP operations

#### ✅ Injection Tokens
- `APP_CONFIG` - Application configuration
- `STORAGE_CONFIG` - Storage preferences
- `API_CONFIG` - API settings

#### ✅ Component-Level Providers
- `TaskFilterService` - Isolated per-component filter state

#### ✅ Multi-Providers
- `TASK_VALIDATOR` token with multiple validator implementations
- `TaskValidatorService` consuming all validators

#### ✅ HTTP Interceptors
- `apiInterceptor` - Functional interceptor pattern

### 3. Review Code Examples

`PROVIDER_EXAMPLES.md` contains:
- All provider types with examples
- Provider scopes explained
- Advanced patterns (multi-providers, forwardRef, etc.)
- Real-world use cases
- Common mistakes and how to avoid them
- Testing strategies

### 4. Prepare Your Environment

For live coding portion, you'll need: 

```bash
# Install Angular CLI
npm install -g @angular/cli

# Create a new Angular project
ng new task-manager --standalone

# Navigate to project
cd task-manager

# Copy the demo files from this workshop package
# into your Angular project structure

# Start development server
ng serve
```

## 📊 Demo Application Features

The Task Manager Application demonstrates:

### Core Features
1. **Create/Read/Update/Delete Tasks**
2. **Task Status Management** (Todo, In Progress, Done)
3. **Priority Levels**
4. **Filtering and Search**
5. **Local Storage Persistence**
6. **Validation System**
7. **Notifications**
### Provider Patterns Demonstrated

| Feature | Pattern | Location |
|---------|---------|----------|
| Task Management | Root Singleton | `task.service.ts` |
| API Communication | Root Singleton | `api.service.ts` |
| Notifications | Root Singleton | `notification.service.ts` |
| App Configuration | Value Provider + InjectionToken | `app.tokens.ts` |
| API Configuration | Factory Provider | `app.config.ts` |
| HTTP Interceptor | Functional Interceptor | `api.interceptor.ts` |
| Task Filtering | Component-Level Provider | `task-filter.service.ts` |
| Validation System | Multi-Provider | `validator.interface.ts` |
| Validation Coordination | Multi-Provider Consumer | `task-validator.service.ts` |

## 🎓 Workshop Flow

### Part 1: Introduction (5 min)
- What are providers?
- Why dependency injection matters
- Quick overview of types

### Part 2: Basic Patterns (10 min)
- Class Providers
- Value Providers
- Factory Providers
- Existing Providers
- Show `app.tokens.ts` and `app.config.ts`

### Part 3: Provider Scopes (10 min)
- Root-level
- Module-level
- Component-level
- Compare `TaskService` vs `TaskFilterService`

### Part 4: Advanced Patterns (15 min)
- Multi-providers for extensibility
- Injection tokens for type safety
- Factory providers for conditional logic
- Implement validator plugin system:
  - Show `validator.interface.ts`
  - Show `task-validator.service.ts`
  - Add a new validator live
  - Show it automatically working

### Part 5: Real-World Application (12 min)
- Walk through the Task Manager App 
- Show provider registration in `tasks-feature.module.ts`
- Demonstrate component interaction
- Show how services coordinate
- Modify a provider configuration live
- DEMO: 
  - Add demo data
  - Create a task 
  - Filter tasks
  - Show validation in action

### Part 6: Best Practices & Anti-Patterns (5 min)
- Review good vs bad examples from `PROVIDER_EXAMPLES.md`
- Discuss production code examples from your codebase
- Common mistakes to avoid

### Part 7: Q&A (3 min)

## 🎯 Key Concepts to Emphasize

### 1. Provider Types Decision Tree

```
Need to provide a service?
├─ Is it a singleton for the entire app?
│  └─ Use: @Injectable({ providedIn: 'root' })
├─ Does it need configuration?
│  └─ Use: InjectionToken + Value Provider
├─ Does creation depend on runtime conditions?
│  └─ Use: Factory Provider
├─ Do you need multiple implementations?
│  └─ Use: Multi-Provider
└─ Does each component need its own instance?
   └─ Use: Component-level provider
```

### 2. Scope Selection Guide

| Scope | Use When | Example |
|-------|----------|---------|
| Root | App-wide singleton, shared state | AuthService, UserService |
| Module | Feature-specific, lazy-loaded | Feature services |
| Component | Isolated state, multiple instances | Form services, filters |

### 3. Multi-Provider Benefits

- **Plugin Architecture**: Add features without modifying core code
- **Open/Closed Principle**: Open for extension, closed for modification
- **Testability**: Test each implementation independently
- **Separation of Concerns**: Each plugin focuses on one responsibility

## 💡 Live Coding Suggestions

### Exercise 1: Add a New Validator (5 min)

```typescript
export class TagsValidator implements TaskValidator {
    readonly name = 'Tags Validator';
    
    validate(task: CreateTaskDto | Task) : ValidationResult {
        if(task.tags && task.tags.length > 5) {
            return {
                valid: false,
                errors: ['Maximum 5 tags allowed']
            }
        }
        
        return { valid: true };
    }
}

providers: [
    {
        provide: TASK_VALIDATOR, useClass: TagsValidator, multi: true
    }
]

// It automatically works: No changes to TaskValidatorService needed
```

### Exercise 2: Change Provider Scope (3 min)

```typescript
// Show what happens when you move TaskService from root to component level
// Demonstrate isolated state vs shared state

@Injectable({ providedIn: 'root' })
export class TaskService {}

@Component({
    providers: [TaskService]
})
```

### Exercise 3: Factory Provider with Config (5 min)
```typescript
export function createStorageService(config: AppConfig) {
    if(config.features.offlineMode) {
        return new LocalStorageService();
    } else {
        return new CloudStorageService();
    }
}

providers: [
    {
        provide: StorageService,
        useFactory: createStorageService,
        deps: [APP_CONFIG]
    }
]
```
### During the Workshop
- Keep introduction brief (5 min max)
- Dive into code quickly - developers learn by seeing
- Use Angular DevTools to visualize the injector hierarchy
- Encourage questions throughout
- Show errors and how to fix them (valuable learning!)
- Relate concepts to your team's existing codebase

### Live Coding Tips
- Use keyboard shortcuts visibly
- Explain what you're typing as you type
- Make intentional mistakes to demonstrate error messages
- Use console.log to show service instantiation
- Show the network tab for API calls
- Use Angular DevTools to inspect providers

## 🔍 Angular DevTools Demo Points

1. **Show Injector Hierarchy**
    - Open Angular DevTools
    - Navigate to Injector Graph
    - Show how services are provided at different levels

2. **Demonstrate Provider Resolution**
    - Select a component
    - Show its injector
    - Show how it resolves dependencies

3. **Visualize Service Instances**
    - Show that root services have one instance
    - Show that component services have multiple instances

## 📚 Additional Resources

### Official Documentation
- [Angular DI Guide](https://angular.io/guide/dependency-injection)
- [Angular Providers Guide](https://angular.io/guide/providers)
- [Hierarchical Injectors](https://angular.io/guide/hierarchical-dependency-injection)

### Workshop Materials
- `WORKSHOP_PLAN.md` - Detailed timeline
- `PROVIDER_EXAMPLES.md` - Code reference
- Demo Application - Working examples

### Follow-up Exercises
1. **Beginner**: Refactor a service to use `providedIn: 'root'`
2. **Intermediate**: Create a configuration service with InjectionToken
3. **Advanced**: Implement a plugin system using multi-providers
4. **Expert**: Build a dynamic feature loader

## ❓ Common Questions & Answers

### Q: When should I use providedIn vs module providers?
**A**: Use `providedIn: 'root'` for singletons (recommended, tree-shakeable). Use module providers when you need control over instantiation timing or for feature modules.

### Q: Can I have multiple providers for the same token?
**A**: Yes, with `multi: true`. All implementations will be injected as an array. Without `multi: true`, the last provider wins.

### Q: What's the difference between useValue and useFactory?
**A**: `useValue` is for static values known at compile time. `useFactory` is for values that need to be computed at runtime.

### Q: When should I use component-level providers?
**A**: When each component instance needs its own service instance (isolated state) and when you want the service lifecycle tied to the component.

### Q: How do I test code with providers?
**A**: Use `TestBed.configureTestingModule()` to override providers with mocks or test values.

## 🎯 Success Metrics

After the workshop, team members should be able to:
- [ ] Explain the four main provider types
- [ ] Choose the appropriate provider scope
- [ ] Use InjectionToken for type-safe configuration
- [ ] Implement a multi-provider system
- [ ] Avoid common provider anti-patterns
- [ ] Refactor existing code to use better provider patterns

## 📞 Support

For questions or feedback about this workshop:
- Review the code examples in this package
- Check Angular's official documentation
- Discuss patterns with your team

## 🔄 Next Steps

After the workshop:
1. **Immediate**: Identify one service in your codebase to refactor
2. **This week**: Implement InjectionToken for configuration
3. **This sprint**: Add a multi-provider system where appropriate
4. **Ongoing**: Review PRs for proper provider usage

## 📄 License

This workshop material is provided for educational purposes.

---

**Happy Learning! 🚀**


