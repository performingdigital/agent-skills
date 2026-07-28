---
name: laravel-inertia
description: General rules and guidance for laravel inertia application developed by performing digital
---

# Performing Digital Laravel Inertia Application Guidelines

## General Rules

- Follow existing conventions and inspect sibling files before creating or editing code. Reuse existing components and abstractions first.
- Use descriptive names, explicit types, and focused responsibilities. Runtime crashes are better than bugs
- Memory is a resource don't waste it.
- Do not add dependencies, create base folders, or create documentation files without approval.
- Keep replies concise and emoji-free. 
- Together we serve the users.

## Technology Stack

### PHP 8.5 and Laravel Packages
- inertiajs/inertia-laravel
- laravel/framework
- laravel/horizon
- laravel/octane
- laravel/prompts
- laravel/reverb
- laravel/sanctum
- laravel/scout
- pestphp/pest
- phpunit/phpunit
- tightenco/ziggy
- spatie/laravel-data
- spatie/laravel-route-attributes
- spatie/laravel-event-sourcing
- performing/laravel-command-bus
- performing/laravel-harmony
- performing/laravel-field-attributes
### JS
- @inertiajs/vue3
- laravel-echo
- tailwindcss 4
- vue 3

Follow version-specific conventions reading from the composer.json and package.json.

## Skills and Documentation

- Activate the relevant project skill whenever its domain is involved: `pest-testing` for tests; `inertia-vue-development` for Inertia/Vue; `tailwindcss-development` for styling; and `ai-sdk-development` for Laravel AI SDK features.
- Before changing Laravel, Inertia, Pest, or Tailwind code, use Laravel Boost's version-specific `search-docs` tool. Use broad, topic-based queries and do not include package names in them.
- Use Laravel Boost tools where applicable: inspect schemas before migrations or models; use database queries for read-only data and Tinker for PHP/Eloquent debugging; check recent browser logs for frontend issues; inspect unfamiliar Artisan command options; and resolve absolute URLs before sharing them.

## Project Structure and Responsibilities

- Organize domain code under `Domain/<DomainName>/`, using `Contracts`, `Commands`, `Enums`, `Handlers`, `Http/Controllers`, `Http/Resources`, and `Models` as appropriate. Use PascalCase PHP classes and kebab-case route paths and names.
- Put cross-cutting, framework-facing, or shared application code in `app/Core/`. Keep domain-specific behavior inside its domain.
- Put behavior with the model, value object, or domain component that owns the concept. Do not add methods solely because a caller needs them; use the relevant entity, value object, query object, utility, or domain component instead.
- Use `php artisan make:* --no-interaction` to create Laravel files, and `make:class` for generic PHP classes. Do not create standalone verification scripts when tests can verify the behavior.

## Static Analysis and Formatting PHP
- use `mago fmt` to format the PHP files
- use `mago analyze <path>` to run a static analysis on php files. avoid using `mago analyze` without a path, output will be usually overwhelming and impossible to reason about it.
- use `mago lint --fix` to lint and fix safe things
- Forget about `pint` use `mago`
- Mago is still in early development although stable some bugs are possible.

## Routing, Commands, and Data

- Define routes with `spatie/laravel-route-attributes`, not `web.php`. Use explicit named `#[Get]`, `#[Post]`, `#[Put]`, and `#[Delete]` attributes; add `#[Middleware('auth')]` to authenticated controllers.
- Controllers render Inertia pages with `Inertia::render()`, or using `Page` class from `performing/laravel-harmony`. Generate links with named routes and `route()`, and execute business operations through `command()`.
- Commands are Spatie Laravel Data classes in `Commands`; put business logic in command handlers registered using `#[CommandBusHandler]` attribute on the handle method. Create distinct commands for create, update, delete, and other operations. 
- Avoid implementing custom `fromModel()` methods. Prefer Spatie Data’s default `UpdateUserCommand::from($user)` mapping. When a command needs a computed or strongly typed value, expose it through an Eloquent cast or accessor. Accessors may return Data objects, such as Seconds.
```php
use Illuminate\Support\Number;
use Spatie\LaravelData\Data;
use Spatie\TypeScriptTransformer\Attributes\TypeScript;

use function Psl\Type\float;
use function Psl\Type\string;

#[TypeScript]
final class Seconds extends Data
{
    public function __construct(
        public readonly float $seconds,
        public readonly string $formatted,
    ) {}

    public static function fromSeconds(float|int|string|null $seconds): self
    {
        $value = float()->coerce($seconds);
        $hours = $value / 3600;

        return new self(
            seconds: $value,
            formatted: string()->assert(Number::format($hours, 2)) . 'h',
        );
    }
}
```
- Use Spatie Laravel Data for input validation and output transformation. Add `#[TypeScript]` to data exposed to the frontend; typed properties cover basic scalar validation, with validation attributes only for additional constraints.
- No ViewModels: don’t create PHP classes whose only job is preparing data for an Inertia page. Use Data classes, Eloquent resources, and Inertia props instead.
- Avoid service classes: don’t create generic UserService or InvoiceService classes unless it clearly fits the owning abstraction. Put an action’s logic in a command and its handler, or on the model/value object that owns it.

