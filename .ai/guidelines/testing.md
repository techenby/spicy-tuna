# Testing Guidelines

This document outlines the testing standards for this project. All tests use [Pest PHP](https://pestphp.com/) with Laravel's testing utilities.

## Test Function Style

Use `test()` instead of `it()` for all tests.

```php
// Correct
test('user can create a post', function () {
    // ...
});

// Incorrect
it('can create a post', function () {
    // ...
});
```

## Authentication in Tests

Always chain `actingAs` with the actual action step rather than calling it on a separate line. Do not use the pest `actingAs` helper.

### HTTP Tests

```php
// Correct
test('user can view dashboard', function () {
    $user = User::factory()->create();

    $this->actingAs($user)
        ->get('/dashboard')
        ->assertOk();
});

// Incorrect
test('user can view dashboard', function () {
    $user = User::factory()->create();

    actingAs($user)
        ->get('/dashboard')
        ->assertOk();
});

// Incorrect
test('user can view dashboard', function () {
    $user = User::factory()->create();

    $this->actingAs($user);

    $this->get('/dashboard')
        ->assertOk();
});
```

### Livewire Tests

Use `Livewire::actingAs($user)->test()` to chain authentication with the Livewire test.

```php
// Correct
test('user can update profile', function () {
    $user = User::factory()->create();

    Livewire::actingAs($user)
        ->test(UpdateProfile::class)
        ->set('name', 'New Name')
        ->call('save')
        ->assertHasNoErrors();
});

// Incorrect
test('user can update profile', function () {
    $user = User::factory()->create();

    $this->actingAs($user);

    Livewire::test(UpdateProfile::class)
        ->set('name', 'New Name')
        ->call('save')
        ->assertHasNoErrors();
});
```

## Livewire Testing

Use the `Livewire` facade instead of the `livewire()` helper function.

```php
use Livewire\Livewire;

// Correct
test('component renders', function () {
    Livewire::test(MyComponent::class)
        ->assertOk();
});

// Correct (with authentication)
test('component renders for user', function () {
    $user = User::factory()->create();

    Livewire::actingAs($user)
        ->test(MyComponent::class)
        ->assertOk();
});

// Incorrect (don't use the helper function)
test('component renders', function () {
    livewire(MyComponent::class)
        ->assertOk();
});
```

## Assertion Chaining

Chain assertions on the response object rather than making separate assertion calls.

```php
// Correct
test('create component renders correctly', function () {
    Livewire::actingAs($this->admin)
        ->test(Create::class, ['organization' => $this->organization])
        ->assertOk()
        ->assertSee('Create New HCP')
        ->assertSee('First Name')
        ->assertSee('Last Name');
});

// Incorrect
test('create component renders correctly', function () {
    $this->actingAs($this->admin);

    $component = Livewire::test(Create::class, ['organization' => $this->organization]);

    $component->assertOk();
    $component->assertSee('Create New HCP');
    $component->assertSee('First Name');
});
```

## Expectation Chaining

Chain multiple expectations using `->and()` when testing related values. Break out of the chain when testing something distinct.

```php
// Correct
test('user is updated from socialite', function () {
    // ... setup ...

    $dbUser = User::firstWhere('email', $user->email);

    expect($dbUser->name)->toBe($user->name)
        ->and($dbUser->email_verified_at)->not()->toBeNull()
        ->and($dbUser->id)->toBe($existingUser->id);
});

// Incorrect
test('user is updated from socialite', function () {
    // ... setup ...

    $dbUser = User::firstWhere('email', $user->email);

    expect($dbUser->name)->toBe($user->name);
    expect($dbUser->email_verified_at)->not()->toBeNull();
    expect($dbUser->id)->toBe($existingUser->id);
});
```

When testing distinct concepts, start a new expectation chain:

```php
// Correct - separate chains for distinct concepts
test('profile information can be updated', function () {
    // ... action ...

    $user->refresh();

    // User attributes chain
    expect($user->name)->toBe('New Name')
        ->and($user->email)->toBe('new@example.com');

    // Separate assertion for verification status
    expect($user->email_verified_at)->toBeNull();
});
```

## Pest Expectations vs Laravel Assertions

Use Pest's `expect()` API for value assertions. Use Laravel's built-in assertions for framework-specific checks.

### Use `expect()` for Value Assertions

```php
// Correct - use expect() for checking values
expect($user->name)->toBe('John Doe')
    ->and($user->email)->toBe('john@example.com')
    ->and($user->is_admin)->toBeTrue();

expect($collection)->toHaveCount(3);
expect($result)->toBeNull();
expect($items)->toBeEmpty();

// Incorrect - don't use PHPUnit assertions for values
$this->assertEquals('John Doe', $user->name);
$this->assertTrue($user->is_admin);
$this->assertNull($result);
$this->assertCount(3, $collection);
```

### Use Laravel Assertions for Framework Features

These Laravel-specific assertions should be used as-is (not converted to `expect()`):

```php
// Database assertions
$this->assertDatabaseHas('users', ['email' => 'test@example.com']);
$this->assertDatabaseMissing('users', ['email' => 'deleted@example.com']);
$this->assertModelMissing($user);

// Authentication assertions
$this->assertAuthenticated();
$this->assertGuest();
$this->assertAuthenticatedAs($user);

// Session assertions
$response->assertSessionHas('success');
$response->assertSessionHasErrors(['email']);

// Response assertions (chain these on response objects)
$response->assertOk();
$response->assertRedirect('/dashboard');
$response->assertSee('Welcome');
```

### Common Pest Expectation Methods

| PHPUnit Assertion | Pest Expectation |
|-------------------|------------------|
| `assertTrue($x)` | `expect($x)->toBeTrue()` |
| `assertFalse($x)` | `expect($x)->toBeFalse()` |
| `assertNull($x)` | `expect($x)->toBeNull()` |
| `assertNotNull($x)` | `expect($x)->not->toBeNull()` |
| `assertEquals($a, $b)` | `expect($b)->toBe($a)` |
| `assertSame($a, $b)` | `expect($b)->toBe($a)` |
| `assertCount($n, $arr)` | `expect($arr)->toHaveCount($n)` |
| `assertEmpty($x)` | `expect($x)->toBeEmpty()` |
| `assertInstanceOf(A, $x)` | `expect($x)->toBeInstanceOf(A::class)` |
| `assertArrayHasKey($k, $a)` | `expect($a)->toHaveKey($k)` |

## Response Status Assertions

Use specific assertion methods instead of generic `assertStatus()`.

```php
// Correct
$response->assertOk();           // 200
$response->assertCreated();      // 201
$response->assertNoContent();    // 204
$response->assertNotFound();     // 404
$response->assertForbidden();    // 403
$response->assertUnauthorized(); // 401

// Incorrect
$response->assertStatus(200);
$response->assertStatus(404);
```

## Test Organization

### File Naming

- Feature tests: `tests/Feature/{Domain}/{FeatureName}Test.php`
- Unit tests: `tests/Unit/{ClassName}Test.php`
- Livewire component tests: `tests/Feature/Livewire/{Component}Test.php`

### Using beforeEach

Use `beforeEach` to set up common test fixtures.

```php
beforeEach(function () {
    $this->admin = User::factory()->admin()->create();
    $this->organization = Organization::factory()->create();
});

test('admin can view organization', function () {
    Livewire::actingAs($this->admin)
        ->test(Show::class, ['organization' => $this->organization])
        ->assertOk();
});
```

## Database Assertions

```php
// Assert record exists
$this->assertDatabaseHas('users', [
    'email' => 'test@example.com',
]);

// Assert record does not exist
$this->assertDatabaseMissing('users', [
    'email' => 'deleted@example.com',
]);

// Assert model was deleted
$this->assertModelMissing($user);
```

## Imports

Standard imports for test files:

```php
<?php

use App\Livewire\MyComponent;
use App\Models\User;
use Livewire\Livewire;
```

Note: Import `Livewire\Livewire` for Livewire tests
