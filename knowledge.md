:Patterns
  similar goals, archieve them in different ways
  modularity, flexibility, testability, maintainability
  reusable solution

  :Software design patterns
    @read php https://github.com/domnikl/DesignPatternsPHP

    :Behavioral
      :Chain Of Responsibilities
        @read php https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/ChainOfResponsibilities

        consisting of a source of command objects and a series of processing objects

        each processing object contains logic that defines the types of command objects that it can handle
        the rest are passed to the next processing object in the chain

        @purpose
          to build a chain of objects to handle a call in sequential order
          if one object cannot handle a call, it delegates the call to the next in the chain and so forth

        @examples
          - SPAM filter
          - a loggin framework
          - Caching (memcache->db)

        @example
          class Handler
            $successor = null

            append (Handler $handler) ->
              if not $successor
                $successor = $handler
              else
                $successor.append($handler)

            handle (args) ->
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
        @read php https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/Command
        to encapsulate invocation and decoupling

        @example
          interface CommandInterface
            execute () -> ;

          class Invoker
            setCommand (CommandInterface $cmd) ->
              $command = $cmd

            run () ->
              $command->execute()

          class Receiver
            write (str) -> print str

          class HelloCommand implements CommandInterface
            __construct(Receiver $console) ->
              $output = $console

            execute () ->
              $output->write('hello world')

          $receiver = new Receiver

          $invoker = new Invoker
          $invoker->setCommand(new HelloCommand($receiver))
          $invoker->run()

      :Iterator
        @read php https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/Iterator

        to make an object iterable and to make it appear like a collection of objects

        @examples
          to process a file line by line by just running over all lines

        @example
          class Book
          class BookList
          class BookListIterator
          class BookListReverseIterator

      :Mediator
        @read php https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/Mediator

        provides an easy to decouple many components working together
        all components (called Colleague) are only coupled to the MediatorInterface

        @example
          abstract class Colleague
            __construct (MediatorInterface $medium) -> ;

          interface MediatorInterface
            sendResponse ($content)
            makeRequest ()
            queryDb ()

          class Mediator implements MediatorInterface
            setColleague(Database $database, Client $client, Server $server) -> ;
            sendResponse() -> client.output()
            makeRequest() -> server.process()
            queryDb() -> database.process()

          class Client extends Colleague
            request() -> medium->makeRequest()
            output(args) -> print(args)

          $client = new Client($media)
          $media = new Mediator()
          $media->setColleague(new Database($media), $client, new Server($media));
          $client->request()

      :Memento
        @php read https://github.com/domnikl/DesignPatternsPHP/tree/master/Behavioral/Memento

        provide the ability to restore an object to its previous state (undo via rollback)

        @examples
          class Memento
            __construct($stateToSave) -> ;
            getState() -> return $state

          class Originator
            setState ($state) -> ;
            saveToMemento() ->
              return new Memento($state);
            restoreFromMemento (Memento $memento) ->
              $state = $memento->getState()

          class Test
            $first = new Originator
            $first->setState('aa')
            $first->saveToMemento()
            $someState = $first->restoreFromMemento()

      :NullObject
      :Observer
      :Specification
      :State
      :Strategy
      :TemplateMethod
      :Visitor

    :Creational
      :Abstract Factory
        to create series of related or dependent objects
        without specifying their concrete classes

        @example
          interface Picture { ... }
          interface Text { ... }

          abstract class AbstractFactory
            function createPicture()
            function createText()

          class HtmlFactory extends AbstractFactory
            function createPicture()
              return new Html\Picture(...)
            function createText()
              return new Html\Text(...)

          class JsonFactory extends AbstractFactory
            function createPicture()
              return new Json\Picture(...)
            function createText()
              return new Json\Text(...)

          $factory = new HtmlFactory
          $factory instanceof Picture

      :Builder
        is an interface that build parts of a complex object

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

      :Factory Method
        is an object for creating other objects
        a class creates the object you want to use

        depends on abstractions, not concrete class
        (archieve the "D" from SOLID principles)

        in class-based programming a factory is an abstraction of a constructor of a class
        in prototype-based programming a factory is an abstraction of a prototype object

        factory method / factory function

        @pros
          if you'll change a source-class later - you'll need to make changes only in factory-method (instead of every place where it used)
          instead of repeating logic of creating new instance, you delegate it to factory

        @cons
          unneeded complexity

        @example
          class AutomobileFactory
            static function create($param)
              return new Automobile($param1)

          new AutomobileFactory::create('Opel', 'Astra')

        :SimpleFactory
          @example
            class StaticFactory
              classes =
                name1: className1
                name2: className2

              factory (name) ->
                className = classes[name]
                return new className

        :StaticFactory
          @example
            class StaticFactory
              factory (name) ->
                className = '\Some' + name
                return new className

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

      :Pool
        object pool
        uses a set of initialized objects kept ready to use

      :Prototype
        to avoid the cost of creating objects the standard way (new Foo())
        and instead create a prototype and clone it

      :Singleton
        to have only one instance of this object in the application that will handle all calls

    :Structural
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
      :FluentInterface
      :Proxy
      :Registry


    :Mapper
      @l20n :Распределитель
      @read http://design-pattern.ru/patterns/mapper.html

    :Money
      @l20n :Деньги
      uses for multicurrency
      @problem how to add 10usd to 10uah?
      @problem how to round the result?

    :Registry
      @l20n :Реестр
      to access common objects and services

    :Value Object
      @l20n :Объект-значение
      like small data-type

    :Repository
      @l20n :Репозиторий

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

:REST
  Representational State Transfer
  ROA = Resource Oriented Architecture
  
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
  protocol developed by Google

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