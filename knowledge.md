:RPM
  Red Hat Package Manager
  is a package management system

:Patterns
  similar goals, archieve them in different ways
  modularity, flexibility, testability, maintainability
  reusable solution

  :Software design patterns
    @read #php https://github.com/domnikl/DesignPatternsPHP
    @read #php GOF http://www.fluffycat.com/PHP-Design-Patterns/

    :Creational
      focus on handling object creation mechanisms
      how object will be created

      :Factory Method
        @read #php http://www.fluffycat.com/PHP-Design-Patterns/Factory-Method/
        
        creates the object you want to use
        will create the exact subclass depend on the value you pass
        depends on abstractions, not concrete class (archieve the "D" from SOLID principles)

        in class-based programming a factory is an abstraction of a constructor of a class
        in prototype-based programming a factory is an abstraction of a prototype object

        @pros
          if source-class will changed - you'll need to make change only in factory-method
          instead of repeating logic of creating new instance, you delegate it to factory

        @cons
          unneeded complexity

        @example
          abstract class AbstractFactoryMethod
            abstract function makePHPBook($param)

          class OReillyFactoryMethod extends AbstractFactoryMethod
            $context = "OReilly"
            function makePHPBook($param) ->
              return new OReillyPHPBook($param)

          class SamFactoryMethod extends AbstractFactoryMethod
            $context = "Sam"
            function makePHPBook($param) ->
              return new OReillyPHPBook($param)

          (new OReillyFactoryMethod)->makePHPBook('test')

        :SimpleFactory
          the Factory Method w\ map of classLabel <-> className

          @example
            class StaticFactory
              classes =
                name1: className1
                name2: className2

              factory (name) ->
                className = classes[name]
                return new className

        :StaticFactory
          the Factory Method w\ realtime generation name of className

          @example
            class StaticFactory
              factory (name) ->
                return new ('\Some' + name)

      :Abstract Factory
        @read http://www.dofactory.com/javascript/abstract-factory-design-pattern

        provide an interface
        for creating families of related or dependent objects
        without specifying their concrete classes

        creates objects that are related by a common theme

        is an object that creates other objects

        @example
          AbstractFactory

          LightFactory extends AbstractFactory
            ..creates a light html elements

          DarkFactory extends AbstractFactory
            ..creates a dark html elements

          both creates the same types of controls

        @example
          // declares an interface for creating products
          abstract class AbstractFactory
            abstract method1()
            abstract method2()

          // others can be - EmployeeFactory, VendorFactory
          class ConcreteFactory extends AbstractFactory
            method1() -> return new Product1
            method2() -> return new Product2

          // others can be - Employee, Vendor
          // instances being created by the factory
          class Product1 implements AbstractProduct
          class Product2 implements AbstractProduct

          // an interface for the instances that are being created
          interface AbstractProduct

          $factory = new ConcreteFactory
          $factory instanceof AbstractProduct

      :Builder
        @read #php http://www.fluffycat.com/PHP-Design-Patterns/Builder/

        is an interface that build parts of a complex object

        separate the construction of a complex object from its representation
        so that the same construction process can create different representations

        construction details are hidden
        encapsulate construction
        is to simplify client code that creates complex objects

        @example
          interface BuilderInterface
            createVehicle
            getVehicle
            addWheel
            addEngine
            addDoors

          class BikeBuilder implements BuilderInterface
            createVehicle -> new Parts/Bike

          class CarBuilder implements BuilderInterface
            createVehicle -> new Parts/Car

      :Pool
        object pool
        uses a set of initialized objects kept ready to use

      :Prototype
        @read #php http://www.fluffycat.com/PHP-Design-Patterns/Prototype/

        make new objects by cloning the objects which you set as prototypes

        pros:
          to avoid the cost of creating objects the standard way (new Foo())
          and instead create a prototype and clone it

        @example
          abstract class BookPrototype
            abstract function __clone
            function getTitle -> this.$title
            function setTitle ($title) -> this.$title = $title
            function getTopic -> this.$topic

          class PHPBookPrototype extends BookPrototype
            function __construct -> this.$topic = 'PHP'
            function __clone -> ;

          class SQLBookPrototype extends BookPrototype
            function __construct -> this.$topic = 'SQL'
            function __clone -> ;

          $book1 = clone $sqlProto;

      :Singleton
        @read #js http://addyosmani.com/resources/essentialjsdesignpatterns/book/#singletonpatternjavascript

        a class distributes the only instance of itself

        cons:
          global access point

        :Multiton
          to have only a list of named instances that are used (like a Singleton but with N instances)
          stores inself few Singleton instances
          use DependencyInjection instead of

          @example
            class Multiton
              getInstance (name) ->
                if not self::instances[name]
                  self::instances[name] = new self

                return self::instances[name]

    :Behavioral
      based on the way objects play and work together
      about their communication between

      :Chain Of Responsibilities
        @read php https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/ChainOfResponsibilities
        @read php http://www.fluffycat.com/PHP-Design-Patterns/Chain-Of-Responsibility/

        a method called in one object,
        will move up a chain of objects
        until one is found that can properly handle the call

        consisting of a source of command objects and a series of processing objects

        each processing object contains logic that defines the types of command objects that it can handle
        the rest are passed to the next processing object in the chain

        @purpose
          to build a chain of objects to handle a call in sequential order
          if one object cannot handle a call, it delegates the call to the next in the chain and so forth

        @examples
          - SPAM filter
          - a logging framework
          - Caching (memcache -> db -> file -> void)

        @example
          class Handler
            $successor = null

            function append (Handler $handler) ->
              if not $successor
                $successor = $handler
              else
                $successor.append($handler)

            function handle (args) ->
              $processed = processing(args)
              if not $processed
                $processed = $successor.handle(args)
              return $processed

            processing (args) -> ;

          class FastStorage extends Handler
            processing (args) ->
              ...
              return true/false

          class SlowStorage extends Handler
            processing (args) ->
              ...
              return true/false

          $chain = new FastStorage(..params..)
          $chain.append(new SlowStorage(..params..))

      :Command
        @read #php https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/Command
        @read #php http://www.fluffycat.com/PHP-Design-Patterns/Command/

        to encapsulate invocation and decoupling
        an object encapsulates everything needed to execute a method in another object

        @example
          interface CommandInterface
            function execute () -> ;

          class Invoker
            function setCommand (CommandInterface $cmd) ->
              $command = $cmd

            function run () ->
              $command->execute()

          class Receiver
            function write ($str) -> print $str

          class HelloCommand implements CommandInterface
            function __construct(Receiver $console) ->
              $output = $console

            function execute () ->
              $output->write('hello world')

          $receiver = new Receiver

          $invoker = new Invoker
          $invoker->setCommand(new HelloCommand($receiver))
          $invoker->run()

      :Iterator
        @read #php https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/Iterator
        @read #php http://www.fluffycat.com/PHP-Design-Patterns/Iterator/

        to make an object iterable and to make it appear like a collection of objects
        traverse

        @examples
          to process a file line by line by just running over all lines

        @example
          class Book
          class BookList
          class BookListIterator
          class BookListReverseIterator

      :Mediator
        @read #php https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/Mediator
        @read #php http://www.fluffycat.com/PHP-Design-Patterns/Mediator/

        provides an easy to decouple many components working together
        all components (called Colleague) are only coupled to the MediatorInterface

        @example
          abstract class Colleague
            function __construct (MediatorInterface $medium) -> ;

          interface MediatorInterface
            function sendResponse ($content)
            function makeRequest ()
            function queryDb ()

          class Mediator implements MediatorInterface
            function setColleague(Database $database, Client $client, Server $server) -> ;
            function sendResponse() -> client.output()
            function makeRequest() -> server.process()
            function queryDb() -> database.process()

          class Client extends Colleague
            function request() -> medium->makeRequest()
            function output(args) -> print(args)

          $client = new Client($media)
          $media = new Mediator()
          $media->setColleague(new Database($media), $client, new Server($media));
          $client->request()

      :Memento
        @read #php https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/Memento
        @read #js http://www.dofactory.com/javascript/memento-design-pattern
        @read #php http://www.fluffycat.com/PHP-Design-Patterns/Memento/

        one object stores another objects state
        provide the ability to restore an object to its previous state (undo via rollback)

        captures the object internal state,
        so object can be restored to this state later

        @diagram
          class Originator
            createMemento()
            setMemento(memento)

          class Memento extends Originator
          class Caretaker extends Memento

        @example
          class BookReader
            function __construct (BookReader $bookReader) ->
              $this->setPage($bookReader)
              $this->setTitle($bookReader)

            function getPage ()
            function setPage ($page)

          class BookMark
            function getPage (BookReader $bookReader)
            function setPage (BookReader $bookReader)

          $bookReader = new BookReader ("")
          $bookMark = new BookMark ($bookReader)

          $bookReader->setPage("104")
          $bookMark->setPage($bookReader)

          $bookMark->getPage($bookReader)

        @example
          class Memento
            function __construct($stateToSave) -> ;
            function getState() -> return $state

          class Originator
            function setState ($state) -> ;
            
            function saveToMemento() ->
              return new Memento($state)

            function restoreFromMemento (Memento $memento) ->
              $state = $memento->getState()

          class Test
            $first = new Originator
            $first->setState('aa')
            $first->saveToMemento()
            $someState = $first->restoreFromMemento()

      :NullObject
        @read #php https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/NullObject

        @examples
          #php SF2 null logger of profiler
          #php SF2 null output in Symfony/Console

        @diagram
          class LoggerInterface
          class PrintLogger implements LoggerInterface
          class NullLogger implements LoggerInterface

      :Observer
        @read http://www.dofactory.com/javascript/observer-design-pattern
        Pub/Sub == Publication/Subscription

         one-to-many dependency between objects so that when one object changes state,
         all its dependents are notified and updated automatically

         offers a subscription model
         where objects subscribe to an event
         get notified when the event occurs

         is a cornerstone of event-driven programming
         promotes loose coupling

         @diagram
          // e.g.: Click
          class Subject
            subscribe()
            unsubscribe()
            notify()

          // e.g.: clickHandler
          class Observers

      :Specification

      :State
        @read http://www.dofactory.com/javascript/state-design-pattern

        allow an object to alter its behavior when its internal state changes.

        @diagram
          // e.g.: TrafficLight
          class Context
            request() -> ;

          // e.g.: Red, Yellow, Green
          class State1
            handle() -> ;

      :Strategy
        define a family of algorithms, encapsulate each one, and make them interchangeable.
        lets the algorithm vary independently from clients that use it.

        encapsulates alternative algorithms for a particular task
        allows a method to be swapped out at runtime by any other method

        @examples
          test the performance of few algorithms
            one-by-one

        @diagram
          // maintains a reference to the current Strategy object
          // e.g.: Shipping
          class Context
            request()
            process()

          // implements the algorithm
          // e.g.: UPS, USPS, Fedex, etc
          class Strategy1 extends Context
          class Strategy2 extends Context
          class Strategy3 extends Context

        @example
          class Shipping
            setStrategy (company) -> ;
            calculate (package) ->
              company.calculate(package)

          class UPS
            calculate (package) -> '$20'

          class USPS
            calculate (package) -> '$18'

          class Fedex
            calculate (package) -> '$15'

          $ups = new UPS
          $usps = new USPS
          $fedex = new Fedex

          $shipping = new Shipping
          $shipping.setStrategy($ups)
          print $shipping.calculate()

          $shipping.setStrategy($usps)
          print $shipping.calculate()

          $shipping.setStrategy($fedex)
          print $shipping.calculate()

      :TemplateMethod
        @read http://www.dofactory.com/javascript/template-method-design-pattern

        define the skeleton of an algorithm in an operation, deferring some steps to subclasses
        lets subclasses redefine certain steps of an algorithm without changing the algorithm structure

        @examples
          RouteProcess has preProcess, process, postProcess methods

        @diagram
          // provides the hooks
          // implements the basic methods
          // e.g.: datastore
          class AbstractClass
            step1()
            step2()
            step3()

          // implements the primitive steps
          // e.g.: mysql
          class ConcreteClass extends AbstractClass
            step1()
            step2()
            step3()

      :Visitor
        @read #js http://www.dofactory.com/javascript/visitor-design-pattern
        @read #php https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/Visitor

        an operation to be performed on the elements of an object structure
        lets you define a new operation without changing the classes of the elements on which it operates

        defines a new operation to a collection of objects without changing the objects themselves
        the new logic resides in a separate object called the Visitor

        are useful when building extensibility in a library or framework

        objects have a 'visit' method, that accepts a Visitor object

        @example
          // maintains a collection of Elements which can be iterated over
          // e.g.: employees array
          ObjectStructure

          // defines an accept method
          // the "visitor" param is "this"
          // e.g.: Employee objects
          Elements <- ObjectStructure
            accept (visitor) -> ;

          // implements a visit method
          // e.g. ExtraSalary, ExtraVacation
          Visitor
            visit (element) -> ;

        @example
          class Employee
            accept (visitor) -> visitor.visit(self)
            get/set Name() -> ;
            get/set Salary() -> ;
            get/set Vacation() -> ;

          class ExtraSalary
            visit (employee) -> employee.setSalary(...)

          class ExtraVacation
            visit (employee) -> employee.setVacation(...)

          $visitorSalary = new ExtraSalary
          $visitorVacation = new ExtraVacation
          $employee1 = new Employee
          $employee1.accept($visitorSalary)
          $employee2 = new Employee
          $employee2.accept($visitorVacation)

      :Module in javascript
        The Module pattern
          var myObject = (function () {
            ...
            return { ... }
          })()

        Object literal notation
          var myObject = { ... }

        AMD modules

        CommonJS modules

        ECMAScript Harmony modules

    :Structural
      based on the idea of building blocks of objects

      are concerned with object composition
      and typically identify simple ways to realize relationships between different objects

      :Adapter
        @read php https://github.com/domnikl/DesignPatternsPHP/tree/master/Structural/Adapter

        to translate one interface for a class into a compatible interface
        allows classes to work together (that normally could not because of incompatible interfaces)

        @example
          interface PaperBookInterface
          class Book implements PaperBookInterface

          interface EBookInterface
          class Kindle implements EBookInterface

          class EBookAdapter implements PaperBookInterface
            __construct (EBookInterface $ebook)

          someBook = new Kindle()
          new EBookAdapter(someBook)

      :Bridge
        decouple an abstraction from its implementation, so that the two can vary independently

        @example
          interface Workshop
            work -> ;

          class Assemble implements Workshop
            work -> 'Assembled'

          class Car extends Vehicle
            __construct (Workshop $workShop1, Workshop $workShop2) ->
              parent::__construct ($workShop1, $workShop2)

            manufacture ->
              'car'
              workShop1->work()
              workShop2->work()

          class Motorcycle extends Vehicle
            __construct (Workshop $workShop1, Workshop $workShop2) ->
              parent::__construct ($workShop1, $workShop2)

            manufacture ->
              'Motorcycle'
              workShop1->work()
              workShop2->work()

          class Produce implements Workshop
            work -> 'Produced'

      :Composite
        to treat a group of objects the same way as a single instance of the object.

        @example
          class FormElement
            render -> ;

          class InputElement extends FormElement
            render -> 'input'

          class TextElement extends FormElement
            render -> 'text'

          class Form extends FormElement
            array elements

            render ->
              foreach elements as element
                out += element.render()

              return out

            addElement (FormElement element) ->
              elements.push element

          embed = new Form
          embed.addElement(new InputElement)

          form = new Form
          form.addElement(new InputElement)
          form.addElement(new TextElement)
          form.addElement(embed)

          form.render()

      :DataMapper
        @read php https://github.com/domnikl/DesignPatternsPHP/blob/master/Structural/DataMapper/UserMapper.php

        is a Data Access Layer that performs bidirectional transfer of data between a persistent data store
        an in memory data representation (the domain layer)

        to keep the in memory representation and the persistent data store independent of each other
        and the data mapper itself

        the key point of this pattern is, unlike Active Record pattern, the data model follows SRP

        @example
          class User
            private id
            private username
            private email

            __construct($id = null, $username = null, $email = null) -> ;
            __get() -> ;
            __set() -> ;

          class UserMapper
            __construct (DBAL $dbLayer) -> ;

            save (User $user) ->
              $data =
                id: user->id
                username: user->username
                email: user->email

              $adapter->save($data)

            findById ($id) ->
              $row = $adapter->find($id)
              return mapObject($row)

            mapObject (array $row) ->
              $entry = new User
              $entry->setId $row[id]
              $entry->setUsername $row[username]
              $entry->setEmail $row[email]

              return $entry

      :Decorator
        @read php https://github.com/domnikl/DesignPatternsPHP/tree/master/Structural/Decorator

        to dynamically add new functionality to class instances

        @example
          interface RendererInterface
            renderData -> ;

          class Webservice implements RendererInterface
            __construct (data) -> ;
            renderData -> data

          abstract class Decorator implements RendererInterface
            __construct (RendererInterface $wrappable) -> ;

          class RenderInJson extends Decorator
            renderData ->
              output = wrapped.renderData()
              return json_encode(output)

          class RenderInXml extends Decorator
            renderData ->
              output = wrapped.renderData()
              return xml_encode(output)

          service = new Webservice
          (new RenderInJson).renderData(service)

      :IOC
        Inversion of Control

        instead of a higher level class depending on a lower level class (dependecy) implementation,
        the control is inversed so the lower level class implementation depends on an abstraction required by the higher level class.

        reversed to DependencyInjection

        DI and SL are implementation of ICO

        @examples
          interface IRepository
          class XmlRepository implements IRepository
          class MyClass
            __construct (IRepository $repo) {}

        :Service Locator
          @read #php https://github.com/domnikl/DesignPatternsPHP/tree/master/More/ServiceLocator

          implement a loosely coupled architecture in order to get
          better testable, maintainable and extendable code

          are an implementation of the Inverse of Control pattern

          you can register a service for a given interface

          @examples
            ZF2 uses SL to create/share service that used in framework (EventManager, ModuleManager, etc)

          terms
            Service - main object, that you can receive from container
            Service Definition - the logic of initialized service
            Service Container - central object that saves all descriptions and can create objects by them

          @diagram
            class dependencies: C -> A -> B
            C -> ServiceLocator -> A -> ServiceLocator -> B

          @diagram
            interface ServiceLocatorInterface
              get ($interface) -> ;
              has ($interface) -> ;

            class ServiceContainer implementation ServiceLocatorInterface
              $services
              add ($interface, $service) -> call $services[$key]
              has ($key) -> $services[$key]
              get ($key) -> call $services[$key]

            interface LogServiceInterface
            class LogService implements LogServiceInterface

            interface DatabaseServiceInterface
            class DatabaseService implements DatabaseServiceInterface

          @example
            $container = new ServiceContainer();
            $container['grabber'] = -> return new Grabber();
            $container['html_filter'] = -> return new HtmlExtractor();

        :DependencyInjection
          @read php https://github.com/domnikl/DesignPatternsPHP/tree/master/Structural/DependencyInjection

          to implement a loosely coupled architecture in order to get better testable, maintainable and extendable code

          @example
            abstract class AbstractConfig
              __construct($storage) -> ;

            interface Parameters
              get($key);
              set($key, $value);

            class ArrayConfig extends AbstractConfig implements Parameters
              get($key) ->
                storage[$key]

              set($key, $value) ->
                storage[$key] = $value

            class Connection
              __construct(Parameters $config) -> ;

            source = array(...)
            config = new ArrayConfig(source)

            connection = new Connection(config)
            connection.connect()

      :Facade
        @read http://www.dofactory.com/javascript/facade-design-pattern

        provide a unified interface to a set of interfaces in a subsystem
        defines a higher-level interface that makes the subsystem easier to use

        @diagram
          // e.g.: Mortgage
          class Facade
            method()

          // e.g.: Bank, Credit, Background
          class SubSystem1 extends Facade
          class SubSystem2 extends Facade
          class SubSystem3 extends Facade

      :FluentInterface

      :Proxy
        a place holder object representing the true object

      :Registry
        @read #php http://habrahabr.ru/post/183658/

        is a container where we keep an objects,
        pass them inside the application

        cons
          the redundant object will be created (that probably will be never used after)
          additional dependency in one place
          the method that accepts $registry can use any of services inside
            so we cant say what exactly service will be used
            we need to check to source code before

        @example
          $registry = new ArrayObject();
          $registry['grabber'] = new Grabber();
          $registry['filter'] = new HtmlExtractor();
          $registry['google_finder'] = new GoogleFinder($registry['grabber'], $registry['filter']);

      :Flyweight
        @read #js http://www.dofactory.com/javascript/flyweight-design-pattern

        sharing to support large numbers of fine-grained objects efficiently
        conserves memory
        is an object normalization technique

        @diagram
          // calls to FlyweightFactory to obtain flyweight objects
          // e.g.: Computer
          class Client

          // creates and manages flyweight objects
          // e.g.: 
          class FlyweightFactory extends Client
            getFlyweight()

          // maintains intrinsic data to be shared across the application
          // e.g.: 
          class Flyweight extends FlyweightFactory

    Other
      :Delegation
        @read #php https://github.com/domnikl/DesignPatternsPHP/tree/master/More/Delegation

        @example
          class TeamLead
            __construct(JuniorDeveloper $junior) ->
              $slave = $junior

            writeCode () ->
              $slave->writeBadCode()

          class JuniorDeveloper
            writeBadCode () -> ;

          $junior = new JuniorDeveloper
          $lead = new TeamLead($junior)
          $lead->writeCode()

      :Money
        @l20n :Деньги
        uses for multicurrency
        @problem how to add 10usd to 10uah?
        @problem how to round the result?

      :Value Object
        @l20n :Объект-значение
        like small data-type

    :Mapper
      @l20n :Распределитель
      @read http://design-pattern.ru/patterns/mapper.html


    :Registry
      @l20n :Реестр
      to access common objects and services

    :Active Record
      @l20n :Активная запись

      one object for related data and theirs behaviors
      since majority data is persistent, AR stores logic for accessing to those data
      one object wrap presentation of one unit from DB

      @example
        class Employee
          int $id
          string $name
          string $lastname
          get()
          insert()
          update()
          delete()
          changeName()

  :Architectural Patterns
    divide and conquer
    similar to Software Design Patterns, but have a broader scope

    :EDA
      @read http://en.wikipedia.org/wiki/Event-driven_architecture
      Event-driven architecture

    :MV* (octopus)
      @watch MV* http://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DPR305
      
      based upon a separation of duties
      model - data (business loginc)
      view - presentation layer
      c/vm/p - glue logic
      
      all are MVC-inspired

      :MVC
        @read http://www.itu.dk/courses/VOP/E2005/VOP2005E/8_mvc_krasner_and_pope.pdf

        first described in 1979 for Smalltalk
        "C" is centerpiece that decouples the Model and View
        separate graphical interface from business logic
        separate business logic from data

        View is requesting data from the Model and then deciding how to render it and which templates to use
        Controller is responsible for changing the state of both View and Model

        model
          independent of the other parts of the product
          does not need to know about the elements of design
          and how it will be displayed

          active
            informs to View that changes occurred

          passive
            #

        view
          read-only from model
          application state

        controller

        control flow:
          - user interact

      :MVP
        @read #dotnet http://rsdn.ru/article/patterns/ModelViewPresenter.xml

        originated in early 1990s
        is derivativive of MVC
        View cooperates with Presenter (by calling the relevant functions)

        types of implementation
          passive view
            view is passive, and unaware of model
            presenter changes state of model, reads information and passes it to view
            Presenter requests information from Model collects it, modifies it, and passes it to the passive View
          supervising controller

      :MVVM
        largely based on MVC

        controller is swapped out for a ViewModel
        ViewModel is responsible for the translation between View's expectations and Models's logic
        View requests data from controller
        Controller translates the request so that Model can understand it

        ViewModel is a "Model of the View"
          extends the Model with behaviors
          data binding between View and Model
          passed commands between the View and Model
          is an abstraction over the view

        view-model

        @example
          Silverlight
          Windows Presentation Foundation (WPF)

      :MVA
        Model-View-Adapter

      :HMVC (PAC)
        ability of a controller to execute sub-controllers
        each with own independent triad of M, V and C
        You gain modularity and maintainability
        pay with some hit in performance

  :Resign Patterns :Anti-pattern
    anti-pattern represents a lesson that has been learned

    @read http://addyosmani.com/resources/essentialjsdesignpatterns/book/#antipatterns

    in 1995 by Andrew Koenig

    describe a bad solution
    describe how to get out

    @examples
      - global namespace by defining a large number of variables in the global context

      - eval()

      - Modifying the Object class prototype

      - 

    hard code
    soft code
    magic numbers
    magic strings
    magic button
    race condition
    accidental complexity
    code spaghetti
    God Object
    detonator
    absolver
    stake
    public Morozov
    dependency hell

    mushroom management
      you all are the ants, and I'm the anthill
    seagull management
    design by commitee
    analysis paralysis
    Knight in shining armor
    Tempest Method
    witch hunt

