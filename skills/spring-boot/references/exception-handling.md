# Exception Handling and Utility Classes

- [Where to place exception classes](#where-to-place-exception-classes)
- [Where to place utility classes](#where-to-place-utility-classes)
- [Where to place GlobalExceptionHandler](#where-to-place-globalexceptionhandler)
- [Base exception classes](#base-exception-classes)
- [Module-specific domain exceptions](#module-specific-domain-exceptions)

## Where to place exception classes

Place exception classes in the following locations based on their scope:

- **Cross-cutting base exceptions** (e.g., `DomainException`, `ResourceNotFoundException`): place in the `shared/` package so every module can depend on them.
- **Module-specific domain exceptions** (e.g., `InvalidUserCreationException`): place inside the relevant module's `domain/` package.

```
dev.sivalabs.projectname/
├── shared/
│   ├── exceptions/
│   │   ├── DomainException.java          # Base class for all domain/business exceptions
│   │   └── ResourceNotFoundException.java # Thrown when a requested resource does not exist
│   └── utils/
│       └── StringUtils.java              # Cross-cutting utility classes
├── users/
│   └── domain/
│       └── InvalidUserCreationException.java  # Module-specific domain exception
└── config/
    └── GlobalExceptionHandler.java       # Centralised HTTP exception mapping
```

## Where to place utility classes

Place utility/helper classes in `shared/utils/` so they are available to all modules without creating circular dependencies.

```
dev.sivalabs.projectname/
└── shared/
    └── utils/
        └── StringUtils.java
```

## Where to place GlobalExceptionHandler

Place `GlobalExceptionHandler` in the top-level `config/` package alongside other global configuration classes (`WebMvcConfig`, `SecurityConfig`).  
This keeps all infrastructure/framework-level concerns in one place and separate from domain logic.

```
dev.sivalabs.projectname/
└── config/
    ├── WebMvcConfig.java
    ├── SecurityConfig.java
    └── GlobalExceptionHandler.java
```

## Base exception classes

Create these two base exception classes in `shared/exceptions/`:

### DomainException.java

```java
package dev.sivalabs.projectname.shared.exceptions;

public class DomainException extends RuntimeException {

    public DomainException(String message) {
        super(message);
    }

    public DomainException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

### ResourceNotFoundException.java

```java
package dev.sivalabs.projectname.shared.exceptions;

public class ResourceNotFoundException extends RuntimeException {

    public ResourceNotFoundException(String message) {
        super(message);
    }

    public ResourceNotFoundException(String resourceName, Object id) {
        super(resourceName + " not found with id: " + id);
    }
}
```

## Module-specific domain exceptions

Create domain exceptions inside the module's `domain/` package and extend `DomainException`:

```java
package dev.sivalabs.projectname.users.domain;

import dev.sivalabs.projectname.shared.exceptions.DomainException;

public class InvalidUserCreationException extends DomainException {

    public InvalidUserCreationException(String message) {
        super(message);
    }
}
```

These module-specific exceptions are then caught by the `GlobalExceptionHandler` via the `DomainException` handler, so no extra handler method is required.
