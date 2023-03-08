## PHP Guidelines

### Introduction

This document provides a set of strategies/recommendations for scaling up development teams and structuring projects. To achieve team scalability, there are two goals that drive the process: writing code as if it was developed by a single developer, and writing documentation that can be understood by someone off the street.

These goals are unattainable, and they are set to drive a process that improves the team's understanding of the project.

Before implementing any processes, an expert analysis is necessary. If the project's code is on a physical server or VPS, the code is delivered to the server via a pull from the repository of the current version of the project, or through direct delivery of the application via SSH. The project architecture ends with MVC.

In this case, it is strongly recommended not to try to convert everything into microservices that will run on Kubernetes in the cloud in a single iteration.

People work on the project, and there is a risk of encountering misunderstandings and non-acceptance from the team when implementing relevant tools, architectural solutions, and services. The project must go through all stages of development. The time it takes to create a process and implement new tools depends solely on the team.

The team is not homogeneous. The end of the implementation process can be considered when 80% of the team follows the established process. The introduction of new technology generates/changes/imposes restrictions on the work process. Before implementation, an analysis of the team's readiness is required.

### Basic concepts and logic for applying theoretical knowledge

- How to write code? Basic approaches to coding.
    - [Functional programming](./architecture/functional-programming.md)
    - [Object-oriented programming](./architecture/oop.md)
- Where to write code? Project structuring. 
    - [GRASP](./architecture/grasp.md)
    - [SOLID](./architecture/solid.md)
    - [Design patterns](./architecture/design-patterns.md)

