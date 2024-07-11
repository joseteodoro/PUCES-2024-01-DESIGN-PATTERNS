# Patterns

Design patterns in object-oriented programming (OOP) provide reusable, time-tested solutions to common software design problems. These patterns offer blueprints for structuring objects and their interactions, promoting flexibility, maintainability, and code clarity. Understanding design patterns is a valuable asset for any OOP developer striving to create well-structured applications.

## Creational Patterns / Singleton

Uma unica instancia em memoria em qualquer lugar da sua aplicação!

- let's code statically

- lets you ensure that a class has only one instance, while providing a global access point to this instance

- abstract factories, builders, prototypes and pools can all be implemented as singletons

- what about concurrence?

```java

// unica leitura do nosso arquivo de configuracao
public class ConfigFileSingleton {
    
    // on-demand | lazy load
    private static ConfigFileSingleton INSTANCE = null;

    // qualquer lugar do seu codigo vc invoca essa funcao, sem ncessidade de um new
    // quem chama paga o preco da carga
    // nao trabalha bem com concorrencia
    public static ConfigFileSingleton getInstance() {
        if (INSTANCE == null) {
            INSTANCE = new ConfigFileSingleton();
        }
        return INSTANCE;
    }

    private String fileContent = null;

    private ConfigFileSingleton() {
        fileContent = OS.readFile("./config.yaml")
    }

    ///...
}


//
var config1 = ConfigFileSingleton.getInstance()
var config2 = ConfigFileSingleton.getInstance()

config1 == config2

// unica leitura do nosso arquivo de configuracao
public class ConfigFileSingleton {
    
    // eager
    // nao tem problema de concorrencia
    private static ConfigFileSingleton INSTANCE = new ConfigFileSingleton();

    // qualquer lugar do seu codigo vc invoca essa funcao, sem ncessidade de um new
    // quem paga o preco é o startup da aplicacao
    public static ConfigFileSingleton getInstance() {
        return INSTANCE;
    }

    private String fileContent = null;

    private ConfigFileSingleton() {
        fileContent = OS.readFile("./config.yaml")
    }

    ///...
}


// unica leitura do nosso arquivo de configuracao
public class ConfigFileSingleton {
    
    // on-demand | lazy load
    // seguro pra concorrencia
    private static ConfigFileSingleton INSTANCE = null;

    // qualquer lugar do seu codigo vc invoca essa funcao, sem ncessidade de um new
    // quem chama paga o preco da carga
    // ganha o custo do semaforo
    // seguro para concorrencia
    public static  ConfigFileSingleton getInstance() {
        if (INSTANCE == null) {
            INSTANCE = new ConfigFileSingleton();
        }
        return INSTANCE;
    }

    private String fileContent = null;

    private ConfigFileSingleton() {
        fileContent = OS.readFile("./config.yaml")
    }

    ///...
}


public class DatabaseConfigFileSingleton extends ConfigFileSingleton {

/// nao herda nada static

}



// quem chama isso é seu framework (spring / .net framework)
@Component
public class Factory {
    @Bean
    public ConfigFile build() {
        return new ConfigFile();
    }
}

// exemplo de problema
public class Transaction {
    start()
    commit()
    rollback()
}

@Component
public class Factory {
    @Bean
    public ConfigFile build() {
        return new OrderController();
    }
}

class OrderController {

    private Transaction trx;

    POST
    save(){
        this.trx = new Transaction()
    }

    POST
    append(){
        //
    }

    POST
    finish() {
        this.trx.commit()
    }

}

```

### Cons

- violates the Single Responsibility Principle. The pattern solves two problems at the time

- the Singleton pattern can mask bad design, for instance, when the components of the program know too much about each other

- it may be difficult to unit test the client code

- if not synchronized, can lead to race conditions

## Creational Patterns / Factory method

- Keep going with contracts and types (when possible)

- enables extension (creating new types and reusing the same contract without other changes)

- centralize and hide object creation / logic / dependencies

### Usage

