# GitHub Copilot Instructions

## Priority Guidelines

When generating code for this repository:

1. **Version Compatibility**: Always detect and respect the exact versions of languages, frameworks, and libraries used in this project
2. **Context Files**: Prioritize patterns and standards defined in the .github/instructions directory
3. **Codebase Patterns**: When context files don't provide specific guidance, scan the codebase for established patterns
4. **Architectural Consistency**: Maintain our Domain-Driven Design (DDD) architectural style and established boundaries
5. **Code Quality**: Prioritize maintainability, performance, security, accessibility, and testability in all generated code

## Technology Stack

### Backend (.NET)

- **Target Framework**: .NET 8.0 (net8.0)
- **C# Language Version**: Latest (C# 12+)
- **Nullable Reference Types**: Enabled
- **Implicit Usings**: Enabled

**Key Packages:**

- MediatR (12.5.0) - CQRS pattern
- FastEndpoints (6.1.0) - API endpoints
- FluentValidation (12.0.0) - Validation
- Entity Framework Core (9.0.5) - ORM
- ErrorOr (2.0.1) - Result pattern
- Ardalis.SmartEnum (8.2.0) - Smart enums
- Serilog - Structured logging
- Rebus - Message bus (Azure Service Bus)
- Microsoft.Identity.Web - Authentication
- .NET Aspire (13.1.0) - Cloud-native orchestration

### Frontend (Angular)

- **Angular Version**: 19.1.x (standalone components by default)
- **TypeScript Target**: ES2022
- **Module Resolution**: Bundler
- **Strict Templates**: Enabled

**Key Packages:**

- PrimeNG (19.0.5) - UI components
- @azure/msal-angular (4.0.7) - Authentication
- ngx-translate - Internationalization (pl, en)
- RxJS (7.x) - Reactive programming
- Tailwind CSS - Styling

## Project Architecture

### Backend Layer Structure

```
src/backend/
├── WeTeach.Api/              # Presentation layer (FastEndpoints, Controllers)
├── WeTeach.Application/      # Application layer (Commands, Queries, Handlers)
├── WeTeach.Domain/           # Domain layer (Aggregates, Entities, Value Objects)
├── WeTeach.Infrastructure/   # Infrastructure layer (Repositories, External Services)
├── WeTeach.Contracts/        # DTOs and API contracts
├── WeTeach.AppHost/          # .NET Aspire orchestration
└── WeTeach.ServiceDefaults/  # Shared service configurations
```

### Frontend Structure

```
src/frontend/src/app/
├── core/                     # Core services, guards, interceptors
│   ├── components/           # Core components (nav-bar, etc.)
│   ├── guards/               # Route guards
│   ├── models/               # Core interfaces
│   └── services/             # Core services
├── modules/                  # Feature modules (lazy-loaded)
│   ├── teacher/              # Teacher feature
│   ├── student/              # Student feature
│   ├── lessons/              # Lessons feature
│   └── ...                   # Other features
└── shared/                   # Shared components, pipes, directives
    ├── components/           # Reusable components
    ├── directives/           # Custom directives
    ├── models/               # Shared interfaces
    ├── pipes/                # Custom pipes
    └── services/             # Shared services
```

## Domain-Driven Design Patterns

### Aggregate Structure

Follow the established pattern for aggregates:

```csharp
// Location: WeTeach.Domain/{AggregateName}Aggregate/
public class AggregateName : AggregateRoot<AggregateNameId, Guid>
{
    // Private collections
    private readonly List<EntityType> _entities = [];

    // Public read-only access
    public IReadOnlyList<EntityType> Entities => _entities.AsReadOnly();

    // Factory method
    public static AggregateName Create(...) { }

    // Private constructor
    private AggregateName(...) : base(id) { }
}
```

### Value Object Pattern

```csharp
// Location: WeTeach.Domain/{AggregateName}Aggregate/ValueObjects/
public sealed class AggregateNameId : AggregateRootId<Guid>
{
    public AggregateNameId(Guid value) : base(value) { }

    public static AggregateNameId CreateUnique() => new(Guid.NewGuid());
    public static AggregateNameId Create(Guid value) => new(value);
}
```

### Domain Events

Add domain events for significant state changes:

```csharp
aggregate.AddDomainEvent(new AggregateCreatedEvent(aggregate));
```

## Application Layer Patterns

### CQRS with MediatR

**Commands:**

```csharp
// Location: WeTeach.Application/{Feature}/Commands/{CommandName}/
public sealed record CommandName(...) : IRequest<ErrorOr<ResultType>>;

public class CommandNameHandler : IRequestHandler<CommandName, ErrorOr<ResultType>>
{
    public async Task<ErrorOr<ResultType>> Handle(CommandName request, CancellationToken ct)
    {
        // Implementation
    }
}
```

### FastEndpoints Pattern

