# General
- Don't include Claude signatures in generated code.

# Plan Mode
- If available, always consult with `laravel-boost` MCP "Search Docs" tool for accurate and up-to-date documentation for a specific version of a framework/package being used or discussed. This will provide valuable knowledge on framework/package specific concepts, conventions, methods, and practices. As well as examples.

## Keep Plan Mode Short & Concise
- Make the plan extremely concise. Sacrifice grammar for the sake of concision.
- At the end of each plan, give me a list of unresolved questions to answer, if any.

# Keeping Changelog
- If the project has a changelog file, always update it.
- Use concise list items for added, changed, removed, deprecated and fixed, etc. sections.
- List the latest version first.
- Maintain an [Unreleased] section for upcoming changes.

# File Structure
- Always declare a proper namespace for new PHP files.

# Code Style

## Conditional Logic
- Prefer early return conditionals over nesting code inside conditionals.

# Design Principles

## DRY Principle
- Use the DRY principle. As soon duplication occurs, refactor.   
- Exception - same logic for completely different feature/flow. In similar cases, duplication may make more sense.

# Testing
- Always write tests for any new code (changes, new features, fixes).
- Fixing a bug – write a failing test that reproduces the bug, then fix it.
- Refactoring – ensure refactored code has corresponding test coverage. If the code is not tested, test the current implementation, then refactor.
- Never delete planned tests because they're not passing, instead ask what to do.
- Don't create mock objects unless they will actually be called by the code during execution (throws early, not being called in current logic flow)

## Database Setup in Tests
- When a test requires database setup, do it. Don't hesitate.
- Always use model factories to create database records in tests.
- If a page/component requires specific data (SEO settings, configuration, related models), seed it properly through factories.
- Properly test real integration rather than avoiding database setup with simplified assertions.
- Simplified assertions are acceptable, but don't reach for them just to avoid database setup. Use them as additional checks or in unit tests.

## Testing Laravel, LivewirePHP, and FilamentPHP
- Alway prefer using first-party testing helper methods over general testing methods.
- Consider the framework/package documentation via laravel-boost docs tool on testing best practices, examples, and assertions before writing tests manually.
- Use `./vendor/bin/pest --parallel` when running the full test suite to speed things up.
- Mock HTTP calls to prevent outgoing requests during testing.
- When testing code that uses actions/services, prefer mocking the action (or service) classes and their responses over mocking the underlying HTTP calls to prevent outgoing request during testing.

### Best Practices for `assertSee()`
- Avoid using `assertSee()`, if more suitable way to test state/outcome. Only use it when it makes sense (explicitly want to assert some element or text is seen in the rendered response).
- Don't assert against statically rendered text (text content changes frequently).
- Prefer existing element attributes. Use stable attributes like `wire:model`, `name`, `id` that are already in the element.
- Fallback to data attributes - Only add `data-lw-{property}` if no existing attribute is suitable. Convention: `data-lw-{section}` on sections, `data-lw-{entity}="{value}"` on repeated items, `data-lw-{entity}-{part}` on sub-elements (e.g., `data-lw-domain-badge`).
- Exception: when values are created via test factories (known, stable data), assert against those values directly instead of adding data attributes just for testing. Data attributes are for structural assertions (presence, count, order), not for content that's already controlled by the test setup.

### PestPHP
- Use `$this->mock()` over bare `mock()` (Mockery). `$this->mock()` binds the mock in Laravel's container, so the class under test can be resolved via `app()` with all dependencies injected automatically. Bare `mock()` requires manual constructor wiring.
- When writing sequence of expect() assertion, chain them or use higher order proxy calls. Example:
```php
expect($firstOption)
    ->external_id->not->toBeEmpty()
    ->name->not->toBeEmpty()
    ->type->not->toBeEmpty();
```
- Chain method calls on the same object. Example:
```php
expect($e)
    ->getOrderUuid()->toBe(...)
    ->getWhmcsClientId()->toBe(...)
```
- Chain property assertions on models and other objects. Example:
```php
expect($order)
    ->status->toBe(...)
    ->external_id->toBeNull()
```
- Use `->and()` method to connect independent but related assertions. Example:
```php
expect(Auth::check())->toBeTrue()
    ->and(Auth::id())->toBe($user->id)
```

### Testing FilamentPHP

#### Filament Table Column Testing
- Use assertTableColumnExists() with a truth test callback to assert column configuration (URL, description, etc.) instead of reflection.
- Pass the record as the third argument so the column resolves in context.
```php
livewire(ListPosts::class)
    ->assertTableColumnExists('author.name', function (TextColumn $column) use ($expected): bool {
        return $column->getUrl() === $expected;
    }, $record);
```

# Git Commit
- When writing commit messages, use brief concise sentence to describe the entire work as a hole. Followed by a bullet list to describe each change.
- The commit message should be balanced and concise, not too verbose yet not overlooking important details.
- Don't use `git -C <path>` when already in the project working directory. Run git commands directly.

# Laravel

## Laravel Boost Integration
Beware of the laravel-boost MCP server and make the most of the tools it provides. Prioritize using boost tools for database schema lookups, artisan tinker use, reading application logs, and anything else it provides tools for.

## Models
- All models should have tests for relationships, scopes, accessors, mutators and helper functions.

## Migrations
- Creating a new migration VS modifying an existing migration.
  - Modify existing migrations when working on a fresh/undeployed project, migration hasn't been merged/run on shared environments, or if You're certain no other developers have run it.
  - Create new migrations when a migration has been merged to main/master, has run on production, staging, or other developers. And when working on a published package.
  - If in doubt whether to create a new migration or update an existing one, ask for feedback.
- Foreign key syntax – prefer using `$table->foreignIdFor(User::class)` over `$table->foreignId('user_id')` to avoid hardcoding table names.
- When adding new columns, add them after/before a related column (example: `$table->text('description')->after('name');`), or ask if not sure. 

## Factory Guidelines
- When using factories, use Laravel's built-in helper methods. If about to manually add custom methods like `forModel()`, `withModel()` or similar, first search the docs and the factories API. If still about to write a helper method, ask for feedback.
- State methods that set non-relationship attributes (like `withSpecsCache()` or `withTranslation()`) are acceptable.

## Artisan Commands
- **Always use Artisan** to generate files instead of creating them manually.
- When using artisan commands, check the possoble flags and options a command accepts - `php artisan help command`. Or use the Search Docs tool from laravel-boost.  

### Model Creation Flags
```bash
# Basic model with migration, factory, seeder and test
php artisan make:model Product -mfs --pest
```

**Common flags:**
- `-m` migration
- `-f` factory
- `-s` seeder
- `-p` policy
- `-c` controller
- `-p` policy
- `-r` resource
- `-R` requests
- '--api' API resource contriller
- `--pivot` - custom intermediate table model
- `--morph-pivot` - custom polymorphic intermediate table model
- `--pest` Pest test

# Laravel Livewire
- Prefer `<livewire:component />` syntax over `@livewire('component')` directive in Blade templates.
- In tests, prefer `assertSeeLivewire()` and other Livewire-specific assertions over `assertSee(text)` - only use text assertions when there's no other way to test a feature
