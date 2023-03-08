## GRASP

GRASP describe how to properly divide code into logical parts in order to achieve polymorphism.

From GRASP, only one principle will be needed for combining (Information Expert) and two principles for separating logic (Low Coupling and High Cohesion).

Let's start with separating logic, since often in projects without any coding etiquettes, you can encounter either a fat model or a fat controller. These two principles help in both writing new functionality in a new project and refactoring old legacy projects.

Based on the logic that when creating new functionality, a developer tends to create entities beyond what is necessary, and when modifying existing code, the developer tries not to create unnecessary entities, it is necessary to try to maintain a balance.

### Low Coupling

Low Coupling - this principle states that the less `use` in a class, the better. The most vivid example of weak coupling is the God object antipattern. A God object can be minimally dependent on other objects while containing the implementation of logic that is not related to the given object, which reduces high cohesion.

### High Cohesion

High Cohesion - the principle dictates a rule for a class. The class should perform the minimum amount of actions. For example, if a class implements the logic of sending emails, this class should not be responsible for generating the content of this email.

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
        // logic of composing the email
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
With this separation, `Mailer` is responsible only for sending, but not for what is being sent, and `Mail` is responsible for the content of the email. In other words, changing the logic of forming the email content will not affect its sending, and conversely, if there is a need to change the way the email is sent, there is no need to also consider how its content is formed.

### Information Expert

Information Expert - this principle tells us that whoever owns the data should logically be the one to manage it.

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

The example shows that the product in the bill has properties that allow calculating the cost of one item. Therefore, it should be calculated in `BillProduct`. `Bill`, in turn, operates with all products, so the total amount for the bill should be calculated there.
