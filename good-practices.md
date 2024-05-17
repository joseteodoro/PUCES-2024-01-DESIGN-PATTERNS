# good practices for coding

Good coding practices promote modularity, reusability, and maintainability – principles that align perfectly with object-oriented programming (OOP). OOP provides structures (classes, inheritance) to implement these practices. Design patterns offer proven solutions to common problems, often within a concurrent context.  Understanding good practices alongside OOP and design patterns is essential for crafting robust, scalable, and efficient software.


Receitas de bolo ou orientacoes pra evitar os problemas da sua linguagem;

KISS, YAGNI, DRY, SOLID

## KISS
    Keep it simple. Faco o simples primeiro, se precisar depois vc deixa mais complexo.

## YAGNI
    You aint gonna need it. Voce nao vai precisar disso no futuro.

## DRY
    dont repeat yourself; nao faca copy/paste;

    But,

    API -> lib -> (1 unico objecto) libInterna (200 objetos) <-- outro projeto
    // reuso de 20 linhas
    // adicionando uma lib de 1MB
    // eu copio! XD

## SOLID

### SINGLE RESPONSABILITY PRINCIPLE

```ts
class FazTudo {
    Save(us: User): Promise<bool> {
        const update = 'insert into user (id, name, password) values (?,?,?)'
        const db = Sqlite('./db.local');
        return db.connect((connection) => {
            connection.preparedStatement(update, { replacements : user})
        })
    }
}

class DBConnection {
    ...
}

class Repository {
    constructor(connection: DbConnection)
    Save(us: User): Promise<bool> {
        //const update = 'insert into user (id, name, password) values (?,?,?)'
        const update = CONSTANTS.INSERT_USER
        return connection.preparedStatement(update, { replacements : user})
    }
}
```

### OPEN / CLOSED

```ts
class Report {
    build(): PDF {}
}

// agora queremos uma funcionalidade a mais
class ReportPDFAndJSON {
    buildPDF(): PDF {}
    buildJSON(): object {}
}

/// seguindo Open/closed
interface ReportBuilder {
    build: (): ReportOutput
}

class PDFReport implements ReportBuilder {
    build(): PDF {}
}

class JSONReport implements ReportBuilder {
    build(): JSON {}
}

// agora eu quero exportar pra xls
class XLSReport implements ReportBuilder {
    build(): XLS {}
}

// design pattern
class ReportFactory {
    // esconder complexidade de criacao e promover abstracao
    build(type: string = 'pdf'): ReportBuilder {
        if (type === 'pdf') return new PDFReport()
        if (type === 'JSON') return new JSONReport()
        if (type === 'XLS') return new XLSReport()
        return new Error()
    }
}
```

### LISKOV PRINCIPLE

### INTERFACE SEGREGATION

### DEPENDENCY INJECTION



## Keep same granularity

### In a function

**Bad example of granularity:**

```ts
interface Order {
  id: number;
  amount: number;
  status: "placed" | "canceled" | "shipped";
}

function generateOrderReport(orders: Order[]): { totalAmount: number; activeOrderCount: number } {
  let totalAmount = 0;
  let activeOrderCount = 0;

  for (const order of orders) {
    if (order.status !== "canceled") {
      totalAmount += order.amount; // Summing total
      activeOrderCount++;        // Counting active orders

      console.log(`Order ${order.id} (Amount: ${order.amount}) is valid.`); // Report detail within the loop (bad practice)
    } else {
      console.log(`Order ${order.id} is canceled and removed.`); // Report detail within the loop (bad practice)
    }
  }

  return { totalAmount, activeOrderCount };
}

// Example usage
const orders: Order[] = [
  { id: 1, amount: 50, status: "placed" },
  { id: 2, amount: 100, status: "canceled" },
  { id: 3, amount: 75, status: "placed" },
];

const report = generateOrderReport(orders);
console.log(report);
```

**Good granularity**

```ts
interface Order {
  id: number;
  amount: number;
  status: "placed" | "canceled" | "shipped";
}

function filterActiveOrders(orders: Order[]): Order[] {
  return orders.filter(order => order.status !== "canceled");
}

function calculateTotalAmount(orders: Order[]): number {
  return orders.reduce((total, order) => total + order.amount, 0);
}

function countActiveOrders(orders: Order[]): number {
  return orders.length;
}

function generateOrderReport(orders: Order[]): { totalAmount: number; activeOrderCount: number } {
  const activeOrders = filterActiveOrders(orders);
  const totalAmount = calculateTotalAmount(activeOrders);
  const activeOrderCount = countActiveOrders(activeOrders);

  return { totalAmount, activeOrderCount };
}

// Example usage (unchanged)
const orders: Order[] = [
  { id: 1, amount: 50, status: "placed" },
  { id: 2, amount: 100, status: "canceled" },
  { id: 3, amount: 75, status: "placed" },
];

const report = generateOrderReport(orders);
console.log(report);
```

### In a class

### In a package