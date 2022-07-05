# Nomes

## Evite usar nomes ruins

Um bom nome permite que o código seja utilizado por muitos desenvolvedores. O nome deve refletir o propósito e dar contexto.

**Ruim**

```cs
int d = GetDaysSinceModified();
```

**Bom**
```cs
int daySinceModified = GetDaysSinceModified();
```

## Evite nomes que enganam

O nome de uma variável deve refletir para que ela é usada.

**Ruim**

```cs
var dataFromDb = db.GetFromService().ToList();
```

**Bom**

```cs
var listOfEmployee = _employeeService.GetEmployees().ToList();
```

## Evite notação Hungarian (Hungarian Notation)

A notação Hungarian expressa o tipo da variável no nome. É desnecessário em IDE's modernas, pois elas identificam o tipo.

**Ruim**

```cs
int iCounter;
string strFullName;
DateTime dModifiedDate;
```

**Bom**

```cs
int counter;
string fullName;
DateTime modifiedDate;
```

Notação hungarian também não deve ser usada em parâmetros:

**Ruim**

```cs
public bool IsShopOpen(string pDay, int pAmount)
{
    // ...
}
```
**Bom**

```cs
public bool IsShopOpen(string day, int amount)
{
    // ...
}
```

## Use capitalização consistente

A capitalização fala bastante sobre suas variáveis, funções, etc. Essas regras são subjetivas, então o time pode escolher qualquer regra. O ponto é: não interessa qual regra se escolha, seja consistente com essa regra.

**Ruim**

```cs
const int DAYS_IN_WEEK = 7;
const int daysInMonth = 30;

var songs = new List<string> { 'Back In Black', 'Stairway to Heaven', 'Paradise City' };
var Artists = new List<string> { 'ACDC', 'Led Zeppelin', 'Guns and Roses' };

bool EraseDatabase() {}
bool Restore_database() {}

class animal {}
class Alpaca {}
```

**Bom**

```cs
const int DaysInWeek = 7;
const int DaysInMonth = 30;

var songs = new List<string> { 'Back In Black', 'Stairway to Heaven', 'Paradise City' };
var artists = new List<string> { 'ACDC', 'Led Zeppelin', 'Guns and Roses' };

bool EraseDatabase() {}
bool RestoreDatabase() {}

class Animal {}
class Alpaca {}
```

## Use nomes pronunciáveis

Demora um bom tempo para descobrir o significado de variáveis e funções quando o nome não é pronunciável.

**Ruim**

```cs
public class Employee
{
    public DateTime sWorkDate { get; set; } // O que é isso?
    public DateTime modTime { get; set; } // E isso?
}
```

**Bom**

```cs
public class Employee
{
    public DateTime StartWorkingDate { get; set; }
    public DateTime ModificationTime { get; set; }
}
```

## Use a notação Camelcase

Use a notação Camelcase para variáveis e parâmetros em métodos.

**Ruim**

```cs
var employeephone;

public double CalculateSalary(int workingdays, int workinghours)
{
    // ...
}
```

**Bom**

```cs
var employeePhone;

public double CalculateSalary(int workingDays, int workingHours)
{
    // ...
}
```

## Não inclua números em nomes de variáveis, parâmetros, tipos...

Na maioria das vezes eles são uma desculpa para não definir um nome que seja claro e defina a intenção.

**Ruim**

```cs
void UpdatePerson(int id, Person p1)
{
    Person p2 = db.Persons.Find(id);

    p2.Email = p1.Email;
    p2.Name = p1.Name;
    // ...

    db.Update(p2);
}
```

**Bom**

```cs
void UpdatePerson(int id, Person updatedPerson)
{
    Person oldPerson = db.Persons.Find(id);

    oldPerson.Email = updatedPerson.Email;
    oldPerson.Name = updatedPerson.Name;
    // ...

    db.Update(oldPerson);
}
```

## Nomenclaturas para C#

Use a tabela abaixo como guia na hora de nomear variáveis, funções, métodos, parâmetros, etc, em projetos .NET/C#.

|Language element|Casing|Example|
|----------------|------|-------|
|Namespace|Pascal|System.Drawing|
|Type parameter|Pascal|TView|
|Interface|Pascal|IBusinessService|
|Class, struct|Pascal|AppDomain|
|Enum|Pascal|ErrorLevel|
|Enum member|Pascal|FatalError|
|Resource key|Pascal|SaveButtonTooltipText|
|Constant field|Pascal|MaximumItems|
|Private static readonly field|Pascal|RedValue|
|Private field|Camel|listItem|
|Non-private field|Pascal|MainPanel|
|Property|Pascal|BackColor|
|Event|Pascal|Click|
|Method|Pascal|ToString|
|Local function|Pascal|FormatText|
|Parameter|Camel|typeName|
|Tuple element names|Pascal|`(string First, string Last) name = ("John", "Doe");var name = (First: "John", Last: "Doe");(string First, string Last) GetName() => ("John", "Doe");`|
|Variables declared using tuple syntax|Camel|`(string first, string last) = ("John", "Doe");var (first, last) = ("John", "Doe");`|
|Local variable|Camel|listOfValues|


1. Em caso de ambiguidade, a regra mais alta na tabela tem precedência.
1. A tabela está em inglês porque desenvolvedores estão mais acostumados com essa nomenclatura.

---

[Início](csharp.md)