:Functional programming
  trying to simulate the mathematic with a help of programs

:Object-oriented programming
  trying to simulate the surrounding world with a help of programs

:MMU
  Memory Management Unit
  hardware support for virtual memory

  @read ru http://habrahabr.ru/post/211150/
  

:OOD Object Oriented Design
  @read http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod

:SOLID
  @watch https://www.youtube.com/watch?v=jP7fSA8DuJQ

  named by Robert C. Martin

  :SRP Single responsibility
    a class should have only a single responsibility
    there should never be more than one reason for a class to change
    
    doing the one thing - you can focus on that
    the class is doint too much

    @example
      (new Report).Print()
  
  OCP: Open-closed
    software entities (classes, modules, functions, etc) should be opened for extension
    and closed for modification

  LSP: Liskov substitution
    objects in a program should be replaceable with instances of their subtypes without altering the correctness of that program

  ISP: Interface segregation
    many client-specific interfaces are better than one general-purpose interface

  DIP: Dependency inversion
    Depend upon Abstractions. Do not depend upon concretions

:GRASP
  @read http://regfordev.blogspot.de/2011/02/grasp.html#.VVkYPJMrKHo

:OSI
  Open System Interconnection model
  1978

  layers
    Application
      HTTP, Telnet, DNS, FTP, DHCP

    Presentation
      ASCII, Unicode

    Session
      SSL, TLS, SOCKS

    Transport
      TCP, UDP, SMB

    Network
      IPv4, IPv6, TCP/IP

    Data link
      Ethernet, PPP, PPPoE, PPTP

    Physical

