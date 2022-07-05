# Funções

## Evite efeitos colaterais

Uma função tem efeitos colaterais se ela faz alguma coisa além de receber um valor (ou valores) e retornar um valor (ou valores). Por exemplo, elas podem acidentalmente: modificar uma variável global, escrever em um arquivo, entregar todo o teu dinheiro para um estranho.

Ocasionalmente é necessário ter efeitos colaterais: você pode precisar escrever em um arquivo. O que você precisa fazer é centralizar onde você está fazendo este trabalho. Não tenha várias funções e classes escrevendo em um arquivo. Tenha um serviço que faça isso, e **somente um**.

O objetivo é evitar problemas comuns, como compartilhar estado entre objetos sem estrutura, usar dados mutáveis que pode ser escritos por qualquer classe/método e não centralizar o local onde os efeitos colaterais podem ocorrer.

**Ruim**

```cs
// Variável global referenciada pela função a seguir.
// Se tivéssemos outra função que usa a variável name, agora ela seria um array e o programa iria parar.
var name = "Ryan McDermott";

public void SplitAndEnrichFullName()
{
    var temp = name.Split(" ");
    name = $"Primeiro nome é {temp[0]}, e sobrenome é {temp[1]}"; // efeito colateral
}

SplitAndEnrichFullName();

Console.WriteLine(name); // Primeiro nome é Ryan, e sobrenome é McDermott
```

**Bom**

```cs
public string SplitAndEnrichFullName(string name)
{
    var temp = name.Split(" ");
    return $"Primeiro nome é {temp[0]}, e sobrenome é {temp[1]}";
}

var name = "Ryan McDermott";
var fullName = SplitAndEnrichFullName(name);

Console.WriteLine(name); // Ryan McDermott
Console.WriteLine(fullName); //  Primeiro nome é Ryan, e sobrenome é McDermott
```

## Evite condicionais negativas

**Ruim**

```cs
public bool IsNodeNotPresent(string node)
{
    // ...
}

if (!IsNodeNotPresent(node))
{
    // ...
}
```

**Bom**

```cs
public bool IsNodePresent(string node)
{
    // ...
}

if (IsNodePresent(node))
{
    // ...
}
```

## Evite condicionais

Essa parece ser uma tarefa impossível. A primeira vez que se ouve isso, geralmente o programador diz:

> "como vou programar qualquer coisa sem usar `if`?"

A resposta é: você pode usar polimorfismo para obter o mesmo resultado em muitas tarefas. A segunda frase do desenvolvedor é:

> Legal, mas por que eu iria querer fazer isso?

A resposta é um conceito já visto sobre Clean Code: uma função deve fazer uma coisa, e somente uma. Quando classes e funções contém declarações `if`, você está dizendo a quem usa que sua função faz mais de uma coisa.

**Ruim**

```cs
class Airplane
{
    // ...

    public double GetCruisingAltitude()
    {
        switch (_type)
        {
            case '777':
                return GetMaxAltitude() - GetPassengerCount();
            case 'Air Force One':
                return GetMaxAltitude();
            case 'Cessna':
                return GetMaxAltitude() - GetFuelExpenditure();
        }
    }
}
```

**Bom**

```cs
interface IAirplane
{
    // ...
    double GetCruisingAltitude();
}

class Boeing777 : IAirplane
{
    // ...
    public double GetCruisingAltitude()
    {
        return GetMaxAltitude() - GetPassengerCount();
    }
}

class AirForceOne : IAirplane
{
    // ...
    public double GetCruisingAltitude()
    {
        return GetMaxAltitude();
    }
}

class Cessna : IAirplane
{
    // ...
    public double GetCruisingAltitude()
    {
        return GetMaxAltitude() - GetFuelExpenditure();
    }
}
```

## Evite pré-verificação de tipo (parte 1)

**Ruim**

```cs
public Path TravelToTexas(object vehicle)
{
    if (vehicle.GetType() == typeof(Bicycle))
    {
        (vehicle as Bicycle).PeddleTo(new Location("texas"));
    }
    else if (vehicle.GetType() == typeof(Car))
    {
        (vehicle as Car).DriveTo(new Location("texas"));
    }
}
```

**Bom**

```cs
public Path TravelToTexas(Traveler vehicle)
{
    vehicle.TravelTo(new Location("texas"));
}

// ou então...

// pattern matching
public Path TravelToTexas(object vehicle)
{
    if (vehicle is Bicycle bicycle)
    {
        bicycle.PeddleTo(new Location("texas"));
    }
    else if (vehicle is Car car)
    {
        car.DriveTo(new Location("texas"));
    }
}
```

## Evite verificação de tipo (parte 2)

**Ruim**