use the Factory Method to hide types and dependencies of the objects your code should work with (keep the contract!) - Factory + Dependency Inversion

use to remove switch cases clauses or multiple if statements

```java
public interface User {
    String getName();
    String getType();
}

public class Builder {
    User build()
}

// foco na abstracao, nao so esconde complexidade
// como tambem promove reuso e abstracao
public class UserFactory {

    Map<String, Builder> buildersByType;

    public User build(String type, String name, String [] permissions) {
        // return new root
        // return new guest
        // return new paidClient
        if (type.equals("root")) {
            // todas as linhas ncessarias pra criar o root
            //
            //
            //
            return UserRootBuilder.withUsername(name).withPassword(name).build()
        }
        //..
        if (type.equals("guest")) {
            // todas as linhas ncessarias pra criar o Guest
            //
            //
            //
            return UserGuestBuilder.withUsername(name).withPassword(name).build()
        }
    }

}


```

Nao confundir com o builder.

```java
    // builder
    // esconde detalhes da criacao do objeto
    UserRoot root = UserRootBuilder.withUsername("banana").withPassword("batatinha").build()

    // factory
        // esconde detalhes da criacao do objeto
    User user = UserFactory.build("root", "banana")

    if (req.user.exists()) {
        var builder = UserRootBuilder.withUsername("banana").withPassword("batatinha")
        var type = "root";
    }
    else {
        var builder = GuestBuilder.withUsername("banana").withPassword("batatinha")
        var type = "guest"
    }
    UserFactory.build(type, builder)
```

```java

// todos esses ifs vao pra dentro da factory
// esconde complexidade e adiciona generalizacao / abstracao
public class UserFactory {
    User build(condicao) {
        if (uma condicao) {
            return new Root();
        }
        else if (outra condicao)
            return new Guest();
        else if (outra condicao)
            return new PaidUser();
        else 
            return EmptyUser();
    }
}

// se fosse um user
// promove reuso, abstracao, SRP
User us = UserFactory.build(parametros da condicao)
us.save()

```

### Cons

- The code may become more complicated since you need to introduce a lot of new subclasses to implement the pattern

- some coupled code between factory and the concrete classes


## Creational Patterns / Builder

esconder complexidade, deixar valores padrao, simplificar o uso

```java

class DbConnection {
    DBConnection(provider, url, port, user, pass) {}
}
DbConnectionBuilder {
    Integer port = null;
    String provider = "sqlite"
    String url = "sqlite::mem"

    withprovider(String provider){
        //
        return this
    }
    withPort(String Port){
        //
        return this
    }
    withPassword(String Password){
        //
        return this
    }


    //
    public DbConnection build() {
        return DBConnection(this.provider, this.url, this.port, this.user, this.pass)
    }
}

// por default uso sqlite, se tiver em var env eu postgres? mysql local rodando em docker?
var connection = DbConnectionBuilder.build()
connection.Connect()
connection.query("select * from tabelA");

// codificacao fluente
var connection = DbConnectionBuilder
    .withProvider("postgres")
    .withUrl()
    .withPort()
    .build()

connection.Connect()
connection.query("select * from tabelA");
```

Lombok

```java
@Data
@Builder
public class DbConnection {
    String provider;
    String  url;
    String  port;
    String  user;
    String  pass
}

DbConnection.Builder().withUrl()...build()
```

## Factory vs Builder

- ambos escondem complexidade de criacao de objetos;

- builder sempre volta a mesma implementacao;

- a factory sempre volta um tipo generico;


## Behavioral Patterns / Strategy

- Lets you define a family of algorithms, put each of them into a separate class, and make their objects interchangeable.



```java

public interface OrderProcessor {
    void process(Order order)
}


public class PairOrderProcessor implements OrderProcessor {
    public void process(Order order) {
        ///
        if (order.address.state.fee > 0) {
            ////
        } else {
            ////
        }
        ////
    }
}

public class DefaultOrderProcessor implements OrderProcessor {
    public void process(Order order) {
        // faco outra coisa
    }
}

public class PrepaidOrderProcessor implements OrderProcessor {
    public void process(Order order) {
        // faco outra coisa
    }
}

//..
//..
//..
//..
// force reuso, forca abstracao, forcam encapsulamento
var processor = OrderProcessorFactory.build(order.type()).build()
processor.process(order)
//..
//..
//..
//..

```

