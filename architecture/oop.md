## Object-oriented programming

The goal of OOP is to achieve polymorphism. Polymorphism provides scalability to the project. Another advantage of OOP is encapsulation.

Polymorphism represents a single scenario for processing objects of different class instances.
First and foremost, polymorphism should be achieved for classes that implement business logic.

As an example, we can consider the process of creating a single bill in a cafe, where we pay separately for the bar and food due to the different pricing methods.
```php 
interface IBill 
{
    public function getSum(): float;
}

class FoodBill implements IBill 
{
    public function getSum(): float
    {
        # some logic
    }
}

class DrinkBill implements IBill 
{
    public function getSum(): float
    {
        # some logic
    }
}

class FullBill 
{
    private array $bills = [];
    
    public function addBill(IBill $bill): void
    {
        $this->bills[] = $bill;
    }
    
    public function getBill()
    {
        $sum = 0;
        foreach($this->bills as $bill) {
            $sum += $bill->getSum();
        }
        # some logic
    }
}
```
The goal of OOP is to achieve polymorphism, which provides scalability of the project. Another advantage of OOP is encapsulation.

Polymorphism represents a single scenario for processing objects of different class instances. Polymorphism should be achieved primarily for classes that implement business logic.

In this implementation, adding a separate calculation that implements a different logic for calculating the sum will not be difficult. However, the business process itself also plays a role, which dictates the requirements for implementation. It is always necessary to try to anticipate how the process can change from the given task. Otherwise, it is possible to complicate the application where the work process will never change.

Inheritance is a controversial solution for achieving polymorphism due to the risks of creating a God object and/or going into excessive abstraction, which will lead to strong dependence (coupling) of classes with each other and further complicating code readability. If polymorphism is not achieved, abstract classes should not be used if possible.

Encapsulation helps to hide implementation details. Often, to refactor old code, a three-stage approach is used to understand its logic:
- divide the code into logical pieces.
- encapsulate the logic of these pieces of code.
- write unit tests if possible, documenting the logic of the work, and make the necessary changes.