:CORBA

:CORS
  Cross-Origin Resource Sharing
  specification that enables truly open access across domain-boundaries
  alternative for JSONP, since support not only GET http request

:API
  Application Programming Interface
  allows for publicly exposed methods of an application to be accessed and manipulated outside

  when you wish to obtain data from a application without having to actually visit the application itself

  endpoint

:OAuth
  is a mechanism that allows you to create temporary tokens
  is a common used scheme for authentication and authorization

:OAuth2
  is a complete new way of authentication which is easier to implement and maintain

:SOA
  Service Oriented Architecture

:WebService
  a way of integrating applications using the XML, SOAP, WSDL, etc
  for building the API, to simplify the access to the app

  web consists of types of apps:
    1) computing nodes
    2) web-resources

  :JSON-RPC
  
  :JSON-WS
  
  :XML-RPC
    built in 1998 by Microsoft

    pros
      - simple
      - human readable

    cons
      - hasnt strict validation of format
      - need a custom description of service

    @example
      <methodCall>
        <methodName>examples.getAirportCode</methodName>
        <params>
          <param>
             <value><i4>567</i4></value>
          </param>
        </params>
      </methodCall>

  :REST
    Representational State Transfer
    ROA = Resource Oriented Architecture

    by Roy Filding
    
    is an architecture style (set of resources)
    consisting of guidelines and best practices for creating scalable web services
    lead to a more performant and maintainable architecture

    simpler alternative to SOAP and WSDL-based Web services

    is the DBMS of the internet
    is about resources, and how to represent them

    @read http://coreymaynard.com/blog/creating-a-restful-api-with-php/

    for developing web APIs
    stateless client-server relationship
    takes advantage of the HTTP request methods

    ~ when you use REST, things are simpler

    characteristics
    - resources
      uri
        every resource is uniquely adressable using URIs
      uniform interface
        all resources share a uniform
        consists of
          methods (http - GET/POST/DELETE/HEAD)
          representation
    - protocol
      client-server
      stateless
      cachable
      layered

    authentication
      simple way - use the HTTP basic authentication
      :HMAC
        hash based message authentication
        digest = base64encode(hmac("sha256", "secret", "GET+/users/foo/financialrecords"))
        Authentication: hmac johndoe:[digest]
      OAuth
        temporary tokens
        John "asks" the server for a "token" and "secret",
        and with these token and secret, it is allowed to access its protected resources
      OAuth2
        # 

    caching
      The goal of caching is never having to generate the same response twice
      gain speed and reduce server load
      can be used a reverse-proxy (like Varnish)
      for purging caches used http verb PURGE
        resource should be marked expired

    versioning
      /api/v2/

    :HATEOAS
      Hypertext As The Engine Of Application State

      @example
        GET /account/12345 HTTP/1.1
        HTTP/1.1 200 OK
        <?xml version="1.0"?>
        <account>
          <account_number>12345</account_number>
          <link rel="deposit" href="/account/12345/deposit" />
          <link rel="withdraw" href="/account/12345/withdraw" />
        </account>

    :COD
      code-on-demand
      optional constraint in REST


    - separates user interface concerns from data storage concerns
    - improves portability of interface across multiple platforms
    - stateless (no client state)
      each request from client to server must contain all of the information necessary to understand the request
      session state is kept on client
    - ??? cachable
    - uniform interface
    - layered system
    - uniform operations
      in db: insert, select, update, delete (CRUD)
      over http: POST, GET, PUT, DELETE
        PATCH
          for updating partial resources (when you need to update only one field on resource)
          is :idempotent (no effect when called one than once)
            a = 4

    - resources
      any information that can be names is a resource
      resource is a mapping to a set of entities
      are transfered between client and server
      may include metadata describing themselves
    - resource identifiers
      uri - name that uniquely identifies it
      uri like a primary key of each row in a db
      uri - resource locator
      good, clean, structured URIs are helpful for developers
    - representation
      formats html, xml, json, etc

  :CORBA
    Common Object Request Broker Architecture

  :SOAP
    Simple Object Access Protocol
    by Microsoft
    wrapped by envelope
    "you need a rope when you will try to debug the SOAP"
    is evolved from XML-RPC
    more applicable in complex architectures
    if you will have more operations that CRUD
    manipulation with remote objects

    :WSDL
      Web Service Description Language
      specification for developers
      :IDL (Interface Definition Language) for WebServices

      types of request
        one-way
        request-response
        solicit-response
        notification