```cs
public int Combine(dynamic val1, dynamic val2)
{
    int value;
    if (!int.TryParse(val1, out value) || !int.TryParse(val2, out value))
    {
        throw new Exception('Deve ser um tipo numérico');
    }

    return val1 + val2;
}
```

**Bom**

```cs
public int Combine(int val1, int val2)
{
    return val1 + val2;
}
```

## Evite o uso de flags em parâmetros de métodos

Uma flag pode indicar que um método possui mais de uma responsabilidade. É melhor que ele possua apenas uma reponsabilidade. Divida o método em dois se um boleano faz com que o método possua duas responsabilidades.

**Ruim**

```cs
public void CreateFile(string name, bool temp = false)
{
    if (temp)
    {
        Touch("./temp/" + name);
    }
    else
    {
        Touch(name);
    }
}
```

**Bom**

```cs
public void CreateFile(string name)
{
    Touch(name);
}

public void CreateTempFile(string name)
{
    Touch("./temp/"  + name);
}
```

## Não escreva funções globais

Sobrecarregar e poluir globais é uma má prática em muitas linguagens. Pode haver conflitos com outras bibliotecas e o usuário do código não estaria ciente até ter uma exceção em produção. Pense no seguinte: você escreve uma função global `Config()`, mas ela pode conflitar com outra biblioteca que tentar fazer a mesma coisa.

**Ruim**

```cs
public Dictionary<string, string> Config()
{
    return new Dictionary<string,string>(){
        ["foo"] = "bar"
    };
}
```

**Bom**

```cs
class Configuration
{
    private Dictionary<string, string> _configuration;

    public Configuration(Dictionary<string, string> configuration)
    {
        _configuration = configuration;
    }

    public string[] Get(string key)
    {
        return _configuration.ContainsKey(key) ? _configuration[key] : null;
    }
}
```

## Não use o padrão Singleton

Singleton é um anti-padrão. Parafraseando Brian Burton:

> 1. Geralmente são utilizados como instância global. Por que é tão ruim? Porque você **esconde as dependências** da sua aplicação no código em vez de expô-las através de uma interface. Fazer algo ser global em vez de expor em uma interface é um "code smell".
>
> 1. Violam o **princípio da responsabilidade única**: pelo fato de eles **controlarem sua própria criação e ciclo de vida**.
>
> 1. De forma inerente causam alto acoplamento. O alto acoplamento faz com que os testes sejam difíceis, pois é difícil de fazer uma classe que finge ser ela para testes.
>
> 1. Carregam seu estado por todo o ciclo de vida do sistema e isso é mais uma dica para os problemas na hora do teste: você pode terminar em uma situação em que os testes precisam ter uma ordem específica, o que é um grande "não" se tratando de testes unitários. Por quê? Porque todo teste unitário deve ser independente um do outro.

**Observação:** mesmo com as razões acima, tenha em mente que o padrão singleton representa um conceito e, sendo assim, ainda há espaço para sua utilização. Messa os prós e os contras. Geralmente o padrão singleton pode ser implementado por Injeção de Dependência onde a dependência é uma única instância e o padrão Factory, que pode fazer com que determinada classe seja instanciada apenas uma vez, retornando sempre a mesma instância. Em contra partida, isso pode ser tornar um problema se o seu sistema não usa os conceitos de Injeção de Dependência e Factory.

**Ruim**

```cs
class DBConnection
{
    private static DBConnection _instance;

    private DBConnection()
    {
        // ...
    }

    public static GetInstance()
    {
        if (_instance == null)
        {
            _instance = new DBConnection();
        }

        return _instance;
    }

    // ...
}

var singleton = DBConnection.GetInstance();
```

**Bom**

```cs
class DBConnection
{
    public DBConnection(IOptions<DbConnectionOption> options)
    {
        // ...
    }

    // ...
}
//Cria uma instância da classe DBConnection e configura usando o padrão Option.

var options = <resolve from IOC>;
var connection = new DBConnection(options);
```

E agora você deve ter uma instância de `DBConnection` na aplicação.

## Argumentos de função (2 ou menos, idealmente)

Limitar o número de parâmetros em funções é incrivelmente importante porque faz com que os testes fiquem mais fáceis. Tendo mais de três parâmetros leva a uma explosão combinatória onde se deve testar muitos e muitos casos com cada argumento separadamente.

Zero argumentos é o ideal. Um ou dois argumentos é ok e três deve ser evitado. Geralmente, quando se tem mais de dois argumentos é porque a sua função está fazendo muito trabalho. Nos casos em que ela não está, a maioria das vezes um objeto de alto nível é suficiente como argumento.

**Ruim**

```cs
public void CreateMenu(string title, string body, string buttonText, bool cancellable)
{
    // ...
}
```

**Bom**