quando usamos isso?

```java
public class MyRepository {
    // diferentes implementacoes pra reusar trechos do seu codigo
    // usa composicao
    private DbConnection connection;

    public void Save(MyObject obj) {
        connection.save(obj)
    }
}


```


### Cons

- Clients must be aware of the differences between strategies to be able to select a proper one if you dont use a factory;

### Usage

- Use the Strategy pattern when you want to use different variants of an algorithm within an object and be able to switch from one algorithm to another during runtime.

- Use the Strategy when you have a lot of similar classes that only differ in the way they execute some behavior.

- Use the pattern to isolate the business logic of a class from the implementation details of algorithms that may not be as important in the context of that logic.

## Behavioral Patterns / Template Method

Defines the skeleton of an algorithm in the superclass but lets subclasses override specific steps of the algorithm without changing its structure.

### Usage

Use the Template Method pattern when you want to let clients extend only particular steps of an algorithm, but not the whole algorithm or its structure.

Template Method is based on inheritance: it lets you alter parts of an algorithm by extending those parts in subclasses. Strate- gy is based on composition: you can alter parts of the object’s behavior by supplying it with different strategies that corre- spond to that behavior. Template Method works at the class level, so it’s static. Strategy works on the object level, letting you switch behaviors at runtime.

Use the pattern when you have several classes that contain almost identical algorithms with some minor differences. As a result, you might need to modify all classes when the algorithm changes.

```java

public class PairOrderProcessor extends Processor {
    public void process(Order order) {
        ///
        if (order.address.state.fee > 0) {
            ////
        } else {
            ////
            this.makeReservation()
            //
        }
        ////
    }
}

public class DefaultOrderProcessor extends Processor {
    public void process(Order order) {
        // faco outra coisa
        this.makeReservation(order)
        // outra coisaw
    }

}

public abstract class Processor {
    public void DoProcess(Order order) {
        //..
        //..
        //..
        //..
        this.process(order)
        ////
        //..
        //..
        //..
        //..
    }
    public abstract void process(Order order);
    public void makeReservation(order){}
}

```


### Cons

Template methods tend to be harder to maintain the more steps they have.

Try strategy first because its uses composition instead of inheritance.

## Template vs Strategy

- resolvem problemas de promover reuso no seu software

- strategy usa composicao

- strategy contra template, é que vc nao fica preso na cadeia de heranca do template. voce tem um reuso maior em outras partes do seu software;

- template usa heranca;

- template contra strategy, se voce tiver blocos de codigo sendo reutilizados dentro do template, ele funciona melhor que o strategy;

## Structural Patterns / Decorator

// quem resolve esse tipo de problema é ou strategy ou o template

```java
//..
//..
//..

    if
        //
    else if
        //
    else
        //

//..
//..
//.
```

// quem resolve esse tipo de problema é decorator

```java
//..
//..
//..
    if
        //
    else if
        //
    else
        //
```

ou 

```java
    if
        //
    else if
        //
    else
        //
//..
//..
//..
```

tags html

```java
//
//
//
//
//
// calculando impostos e frete
if (client.isPremium() || client.paid()) {
    var delivery = 0
    if (client.isPremium()) {
        var fee = 0
    }
    else if (client.paid()) {
        var fee = 0.10
    }
}
else {
    var delivery = 0.20
    var fee = 0.20
}

public interface Calc {
    Float calc();
}


class class PaidCalc implements Calc {
    private Calc delegate;

    public Float calc() {
        return this.delegate.calc(order) + outro valor
        // faco algo
    }
}

class class PremiumCalc implements Calc {
    private Calc delegate;

    public Float calc() {
        return this.delegate.calc(order) + outro valor
        // faco algo
    }
}

class class DefaultCalc implements Calc {
    private Calc delegate;

    public Float calc() {
        return this.delegate.calc(order) + outro valor
        // faco algo
    }
}

//
var calculadora = new Default();
if (client.isPaid()) {
    calculadora = new PaidCalc(calculadora)
}
if (client.isPremium()) {
    calculadora = new PremiumCalc(calculadora)
}
..
..
..
Float value = calculadora.calc(order)

```