:HAL
  Hypertext Application Language
  is a simple format that gives a consistent and easy way to hyperlink between resources in your API
  human-friendly

:JSON-LD
  JSON-based Serialization for Linked Data

:Database
  :CAP
    theorem

  :Relational database
    :PostgreSQL
      @todo

    :MySQL
      @todo

  :NoSQL
    :Document-Orinted
      :MongoDB
        stores data in collections, that consists of documents
        document - is a big JSON object without specific format and structure
        for storing the random json objects
        written in C++, released at 2009

        features
          ad-hoc queries
          indexing
          replication
          load balancing
          file storage
          aggregation
          server-side js execution
          capped collections

        v3
          arbitrary engines for storing data
            WiredTiger included instead of LevelDB
            e.g. InMemory can be implementated
            was developer by Dr Michael Cahill from PostgreSQL and Keith Bostic from UNIX
          document-level locking
          log-structured merge-trees, LSM
          mongo-driver was rewritted for Ruby
          added explain command to mongo tools (mongo -import, -export, -dump, etc)

        v2
          used LevelDB for r/w ops
          collection-level locking

    :Key-Value
      :Redis
        @todo

    :Column-oriented
      @todo

    :Graph-oriented
      :Neo4j
        @todo

      :GraphDb
        @todo

:Refactoring
  it is not a tool (its smth inside your brain)

