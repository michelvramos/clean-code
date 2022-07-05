# Objetos e estrutura de dados

## Use getters e setters

Em C# use as palavras chave `public`, `protected` e `private` em métodos. Assim, você pode controlar a modificação de propriedades de um objeto.

Vantagens:

1. Quando você quer fazer mais do que simplesmente obter uma propriedade, não será necessário modificar o fonte em vários locais diferentes.
1. É mais fácil de adicionar validação quando usar `set`.
1. Encapsula a representação interna.
1. Mais fácil de fazer log e adicionar tratamento e erros quando se usa `get` e `set`.
1. Ao herdar desta classe é possível sobrepor o comportamento padrão desse objeto.
1. Pode-se aplicar o padrão Lazy Loading nas propriedades desse objeto, por exemplo, quando se busca ele de um servidor.

Além disso, é parte do princípio Aberto/Fechado da orientação à objetos:

- **aberto** para extensão
- **fechado** para modificação

**Ruim**

```cs
class BankAccount
{
    public double Balance = 1000;
}

var bankAccount = new BankAccount();

// Falso comprar tênis...
bankAccount.Balance -= 100;
```

**Bom**

```cs
class BankAccount
{
    private double _balance = 0.0D;

    public double Balance
    {
        get 
        {
            return _balance;
        }
    }

    public BankAccount(balance = 1000)
    {
       _balance = balance;
    }

    public void WithdrawBalance(int amount)
    {
        if (amount > _balance)
        {
            throw new Exception('Amount greater than available balance.');
        }

        _balance -= amount;
    }

    public void DepositBalance(int amount)
    {
        _balance += amount;
    }
}

var bankAccount = new BankAccount();

// Comprar tênis...
bankAccount.WithdrawBalance(price);

// Obter saldo
balance = bankAccount.Balance;
```

## Faça objetos terem métodos `private`/`protected`

**Ruim**

```cs
class Employee
{
    public string Name { get; set; }

    public Employee(string name)
    {
        Name = name;
    }
}

var employee = new Employee("John Doe");
Console.WriteLine(employee.Name); // Nome do funcionário: John Doe
```

**Bom**

```cs
class Employee
{
    public string Name { get; }

    public Employee(string name)
    {
        Name = name;
    }
}

var employee = new Employee("John Doe");
Console.WriteLine(employee.Name); // Nome do funcionário: John Doe
```

---

[Início](csharp.md)