- delegate depende da ordem

negrito(italico(texto)) = <b><i><p>text</p></i></b>
italico(negrito(texto)) = <i><b><p>text</p></b></i>

aplicaDesconto(naoPagaFrete(valorNotaFiscal))
aplicaDesconto(PagaFrete(valorNotaFiscal)) =! PagaFrete(aplicaDesconto(valorNotaFiscal))

## Structural Patterns / Adapter (Wrapper)

* Allows objects with incompatible interfaces to collaborate

* The adapter implements the interface of one object and wraps the other one

* Promotes abstractions and promotes polymorphism;

* Promotes interface seggregation;

### Usage

* Use the Adapter class when you want to use some existing class, but its interface isn’t compatible with the rest of your code;

* Reuse existent code for a new feature;

```java

public interface Exporter<T> {
   public T export();
}

public class OrderReport implements Exporter<XMLContent> {

   public OrderExporter(Date start, Date end) {
      this.start = start;
      this.end = end;
   }

   public XMLContent export() {
      List<Order> orders = this.listOrdersBetween(start, end);
      Template template = this.loadReportTemplate();
      return this.xmlEngine.createReport(template, orders);
   }

}

// temporario
public class JsonOrderReport implements Exporter<JSONContent> {
   private final OrderExporter exporter;

   public JsonOrderReport(Date start, Date end) {
      this.exporter = new OrderExporter(start, end);
   }

   public JSONContent export() {
      return JSONConverter.convertFromXML(this.exporter.export());
   }
}
```

* Reuse to change the contract for new or old code

* Migrations and design refactoring (short lived code)!

```java

public interface ExporterV1<T> {
   public T export();
}

public interface ExporterV2<T> {
   public T export(Date start, Date end);
}

public class OrderReportV1 implements ExporterV1<XMLContent> {

   public OrderReportV1(Date start, Date end) {
      this.start = start;
      this.end = end;
   }

   public XMLContent export() {
      List<Order> orders = this.listOrdersBetween(start, end);
      return this.xmlEngine.createReport(orders);
   }

}

// temporario
public class V2ToV1Adapter implements ExporterV1<XMLContent> {

   public OrderReportV1(Date start, Date end) {
      this.start = start;
      this.end = end;
   }

    public T export() {
        // delegate
        new ConcreteExporterV2().export(this.start, this.end)
    }

}

// temporario
public class V1ToV2Adapter implements ExporterV2<XMLContent> {

    public T export(Date _, Date _) {
        // delegate
        new ConcreteExporterV1().export()
    }

}


// new signature using old existent code without change on the old code
public class OrderReportV2 implements ExporterV2<XMLContent> {

   public XMLContent export(Date start, Date end) {
      return new OrderReportV1(start, end).export();
   }

}

// new content using old interface to pass our new code for old libraries / old code
public class OrderReportAdapter implements ExporterV1<XMLContent>, ExporterV2<XMLContent> {

   public OrderExporter(Date start, Date end) {
      this.start = start;
      this.end = end;
   }

   public XMLContent export() {
      return this.export(this.start, this.end);
   }

   public XMLContent export(Date start, Date end) {
      return new OrderReportV1(start, end).export();
   }

}
```

* to pass existent code / instances for third party library

```java

WAWebClient(datasourceprovider)

// third party connection configuration
public interface DataSourceProvider {
   public DataSource datasource();
}

public class DataSourceAdapter implements DataSourceProvider {

   private EntityManager em;

   public DataSourceAdapter(DataSourceAdapter em) {
      this.em = em;
   }

   public DataSource datasource() {
      EntityManagerFactoryInfo info = (EntityManagerFactoryInfo) em.getEntityManagerFactory();
      return info.getDataSource();
   }

}

// passing our connection to old third library
EntityManager myEntityManagerInstance = get from somewhere;
new OldLibrary(new DataSourceAdapter(myEntityManagerInstance)).doSomething();
```