:MapReduce
  is an algorithm that can process a very large amount of data in parallel
  paradigme
  programming model for distributed computing
  send "map" and "recuce" tasks to cluster

  Collection MapReduce(Collection source, Function map, Function reduce)

  stages
    split
    map
      the responsability is to convert an item from the source collection
      to zero or many instances of Key/Value objects

      (K1,V1) => list(K2,V2)
      ArrayOfKeyValue Map(object itemFromSourceCollection)
    combine/shuffle
      sort all Key/Value instances
      create new object instances where all values are grouped by Key
    reduce
      will return a new item instance that will be included into the result collection
      (K2,list(V2)) => list(K3,V3)
      ItemResult Reduce(KeyWithArrayOfValues item)

:PHP
  @read http://php.net/manual/en/appendices.php

  v5.6
    - Constant Scalar Expressions
      const ONE = 1
    - Variadic Functions via "..."
      public function query($query, ...$params) { ... }
    - Arguments Unpacking via "..."
      $args = [1, 3, 5, 7, 9];
      someMethod(...$args)
      =>
      someMethod(1, 3, 5, 7, 9)
    - Exponentiation via **
      2 ** 3 === 8
    - Use function and use const
      Importing namespaced functions
      namespace {
        use foo\bar as b;
      }
    - Large file uploads
      uploads of over 2GB are now accepted
    - implemented cURL HTTP2 support

  v5.5
    - added Generators
      function test() {
        yield 5;
      }
    - finally keyword added (try/catch)
    - foreach support list() in a value
    - foreach now supports non-scalar keys
    - array and string literal dereferencing
      'test'[1]
      [1,2,3][1]
    - Class name resolution via ::class

  v5.4
    - traits
    - short syntax for arrays
    - function array dereferencing
      foo()[1]
    - Closures now support $this
    - <?= is always available
    - Binary number format has been added
      0b001001101
    - added null-byte protection for exec/system/passthru

  v5.3 (2009)
    - namespaces
    - Late Static Bindings
    - jump labels (limited goto)
    - Support for native Closures
    - __callStatic() and __invoke() added
    - shorthand form of ternary operator
    - Exceptions can now be nested

  v5
    added "clone" work for Object Model
    constants in class
    class visibility protectors (private, protected, public)
    constructors and descructors
    abstract classes
    interfaces
    magic methods
    final methods
    __autoload
    PDO / DOM / Hash - SPL modules

