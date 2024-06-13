# Patterns

Design patterns in object-oriented programming (OOP) provide reusable, time-tested solutions to common software design problems. These patterns offer blueprints for structuring objects and their interactions, promoting flexibility, maintainability, and code clarity. Understanding design patterns is a valuable asset for any OOP developer striving to create well-structured applications.

## Singleton

Uma unica instancia em memoria em qualquer lugar da sua aplicação!

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

## Factory

// esconder complexidade de criacao


## Builder



## Adapter

## Decorator

## Chains of responsability