### Cons

* The overall complexity of the code increases because you need to introduce a set of new interfaces and classes. Sometimes it’s simpler just to change the service class so that it matches the rest of your code;

* The code is not elegant, ugly and can lead to errors;

* should be short lived (short lived code and short lived tests);

## Structural Patterns / Proxy (cache | lazy loading)

class User {

    @Lazy
    List<LoginEntries> logins;

    String name;
    String email;
    String password;
}

List<LoginEntries> logins = EmptyList() {
    // sabe como carregar os objetos de verdade
}

* lets you provide a substitute or placeholder for another object. A proxy controls access to the original object, allowing you to perform something either before or after the request gets through to the original object

* Lazy evaluation

* cache multi level

```java
interface ExpensiveResource {
   public byte [] get();
}

class ConcreteExpensiveResource implements ExpensiveResource {
   public byte [] get() {
      // my huge array
   } 
}

class ProxyExpensiveResource implements ExpensiveResource {
   private byte bytes = null

   public byte [] get() {
       if (bytes != nul) {
            return bytes
       }
      // load only when needed
      bytes = ConcreteExpensiveResoure.loadFromSomewhere();
      return bytes;
   } 
}

```

```java
public interface DataStore {
    String get(String key);
    void put(String key, String value)
}


DataStore store = new Memory(new Redis(new Database()))

store.get()

class Memory {
    private DataStore delegate;

    private cache<String, String> values;

    String get(String key) {
        if (values.keys.has(key)) {
            return values.get(key)
        }
        return delegate.get(key)
    }
}


DataStore store = new Redis(new Database())


```

### Proxy can be

* Access control (protection proxy). This is when you want only specific clients to be able to use the service object; for instance, when your objects are crucial parts of an operating system and clients are various launched applications (including malicious ones).

* Local execution of a remote service (remote proxy). This is when the service object is located on a remote server.

* Caching request results (caching proxy). This is when you need to cache results of client requests and manage the life cycle of this cache, especially if results are quite large.

### Cons

* The code may become more complicated since you need to introduce a lot of new classes.

* The response from the service might get delayed.

## Strategy | template method vs proxy vs adapter vs decorator

// delegate
this.doSomething()

this.strategy
// polimorfismo de apenas parte do codigo
// aumenta o reuso
class OrderExporter {
    private FormatExporter<XML|JSON> delegate;
}

// adapter (temporario)
// convivencia entre codigos nao compativeis (gambiarra)
    this.delegate.

// decorator
    // comportamento de composicao de elementos
    //faz alguma coisa
    //this.delegate

Element e = new B(new I(new P("conteudo")))
Element e =  new I(new P("conteudo"))
Element e =  new P("conteudo")

// proxy
// lazy | cache
// this.delegate

// delegate
strategy, template, adapter, decorator

```java
this.fazedorDeAlgo.facaAlgo()
```

## Behavioral Patterns / Iterator | what about Generator?

### Iterator

```java
   // old fashion C like for
   List<FileContent> files = listFiles();
   List<FileContent> processedFiles = List.of();
   for (int i = 0; i < list.size(); i++) {
      FileContent content = files.get(i);
      doProcess(content);
      processedFiles.add(content);
   }

   // using iterator
   List<FileContent> files = listFiles();
   Iterator<FileContent> it = files.iterator();
   while (it.hasNext()) {
      FileContent content = it.next();
      pushToFTPServer(content);
   }

   // using foreach
   List<FileContent> files = listFiles(); 
   for (FileContent content : files) {
      pushToFTPServer(content);
   }

   // using streams
   listFiles()
      .stream()
      .map(pushToFTPServer); 
```