:SPDY
  SPeeDY
  protocol, developed by Google

:Comet
  allows to send data from server to browser, withoud requesting them by browser

  :long polling
    steps
      client send ajax-request,
      server read/flush/sleep,
      send response in N seconds/minutes,
      client retreive response, parse it, and send another the same request 

  :WebSockets
    @read ru http://habrahabr.ru/post/79038/

  :AJAX
    # 

:DNS
  Domain Name System

  type of records
    A - address record
    AAAA - IPv6 address record
    CNAME - canonical name record
    MX - mail exchange
    NS - name server
    PTR - domain name pointer
    SOA - start of authority

:Load Balancing
  request -> dns -> load-balancer-1/2/n -> server-1/2/3/n

  dns: Amazon Route 53
    @watch https://youtu.be/tj2V0DCaYUM
    Health Checks
    
    types of magic
      Latency Based Routing
        check latency (time to receiving data from server-1/2)
        searching for fast server
      Geo DSN
        only from EU
        default
      Weighted Routin Robin
        server1: weight:100 (probability 0.25)
        server2: weight:300 (probability 0.75)
        for A/B tests
      DNS Failover
        primary/secondary
        always return primary-server
        secondary can be error-page (ex "we now about problem, we're solving it now")
      Combiner

  LB: Elastic LC, HAProxy
  region