### Preamble
The main goal of the team is to solve business process-related problems promptly. Therefore, all code should be written according to [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader-examples.md) and [Clean Code](https://github.com/piotrplenik/clean-code-php). If you are not sure whether you are writing readable code, consult your neighbor. It does not have to be someone from the team. We follow the rule: "If two people understood what was written, the third is likely to understand it too."

The first problems start with implicitly described tasks and poorly designed base. If you have any questions while reading the task, first of all, write them in the task comment and ask the PM. There are often situations where you discuss the clarification of the task directly with the task assigner. If this happens, make it a rule to notify the PM of any changes/clarifications. Ask the task assigner to adjust the task or even just send the correspondence in which you resolved the unclear points to the task. All clarifications and/or changes to the task must be recorded in the task. This can lead to a task re-estimation.

What to do with an existing database? If you do not understand the structure of the database or how it works, contact the team lead. They will either provide guidance or direct you to the person who developed the functionality and/or knows the answers to your questions. We recommend visualizing the part of the tables that you are working with. Do this in [https://oino.uno/db-architecture](https://oino.uno/db-architecture) or [https://www.dbdesigner.net/](https://www.dbdesigner.net/) You can upload the described structure to the task comments. This can help you in the future when reopening the task after some time, or to another person when completing related tasks. Additionally, the described table structure can serve as the basis for documentation for the functionality.

What should you do if the logic described in the task does not match the implementation in the project? This is often related to old functionality. Especially if the person who created it no longer works in the company, and their functionality has been handed over to another person. In this case, it is necessary to inform the PM, and document it in the task. Then act according to the situation.

To understand the business logic of old/new functionality, it is desirable to visualize it using [https://oino.uno/diagrams](https://oino.uno/diagrams) or [https://app.diagrams.net](https://app.diagrams.net/) The flowchart does not have to match the code written exactly. It should visualize the business logic. In the future, this chart can also serve as the foundation for documentation.

Do we need to write documentation? Documentation is only necessary for functionality that has a complex logic that needs to be visualized. What is "complex logic"? If you start having questions during implementation/refactoring/enhancement of how the functionality should work. Then, in any case, a Confluence page with a description of the database structure and a block diagram of the business logic should be created at a minimum. The page is created for you by the PM. The documentation should have fewer words and more visual diagrams with descriptions. It is easier to perceive information visually than through long text.

Documentation should consist of two blocks. You describe the technical documentation. Also, through the PM, ask the product owner to describe the user documentation, which should describe: why the functionality was created, how to use it (preferably with screenshots) from the user's point of view (UX). You need to describe how it should work, and the product owner should describe how to work with it.

#### Used concepts

The team aims to write as one developer. What does it mean? Empirically identified principles and recommendations have been developed that work within the overall system and allow the entire team to write in one style. The basis is taken from the Symfony documentation, SOLID, and DDD.

#### Taking the concept of functionality implementation from Domain Driven Design:

The written code should make sense not only for programmers but also for the business. The programmer and the project manager should speak the "same language." All entities and properties should be named as the project manager names them. If it's "Refunds," then it should be "Refund," if it's "Orders," then it should be "Order." Glossaries should be compiled, and entities should be described wherever possible.

The programmer's priorities should correspond to business priorities.

The programmer should act as a translator from the language of the project manager to the programming language.

We also adhere to the principle of a "layered cake." We delimit responsibility zones and link each zone using dependency injection (DI).
![onion](./images/onion.png)

#### Used from SOLID:

The Single Responsibility Principle states that a service should have only one responsibility. It is easily implemented in our conditions, both during refactoring and when developing new functionality.

The Dependency Inversion Principle states that high-level services should not depend on low-level services. Both types of services should depend on abstractions. Abstractions should not depend on details. Details should depend on abstractions.

The Interface Segregation Principle suggests that it is better to have many interfaces for specific implementations than one universal interface. If there is a need to write unit tests, this principle should be followed. There is no need to write an interface for each class in the project.

The Liskov Substitution Principle states that if S is a subtype of T, then objects of type T can be replaced with objects of type S without any desired properties being changed. It is partially achievable and depends on implementation.

#### Not used from SOLID:

The Open Closed Principle A class should be open for extension but closed for modification. The biggest problem is that not everyone from other teams may follow the SOLID principles. In projects with 10+ people, it is difficult to maintain versioning of objects.

We use Dependency Injection to remove dependencies between Controllers, Services, and Repositories and to reduce code coupling.

### General implementation scheme

Based on MVC, we build a basic architecture:

Service - a class that implements business logic.

Repository - a class that manages data storage (MySQL, MongoDB, ClickHouse). We write all queries to the databases in it.

Entity - a class that describes the structure of databases.

Transformer - a class that transforms the final result for further transmission. Place it next to the service for which it is intended.

To link the Controller, Service, and Repository together, we use a Value Object. The Value Object also combines the concept of DTO to reduce the number of entities. Often, DTOs when changing business logic often merge into Value Objects.

## General Interaction Scheme

### Prohibited/Permitted/Required in Domain

In the Domain, the use of ORM models is prohibited. Instead, we use Entity to store data and Repository to write queries to the database. And for business relationships between entities, we use Aggregate.

In Service methods, explicit variables of types such as int/string/float/bool, as well as Entity/ValueObject/Aggregate objects, should be passed as input.

It is prohibited to write a Controller that implements multiple methods. We write one route - one Controller.

In Controllers that implement business logic from Domains, it is prohibited to use the basic Request.

The use of traits is prohibited in projects.
Direct access to the container is prohibited (with the exception of service providers and factories).

#### Project structure

---- src/  
---- ---- ExampleDomain/  
---- ---- ---- [Aggregates/](#aggregates)  
---- ---- ---- [Constants/](#constants)  
---- ---- ---- [Entities/](#entity)  
---- ---- ---- [Exceptions/](#exceptions)  
---- ---- ---- [Repositories/](#repository)  
---- ---- ---- [Services/](#services)  
---- ---- ---- [ValueObjects/](#dto-и-valueobject)  
---- ---- ExampleInfrastructure/  
---- ---- ---- Commands/  
---- ---- ---- Controllers/  
---- ---- ---- EventListeners/  
---- ---- ---- Requests/  
---- ---- ---- Responses/  

### Entity

Entity - defines a certain entity in the business logic and always has an identifier (a unique key. The key can be expressed as an id/guid/uuid or a combination of properties (a composite unique key) that will identify the entity), by which the Entity can be found or compared to another Entity. If two Entities have identical identifiers, they are the same Entity. It is almost always mutable.

```php
<?php
namespace App\BrandDomain\Entities;

use Doctrine\ORM\Mapping\Entity;
use Doctrine\ORM\Mapping\Table;
use Symfony\Component\Uid\Uuid;
use Doctrine\ORM\Mapping as ORM;
use Doctrine\ORM\Mapping\UniqueConstraint;

/**
 * @Entity
 * @Table(name="brands")
 */
class BrandEntity
{
    /**
     * @ORM\Id()
     * @ORM\Column(type="uuid")
     */
    private Uuid $guid;

    public function __construct(
        /**
         * @ORM\Column(length: 140)
         */
        private readonly int $name,
    ) {
        $this->guid = Uuid::v4();
    }

    public function getGuid(): Uuid
    {
        return $this->guid;
    }
}
```

### DTO and ValueObject

Data Transfer Object (DTO) - is an object, a data structure that carries information between processes (Controllers, Services, Repositories). It is desirable not to change already set properties to get rid of implicit logic. It has no dependencies. It only contains typed properties and getters/setters. No logical actions can be performed in this object. It is often used to pass filters from the controller to services and repositories.

Why pass an object instead of just specifying a typed variable in the method executor? When supporting functionality, the amount of code and features increases. The maximum number of variables passed to a method should be a maximum of 5. If a service is frequently used in different functionalities, each call will need to be rewritten. This is not correct. Passing an object to the executor of a service or repository method will maintain the structure of the code.

Value Object - is an immutable type, the value of which is set at creation and does not change throughout the life of the object. It has no identifier. They can contain logic (validation, implement interfaces \JsonSerializable, Arrayable, \Countable\Serializable, etc.) and usually they are not used to transfer information between applications. They are used inside services to work with entities. They can be returned as a result of a service execution. If two Value Objects are structurally identical, they are equivalent. Methods for checking and filtering data can be written, for example isEmpty, equals, sanitizedName.
```php
<?php
namespace App\BrandDomain\ValueObjects;

final class BrandCreatorObject
{
    public function __construct(
        public readonly string $brandName,
    ) {}
}
```

### Aggregates 

Aggregate - the use of aggregates allows avoiding excessive connection between objects that make up the model. This avoids confusion and simplifies the structure by not allowing the creation of tightly coupled systems.

Instead of adding unnecessary relationships to an entity and then creating an entity without any relationships, for example.

```php
<?php
namespace App\BrandDomain\Aggregates;

use \App\BrandDomain\Entities\BrandEntity;
use \App\BrandDomain\ValueObjects\BrandCreatorObject;

final class BrandCreatorAggregate
{
    public function __construct(
        public readonly BrandEntity $brandEntity,
        public readonly BrandCreatorObject $brandCreatorObject,
    ) {}
}
```

### Constants 

Constant - is a regular class with constants. Everyone always encounters regular expressions, status IDs, etc. Now, all constants are moved to separate classes for reuse.

```php
<?php
namespace App\BrandDomain\Constants;

final class DbConst
{
    const BRANDS = 'brands';
}
```

### Exceptions 

Exceptions - stores named errors. Do not throw the default exception if you need to throw an error - throw a custom error. They should be used and necessary everywhere in Repositories, Services, etc.
```php
<?php
namespace App\BrandDomain\Exceptions;

final class BrandCreatorException extends \Exception
{
}
```

### Dependency injection

Dependency injection (DI) is a style of object configuration where an object's fields are set by an external entity. In other words, objects are configured by external objects. DI is an alternative to self-configuration of objects.

### Repository

Repository - these are classes that represent collections of objects. They do not describe storage in databases, caching, or the solution of any other technical problem. Repositories represent collections. How we store these collections is simply an implementation detail. In repositories, we write all queries to databases such as MySQL, MongoDB, ClickHouse, etc. We do not implement methods for deleting/editing/creating entities in repositories. We use UnitOfWork for this.
```php
<?php
namespace App\BrandDomain\Repositories;

use App\BrandDomain\ValueObjects\BrandFilterObject;
use App\BrandDomain\ValueObjects\BrandObject;
use Doctrine\DBAL\Connection;
use App\BrandDomain\Constants\DbConst;
use Symfony\Component\Uid\Uuid;

final class BrandRepository
{
    public function __construct(
        private Connection $connection;
    ) {
    }

    // Example of retrieving a single object
    public function getBrandByGuid(Uuid $guid): BrandObject
    {
        $builder = $this->connection->createQueryBuilder();
        $brand = $builder->select([
                'guid',
                'name',
            ])
            ->from(DbConst::BRANDS)
            ->andWhere("guid = {$builder->createNamedParameter($guid->toString())}")
            ->fetchAssociative();

        return new BrandObject(
            Uuid::fromString($brand['guid']),
            $brand['name'],
        );
    }

    // An example of obtaining a list  
    public function getBrandsByFilter(BrandFilterObject $filter): \Generator
    {
        $builder = $this->connection->createQueryBuilder();
        $query = $builder->select([
                'guid',
                'name',
            ])
            ->from(DbConst::BRANDS);
        ...
        foreach ($query->executeQuery()->iterateAssociative() as $brand) {

            yield new BrandObject(
                Uuid::fromString(brand['guid']),
                $brand['name'],
            );
        }
    }
    
    // Example of checking for existence
    public function isExistByName(string $name): bool
    {
        $builder = $this->connection->createQueryBuilder();
        $guid = $builder->select([
            'guid',
        ])
            ->from(DbConst::BRANDS)
            ->andWhere("name = {$builder->createNamedParameter($name)}")
            ->fetchFirstColumn();

        return !empty($guid);
    }
}
```

### UnitOfWork 

UnitOfWork - [pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html) is used in Service to save/edit/delete all entities within a single transaction. An example of implementation is [Entity Manager in Doctrine](https://symfony.com/doc/current/doctrine.html).

### Services 

Service - is a class or classes that implement business logic and interact with entities. The implementation of the service depends on the use cases. Services can implement their own architecture, implement patterns, and use additional entities created within the service. Upon exiting the service, we still obtain the entities described here: Entity/Aggregate/ValueObject.
```php
<?php
namespace App\BrandDomain\Services\Create;

use App\BrandDomain\ValueObjects\BrandCreatorObject;
use App\BrandDomain\Aggregates\BrandCreatorAggregate;
use App\BrandDomain\Repositories\BrandRepository;
use App\BrandDomain\Exceptions\BrandCreatorException;
use Doctrine\ORM\EntityManagerInterface;

final class BrandCreator
{
    public function __construct(
        private readonly BrandRepository $brandRepository,
        private readonly EntityManagerInterface $entityManager,
    ) {}

    public function create(
        BrandCreatorObject $brandCreatorObject,
    ): BrandCreatorAggregate
    {
        if ($this->brandRepository->isExistByName($brandCreatorObject->brandName)) {
            throw new BrandCreatorException("Brand exist");
        }
        $brand = new BrandEntity($brandCreatorObject->brandName);

        $this->entityManager->persist($brand);

        return new BrandCreatorAggregate(
            $brand,
            $brandCreatorObject,
        );
    }
}
```

### Collections

We recommend using the implementation [https://github.com/ramsey/collection](https://github.com/ramsey/collection) as they are typed. If possible, avoid using arrays.

## Automated Architecture Checking

To automate the checking of architecture, we recommend using [deptrac](https://github.com/qossmic/deptrac) and [psalm](https://github.com/vimeo/psalm) in projects at level 3. It should be checked on every pushed branch to the repository using an action.
