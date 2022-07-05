# Variáveis

## Evite mútiplos `return` em aninhamento profundo

Quando há muitas declarações if/else aninhadas, o código fica difícil de ler. 

No exemplo **ruim** temos vários blocos `if/else` e dentro de cada bloco existe um `return true/false;`. No exemplo **bom** existe apenas um ponto de saída da função (apenas um `return`), o que é muito bom. Além disso, de alguma forma se consegue diminuir o número de condicionais.
No fim das contas, o código **bom** deixa explícito o que está acontecendo, enquanto o código ruim obriga o leitor a manter um mapa mental de cada `if/else/return` no caminho.

**Explícito é melhor do que implícito**.

**Ruim**

```cs
public bool IsShopOpen(string day)
{
    if (!string.IsNullOrEmpty(day))
    {
        day = day.ToLower();
        if (day == "friday")
        {
            return true;
        }
        else if (day == "saturday")
        {
            return true;
        }
        else if (day == "sunday")
        {
            return true;
        }
        else
        {
            return false;
        }
    }
    else
    {
        return false;
    }

}
```

**Bom**

```cs
public bool IsShopOpen(string day)
{
    if (string.IsNullOrEmpty(day))
    {
        return false;
    }

    var openingDays = new[] { "friday", "saturday", "sunday" };
    return openingDays.Any(d => d == day.ToLower());
}
```

## Evite mapeamento mental

Não force o seu colega que está lendo o código a traduzir o que a variável significa. **Explícito é melhor do que implícito**.

**Ruim**

```cs
var l = new[] { "Austin", "New York", "San Francisco" };

for (var i = 0; i < l.Count(); i++)
{
    var li = l[i];
    DoStuff();
    DoSomeOtherStuff();

    // ...
    // ...
    // ...
    // Espera, o que é mesmo 'li'???
    Dispatch(li);
}
```

**Bom**

```cs
var locations = new[] { "Austin", "New York", "San Francisco" };

foreach (var location in locations)
{
    DoStuff();
    DoSomeOtherStuff();

    // ...
    // ...
    // ...
    Dispatch(location);
}
```

## Evite a string mágica

Strings mágicas são valores string que são especificados diretamente no código e tem impacto no comportamento da aplicação. Frequentemente tais strings serão duplicadas no sistema. Como elas não podem ser automaticamente atualizadas usando ferramentas, elas se tornam fonte de bugs quando são feitas modificações em algumas strings mas não em outras.

**Ruim**

```cs
if (userRole == "Admin")
{
    // ...
}
```

**Bom**

```cs
const string ADMIN_ROLE = "Admin"

if (userRole == ADMIN_ROLE)
{
    // ...
}
```

Da forma acima, somente um local do sistema precisa modificado e o resto do sistema funcionará de acordo.

## Não inclua contexto desnecessário

Se o nome da classe/objeto te diz alguma coisa, não repita isso no nome da variável.

**Ruim**

```cs
public class Car
{
    public string CarMake { get; set; }
    public string CarModel { get; set; }
    public string CarColor { get; set; }

    //...
}
```

**Bom**

```cs
public class Car
{
    public string Make { get; set; }
    public string Model { get; set; }
    public string Color { get; set; }

    //...
}
```

## Use nomes de variáveis pronunciáveis e com significado

**Ruim**

```cs
var ymdstr = DateTime.UtcNow.ToString("MMMM dd, yyyy");
```

**Bom**

```cs
var currentDate = DateTime.UtcNow.ToString("MMMM dd, yyyy");
```

## Use o mesmo vocabulário para o mesmo tipo de variável

**Ruim**

```cs
var user = service1.GetUserInfo();
var user = service2.GetUserData();
var user = service3.GetUserRecord();
var user = service4.GetUserProfile();
```

**Bom**

```cs
var user = serviceN.GetUser();
```

## Use nomes que possam ser encontrados na busca (parte 1)

Iremos ler mais código do que escrever em toda a nossa carreira. É importante que o código seja legível e buscável. Quando não nomeamos nossas variáveis com nomes significativos, acabamos por lesar quem está lendo. Faça com que os nomes de variáveis sejam buscáveis.

**Ruim**

```cs
// Para que serve a variável data?
var data = new { Name = "John", Age = 42 };

var stream1 = new MemoryStream();
var ser1 = new DataContractJsonSerializer(typeof(object));
ser1.WriteObject(stream1, data);

stream1.Position = 0;
var sr1 = new StreamReader(stream1);
Console.Write("JSON form of Data object: ");
Console.WriteLine(sr1.ReadToEnd());
```

**Bom**

```cs
var person = new Person
{
    Name = "John",
    Age = 42
};

var stream2 = new MemoryStream();
var ser2 = new DataContractJsonSerializer(typeof(Person));
ser2.WriteObject(stream2, data);

stream2.Position = 0;
var sr2 = new StreamReader(stream2);
Console.Write("JSON form of Data object: ");
Console.WriteLine(sr2.ReadToEnd());
```
## Use nomes que possam ser encontrados na busca (parte 2)

**Ruim**

```cs
var data = new { 
    Name = "John", 
    Age = 42, 
    PersonAccess = 4
    };

// Para que serve o 4?
if (data.PersonAccess == 4)
{
    // editar ...
}
```

**Bom**

```cs
public enum PersonAccess : int
{
    ACCESS_READ = 1,
    ACCESS_CREATE = 2,
    ACCESS_UPDATE = 4,
    ACCESS_DELETE = 8
}

var person = new Person
{
    Name = "John",
    Age = 42,
    PersonAccess= PersonAccess.ACCESS_CREATE
};

if (person.PersonAccess == PersonAccess.ACCESS_UPDATE)
{
    // editar ...
}
```

## Use argumentos padrão em vez de lógica

**Ruim**

```cs
public void CreateMicrobrewery(string name = null)
{
    var breweryName = !string.IsNullOrEmpty(name) ? name : "Hipster Brew Co.";
    // ...
}
```

**Bom**

```cs
public void CreateMicrobrewery(string breweryName = "Hipster Brew Co.")
{
    // ...
}
```
---

[Início](csharp.md)