```csharp
// Location: WeTeach.Api/Endpoints/{Feature}/
[HttpPost("route/{param:type}"), AllowAnonymous]
public class EndpointName : EndpointWithErrorHandling<RequestType, ResponseType>
{
    private readonly ISender _sender;

    public EndpointName(ISender sender) => _sender = sender;

    public override async Task HandleAsync(RequestType request, CancellationToken ct)
    {
        var result = await _sender.Send(command, ct);
        await result.SwitchAsync(
            success => SendOkAsync(response, ct),
            error => ProblemAsync(error, ct));
    }
}
```

## Code Quality Standards

### Maintainability

- Write self-documenting code with clear naming
- Follow established patterns for consistency
- Keep functions focused on single responsibilities
- Use file-scoped namespace declarations
- Use pattern matching and switch expressions

### Performance

- Use `async`/`await` for I/O-bound operations
- Use `IReadOnlyList<T>` for collection exposure
- Apply caching with LazyCache where appropriate
- Use Dapper for complex read queries

### Security

- Implement authorization at the aggregate level
- Use parameterized queries (EF Core/Dapper)
- Validate input in the Application layer
- Follow Microsoft Identity Web patterns for authentication

### Testability

- Follow `MethodName_Condition_ExpectedResult()` naming pattern
- Use constructor injection for dependencies
- Domain logic belongs in the Domain layer
- Do not emit "Act", "Arrange" or "Assert" comments

## Angular Development Standards

### Component Pattern

```typescript
// Standalone components by default
@Component({
  selector: 'app-component-name',
  templateUrl: './component-name.component.html',
  imports: [CommonModule, ...],  // Import dependencies directly
})
export class ComponentNameComponent implements OnInit {
  // Use inject() for DI in standalone components
  private readonly service = inject(ServiceName);

  // Use signals for reactive state (Angular 19+)
  data = signal<DataType | null>(null);
  isLoading = signal(false);
}
```

### Service Pattern

```typescript
@Injectable({
  providedIn: "root",
})
export class ServiceName {
  private readonly http = inject(HttpClient);

  getData(): Observable<DataType> {
    return this.http.get<DataType>(`${environment.apiUrl}/endpoint`);
  }
}
```

### Routing Pattern

- Use lazy loading for feature modules
- Apply route guards (MsalGuard, custom guards)
- Follow `loadChildren` pattern for module routes

## Naming Conventions

### .NET

- **Classes/Methods/Properties**: PascalCase
- **Private fields**: camelCase with no prefix (e.g., `_attendees`)
- **Interfaces**: Prefix with "I" (e.g., `IUserService`)
- **Commands**: `{Action}{Entity}Command`
- **Handlers**: `{Command}Handler`
- **Endpoints**: `{Action}{Entity}Endpoint`
- **Aggregates**: `{Name}Aggregate` folder, `{Name}.cs` file

### Angular/TypeScript

- **Components**: kebab-case files, PascalCase classes
- **Services**: camelCase files, PascalCase classes
- **Interfaces**: Prefix with "I" (e.g., `IUserData`)
- **Routes**: kebab-case paths

## Documentation Requirements

- Match the level and style of comments found in existing code
- Use XML doc comments for public APIs in .NET
- Document non-obvious behavior and business rules
- Follow existing patterns for parameter descriptions

## Error Handling

### .NET (ErrorOr Pattern)

```csharp
// Return errors instead of throwing exceptions
return Error.Validation("Error.Code", "Error description");

// Handle results with Switch/SwitchAsync
result.SwitchAsync(
    success => HandleSuccess(success),
    error => HandleError(error));
```

### Angular

- Use RxJS operators (`catchError`) for error handling
- Implement global error interceptors
- Show user feedback via toast notifications (ngx-toastr)

## Azure Integration

- @azure Rule - Use Azure Best Practices: When generating code for Azure, running terminal commands for Azure, or performing operations related to Azure, invoke your `azure_development-get_best_practices` tool if available.
- Use Azure Service Bus with Rebus for messaging
- Use Azure Blob Storage for file storage
- Authenticate with Microsoft Entra ID (External ID)
- Use Azure OpenAI for AI features
- Deploy with Terraform (see infrastructure/terraform/)

## Ubiquitous Language

Reference the domain language defined in `docs/UbiquitousLanguage.md`:

- **User (Użytkownik)**: Person with an account, can have roles: Teacher, Student, FamilyMember
- **Teacher (Nauczyciel)**: Provides private lessons
- **Student (Uczeń)**: Learns through the application
- **PrivateLesson (Korepetycje)**: Knowledge transfer at specific time, place, topic, and duration
- **Lesson (Lekcja)**: Self-study lesson prepared by teacher
- **Drive (Dysk)**: File storage space for lessons
- **MediaFile (Plik)**: Educational material file

## Version Control

- Follow Semantic Versioning
- Document breaking changes in changelogs
- Use conventional commit messages when applicable

## General Best Practices

- Scan the codebase thoroughly before generating any code
- Respect existing architectural boundaries without exception
- Match the style and patterns of surrounding code
- When in doubt, prioritize consistency with existing code over external best practices
- Reference instruction files in `.github/instructions/` for detailed guidelines:
  - `angular.instructions.md` - Angular-specific patterns
  - `csharp.instructions.md` - C# coding standards
  - `dotnet-architecture-good-practices.instructions.md` - DDD and architecture guidelines