:HTTP
  :TCP/IP
    TCP, IP, UDP, etc
    layers
      Application layer
        data for applications
      Transport layer
        delivering data
      Internet layer
        delivering between networks
      Link layer
        transmission packets on physical layer

:HTTPS
  @read #ru https://blog.yandex.ru/post/77455/

  handshake algorithm
    1) Bob -> box(with Bob-lock) -> Alice
    2) Alice -> box (with Bob-lock, and with Alice-lock) -> Bob
    3) Bob remove own Bob-lock -> box (with Alice->lock) -> Alice
    4) Alice got box(with Alice-lock)

:WebSocket
  @read #ru http://habrahabr.ru/post/79038/

  0x00, <UTF-8 encoded string>, 0xFF
  0x80, <length>, <binary body>

:HTTP2
  @read http://daniel.haxx.se/http2/
  @read http://tools.ietf.org/html/draft-ietf-httpbis-http2-12

  by developer (why made curl/libcurl) from Mozilla
  by HTTPbis (from latin "bis" means two)

  based on SPDY
  no minor versions ever (the next will be http3)
  binary protocol
    detecting the start and the end of packet is the one of biggest problem
  using frames
    12 different frames (DATA, HEADERS, etc)
    type, length, flags, identifier, data
      identifier to assiciate different parallel streams
  threads multiplexing
    different streams are mixed up, but server can recognize them by frame identifier
  stateless


  http 1.1
    http conveyor belt transmission
      like a queue in supermarket - you don't know persons in the front of you,
      and you don't know how much time they will occupy
    using sprites
    embeded images
      css background url with data image/png
    concating
    resources sharding
      since HTTP allows only 2 parallel tcp connections

