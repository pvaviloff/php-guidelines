## Design Patterns

Patterns are only necessary where they are appropriate. Each pattern has its own goal and scope of application. If there is no certainty that the use of a pattern will solve the problem of scaling the application, then it is not recommended to use it.

One of the most common and useful patterns is the Factory Method. Its main advantage is that it separates the specifics from the general process, achieving encapsulation. In addition, it adheres to the principle of Inversion of Control. Its implementation does not lead to excessive abstraction, which preserves readability and does not turn the code into a mess.

The typical problem sounds almost always the same: we have different entities that should have the same behavior.

Let's take a warehouse, where goods can arrive in different ways and there may be differences in inventorying, but they are still stored in the same warehouse. For example, the goods can be delivered by truck or by ship.
```php
interface IArrival 
{
    public function getProducts(): array;
}

class TruckArrival implements IArrival
{
    public function getProducts(): array
    {
    # some logic
    }
}

class ShipArrival implements IArrival
{
    public function getProducts(): array
    {
    # some logic
    }
}

class StockControl
{
    const TRUCK_ARRIVAL = 1;
    const SHIP_ARRIVAL = 2;

    private function getArrival(int $arrivalType): IArrival
    {
        $arrival = null;
        if ($arrivalType === self::TRUCK_ARRIVAL) {
            $arrival = new TruckArrival();
        } elseif ($arrivalType === self::SHIP_ARRIVAL) {
            $arrival = new ShipArrival();
        } else {
            throw new \Exception("{$arrivalType} don`t exist");
        }
        
        return $arrival;
    }
    
    public function control()
    {
        # some logic
        $arrival = $this->getArrival($arrivalType);
        $products = $arrival->getProducts();
        # some logic
    }
}
```

This way, the task is divided into two subtasks. The first one is to identify the goods and receive them. The second one is to place them in the warehouse.

There are also various variations, interpretations, and subtypes of patterns, which led to a violation of the principle of not creating entities beyond what is necessary.
