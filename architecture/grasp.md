## GRASP принципы

Принципы GRASP описывают как разделить правильно код на логические части так, чтоб достичь полиморфизма.

Из GRASP понадобится всего один принцип для объединения(Information Expert) и два принципа для разделения логики (Low Coupling и High Cohesion).

Начнем с разделения логики, так как зачастую в проектах без какого-либо этикета написания кода можно встретить или fat model, или fat controller.
Эти два принципа помогают как в написании нового функционала в новом проекте, так и при рефакторинге старого легаси проекта.
Руководствуясь логикой, что при создании нового функционала разработчик склонен плодить сущности сверх необходимого, а при доработке наоборот старается не создавать лишних сущностей, необходимо постараться выдерживать баланс.

### Low Coupling

Low Coupling (слабое зацепление) - этот принцип гласит чем меньше `use` в классе тем лучше.
Самым ярким примером слабого зацепления является антипаттерн God object. 
God object может быть минимально зависим от других объектов при этом содержать в себе реализацию логики не относящуюся к данному объекту, что уменьшает высокую связанность.

### High Cohesion

High Cohesion (высокая связанность) - принцип диктует правило для класса. Класс должен выполнять минимальное количество действий.
К примеру если класс реализует логику рассылки писем, то этот класс не должен отвечать за формирование содержимого этого письма.

```php 
class Mail {
    protected string $mailTo;
    
    protected string $subject;

    protected string $bodyBoilerplate = 'Hi \s';

    public function getMailTo(): string
    {
        return $this->mailTo;
    }
    
    public function getSubject(): string
    {
        return $this->subject;
    }

    public function getBody()
    {
        // логика формирования письма
        return sprintf($this->bodyBoilerplate, $username);
    }
}

class Mailer
{
    public function send(Mail $mail)
    {
        mail($mail->getMailTo(), $mail->getSubject(), $mail->getBody());
    }
}
```

При помощи такого разделения `Mailer` отвечает только за отправку, но не отвечает за отправляемое, а `Mail` наоборот.
То есть изменив логику формирования письма это никак не повлияет на его отправку и наоборот, если есть необходимость поменять способ отправки не нужно будет задумываться еще и формировании его содержимого.

### Information Expert

Information Expert - этот принцип говорит нам, что кто владеет данными, тот и должен по логике ими управлять.

```php
class BillProduct {
    private int $quantity;
    
    private float $price;
    
    public function getSum(): float
    {
        return $this->quantity * $this->price;
    }
}

class Bill {
    private array $billProducts = [];
    
    public function addBillProduct(BillProduct $billProduct): void
    {
        $this->billProducts[] = $billProduct;
    }
    
    public function getSum()
    {
        $totalSum = 0;
        foreach($this->billProducts as $billProduct) {
            $totalSum += $billProduct->getSum();
        }
        ...
    }
}

```

Из примера видно, что у продукта из счёта есть свойства, которые позволяют рассчитать стоимость одной позиции. Значит она и должна быть рассчитана в `BillProduct`.
А `Bill` оперирует уже всеми продуктами. Значит общая сумма по счету должна быть рассчитана в нем.