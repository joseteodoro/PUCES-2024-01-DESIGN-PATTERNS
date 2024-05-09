# P.O.O

Object-oriented programming (OOP) centers around the concept of "objects" that encapsulate data (attributes) and the code that manipulates it (methods). This organization promotes code reusability, modularity, and easier understanding of complex systems. OOP emphasizes breaking down problems into self-contained objects that interact with each other.

- reuse;
- manutenabilidade;
- fazer nossa vida de desenvolvedor mais facil;

```go
// comportamento esta separado dos dados
type User struct {
    Name string
    Password string
    Id int
    Type int
}

// free function
func ShouldLogin(user *User, passwordAttempt string) bool {
    return user.Password == passwordAttempt
}

func Login(username string, password string, type string) *User {
    // nao queremos isso
    if (type == 1) // login client credential API
    if (type == 2) // login para o guest
    if (type == 3) // login para o userClient
    if (type == 4) // login para root

    
    ..
}

// tenta logar
func Login(req HttpRequest) {
    user := req.User
    correct := ShouldLogin(user)
    if (correct) {
        req.Redirect("/home")
    } else {
        req.Redirect("/forbidden")
    }
}

```

```java
    class User {
        private Integer Id;
        private String Password;
        private String Name;

        // assinatura de metodo nome + parametros
        public Boolean ShouldLogin(String receivedPassword) {}
        public void Logout() {}
        public void Save() {}
        public void ResetPassword(String newPassword) {}
    }
    var correct = new User().ShouldLogin(receivedPassword)
    //.save()
    //.trim()
    //.replace()

    class Root {
        public Boolean ShouldLogin(String receivedPassword) {}
        public void Logout() {}
        public void Save() {}
        public void ResetPassword(String newPassword) {}
    }
    class Guest {
        public Boolean ShouldLogin(String receivedPassword) {}
        public void Logout() {}
        public void Save() {}
        public void ResetPassword(String newPassword) {}
    }
    class UserClient {
        public Boolean ShouldLogin(String receivedPassword) {}
        public void Logout() {}
        public void Save() {}
        public void ResetPassword(String newPassword) {}
    }
    class ClientCredential {
        public Boolean ShouldLogin(String receivedPassword) {}
        public void Logout() {}
        public void Save() {}
        public void ResetPassword(String newPassword) {}
    }

    // queremos isso
    User user = req.getUser(); // new UserClient, new Guest, new Root, new User
    user.login()

    // conseguir reusar comportamento baseado no contrato = abstracao
    guest.login() != root.login()
    // polimorfismo (metodo diferentes com a mesma assinatura)

```

## Tipagem

### Linguagens fortemente tipadas

Golang, Java, C++, .Net, Typescript

```java
interface User { // contrato
    void Save();
}

class ConcreteUser implements User { // mas nao honra o contrato
    // vazio
}

class VarzeaUser { // mas nao honra o contrato
    // vazio
}

User us = new VarzeaUser() // estoura erro

// fortemente tipado forca vc honrar o contrato
// compilador aponta o erro
```

Em linguagens fortemente tipadas o compilador ajuda a saber se voce esta
honrando o contrato ou nao!

### Linguagens fracamente tipadas

// lint do JS, Python, Ruby te falam!

Php, Python, JS, ruby

```python
class User:
    def Save(self):
        pass

class ConcreteUser(User): // extendendo explicitamente
    // herdar automaticate

class VarzeaUser: // nao honra o contrato
    def save(self): // note o camel case
        pass

User().Save()
ConcreteUser().Save()
us = User()
us = VarzeaUser()

us.Save() // quebra em runtime

// duck typing (voa feito pato, se grasna feito pato, deve ser um pato)
```


### Tipagem fraca

Nos que devemos honrar o contrato do tipo (teoria das categorias)

interface I:
    +(I) => I
    *(I) => I
    /
    -

interface User:
    save()
    delete()
    login()

// LISKOV (SOLID)
    Root (filho de) User, Root honre todos os contratos do User

## Heranca

```ts
interface User {
    Save(): any;
}

//OO fazendo a heranca pra gente
class RootUser implements User {
    Save(): any {
        // do something
    }
}

//se fosse sem copia automatica de atributos
class GuestUser {
    // contrato / assinatura é nome + parametros
    ...RootUser,
    Save(): any { // overhide
        return RawSave(false)
    }
    
    RawSave(force: bool): any { // overload (nao funfa no Golang)
        if (userExists && !force) return;
        //1
        //2
        //3
        //4
    }

}

// aberto pra extensao, fechado pra mudancas (SOLID)
class UpdatableGuestUser extends GuestUser {
    constructor(force = false);
    Save(): any { // overload (nao funfa no Golang)
        // internalInfo ///
        if (userExists && !force) return;
        super.Save()
    }
}
```

## Pilares do OO

Encapsulamento, abstracao, polimorfismo e heranca.

- Encapsulamento (esconde tudo que nao precisa ser exposto);
    - evitar acesso ao estado interno dos componentes;
    - visibilidade de funcoes e campos (controle acesso);
    - evitar propagação de mudanças;
    problemas:
        - falta dele: abrir detalhes que deveriam estar ocultos;
        - modificar contrato exposto gera breaking change;

- Abstração (confiar no contrato e nao na implementacao);
    problemas:
        - dificuldade de saber quem é a implementacao corrente; // para alguns
        - ficar complexo demais ou fino (granular) demais
    - KISS (keep it simple)

```js
// granularidade por tipo
class User {
    Save()
    Login()
    Delete()
}

interface Persistable {
    Save()
}

interface Signable {
    Login()
}

interface Deletable {
    Delete()
}

class User implements Signable, Deletable, Persistable {
    Save()
    Login()
    Delete()
}
```

- Polimorfismo (ja q eu confio em contrato, eu uso diferentes comportamentos com a mesma assinatura);
    Problemas:
        - mesmo da abstracao

- Herança (tipagem + importar (herda) caracteristicas da classe pai);
    Problemas:
        - cadeia de heranca muito comprida;
        - ficar complexo demais ou fino (granular) demais;
        - extender um tipo diferente que nao faz sentido conceitualmente;

```cadeia de heranca longa é ruim
GuestUser -----------> AbstractUser -----------> AbstractUser
    Save() {                Save() {}                Save() {}
        super.Save()
    }
```

Algumas linguagens nao aceitam heranca multipla
User extends Deletable, Signable, Persistable

User.super().Save() // so um pai tem
User.super().toString() // todos os pais tem

pais = [Deletable, Signable, Persistable]
User.super(pais[0]).toString()

// tentando reaproveitar as funcoes em linguagem
// que nao tem heranca multipla

    User extends Persistable
    Persistable extends Signable
    Signable extends Deletable

// se nao heranca multipla, vc precisa usar composicao
    User {
        Persistable
        Signable
        Deletable
    }
// ou usar interfaces pra gerar contratos

TS com heranca multipla
```ts
interface IScrollButtonProps
  extends Pick<
    HTMLAttributes<HTMLButtonElement> &
      ButtonHTMLAttributes<HTMLButtonElement>,
    'onClick' | 'disabled'
  > {
  direction: 'left' | 'right';
  topHeight?: string;
}
```