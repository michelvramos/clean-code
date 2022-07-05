# Classes

## Use encadeamento de métodos

Esse padrão é muito útil e é comum encontrarmos ele em bibliotecas. Ele permite que o código seja expressivo e menos verboso. Por essa razão, use encadeamento de métodos e veja como o seu código fica limpo.

**Ruim**

```cs
var list = new List<int>() { 1, 2, 3, 4, 5 };
list.Add(1);
list.Insert(0, 0);
list.RemoveAt(1);
list.Reverse();
list.ForEach(value => value.WriteLine());
list.Clear();
```

**Bom**

```cs
public static class ListExtensions
{
    public static List<T> FluentAdd<T>(this List<T> list, T item)
    {
        list.Add(item);
        return list;
    }

    public static List<T> FluentClear<T>(this List<T> list)
    {
        list.Clear();
        return list;
    }

    public static List<T> FluentForEach<T>(this List<T> list, Action<T> action)
    {
        list.ForEach(action);
        return list;
    }

    public static List<T> FluentInsert<T>(this List<T> list, int index, T item)
    {
        list.Insert(index, item);
        return list;
    }

    public static List<T> FluentRemoveAt<T>(this List<T> list, int index)
    {
        list.RemoveAt(index);
        return list;
    }

    public static List<T> FluentReverse<T>(this List<T> list)
    {
        list.Reverse();
        return list;
    }
}

internal static void ListFluentExtensions()
{
    var list = new List<int>() { 1, 2, 3, 4, 5 }
        .FluentAdd(1)
        .FluentInsert(0, 0)
        .FluentRemoveAt(1)
        .FluentReverse()
        .FluentForEach(value => value.WriteLine())
        .FluentClear();
}
```

## Prefira composição em vez de herança

Conforme dito no famoso livro _Design Patterns_ escrito pela Gang of Four, onde for possível você deve preferir composição em vez de herança. Existem boas razões para usar herança e boas razões para usar composição.

O ponto principal desta máxima é que quando instintivamente você escolhe herança, pense duas vezes e analise se composição faria a modelagem do seu problema melhor. Às vezes, é possível.

Abaixo uma lista simples e não exaustiva  de **SUGESTÕES** de quando se deve usar herança:

1. Quando a herança representa uma relação "é-um" e não "possui-um" (humano->animal vs Usuario->DadosDoUsuario).
1. Quando se pode reusar código da classe base (humanos podem ser mover, como animais).
1. Quando se quer fazer modificações globais em classes derivadas modificando apenas a classe base (modificar o gasto calórico de animais quando eles se movem).

**Ruim**

```cs
class Employee
{
    private string Name { get; set; }
    private string Email { get; set; }

    public Employee(string name, string email)
    {
        Name = name;
        Email = email;
    }

    // ...
}

// Ruim, pois empregado "possui" dados de imposto.
// EmployeeTaxData não é um tipo de empregado.

class EmployeeTaxData : Employee
{
    private string Name { get; }
    private string Email { get; }

    public EmployeeTaxData(string name, string email, string ssn, string salary)
    {
         // ...
    }
    // ...
}
```

**Bom**

```cs
class EmployeeTaxData
{
    public string Ssn { get; }
    public string Salary { get; }

    public EmployeeTaxData(string ssn, string salary)
    {
        Ssn = ssn;
        Salary = salary;
    }
    // ...
}

class Employee
{
    public string Name { get; }
    public string Email { get; }
    public EmployeeTaxData TaxData { get; }

    public Employee(string name, string email)
    {
        Name = name;
        Email = email;
    }

    public void SetTax(string ssn, double salary)
    {
        TaxData = new EmployeeTaxData(ssn, salary);
    }
    // ...
}
```

---

[Início](csharp.md)