```js
// eager list
[].forEach() // itera na ordem da lista e sincrono, nao tem retorno
[].map() // itera fora da ordem e assincrono, com retorno

[].map(
    item => {
        // faz alguma coisa
        // retorna um outro item
    }
}

[].reduce // stream.grouping
[].reduce( // itera na ordem da lista e sincrono, tem retorno
    (acc, item) => {
        acc
        return acc;
        // faz alguma coisa
        // retorna um outro item
    }, initial
}
```

### Generator

- economia de recurso pra executar grandes cargas on-demand

```js
    // on-demand list
   function* generator() {
      yield 1; // first call stops here
      yield 2; // second call stops here
      yield 3; // third call stops here
      // end of generator (no more items)
   }

   const gen = generator(); // "Generator { }"

   console.log(gen.next().value); // 1
   console.log(gen.next().value); // 2
   console.log(gen.next().value); // 3
```


```js
   function* infinite() {
      let index = 0;

      while (true) {
         yield index++; // each call stops here and return the index value before its increment!
      }
   }

   const generator = infinite(); // "Generator { }"

   console.log(generator.next().value); // 0
   console.log(generator.next().value); // 1
   console.log(generator.next().value); // 2

    function* dbEntries(resultSet rs) {
        let index = 0;
        yield rs.next();
   }
```

## Behavioral Patterns / Command

Turns a request into a stand-alone object that contains all information about the request. This transformation lets you parameterize methods with different requests, delay or queue a request's execution, and support undoable operations;

```js
// using code as usual
orderService.newOrder({
   clientId: 1001,
   items: [
      {id: 1, quantity: 1, price: 1.1},
      {id: 2, quantity: 1, price: 1.2},
      {id: 25, quantity: 10, price: 1.2},
      {id: 1023, quantity: 10, price: 0.3}
   ]
})

// using commands
const message = {
   command: 'new-order',
   params: {
      clientId: 1001,
      items: [
         {id: 1, quantity: 1, price: 1.1},
         {id: 2, quantity: 1, price: 1.2},
         {id: 25, quantity: 10, price: 1.2},
         {id: 1023, quantity: 10, price: 0.3}
      ]
   }
}

newOrderQueue.send(message);
```

### Usage

Use the Command pattern when you want to parametrize objects with operations;

Use the Command pattern when you want to queue operations, schedule their execution, or execute them remotely;

Use the Command pattern when you want to implement reversible operations. Like a sequence of events and you final state is also a compilation of all those commands;

```js
const operations = [
   {user: 1, action: 'deposit', value: 100.0, createdA: 1622139300108},
   {user: 1, action: 'debit', value: 100.0, createdA: 1622139300108},
   {user: 34, action: 'deposit', value: 10000.0, createdA: 1622139300108},
   {user: 63, action: 'deposit', value: 120.0, createdA: 1622139300108},
   {user: 34, action: 'withdraw', value: 130.0, createdA: 1622139300108},
   {user: 85, action: 'deposit', value: 101.0, createdA: 1622139300108},
   {user: 3, action: 'deposit', value: 110.0, createdA: 1622139300108},
   {user: 2, action: 'deposit', value: 20.0, createdA: 1622139300108},
   {user: 1, action: 'deposit', value: 100.0, createdA: 1622139300108},
   {user: 60, action: 'deposit', value: 5.0, createdA: 1622139300108},
   {user: 34, action: 'deposit', value: 12.0, createdA: 1622139300108},
   {user: 1, action: 'withdraw', value: 150.0, createdA: 1622139300108}
   {user: 1, action: 'snapshot', value: 50.0, createdA: 1622139300108}
   {user: 1, action: 'withdraw', value: 150.0, createdA: 1622139300108}
   {user: 1, action: 'deposit', value: 150.0, createdA: 1622139300108},
]

// we try to process and got an error!
operations.push({user: 1, action: 'withdraw', value: 20.0, createdA: 1622139300108})

//so, we add a compensation to fix the value
operations.push({user: 1, action: 'deposit', value: 20.0, createdA: 1622139300108})

// and then,
const operations = [
   {user: 1, action: 'deposit', value: 100.0, createdA: 1622139300108},
   {user: 34, action: 'deposit', value: 10000.0, createdA: 1622139300108},
   {user: 63, action: 'deposit', value: 120.0, createdA: 1622139300108},
   {user: 34, action: 'withdraw', value: 130.0, createdA: 1622139300108},
   {user: 85, action: 'deposit', value: 101.0, createdA: 1622139300108},
   {user: 3, action: 'deposit', value: 110.0, createdA: 1622139300108},
   {user: 2, action: 'deposit', value: 20.0, createdA: 1622139300108},
   {user: 1, action: 'deposit', value: 100.0, createdA: 1622139300108},
   {user: 60, action: 'deposit', value: 5.0, createdA: 1622139300108},
   {user: 34, action: 'deposit', value: 12.0, createdA: 1622139300108},
   {user: 1, action: 'withdraw', value: 150.0, createdA: 1622139300108},
   {user: 1, action: 'withdraw', value: 17.46, createdA: 1622139300108}, //<-- error
   {user: 1, action: 'deposit', value: 17.46, createdA: 1622139300108} //<--- compensation
]

// seems to be a document oriented nosql?
```

