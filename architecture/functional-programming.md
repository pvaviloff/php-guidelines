## Functional Programming

In PHP, functional programming is not fully implemented.
However, from this approach to writing code, the most important concept can be highlighted - **Pure functions**.

_A pure function is defined as a function that has no side effects on input-output and memory (it depends only on its parameters and returns only its result). (c) Wikipedia_

In PHP, there are pure functions out of the box. For example, `array_map` or `array_filter`. There are also impure functions, such as `date`.

Pure functions have a huge advantage - predictable behavior.
```php 
$orderProducts = [
    ['quantity' => 3, 'price' => 3.6], 
    ['quantity' => 4, 'price' => 12.3],
];

$orderProductsSum = array_map(fn($product) => $product['quantity'] * $product['price'], $orderProducts);
$orderSum = array_sum($orderProductsSum);
```

These functions do not modify the input parameters. With the same input parameters, the output is always the same.

This concept can also be applied to object methods.