## PHP, Enums, and Models

- Always use braces, explicit parameter and return types, constructor property promotion, and useful PHPDoc, especially array shapes. Avoid empty public constructors and inline comments unless logic is unusually complex.
- Keep `mixed` at untyped boundaries. Use `Psl\Type` to make external values concrete, `assert()` when the value must already match, and `coerce()` only when conversion is part of the contract.
- Use enums, not class constants, for domain values. Use PascalCase cases and follow existing conventions, including `label()` and `options()` where applicable.
- Prefer Eloquent models and typed relationships. Eager-load to prevent N+1 queries, use `Model::query()` instead of `DB::` unless a query is genuinely complex, and use the query builder only for complex queries.
- Define casts in `casts()` when consistent with nearby models. Use `HasUuids` for UUID primary keys and the project `Searchable` trait for searchable models. Use environment variables only in config files.
- When creating models, create useful factories and seeders. For API work, follow project convention; otherwise prefer versioned Eloquent API Resources. 
- Prefer `match (true) { ... }` for ordered, mutually exclusive conditions that directly return a value or throw. Use if for stateful control flow, side effects, or conditions that are clearer as guards.

## Frontend and Inertia

- Create pages under `resources/js/pages/<EntityName>/`, use the convention:  `Index.vue` , `Show.vue`,  and `Create.vue` and `Edit.vue` sharing one `Form.vue` .
- Reuse project components; prefer shadcn-vue and `performing/laravel-harmony` for datatables, actions, and links. Never edit `resources/types/generated.d.ts` manually or define inline component interfaces.
- Build index pages with `<Datatable>`. Wrap fields in `<FormInput>` and use the matching input component, such as `<InputCurrency>`, `<InputTime>`, or `<Checkbox>`. Use tabs only for complex forms and description lists for form sidebars.
- use `<Link>` for internal navigation and Inertia form helpers instead of plain anchors or manual client-side requests.
- Avoid polluting the component with too many javascript functions, if it make sense make a reusable composable or extract a component.
- Prefer server-side logic over unnecessary client JavaScript. Keep Vue templates declarative. Do not put conditional logic, magic strings, or field configuration in template bindings. Use property, constant, or field configuration object, then bind the result. In practice avoid this: `<Input :maxlength="value.field === 'Pagamento' ? 5 : 3" />` and prefer values driven by the server side field’s configuration rather than the "Pagamento" hardcoded string.
- Use Inertia 2 features when useful: deferred and merged props, polling, prefetching, and infinite scrolling. Deferred props need a visible skeleton or empty loading state.
- Show field-level validation errors, clear errors and flash notifications, and disabled loading submit controls. Sheet or modal forms submitted with AJAX should return JSON.
- Avoid running `pnpm build`, the dev server is already running

## Migrations
- keep migrations simple
- skip down methods
- data migration/backfill is not always needed 
- if the feature is still in progress we can keep on single migration otherwise we need add a new one.
- When changing a column, preserve every existing attribute in its migration.


## Laravel Conventions

- Middleware, exceptions, and routing are configured in `bootstrap/app.php`; providers live in `bootstrap/providers.php`; console configuration belongs in `bootstrap/app.php` or `routes/console.php`. There is no `app/Http/Kernel.php` or `app/Console/Kernel.php`, and commands in `app/Console/Commands` are auto-discovered.
- Use Laravel's built-in authorization: use policies or existing authorization patterns for every non-public resource action
- Create job for long-running work implementing ShouldQueue interface.
- Laravel 12 supports limited eager loading, for example `$query->latest()->limit(10)`.
- **Octane safety:** do not keep request, authenticated-user, or tenant-specific state in singletons, static properties, or long-lived services. Use scoped bindings for per-request state. Laravel clears scoped bindings between Octane requests; singletons persist.

## Testing

- Use Pest for all tests. Test observable behavior, changed behavior, validation, and relevant CRUD flows; use existing factories and states, and never delete tests without approval.
- Every functional change needs a passing automated test. Create tests with `php artisan make:test --pest`, favor feature tests, and run only the affected file or filter:

```bash
php artisan test --compact path/to/Test.php
php artisan test --compact --filter=testName
```

## Implementation Checklist

Before implementing, identify the feature and domain boundary; affected models, relationships, migrations, commands, handlers, data classes, controllers, pages, and components; any nested creation; validation, authorization, loading, and error states; and the smallest useful automated test coverage.

When the work is complete, respond only with `I am done` unless there is something serious to report.
