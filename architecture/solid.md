## SOLID принципы

Из SOLID понадобиться всего один принцип Dependency Inversion и его реализация dependency injection, который поможет обеспечить Low Coupling.

Dependency Inversion (Принцип инверсии зависимостей) главная цель данного принципа уменьшить зацепление классов между собой.

```php
interface FileContract {
    public function getDocumentBody(): string;
}

class Bill implements FileContract {
    public function getDocumentBody(): string
    {
        ...
        return "Bill sum";
    }
}

class Report implements FileContract {
    public function getDocumentBody(): string
    {
        ...
        return "Report data";
    }
}

class Printer {
    public function execute(FileContract $file) 
    {
        // логика печати
    }
}

class PrintBill {
    public function execute() 
    {
        $printer = new Printer();
        
        $bill = new Bill();
        $printer->execute($bill);
    }
}

class PrintReport {
    public function execute() 
    {
        $printer = new Printer();
        
        $report = new Report();
        $printer->execute($report);
    }
}

```
Класс `Printer` зависит от контракта `FileContract`, который нужно реализовать. Такой подход позволяет использовать класс `Printer` для печати чего угодно.
Единственная проблема. Предположим что класс `Printer` будет у себя в конструкторе принимать еще какой-то объект в конструкторе допустим `PrinterSettings`. 
Тогда получается что `PrintReport` и `PrintBill` будут зависеть от класса `PrinterSettings` хотя кроме как для настройки `Printer` он применяться не будет. 

Обратимся к инструменту, который решит проблему с зависимостями. Убрать эту лишнюю зависимость можно при помощи Dependency injection которая обеспечивает инверсию управления из ООП принципов.
Dependency injection при помощи абстракции один раз настроив класс его можно использовать в других реализациях, не затягивая лишние зависимости класса.
Примеры реализации есть в вендорном пакете [php di](https://github.com/PHP-DI/PHP-DI), [symfony](https://symfony.com/doc/current/components/dependency_injection.html) и [laravel](https://laravel.com/docs/8.x/providers) 

```php
class PrintReport {
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

Переменная `$container` инициализируется в ядре и глобальна по всему приложению. Исполняется фасадом/синглтоном или иными способами.
В laravel это функция `app()`, в symfony класс `ContainerBuilder` отвечает за получение настроенного объекта;