:Graphs
  V - vertex
  E - edge (directed, undirected)

  @example
    V = {1, 2, 3, ..., 13}
    E = {{1,2}, {9,11}, {7,13}}

:Software development process
  :Methodologie
    :Waterfall
      @read http://leadinganswers.typepad.com/leading_answers/files/original_waterfall_paper_winston_royce.pdf
      @watch https://www.youtube.com/watch?v=X1c2--sP3o0&feature=youtu.be

      design process
      used in software development processes
      in which progress is seen as flowing steadily downwards (like waterfall)

      iterational software developoment

      used to development of airplanes/bridges/etc
      perfectionism at every stage

      model
        requirements
        design
        implementation
        verification
        maintenance

      pros
        no remaking the work that was completed
          a clear sequence of steps of the life cycle
          without possibility return to the previous step
        good specification flows into a good documentation
          and as a result we will have a lot of documentation
        clear model
        developers may have low qualifications

      cons
        required perfectionism at every stage
        it is difficult to make changes
        excessive design
        dividing developers to "perfect" and "code monkeys"

    :RUP
      Rational Unified Process
      @read ru http://www.interface.ru/rational/rup01_t.htm
      
      strict approach for the allocation of problems
      and responsibility inside of organization-developer

      the goal is to guarantee creating the application exact in time and exact in project-budged
      with UML

      iterational software developoment
      requirements management
      using the component architecture
      visual modeling
      software quality testing
      monitoring changes in software

      in workflows

      has knowledge base in Web

    :Scrum
      @read http://scrum.org.ua/wp-content/uploads/ScrumAndKanbanRuFinal.pdf
      @read ru http://scrum.org.ua/wp-content/uploads/2008/12/scrum_xp-from-the-trenches-rus-final.pdf

:Deferred
  decouple logic from behaviors
  FIFO (queue)

  API
    .then(doneCallbacks, failedCallbacks)
    .done(doneCallbacks)
    .fail(failedCallbacks)
    .resolve(args)
    .reject(args)
    .resolveWith(context, args)
    .rejectWith(context, args)
    .isResolved()
    .isRejected()

  @example
    d = $.Deferred()
    setTimeout(d.resolve, 3000)
    return d.promise()

  :Promise
    object
    implements observer
    "object is observable or not"

:Rainbow Table
  is a precomputed table for reversing cryptographic hash functions
  usually for cracking password hashes

:Security
  :OWASP Top-10
    Injections
      by Structured Query Language
    Broken Authentication and Session Management
    Cross Site Scripting
    Insecure Direct Object References
      mysite.ru/read_message.jsp?id=123654
    Security Misconfiguration
    Sensitive Data Exposure
    Missing Function Level Access Control
    Cross-Site Request Forgery, CSRF/XSRF
    Using Components with Known Vulnerabilities
    Unvalidated Redirects and Forwards

:Tests
  :Coupling and :Cohesion
    @read http://home.adelphi.edu/sbloch/class/adages/coupling_cohesion.html
    @read https://msdn.microsoft.com/en-us/magazine/cc947917.aspx

    "Coupling" describes the relationships between modules
    "cohesion" describes the relationships within them

:Docker
  consists of two parts:
    1) a daemon
      a server process that manages all the containers
    2) client
      acts as a remote control for the daemon

  Docker Hub Registry
    containers images cloud-base store

  @examples
    docker search <name>
    docker pull <username>/<container>
    docker run <username>/<container> <command>
    docker inspect <container-id>

  Boot2Docker windows client for Docker
    boot2docker ssh
    boot2docker ip

:git
  save like to a database
  never commit half-done work

  :rebase
    @read #eng https://www.atlassian.com/git/tutorials/merging-vs-rebasing/conceptual-overview
    @read #ru http://habrahabr.ru/post/161009/

    never to do it on public (e.g. master) branches,
    where are working >=one developers (since their master-HEAD will be not modified)

    @example
      master: A -> B -> C (master-HEAD)
      feature: A -> D -> E -> F (feature-HEAD)
      rebase feature master
      feature: A -> B -> C -> D -> E -> F (feature-HEAD)

:UNIX
  
  :procfs
    is a special filesystem in Unix-like operating systems that presents information about processes
    it is mapped to a mount point named /proc at boot time

    /proc/PID/cmdline
    /proc/PID/cwd
    /proc/PID/environ
    /proc/PID/exe
    /proc/PID/fd
    /proc/PID/fdinfo
    /proc/PID/maps
    etc
    /proc/buddyinfo
    /proc/cmdline
    /proc/cpuinfo
    etc

  :LXC
    LinuX Containers


  commands
    :nice
      used to invoke a utility or shell script with a particular priority
      is usually called niceness

      highest priority: -20
      lowest priority: 20
      $ nice -n 19 tar cvzf archive.tgz largefile