```cs
public class MenuConfig
{
    public string Title { get; set; }
    public string Body { get; set; }
    public string ButtonText { get; set; }
    public bool Cancellable { get; set; }
}

var config = new MenuConfig
{
    Title = "Foo",
    Body = "Bar",
    ButtonText = "Baz",
    Cancellable = true
};

public void CreateMenu(MenuConfig config)
{
    // ...
}
```

## Funções devem fazer somente uma coisa

Essa é, de longe, a regra mais importante da engenharia de software. Quando funções fazem mais de uma coisa, elas são difíceis de escrever, testar e raciocinar sobre. Quando você consegue isolar uma função a somente uma ação, ela pode ser refatorada facilmente e a leitura do seu código será muito mais fácil. Se você levar consigo nada mais desse guia além disso, você já estará à frente de muitos desenvolvedores.

**Ruim**

```cs
public void SendEmailToListOfClients(string[] clients)
{
    foreach (var client in clients)
    {
        var clientRecord = db.Find(client);
        if (clientRecord.IsActive())
        {
            Email(client);
        }
    }
}
```

**Bom**

```cs
public void SendEmailToListOfClients(string[] clients)
{
    var activeClients = GetActiveClients(clients);
    // ...
}

public List<Client> GetActiveClients(string[] clients)
{
    return db.Find(clients).Where(s => s.Status == "Active");
}
```

## Nome de função deve dizer o que ela faz

**Ruim**

```cs
public class Email
{
    // ...
    public void Handle()
    {
        SendMail(this._to, this._subject, this._body);
    }
}

var message = new Email(...);
// O que é isso? Uma handle para a message? Estamos salvando em um arquivo?
message.Handle();
```

**Bom**

```cs
public class Email
{
    //...

    public void Send()
    {
        SendMail(this._to, this._subject, this._body);
    }
}

var message = new Email(...);
// Claro e óbvio
message.Send();
```

## O chamador da função e chamados pela função devem ser próximos

Se uma função chama outra, mantenha essas funções verticalmente próximas no código-fonte, de preferência a função chamada abaixo da função chamadora. Faça seu código ser lido como um jornal (de cima para baixo).

**Ruim**

```cs
class PerformanceReview
{
    private readonly Employee _employee;

    public PerformanceReview(Employee employee)
    {
        _employee = employee;
    }

    private IEnumerable<PeersData> LookupPeers()
    {
        return db.lookup(_employee, 'peers');
    }

    private ManagerData LookupManager()
    {
        return db.lookup(_employee, 'manager');
    }

    private IEnumerable<PeerReviews> GetPeerReviews()
    {
        var peers = LookupPeers();
        // ...
    }

    public PerfReviewData PerfReview()
    {
        GetPeerReviews();
        GetManagerReview();
        GetSelfReview();
    }

    public ManagerData GetManagerReview()
    {
        var manager = LookupManager();
    }

    public EmployeeData GetSelfReview()
    {
        // ...
    }
}

var  review = new PerformanceReview(employee);
review.PerfReview();
```

**Bom**

```cs
class PerformanceReview
{
    private readonly Employee _employee;

    public PerformanceReview(Employee employee)
    {
        _employee = employee;
    }

    public PerfReviewData PerfReview()
    {
        GetPeerReviews();
        GetManagerReview();
        GetSelfReview();
    }

    private IEnumerable<PeerReviews> GetPeerReviews()
    {
        var peers = LookupPeers();
        // ...
    }

    private IEnumerable<PeersData> LookupPeers()
    {
        return db.lookup(_employee, 'peers');
    }

    private ManagerData GetManagerReview()
    {
        var manager = LookupManager();
        return manager;
    }

    private ManagerData LookupManager()
    {
        return db.lookup(_employee, 'manager');
    }

    private EmployeeData GetSelfReview()
    {
        // ...
    }
}

var review = new PerformanceReview(employee);
review.PerfReview();
```

## Encapsule condicionais

**Ruim**

```cs
if (article.state == "published")
{
    // ...
}
```

**Bom**

```cs
if (article.IsPublished())
{
    // ...
}
```

## Remova código morto

Código morto é tão ruim quanto código duplicado. Não há motivo para mantê-lo no código-fonte. Se não está sendo usado, jogue-o fora. Ele estará seguro no histórico de versões caso ele seja necessário.

**Ruim**

```cs
public void OldRequestModule(string url)
{
    // ...
}

public void NewRequestModule(string url)
{
    // ...
}

var request = NewRequestModule(requestUrl);
InventoryTracker("apples", request, "www.inventory-awesome.io");
```

**Bom**

```cs
public void RequestModule(string url)
{
    // ...
}

var request = RequestModule(requestUrl);
InventoryTracker("apples", request, "www.inventory-awesome.io");
```

---

[Início](csharp.md)