## SAGA

Saga orquestrada;

Saga coreografada;

[saga explained](https://medium.com/@jteodoro/saga-para-transa%C3%A7%C3%B5es-distribu%C3%ADdas-em-micro-servi%C3%A7os-6b1467dccc91)

[saga example](https://github.com/joseteodoro/saga-example-with-amqp)

## Chains of responsability

- pass to next until someone answer

```js
// middleware nodejs

const auth = (request, response, next) => {
   return isValidUser(request.headers.Authorization)
      ? next()
      : response.write(401, "Unauthorized!");
}

const saveOrder = (request, response, next) => {
   return Order.save(request.body)
      .then(
         (order) => response.write(201, order)
      );
}

const endpoint = router.post(auth, saveOrder, ?);
//middlewares e filters servlets
```

Command and CoR combined

```js
const messages = [
   {
      command: "create-user", 
      payload: {name: 'user-01', email: '01@email.com'}
   },
   {
      command: "disable-user",
      payload: {name: 'user-99'}
   },
   {
      command: "enable-user",
      payload: {name: 'user-1001'}
   },
   {
      command: "delete-user",
      payload: {name: 'user-100'}
   },
]

const drop = ({command, payload}, next) => {
   if (command !== "delete-user") return next();
   return UserModel.delete(payload.name);
}

const create = ({command, payload}, next) => {
   if (command !== "create-user") return next();
   return UserModel.save(payload);
}

const enable = ({command, payload}, next) => {
   if (command !== "enable-user") return next();
   return UserModel.enable(payload.name);
}

const disable = ({command, payload}, next) => {
   if (command !== "disable-user") return next();
   return UserModel.disable(payload.name);
}

const process = (message) => {
   return stack(
      drop,
      create,
      enable,
      disable,
      ok
   );
}
// send each message to be processed
messages.map(msg => process(msg));

stack(
    criarConta
    // operacoes comuns
    Deposito
    Saque
    // operacoes
    Saldo // terminador
)

```

what about reuse filters?

```js
const processPay = (message) => {
   return stack(
      userExists,
      hasBalance,
      pay
   );
}

const processWithdraw = (message) => {
   return stack(
      userExists,
      hasBalance,
      withdraw
   );
}
```

## Creational Patterns / Object pool

// reuso de recursos escassos

pool conexoes de db
postgres (400 conexoes simultaneas)
10 instancias (40)

pool: [*db1, db2, db3, ..., *dbn]
pool: [android1, android2, iphone1, ..., *dbn]

pool.get()
pool.dispose(android)

{
    min: 10,
    max: 20,
    maxIdle: 120 segundos,
}

pool.get() // se nao tem ninguem disponivel

// hikari
// entity framework
// postgres (pgbouncer)

## Creational Patterns / Prototype


## Structural Patterns / Flyweight


## Prototype vs Flyweight vs Object Pool