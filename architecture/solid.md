## SOLID

The only SOLID principle needed is Dependency Inversion and its implementation through dependency injection, which helps achieve Low Coupling.

The main goal of the Dependency Inversion principle is to reduce the coupling between classes.

```php
interface FileContract 
{
    public function getDocumentBody(): string;
}

class Bill implements FileContract 
{
    public function getDocumentBody(): string
    {
        # some logic
        return "Bill sum";
    }
}

class Report implements FileContract 
{
    public function getDocumentBody(): string
    {
        # some logic
        return "Report data";
    }
}

class Printer 
{
    public function execute(FileContract $file) 
    {
        // printing logic
    }
}

class PrintBill 
{
    public function execute() 
    {
        $printer = new Printer();
        
        $bill = new Bill();
        $printer->execute($bill);
    }
}

class PrintReport 
{
    public function execute() 
    {
        $printer = new Printer();
        
        $report = new Report();
        $printer->execute($report);
    }
}

```
The `Printer` class depends on the `FileContract` interface, which needs to be implemented. This approach allows the `Printer` class to be used for printing anything. The only problem is that if the `Printer` class also takes another object in its constructor, for example, `PrinterSettings`, then `PrintReport` and `PrintBill` will also depend on the `PrinterSettings` class, even though it is only used for setting up the `Printer`.

We can turn to the tool that solves the dependency problem. The unnecessary dependency can be removed using Dependency Injection, which provides inversion of control from the principles of OOP.
By configuring the class once using an abstraction, it can be used in other implementations without dragging unnecessary dependencies into the class.
There are implementation examples in the vendor packages [php di](https://github.com/PHP-DI/PHP-DI), [symfony](https://symfony.com/doc/current/components/dependency_injection.html) and [laravel](https://laravel.com/docs/8.x/providers).

```php
class PrintReport 
{
    private Printer $printer;

    public function __construct(Printer $printer)
    {
        $this->printer = $printer;
    }
    public function execute() 
    {
        $report = new Report();
        $this->printer->execute($report);
    }
}

$printReport = $container->get(PrintReport::class);
$printReport->execute();
```

The `$container` variable is initialized in the core and is global throughout the application. It is executed through a facade/singleton or other means. In Laravel, this is the `app()` function, while in Symfony, the `ContainerBuilder` class is responsible for obtaining the configured object.
