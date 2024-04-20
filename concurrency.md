# concurrency

Concurrency is crucial when studying design patterns because ignoring it can lead to subtle and devastating errors.  Without proper concurrency management, your applications may suffer from race conditions, deadlocks, or unpredictable performance.  Many design patterns inherently involve complex concurrency challenges; failing to address them will result in unreliable and potentially disastrous software.

## Pipeline (divisao de tempo de processador)

Escalonador de processos;

Lei de Moore (capacidade de processadores dobra a cada 8 meses);

Estrategias de resolucao de problemas e nos problemas possiveis;

## Problems

- deadlocks;
- race conditions;
- starvation;   

## possible solutions

- do not share writeable spaces;
- immutable;
- sincronize (and pay the cost):
    - locks;
    - semaphores;
    - multiplexers; MX; MUX;
    - barriers;
        - lock counter;
        - Promise.all
        - CountDownLatch;
    - event drivens:
        - event emitter (js);
        - channels (golang);
        - java (streams)

```js
// reactive / non blocking / event driven

let counter = 0
Promise.race(
    () => {
        ...
        counter++
    },
    () => {
        ...
        counter++
    }
)

Database.save(newUser)
    .then(() => Profile.createNewProfile(newProfile))
    .then(() => Sms.notify(hello(newProfile.phone)))

//imperative
let counter = 0
await Database.save(newUser)
counter++
await Profile.createNewProfile(newProfile)
counter++
await Sms.notify(hello(newProfile.phone))
counter++
```

// NIO non-blocking-io java (2002)
-- Netty

```js
// mutex
const result = await Promise.race(download(), timeout(10_sec))
```
