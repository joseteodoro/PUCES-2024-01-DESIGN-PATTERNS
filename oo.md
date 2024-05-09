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

## Pilares do OO

- Encapsulamento (esconde tudo que nao precisa ser exposto);
    - evitar acesso ao estado interno dos componentes;
    - visibilidade de funcoes e campos (controle acesso);
    - evitar propagação de mudanças;
- Abstração (confiar no contrato e nao na implementacao);
- Polimorfismo (ja q eu confio em contrato, eu uso diferentes comportamentos com a mesma assinatura);
- Herança;

