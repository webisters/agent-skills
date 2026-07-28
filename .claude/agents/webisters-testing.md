---
name: webisters-testing
description: Write and run tests in a Webisters project
---

Tests live under `tests/` with namespace `Tests\`. PHPUnit config is `phpunit.xml.dist`.

## Running tests

```bash
vendor/bin/phpunit                                    # full suite
vendor/bin/phpunit tests/SomeTest.php                 # single file
vendor/bin/phpunit --filter testMethodName            # single method
```

## Test class structure

```php
<?php declare(strict_types=1);
namespace Tests;

use PHPUnit\Framework\TestCase;

final class ExampleTest extends TestCase
{
    public function testSomething() : void
    {
        self::assertSame('expected', 'expected');
    }
}
```

## Capturing CLI output

Use `Framework\CLI\Streams\Stdout` for commands that write to stdout:

```php
use Framework\CLI\Streams\Stdout;

protected function setUp() : void
{
    Stdout::init();
    Stdout::reset();
}

public function testOutput() : void
{
    $command = new MyCommand();
    $command->run();
    self::assertStringContainsString('expected text', Stdout::getContents());
}
```

## Testability pattern

For commands with side effects (filesystem, network, registry), make the class non-final with `protected` seam methods, then override them in a harness:

```php
// src/Commands/MyCommand.php
class MyCommand extends Command
{
    protected function doSideEffect() : string
    {
        return shell_exec('real command');
    }
}

// tests/Commands/MyCommandTest.php
final class MyCommandHarness extends MyCommand
{
    private string $fakeResult = '';

    public function setResult(string $r) : void { $this->fakeResult = $r; }

    protected function doSideEffect() : string { return $this->fakeResult; }
}
```
