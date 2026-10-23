# Backend Code Style Practices

General, language-agnostic code style and design conventions for backend code.
Derived from the [Paysera PHP Style Guide](https://github.com/paysera/php-style-guide), with PHP- and
Symfony-specific details removed so the rules apply to any backend language (PHP, Java, Kotlin, C#, Go,
TypeScript, Python, etc.).

Examples use TypeScript-like pseudocode. Translate them to your language's idioms. Where a language has an
official convention (casing, file layout, formatter), the language convention takes precedence over formatting
rules here, but never over the design rules.

## Table of contents

<!-- toc -->

- [General](#general)
  * [Basics](#basics)
    + [Legacy code](#legacy-code)
    + [One type per file](#one-type-per-file)
    + [Small, understandable units](#small-understandable-units)
    + [Language and runtime versions](#language-and-runtime-versions)
    + [Framework versions](#framework-versions)
    + [New projects](#new-projects)
- [Code style](#code-style)
  * [Naming](#naming)
    + [Full names](#full-names)
    + [Classes are nouns](#classes-are-nouns)
    + [No `Service` suffix](#no-service-suffix)
    + [Application names](#application-names)
    + [Interface naming](#interface-naming)
    + [Properties are nouns or adjectives](#properties-are-nouns-or-adjectives)
    + [Methods are verbs; questions return booleans](#methods-are-verbs-questions-return-booleans)
    + [Constants](#constants)
    + [Events and listeners](#events-and-listeners)
    + [Routes and CLI commands](#routes-and-cli-commands)
    + [Namespaces, packages, modules](#namespaces-packages-modules)
  * [Types](#types)
    + [Strict types over documentation](#strict-types-over-documentation)
    + [One type per return value and argument](#one-type-per-return-value-and-argument)
    + [Nothing means nothing](#nothing-means-nothing)
    + [Honest nullability](#honest-nullability)
    + [Optional and nullable arguments](#optional-and-nullable-arguments)
    + [Type hint the narrowest interface](#type-hint-the-narrowest-interface)
    + [Accept the narrowest arguments](#accept-the-narrowest-arguments)
    + [Fluent interfaces](#fluent-interfaces)
    + [Types in public library APIs](#types-in-public-library-apis)
  * [Comments](#comments)
    + [No AI-generated comments](#no-ai-generated-comments)
    + [Prefer code over comments](#prefer-code-over-comments)
    + [No duplicated documentation](#no-duplicated-documentation)
    + [Comment styles](#comment-styles)
    + [No metadata comments](#no-metadata-comments)
    + [No commented-out code](#no-commented-out-code)
    + [Deprecations](#deprecations)
  * [Expressions and control flow](#expressions-and-control-flow)
    + [Strict comparison](#strict-comparison)
    + [Explicit `null` checks](#explicit-null-checks)
    + [No conversion to boolean](#no-conversion-to-boolean)
    + [No comparison to boolean literals](#no-comparison-to-boolean-literals)
    + [Static value on the right](#static-value-on-the-right)
    + [Return boolean expressions directly](#return-boolean-expressions-directly)
    + [No assignments in conditions](#no-assignments-in-conditions)
    + [Check things explicitly](#check-things-explicitly)
    + [No aliases](#no-aliases)
    + [Removing prefixes and suffixes](#removing-prefixes-and-suffixes)
    + [No unnecessary structures](#no-unnecessary-structures)
    + [Logical operators](#logical-operators)
  * [Variables](#variables)
    + [No unnecessary variables](#no-unnecessary-variables)
    + [No reuse of variables](#no-reuse-of-variables)
  * [Classes and visibility](#classes-and-visibility)
    + [Most restrictive visibility](#most-restrictive-visibility)
    + [No public mutable properties](#no-public-mutable-properties)
    + [Defaults in the constructor](#defaults-in-the-constructor)
    + [Parent constructor first](#parent-constructor-first)
    + [Member order](#member-order)
    + [String conversion](#string-conversion)
    + [Entities](#entities)
  * [Errors and exceptions](#errors-and-exceptions)
    + [Throwing](#throwing)
    + [Catching](#catching)
  * [Formatting](#formatting)
  * [Static analysis and warnings](#static-analysis-and-warnings)
- [Architecture and design](#architecture-and-design)
  * [Global state and dependencies](#global-state-and-dependencies)
    + [No global access](#no-global-access)
    + [Static methods](#static-methods)
    + [Dependency injection](#dependency-injection)
    + [No dynamic service lookup](#no-dynamic-service-lookup)
    + [Stateless services](#stateless-services)
    + [Configuration over hard-coding](#configuration-over-hard-coding)
    + [Composition over inheritance](#composition-over-inheritance)
    + [Module dependencies](#module-dependencies)
  * [Data representation](#data-representation)
    + [Objects over primitives](#objects-over-primitives)
    + [Normalize early, denormalize late](#normalize-early-denormalize-late)
    + [Objects, not identifiers, inside business logic](#objects-not-identifiers-inside-business-logic)
    + [One representation per concept](#one-representation-per-concept)
  * [Changing state and events](#changing-state-and-events)
    + [State changes in dedicated classes](#state-changes-in-dedicated-classes)
    + [Concrete methods over generic ones](#concrete-methods-over-generic-ones)
    + [Selecting implementations at run-time](#selecting-implementations-at-run-time)
    + [Extending entities without inheritance](#extending-entities-without-inheritance)
    + [Events notify, they do not command](#events-notify-they-do-not-command)
- [Persistence](#persistence)
  * [Transactions and persistence](#transactions-and-persistence)
    + [Transactions only in the top layer](#transactions-only-in-the-top-layer)
    + [One commit per unit of work](#one-commit-per-unit-of-work)
    + [Queries only in repositories](#queries-only-in-repositories)
    + [Repository method naming](#repository-method-naming)
    + [Repository return types](#repository-return-types)
    + [Repositories are injected](#repositories-are-injected)
    + [Do not prefetch and filter by the same relation](#do-not-prefetch-and-filter-by-the-same-relation)
    + [Date ranges](#date-ranges)
    + [Pagination](#pagination)
  * [Database](#database)
    + [Naming](#naming-1)
    + [Columns](#columns)
    + [IDs](#ids)
    + [Money in the database](#money-in-the-database)
    + [Mapping](#mapping)
    + [Migrations](#migrations)
- [Entry points and APIs](#entry-points-and-apis)
  * [Controllers, commands and workers](#controllers-commands-and-workers)
  * [REST APIs](#rest-apis)
- [Security](#security)
  * [Sensitive values](#sensitive-values)
- [Project setup and maintenance](#project-setup-and-maintenance)
  * [Configuration](#configuration)
    + [Constants or configuration](#constants-or-configuration)
    + [Configuration format and location](#configuration-format-and-location)
    + [Example configuration files](#example-configuration-files)
    + [Secrets](#secrets)
  * [Directory structure](#directory-structure)
    + [Module layout](#module-layout)
    + [Integration namespaces](#integration-namespaces)
    + [Tests](#tests)
    + [Example](#example)
  * [Working with legacy code](#working-with-legacy-code)
    + [New features in new code](#new-features-in-new-code)
    + [Current code and legacy code](#current-code-and-legacy-code)
    + [Interfaces and adapters](#interfaces-and-adapters)
  * [Libraries, versioning and dependencies](#libraries-versioning-and-dependencies)
    + [Semantic versioning](#semantic-versioning)
    + [Changelog](#changelog)
    + [Releases](#releases)
    + [Version constraints on our libraries](#version-constraints-on-our-libraries)
    + [Version constraints on third-party dependencies](#version-constraints-on-third-party-dependencies)
- [Other](#other)
  * [Acknowledgements](#acknowledgements)

<!-- tocstop -->

---

# General

## Basics

### Legacy code

When modifying legacy code that follows other conventions, we may keep the style already used there.
New files and new modules always follow this guide.

### One type per file

We put every class, interface or enum in its own file.

### Small, understandable units

We write self-explanatory code. A method should be understandable by reading its body alone.
If it is not, we split it and name the parts by their meaning.

### Language and runtime versions

- Applications use a supported (at least for security fixes) language runtime version.
- Shared libraries target the **lowest** runtime version used by any actively maintained project that depends on them.

### Framework versions

- We use only stable releases of frameworks, and only versions that are still supported.
- We prefer LTS versions. A newer non-LTS version is allowed only if the project will be upgraded often enough to
  stay supported, even when it is not actively developed.
- We keep at most two different major framework versions across our projects.

> **Why?** Every additional major version must be supported by all shared libraries and modules.

### New projects

We bootstrap new projects from the company skeleton/template for that project type (web, API, processing node),
not from scratch.

> **Why?** The skeleton contains fixes and improvements accumulated over years, and the basic features that shared
> libraries rely on.

---

# Code style

## Naming

### Full names

We use full words, not abbreviations or single letters.

```text
entityManager   not  em
exception       not  e, ex, err
request         not  req
response        not  res, resp
transaction     not  tx, trx
configuration   not  cfg, conf
index           not  i       (except trivial loop counters, if your team allows it)
```

Well-known acronyms that are words on their own are fine: `id`, `url`, `iban`, `html`, `json`.

> **Why?** Code is read far more often than written. Abbreviations save keystrokes once and cost understanding
> every time the code is read.

### Classes are nouns

Class names are nouns or noun phrases. They name *what the object is*, not *what it does once*.

```text
Correct:  TransferValidator, InvoiceNumberGenerator, UserProvider, PaymentProcessor
Wrong:    ValidateTransfer, GenerateInvoiceNumber, DoPayment
```

### No `Service` suffix

We never use `Service` (or `Helper`, `Util`, `Utils`, `Common`, `Misc`) as a class name suffix.
It says nothing about the responsibility.

For classes that do work, we use a suffix that describes the role, usually an agent noun ending in `-er` / `-or`:

| Suffix        | Responsibility                                          |
|---------------|---------------------------------------------------------|
| `Manager`     | Changes state of a specific entity, validates, dispatches events |
| `Provider`    | Supplies data or objects                                |
| `Resolver`    | Decides/selects something from input                    |
| `Normalizer`  | Converts objects to raw structures (and `Denormalizer` back) |
| `Validator`   | Validates                                               |
| `Updater`     | Updates something                                       |
| `Processor`   | Runs a multi-step process                               |
| `Registry`    | Holds a collection of implementations, selects one      |
| `Factory`     | Creates objects                                         |
| `Client`      | Talks to an external system                             |
| `Repository`  | Reads/writes persisted entities                         |
| `Controller`  | Handles incoming HTTP requests                          |
| `Command`     | Handles a CLI invocation                                |
| `Worker` / `Consumer` | Handles a queue message or job                  |
| `Listener`    | Reacts to an event                                      |

```text
Wrong:   PageService, UserService, PaymentHelper, StringUtils
Correct: PageProvider, UserRegistrationManager, PaymentProcessor, SlugGenerator
```

Plain object names (`Page`, `User`, `Transfer`) are reserved for entities, value objects and DTOs.

### Application names

Application names (in configuration, logging, error tracking, metrics) do not contain repository naming prefixes
like `app-` or redundant suffixes like `-service`.

```text
Wrong:   app-user-api-service
Correct: user-api
```

> **Why?** `app-` is a repository naming convention, not part of the name. In a service architecture every
> application is already a service.

### Interface naming

We follow one consistent convention per codebase. If the language has an idiom (`IReader` in C#,
`Reader` in Go, no prefix in Java/Kotlin), we follow it. Otherwise we suffix with `Interface`.

The rule that matters: an interface and its default implementation must never clash, and the interface
name must describe the abstraction, not the implementation (`TokenStorageInterface`, not `RedisInterface`).

### Properties are nouns or adjectives

Property and field names are nouns or adjectives. Not verbs, not questions.

```ts
class Account {
    private valid: boolean;        // NOT isValid
    private checkNeeded: boolean;  // NOT check, needsCheck
    private createdAt: DateTime;   // NOT create, created
}
```

The question form (`is`, `has`, `can`) belongs to the method that reads the property, not to the property itself.

### Methods are verbs; questions return booleans

- Methods that perform an action or return something are verbs: `calculateFee`, `sendNotification`, `findUser`.
- Methods that return a boolean are questions starting with `is`, `are`, `has`, `can`, `should`.
  Question methods **never have side effects** and **always return a boolean**.
- Getters use `get*`, setters `set*`, adders `add*`, removers `remove*`.
- The method name must be a correct English phrase. This is more important than mechanically
  prefixing the property name.

```ts
interface Account {
    isValid(): boolean;
    isCheckNeeded(): boolean;
    canBeClosed(): boolean;
    areTransactionsIncluded(): boolean;

    // Wrong:
    isCheck(): boolean;
    isNeedsChecking(): boolean;
    isTransactionsIncluded(): boolean;
    getIsValid(): boolean;
}
```

A method that verifies something and throws on failure is a verb, not a question: `assertPending`, `checkPermissions`.

### Constants

Constants for possible values of a property are prefixed with the property name:

```ts
class Transfer {
    static readonly STATUS_NEW = 'new';
    static readonly STATUS_DONE = 'done';
    static readonly TYPE_INTERNAL = 'internal';

    private status: string;
    private type: string;
}
```

If the language has proper enums, we use an enum named after the property (`TransferStatus`) instead.

### Events and listeners

- All events a module dispatches are listed in one final class (or enum) containing only constants, for example
  `PageEvents`, placed in the module root.
- Events are named in the past tense, prefixed with the resource: `PAGE_CREATED`, `TRANSFER_REJECTED`.
- `BEFORE_*` / `AFTER_*` prefixes are allowed for events dispatched around an action.
- We do not name events `ON_*`. `on*` describes the reaction, not the event.
- The constant value equals the constant name, lower-cased and prefixed with the module name, so a search finds both
  the constant and its string usages:

```ts
final class PageEvents {
    static readonly PAGE_CREATED = 'page.page_created';
}
```

- Listener methods are named `on*`, `before*`, `after*`. One method may handle several events if the logic is the same.
- Event classes are named by the data they carry, not by a single use case:

```ts
// Wrong
class PageCreatedEvent { page: Page }

// Correct
class PageEvent { page: Page }

// Correct: a specific event with additional data
class PageChangedEvent { page: Page; previousPage: Page }
```

### Routes and CLI commands

- Route names describe the action, not the HTTP method: `create_page`, not `page_post`.
- Route names and URL paths are prefixed with the module name.
- CLI command names use dashes between words: `invoice:send-reminders`.

### Namespaces, packages, modules

- Singular names: `Entity`, `Controller`, `Repository`, not `Entities`, `Controllers`.
  Exception: words without a singular form.
- Named by the abstraction or feature, not by a class inside them: `UserMerge`, not `UserMergeManager`.
- No directories just for interfaces (no `Interfaces/`, `Contracts/` dumping ground). Interfaces live next to
  the code they belong to.

---

## Types

### Strict types over documentation

We express everything we can in the type system, not in comments or docblocks.

- Enable the strictest mode the language offers (`declare(strict_types=1)`, `"strict": true` in TypeScript,
  mypy/pyright strict mode, nullable reference types in C#, `-Werror` style checks, etc.).
- Every argument, return value and property has a declared type.
- A docblock is only allowed for information the type system **cannot** express
  (for example element type of an array in languages without generics).

```ts
// Wrong
/**
 * @param client Client
 * @param type string
 * @returns Money
 */
getLimit(client, type) { ... }

// Correct
getLimit(client: Client, type: LimitType): Money { ... }
```

> **Why?** Types are checked by the compiler or analyser; docs are not. A docblock that repeats the signature is
> duplication that silently goes stale.

When strict mode or types are added to existing code, we are responsible for every caller still passing the
correct types. Where that cannot be guaranteed, we convert explicitly at the call site.

### One type per return value and argument

A function returns values of one type, optionally nullable. We do not return `boolean | string` or
`SuccessResult | FailureResult` without a common supertype. If several types share an interface, we declare the
interface.

The same applies to arguments.

Exception: a repository query method may accept either an entity or its ID (`Entity | number`). We prefer passing
the object when it is available.

In languages without generics, a collection type is documented together with its element type:
`Collection | User[]`.

### Nothing means nothing

- If a function returns nothing, its return type is `void` (or the language equivalent). It does not return `null`,
  `false` or `true` "just in case".
- If a function returns a value, every path returns explicitly, including `return null`.

```ts
// Wrong
payback(requestId: number): boolean {
    this.makePayback(requestId);
    return true;
}

// Correct
payback(requestId: number): void {
    this.makePayback(requestId);
}
```

> **Why?** A return value implies the caller should act on it. Returning a constant misleads the next developer.

### Honest nullability

- If a property is not initialized in the constructor, its type must be nullable.
- A non-nullable property must never be or become `null` after construction.
- If a getter must not return `null`, it throws when the value is missing, and a separate `has*` method checks presence.

```ts
class Order {
    private customer: Customer | null;

    constructor() {
        this.customer = null;
    }

    getCustomer(): Customer {
        if (this.customer === null) {
            throw new Error('Customer is not set');
        }
        return this.customer;
    }

    hasCustomer(): boolean {
        return this.customer !== null;
    }
}
```

### Optional and nullable arguments

- Optional arguments have a default value.
- A nullable but required argument is passed explicitly: `entity.setValue(null)`, never `entity.setValue()`.
- Setters accept `null` only when there is a use case for resetting the value, see [entities](#entities).

### Type hint the narrowest interface

We depend on the narrowest interface the code actually uses (`UrlGenerator`, not the whole `Router`).
If one implementation fulfils several interfaces, we inject it once per interface.

```ts
class ReportExporter {
    constructor(
        private readonly normalizer: Normalizer,
        private readonly denormalizer: Denormalizer,
    ) {}
}

const resultNormalizer = new ResultNormalizer();
new ReportExporter(resultNormalizer, resultNormalizer);
```

> **Why?** Narrow dependencies make it possible to swap implementations, and show the real usages when an
> interface changes.

### Accept the narrowest arguments

A method accepts only the data it actually uses. If it needs a single property, it takes that property, not the
whole object holding it.

```ts
// Wrong: only the email is used
sendWelcomeEmail(user: User): void {
    this.mailer.send(user.getEmail(), this.welcomeTemplate);
}

// Correct
sendWelcomeEmail(email: Email): void {
    this.mailer.send(email, this.welcomeTemplate);
}

// Wrong: only the transferred amount is used
calculateFee(transfer: Transfer): Money {
    return transfer.getAmount().multiply(this.feeRate);
}

// Correct
calculateFee(amount: Money): Money {
    return amount.multiply(this.feeRate);
}
```

This does not apply to values that belong together (`Money`, not a separate amount and currency) or to identifiers:
a method that needs an entity takes the entity, not its ID, see
[objects, not identifiers](#objects-not-identifiers-inside-business-logic).

> **Why?** The signature shows exactly what the method depends on, it can be reused with data from other sources,
> and tests do not need to build whole objects.

### Fluent interfaces

If a method returns `this`, its return type is the self type (`self`, `this`, `Self`), so subclasses keep their type.

### Types in public library APIs

Adding or narrowing an **argument** type of a public library method is a backward incompatible change and requires
a major release. Narrowing a **return** type is always safe.

When implementing an old interface that lacks types, we keep the argument types as the interface requires but may
narrow the return type.

---

## Comments

### No AI-generated comments

Free-text comments are allowed only if written by a human developer. This covers:

- single-line and multi-line comments in code;
- descriptions in docblocks on classes, interfaces, constants, properties and methods;
- description texts next to `@param`, `@return`, `@throws` and similar tags.

We do not commit comments generated by AI tools. If generated code contains them, we remove them, unless a developer
consciously decides a specific comment is needed and takes ownership of it by writing it themselves.

Machine-checkable annotations are not affected: type annotations, `@throws`, `@deprecated`, `@see`, test data
provider annotations and similar can be generated, as long as they follow the rules in this section.

> **Why?** A comment is a statement someone stands behind. AI-generated comments usually restate what the code
> already says, are written without knowing *why* the code exists, and nobody feels responsible for keeping them
> correct, so they become stale technical debt immediately.

### Prefer code over comments

We refactor code to be understandable instead of explaining it with a comment. Most code needs no comments at all.
A good comment explains *why*, never *what*.

> **Why?** Comments are technical debt. When some comments cannot be trusted, none of them can.

### No duplicated documentation

- No docblock on a fully typed method unless it adds information beyond the signature.
- If a docblock is needed, it contains **only** the additional information (a description, `@throws`, element types,
  a link), not the parts already in the signature.
- No `@inheritdoc`, no docblocks repeating the parent.
- No docblocks on constructor-injected dependencies that are already typed.

```ts
/**
 * @throws TransferProcessingException
 */
processTransfers(transfers: Transfer[]): void;
```

### Comment styles

- Documentation comments (`/** */`, `///`, docstrings) are used only for class, property and method documentation.
- Inside function bodies we use single-line comments only. If a body needs a multi-line explanation, the function
  is too big.
- Inline type assertions for local variables (`/** @var Mail $mail */`, `as Mail`) are a last resort for code
  whose types we cannot fix, such as third-party code.

### No metadata comments

No file header comments, `@author`, `@date`, `@package`, `@version`, change logs in comments.
Version control already holds this information, and it goes stale after the first refactoring.

### No commented-out code

We delete unused code. Version control keeps history. Behaviour that needs switching off goes to configuration.

### Deprecations

Deprecated code is marked with the language's deprecation mechanism (`@deprecated`, `@Deprecated`,
`[Obsolete]`, `#[deprecated]`) and points to the replacement.

---

## Expressions and control flow

### Strict comparison

We always use strict (type-safe) comparison: `===` / `!==` where the language has both. If values of different types
must be compared, we explicitly convert first. Lookups in lists use strict mode too.

We convert and validate input types at the system boundary (controllers, normalizers, deserializers), not deep in
business logic.

> **Why?** Loose comparison is a source of security issues and subtle bugs.

### Explicit `null` checks

We compare to `null` explicitly. We do not rely on truthiness, and we do not use `isNull()`-style helpers when a
comparison operator exists.

```ts
// Wrong
if (user) { ... }
if (!value) { ... }

// Correct
if (user !== null) { ... }
if (value === '') { ... }
```

Null-safe operators (`?.`) are fine.

### No conversion to boolean

We do not cast values to boolean (`!!value`, `(bool)$value`, `bool(value)`). If a value is not a boolean, we check
its possible values explicitly.

### No comparison to boolean literals

```ts
// Wrong
return valid === true;
return valid === false;

// Correct
return valid;
return !valid;
```

Exception: when the value can be something other than a boolean.

### Static value on the right

```ts
// Wrong
if (null === user) { ... }

// Correct
if (user === null) { ... }
```

### Return boolean expressions directly

```ts
// Wrong
const result = d && e ? false : true;
if (a && b) {
    return true;
}
return false;

// Correct
const result = !(d && e);
return a && b;
```

### No assignments in conditions

```ts
// Wrong
if ((project = this.findProject()) !== null) { ... }

// Correct
const project = this.findProject();
if (project !== null) { ... }
```

Exception: `while` loop conditions where it is idiomatic.

### Check things explicitly

We use the operation designed for the check, not one that happens to give the same result.
We avoid side effects even if they are practically impossible in the current situation.

```text
"is the list empty?"     → items.length === 0     not  !items
"is the string empty?"   → text === ''            not  text.length < 1, !text
"is the key present?"    → map.has(key)           not  map.get(key) !== undefined
```

> **Why?** Specific checks are easier to understand and do not silently change behaviour when related code changes.

### No aliases

When a language or SQL has several names for the same operation, we use the canonical one
(`count` not `sizeof`, `!=` not `<>`).

### Removing prefixes and suffixes

We do not use a global "replace" to strip a prefix or suffix, as it also replaces occurrences in the middle.
We check and slice, or use a dedicated `removePrefix` / anchored regex.

### No unnecessary structures

We merge nested `if` statements that can be one condition, and we prefer early returns over deep nesting.

### Logical operators

We use `&&` / `||` style operators where the language offers both symbolic and word variants with different
precedence.

---

## Variables

### No unnecessary variables

```ts
// Wrong
getName(): string {
    const name = this.provider.getName();
    return name;
}

// Correct
getName(): string {
    return this.provider.getName();
}
```

Variables that name an intermediate meaning are welcome:

```ts
canModify(document: Document): boolean {
    const rightsGranted = this.isAdmin() || this.isOwner(document);
    const documentEditable = document.isNew() && !document.isLocked();

    return rightsGranted && documentEditable;
}
```

### No reuse of variables

- We do not reassign function arguments.
- We do not change the type of a variable.
- We create a new, well-named variable instead.

```ts
// Wrong
handle(documentId: string, request: Request): void {
    documentId = documentId.trim();
    let document = request.get('documentId');
    document = this.documentRepository.find(document);
}

// Correct
handle(rawDocumentId: string): void {
    const documentId = rawDocumentId.trim();
    const document = this.documentRepository.find(documentId);
}
```

Prefer immutable bindings (`const`, `final`, `val`, `readonly`) by default.

> **Why?** Code is easier to follow, and static analysis can track types correctly.

---

## Classes and visibility

### Most restrictive visibility

`private` by default, `protected` only when extension is intended, `public` only when called from outside.

> **Why?** Narrow scope makes refactoring and finding usages easier, and tools can report unused members.

### No public mutable properties

We do not expose mutable public fields. Public **read-only** properties are allowed for DTOs and value objects.
We do not use magic accessors or dynamic properties; every property is declared.

### Defaults in the constructor

Default values for properties, including `createdAt`, are set in the constructor, not in property declarations, so
all defaults are in one place and a new object is valid even before it is persisted.

```ts
class Order {
    private items: Collection<OrderItem>;
    private status: string;
    private createdAt: DateTime;

    constructor() {
        this.items = new Collection();
        this.status = Order.STATUS_NEW;
        this.createdAt = new DateTime();
    }
}
```

### Parent constructor first

If a parent constructor must be called, it is the first statement.

> **Why?** The parent may need to initialize state before anything else uses it. Many languages enforce this.

### Member order

1. constants
2. static fields
3. fields
4. constructor
5. construction-phase methods (used by the DI container)
6. static methods
7. instance methods, grouped by functionality, not by visibility

> **Why not by visibility?** Private methods are usually used from one place, so keeping them next to it is easier to
> read. Tools can show the public interface when needed.

### String conversion

`toString` / `__str__` / `ToString` is for debugging only, never for business logic.

> **Why?** It cannot take arguments, often cannot throw, and cannot be extended without breaking every usage.

### Entities

- Thin model: entities and DTOs carry data, business logic lives in dedicated classes.
- No `setId`: an entity is either new or loaded.
- Setters return `this` for a fluent interface (where the language idiom allows).
- Setters replace the value; adders (`add*`) append to a collection.
- Entity and DTO setters accept `null` only if there is a use case to reset an already set value back to `null`.
  A property, its getter or its database column can be nullable while the setter is not: when a value is empty,
  the setter is simply not called.
- The owning side of a relation keeps both sides in sync: `add*` also sets the back reference, and `set*` for a
  collection clears it and calls `add*` for each item.
- No `updatedAt` unless something really needs "time of any change" (for example synchronising to another storage).
  To track a specific state change, store a dedicated field or history record.

```ts
class Tree {
    setLeaves(leaves: Leaf[]): this {
        this.leaves.clear();
        for (const leaf of leaves) {
            this.addLeaf(leaf);
        }
        return this;
    }

    addLeaf(leaf: Leaf): this {
        this.leaves.add(leaf);
        leaf.setTree(this);
        return this;
    }
}
```

---

## Errors and exceptions

### Throwing

We never throw the base exception type. We throw specific exception classes that callers can catch selectively.
Exception: errors that are not intended to be caught.

### Catching

- We never catch the base exception type, except around third-party code that throws it.
- A `try` block wraps only the call that throws the expected exception, not a whole method.
- We never swallow exceptions silently. We handle, rethrow, or log with context.

```ts
// Wrong
try {
    this.prepare();
    const user = this.userClient.fetch(id);
    this.update(user);
} catch (error) {
    return null; // "not found"
}

// Correct
let user: User;
try {
    user = this.userClient.fetch(id);
} catch (error) {
    if (error instanceof UserNotFoundException) {
        return null;
    }
    throw error;
}
this.update(user);
```

> **Why?** A broad catch assumes one of hundreds of possible failures happened and hides the real ones.

---

## Formatting

Use the language's standard formatter where one exists. Where it leaves room for choice:

- **Trailing commas** in multi-line lists, arguments and parameters, if the language allows them.
- **One item per line** once a list is split.
- **Operators at the beginning** of continuation lines (`&&`, `||`, `+`).
- **Split conditions fully**: if a condition is split, each part is on its own line, including the first and the
  closing parenthesis.
- **One chained call per line** in fluent chains.
- **Four-space indentation** per nesting level, never double indentation.
- **Imports** instead of fully qualified names, also in doc comments.
- **Simple string literals** use the plain quote style; double quotes or escapes only when needed. No variable
  interpolation in strings, use formatting or concatenation explicitly.
- **Always call constructors with parentheses**: `new Value()`.
- **Short collection literals** (`[1, 2]`) where the language has them.

```ts
const allowed = (
    user.isActive()
    && (user.isAdmin() || user.isOwner(document))
);

const result = queryBuilder
    .join('transfer.payer', 'payer')
    .where('payer.id = :payerId')
    .setParameter('payerId', payerId)
    .getResult();

const methods = [
    'card',
    'bank_transfer',
    'wallet',
];
```

> **Why?** Every item on its own line is easy to scan, reorder and diff.

---

## Static analysis and warnings

- No compiler, linter, analyser or IDE warnings in committed code.
- Suppressions are allowed only for analyser bugs or third-party code, at the narrowest scope, with a reason.
- Inline type assertions/casts are a last resort, used only when the type cannot be fixed at its source.

---

# Architecture and design

## Global state and dependencies

### No global access

We do not use or access:

- global variables and global constants;
- global mutable state, singletons and `getInstance()`;
- service locators or containers fetched inside business code;
- free global functions that hide dependencies (time, randomness, environment, file system, network);
- environment variables read directly from business code.

Everything a class needs comes through its constructor. Time, randomness and configuration are dependencies too
(inject a `Clock`, a `RandomGenerator`, configuration values).

```ts
// Wrong
class InvoiceNumberGenerator {
    generate(): string {
        const prefix = process.env.INVOICE_PREFIX;
        const year = new Date().getFullYear();
        return prefix + year + Database.getInstance().nextSequence();
    }
}

// Correct
class InvoiceNumberGenerator {
    constructor(
        private readonly prefix: string,
        private readonly clock: Clock,
        private readonly sequenceProvider: SequenceProvider,
    ) {}

    generate(): string {
        return this.prefix + this.clock.now().year + this.sequenceProvider.next();
    }
}
```

> **Why?** Hidden dependencies make behaviour unpredictable, tests require global setup, and nobody can see
> what influences the result.

### Static methods

We avoid static methods with logic. Allowed: named constructors on value objects, pure functions without
dependencies, and framework-required cases (test data providers). Never static access to stateful collaborators.

### Dependency injection

- Objects with behaviour are created only by the dependency injection container or dedicated factories.
- We do not create collaborators inside other classes (`new Mailer()` in a constructor).
- We prefer constructor injection. No setter injection for mandatory dependencies.
- We prefer the fully qualified class name as the service ID. Custom IDs, where used, start with the module name.
- Expensive services that are not always used (repositories, CLI commands) are configured as lazy.
- Autowiring and autoconfiguration are allowed in new projects. We do not enable them in existing projects that are
  configured explicitly.

> **Why?** Turning on autowiring in an explicitly configured project silently changes how services are resolved and
> can override explicit definitions.

### No dynamic service lookup

We never build service IDs, class names or configuration keys dynamically (`container.get('provider.' + type)`).
Implementations are registered explicitly in a registry, through container tags or explicit configuration.

```ts
// Wrong
const provider = this.container.get(`payment.${type}.provider`);

// Correct
class PaymentProviderRegistry {
    private providers: Map<string, PaymentProvider>;

    constructor() {
        this.providers = new Map();
    }

    addProvider(provider: PaymentProvider, type: string): this {
        this.providers.set(type, provider);
        return this;
    }

    getProvider(type: string): PaymentProvider {
        const provider = this.providers.get(type);
        if (provider === undefined) {
            throw new UnknownPaymentTypeException(type);
        }
        return provider;
    }
}
```

> **Why?** Dynamic IDs cannot be found by search, are not checked to exist or to implement the interface, cannot be
> configured individually, and the full list of available types is unknown.

### Stateless services

Once configured, a service does not change its behaviour at run-time. Run-time data is passed as method arguments,
not stored in properties between calls.

```ts
// Wrong
class ManagerSearch {
    private name: string;
    setManagerName(name: string): this { this.name = name; return this; }
    search(): Result[] { ... }
}

// Correct
class ManagerSearch {
    search(managerName: string): Result[] { ... }
}
```

Exception: classes whose primary purpose is collecting state (for example buffering events to dispatch at the end of
a request).

> **Why?** A service that unintentionally becomes context-aware behaves like a global: it has side effects that are
> hard to find, debug and test.

### Configuration over hard-coding

We configure reusable objects instead of writing near-duplicate classes with hard-coded parameters.
See [constants or configuration](#constants-or-configuration).

### Composition over inheritance

We inject collaborators instead of extending base classes with abstract methods. We do not use traits, mixins or
similar code-sharing mechanisms in production code (test code is the exception).

> **Why?** Two abstract methods with two implementations each require four subclasses. Composition requires four
> small classes with no duplication, each testable on its own.

### Module dependencies

- No unnecessary dependencies between modules.
- No circular dependencies between modules.
- Abstractions never depend on their implementations.
- We do not use another module's repositories directly. We go through a class that module exposes.

> **Why?** Even if that class only forwards calls at first, it gives one place to add filtering, other dependencies
> or caching later without touching every caller.

---

## Data representation

### Objects over primitives

When data can be represented by a primitive or an object, we use the object. Values that belong together travel
together.

- Money is **always** amount + currency in one object, never an amount alone. Never floating point.
- Dates use immutable date-time types internally. Methods accepting dates take the most general date interface the
  language offers, for compatibility.
- When creating a date from a Unix timestamp, the time zone is set explicitly; we do not rely on constructors that
  silently ignore it.
- Identifiers, emails, IBANs and similar can be value objects when validation matters.

### Normalize early, denormalize late

Input is converted to domain objects at the boundary (controller, deserializer, message consumer). Output is converted
at the last moment. Business logic only works with domain objects.

### Objects, not identifiers, inside business logic

Business classes receive resolved objects (`Transfer`), not IDs (`transferId`). Resolution by ID happens at the input
layer. If an object is not found, the input layer responds with "not found" (`404`) or a specific validation error
(`400`).

If a method uses only some properties of the object, it takes those properties instead, see
[accept the narrowest arguments](#accept-the-narrowest-arguments).

### One representation per concept

We avoid several classes representing the same thing (a DTO and an entity of the same transfer). We convert to the
main representation as early as possible.

---

## Changing state and events

### State changes in dedicated classes

State of an entity is changed in a manager or similar dedicated class, which:

- validates the initial state;
- changes the state;
- dispatches events;
- performs related side actions (but does not commit, see [transactions](#transactions-only-in-the-top-layer)).

Controllers and listeners do not change entity state directly.

> **Why?** Otherwise the same change gets duplicated in several places and starts behaving differently.

### Concrete methods over generic ones

```ts
// Correct
accept(request: Request): void { ... }
deny(request: Request, reason: DenialReason): void { ... }

// Wrong
changeStatus(request: Request, status: string): void {
    switch (status) { ... }
}
```

> **Why?** Available actions are visible in the interface, each has its own arguments and validation,
> no `switch` or status maps.

### Selecting implementations at run-time

When several implementations of an interface are selected at run-time:

- a registry or manager collects them (through container tags or explicit registration);
- callers go through the manager, never to implementations directly;
- the manager does not hand out implementations: `manager.render(block)`, not `manager.getProvider(block).render(block)`.

> **Why?** When the interface changes, only the manager changes. Cross-cutting behaviour is added in one place.

### Extending entities without inheritance

Instead of ORM inheritance mapping, we extend through a provider key:

- the base entity holds only common data plus a `type` / `providerKey` field;
- the base module defines a provider interface and a manager that registers providers by key;
- other modules implement providers and keep their extra data in their own entities, referencing the base entity.

```text
PageModule:   Block { id, providerKey }, BlockManager.renderBlock(block), BlockProvider interface
TextModule:   TextBlock { block, textId }, TextBlockProvider implements BlockProvider
```

> **Why?** The base module has no reverse dependencies, can be extracted into a library, and is not modified when a
> new type is added.

### Events notify, they do not command

- Events announce that something happened, or allow optional adjustment before something happens.
- Mandatory work is never delegated to an event listener.
- The dispatcher makes no assumptions about listeners. If a result from listeners is required, use an interface
  with a registry instead.

---

# Persistence

## Transactions and persistence

### Transactions only in the top layer

Database transactions (`begin`/`commit`, `flush`, `save` with auto-commit, `@Transactional`, unit-of-work commit)
are controlled **only in the upper layer** of the application, the entry points:

- HTTP controllers / request handlers;
- CLI commands;
- queue workers / message consumers / job handlers;
- schedulers / cron entry points.

Business classes (managers, processors, validators, providers, listeners, repositories) **never** begin, commit or
roll back transactions and never flush.

```ts
// Wrong: business class commits
class TransferManager {
    accept(transfer: Transfer): void {
        transfer.setStatus(Transfer.STATUS_ACCEPTED);
        this.entityManager.flush();
    }
}

// Correct: entry point owns the transaction
class TransferManager {
    accept(transfer: Transfer): void {
        this.assertPending(transfer);
        transfer.setStatus(Transfer.STATUS_ACCEPTED);
        this.eventDispatcher.dispatch(TransferEvents.TRANSFER_ACCEPTED, new TransferEvent(transfer));
    }
}

class AcceptTransferController {
    accept(transfer: Transfer): Response {
        this.transferManager.accept(transfer);
        this.entityManager.flush();
        return this.responseFactory.noContent();
    }
}
```

> **Why?** Entry points run once per request, command or message, so they know the full unit of work.
> Lower-level classes are reused in many combinations; if one of them commits, a caller ends up with a half-saved
> state that cannot be rolled back.

### One commit per unit of work

We commit once per request, command execution or message. Several commits are allowed only when intermediate state
must be persisted, typically around calls to remote systems. These commits are also made in the entry point.

### Queries only in repositories

- We build queries only inside repositories. No query builders or raw SQL in controllers or business classes.
- Callers use purpose-specific repository methods, not generic `findBy({...})` from outside.
- Find by ID with the dedicated `find(id)` method (it uses the ORM identity map / cache).
  Exception: a custom method that also preloads relations.

> **Why?** All queries in one place show which indexes are needed, and adding a common constraint (for example
> soft-deleted rows) requires changing one class.

### Repository method naming

| Prefix                 | Returns                          |
|------------------------|----------------------------------|
| `findOneBy*`           | one entity or `null`             |
| `findBy*`              | list of entities                 |
| `findCountBy*`, etc.   | scalar result                    |
| `getQueryBuilderFor*`  | query builder (e.g. for pagination) |

### Repository return types

A repository returns only instances of its own entity, or scalar values.

If module B (which depends on module A) needs a query returning A's entities filtered by B's data, the query lives in
a repository **inside module B**, not in A's repository. Otherwise A would depend on B.

### Repositories are injected

Repositories are injected through the constructor, never fetched from an entity manager or container at run-time.

### Do not prefetch and filter by the same relation

With an ORM identity map, if we eagerly load a relation and filter by it in the same query, the loaded entities keep
the **filtered** collection, also for later code that loads the same entity.

```ts
// Wrong: users get only their active emails, and stay that way in the identity map
queryBuilder
    .select('user, email')
    .leftJoin('user.emails', 'email')
    .where('email.status = :activeStatus');
```

Instead, either:

1. do not prefetch the relation (`select('user')`);
2. join the relation twice: once for prefetching, once (with a different alias) for filtering;
3. select only the needed scalar fields and build a result DTO.

Prefetching relations we use is recommended; only combining it with filtering on the same join is wrong.

### Date ranges

Date periods are **inclusive at the start and exclusive at the end**: `createdAt >= from AND createdAt < to`.
If the UI shows inclusive end dates, the frontend maps them to this convention.

> **Why?** No adding or subtracting a second, and consecutive periods never overlap or leave gaps.

### Pagination

We prefer cursor-based pagination over offset-based.

---

## Database

### Naming

- `snake_case` table and column names.
- Plural table names: `users`, `transfers`.
- Module prefix where several modules share one database: `payment_transfers`.
- No SQL keywords as names (`key` → `entity_key` in the database, `key` in code).

### Columns

- Choice values (states, types) are stored as readable strings, not magic integers.
- String columns get a deliberate maximum length, not `255` by default. Values are validated against it before saving.
- Query parameters have the same type as the column (string for `VARCHAR`), otherwise indexes are not used.
- `!=` instead of `<>` in SQL.

> **Why deliberate lengths?** Internal temporary tables are sized by declared lengths, so oversized columns cause
> disk I/O and slower queries.

### IDs

We use one ID generation strategy that behaves the same on every database engine we run on, including the one used
in tests (for example auto-increment/identity rather than engine-specific sequences).

### Money in the database

- Stored as a decimal amount column plus a currency string column, never as a serialized object and never as a float.
- The entity exposes only a `Money` getter and setter, not separate amount and currency accessors.
- Conversion happens inside the getter and setter, not in ORM lifecycle events.

```ts
class Product {
    private priceAmount: string | null;
    private priceCurrency: string | null;

    setPrice(price: Money): this {
        this.priceAmount = price.getAmount();
        this.priceCurrency = price.getCurrency();
        return this;
    }

    getPrice(): Money | null {
        return this.priceAmount !== null && this.priceCurrency !== null
            ? new Money(this.priceAmount, this.priceCurrency)
            : null;
    }
}
```

> **Why?** Decimal columns allow aggregation in SQL and validate the value. ORM events may not fire when only the
> object changes, leaving fields unsaved.

### Mapping

We avoid ORM inheritance mapping (single/joined table inheritance). See
[extending entities without inheritance](#extending-entities-without-inheritance).

> **Why?** It queries unused relations, spreads `instanceof` checks, requires the base module to know all
> subclasses, and an entity cannot change its subclass once created.

### Migrations

- Every schema change goes through the migration tool, also for tables not managed by the ORM.
- Schema migrations are generated from the mapping, not hand-written DDL. Running the diff right after migrating
  produces no changes.
- Migrations may contain additional data statements (`UPDATE`, `INSERT`) for the new structure.
- New projects create all tables through migrations, but not the database itself.

---

# Entry points and APIs

## Controllers, commands and workers

- Entry points are thin: resolve input, call business classes, commit, build the response.
- Small controllers: one controller per resource, at most 5–8 actions. Single-action controllers are fine.
- Controllers, commands and workers are registered as services with constructor-injected dependencies; no container
  access inside them.
- Pages without logic are routed directly to a static template, without a controller.
- The controller passes raw data to the view or serializer; it does not pre-encode it (for example to JSON) for the
  template.
- We prefer a static frontend with a REST API over server-side rendered templates.

> **Why small controllers?** With constructor injection, big controllers have many dependencies that are rarely used
> together.

---

## REST APIs

- Controller methods receive denormalized domain objects and return entities, result objects or scalars. They do not
  read the raw request.
- Normalizers/serializers per class convert between domain objects and API representation; the response normalizer is
  selected by the returned type.
- Lists are returned as result objects that carry items and pagination data.
- The server-side model does not use subclasses for API variants; related data lives in separate entities and
  services. Clients may model it however suits them.
- Permissions are checked in controllers (access to a resource) and/or request listeners (access to the API), using
  dedicated voters/policies.
- Pagination is cursor-based.

---

# Security

## Sensitive values

Passwords, tokens, secret codes and any security-relevant values:

- are never logged. Logging is whitelist-based, not "log the whole request". Log normalizers do not extract private
  fields from objects by default;
- are wrapped in a `SensitiveValue`-like object as soon as they enter the system, so they do not appear in stack traces
  or dumps, and are unwrapped only at the lowest level;
- when passed to third-party code that may leak them in exceptions, the exception is caught and rethrown without the
  original as cause;
- are kept in private properties with `SensitiveValue` getters and setters when an entity must hold them;
- are stored hashed when only comparison is needed. Plain values are stored only when the original must be used again.

---

# Project setup and maintenance

## Configuration

### Constants or configuration

Values that rarely change are **constants** in the class they belong to:

- the value is the same in all environments;
- it is domain-specific and belongs to a class;
- tests do not need to override it.

```ts
class Post {
    static readonly MAX_TITLE_LENGTH = 255;
    static readonly ITEMS_PER_PAGE = 10;
}
```

Values become **injected configuration** only when:

- they differ per environment or come from environment variables;
- tests need a different value (shorter timeouts, smaller thresholds);
- the same value is shared by several service definitions.

We do not move static values into the container configuration just to inject them.

> **Why?** Constants are checked by the compiler, supported by the IDE, and live next to the code using them.

### Configuration format and location

- We prefer configuration formats that are explicit and schema-validated, so mistakes are caught early.
- Service wiring is kept separate from business logic where the framework allows it. One class may be configured as
  several services with different arguments.
- Large configuration is split into files by type (`services/controllers`, `services/repositories`,
  `services/normalizers`, `routing/...`) and imported from the main file.
- Modules expose semantic (typed, validated) configuration options. Environment-specific values are set by the
  application, not hard-coded inside modules.
- Implementation class names are written directly in service definitions, not hidden in parameters. To change an
  implementation we override the service by its ID.

### Example configuration files

- Distribution/example files keep the original extension: `parameters.dist.yaml`, `.env.dist`, so tools still
  recognise the format.
- Default values are for the **development** environment and must work out of the box for any developer with the
  standard environment setup. Default passwords are a trivial placeholder like `pass`.
- Production values are never used as defaults.
- Application-level parameters that are only passed into a module's configuration do not use that module's internal
  prefix, so they cannot clash with the module's own parameters.

### Secrets

No secrets in the repository: no private keys, passwords, tokens or shared secret strings. They are provided by the
environment, a secrets manager or files ignored by version control.

---

## Directory structure

### Module layout

Code is grouped into modules by feature. Inside a module, directories are named by the type of class:

```text
Command/      CLI commands
Controller/   HTTP controllers
Entity/       entities, value objects, DTOs
Event/        event classes
Listener/     event listeners
Normalizer/   normalizers / serializers
Processor/    multi-step processes
Repository/   repositories
Service/      main business classes of the module
Worker/       queue consumers / job handlers
```

The module root contains only the module definition and the events constants class.

### Integration namespaces

Classes integrating with another module, or with a specific role that is not the module's main API, go to a namespace
named after that integration:

```text
Callback/CallbackValidator
PageBlock/MenuProvider
DynamicAsset/AssetGroupProvider
```

### Tests

Tests mirror the source structure: the test for `Module/Namespace/PageManager` is
`Module/Tests/Namespace/PageManagerTest` (or the language's standard test location with the same relative path).

### Example

```text
Page/
    Controller/
        PageController
        PageApiController
    Entity/
        Page
        Block
    Event/
        PageEvent
    Listener/
        LocaleListener
    Menu/
        PageMenuProvider
    Normalizer/
        PageNormalizer
    Service/
        PageManager
        BlockProviderInterface
        BlockManager
    Tests/
        Menu/
            PageMenuProviderTest
        Service/
            PageManagerTest
    PageEvents
    PageModule
```

In existing projects we keep the structure already used there.

---

## Working with legacy code

### New features in new code

New features are implemented as new modules following all current conventions.

### Current code and legacy code

All code is split into two parts:

- **current code** follows these conventions and depends only on other current code and third-party libraries;
- **legacy code** is the legacy code itself plus thin adapters integrating it with current code.

Current code never depends directly on legacy code. At run-time it may still call it, but only through interfaces.

### Interfaces and adapters

1. The current module defines an interface for what it needs, with its own DTOs.
2. An integration module (part of legacy code) implements the interface by calling legacy code.
3. The current module receives the implementation through configuration.

```ts
// Current module
interface UserDataProvider {
    getUserData(userId: number): UserData;
}

class NewsletterSender {
    constructor(private readonly userDataProvider: UserDataProvider) {}

    sendNewsletter(newsletter: Newsletter): void {
        const userData = this.userDataProvider.getUserData(newsletter.getUserId());
        // ...
    }
}

// Integration module
class LegacyUserDataProvider implements UserDataProvider {
    getUserData(userId: number): UserData {
        const row = legacyGetUserData(userId);
        if (row === null) {
            throw new UserNotFoundException(userId);
        }
        return new UserData(row.email, row.name);
    }
}
```

> **Why?** It stays clear where new code ends and legacy begins, and legacy parts can be replaced without touching
> new code.

---

## Libraries, versioning and dependencies

### Semantic versioning

Every library uses [semantic versioning](https://semver.org/):

- **MAJOR**: backward incompatible change of the public API;
- **MINOR**: new backward compatible functionality (new classes, methods, optional arguments), **and any change of
  dependencies**, even if they are major upgrades, as long as our own public API is unchanged;
- **PATCH**: internal changes only, usually bug fixes.

While the API is unstable we use `0.x` versions, where minor bumps may break compatibility. If the API is already
stable, we start at `1.0.0`.

> **Why?** Without it, every library update requires checking all previous changes for incompatibilities.

### Changelog

- Every library maintains a `CHANGELOG.md` in [Keep a Changelog](https://keepachangelog.com/) format.
- Every change to the library includes a changelog entry.
- Internal libraries do not use an `Unreleased` section; each change is released right away.
- If the changelog is missing, we create it and port history from other sources (for example `UPGRADE.md`).

### Releases

- We tag every change merged to the main branch, except follow-up instant fixes.
- Before tagging we check existing tags (sorted by version) and branch aliases, then bump exactly one of
  MAJOR/MINOR/PATCH by one.
- We use annotated tags (`git tag -a -m "" 1.2.3`) so author and time are recorded, and push tags only after the
  change has landed.

### Version constraints on our libraries

- Constraints are as wide as compatibility allows. If a library works with two majors of a dependency, it allows both
  (`>=1.0, <3.0`).
- If a project uses classes of a library directly, it requires that library directly, not transitively through
  another library.

> **Why?** Narrow constraints force upgrading whole dependency graphs for no real reason.

### Version constraints on third-party dependencies

- Constraints follow the dependency's actual versioning policy. If it does not really follow semver, we use stricter
  constraints.
- We never depend on a moving branch (`main`, `master`, `latest`). If a dependency has no releases, we pin a commit.
- Any dependency update within constraints must be safe to run at any time.

---

# Other

## Acknowledgements

This guide is based on the [Paysera PHP Style Guide](https://github.com/paysera/php-style-guide).
Its conventions were generalized to apply to backend code in any programming language.
