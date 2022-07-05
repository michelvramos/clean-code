# SOLID

## O que é SOLID?

**SOLID** é um acrônimo mnemônico  apresentado por Michael Feathers para os primeiros cinco princípios nomeados por Robert Martin, o que significa, os 5 princípios básicos da programação e projeto orientado à objetos.

- S: Single Responsibility Principle
- O: Open/Closed Principle
- L: Liskov Substituition Principle
- I: Interface Segregation Principle
- D: Dependency Inversion Principle

## S: Princípio da Responsabilidade Única

Como o declarado no Clean Code, "nunca deve haver mais de um motivo para uma classe mudar". É tentador entulhar uma classe de funcionalidades, como quando vamos viajar levando apenas uma mala. O problema é que a classe não será coesa conceitualmente e ela lhe dará vários motivos para mudanças. Minimizar o tempo que se leva para modificar uma classe é importante.

**Ruim**

```cs
class UserSettings
{
    private User User;

    public UserSettings(User user)
    {
        User = user;
    }

    public void ChangeSettings(Settings settings)
    {
        if (verifyCredentials())
        {
            // ...
        }
    }

    private bool VerifyCredentials()
    {
        // ...
    }
}
```

**Bom**

```cs
class UserAuth
{
    private User User;

    public UserAuth(User user)
    {
        User = user;
    }

    public bool VerifyCredentials()
    {
        // ...
    }
}

class UserSettings
{
    private User User;
    private UserAuth Auth;

    public UserSettings(User user)
    {
        User = user;
        Auth = new UserAuth(user);
    }

    public void ChangeSettings(Settings settings)
    {
        if (Auth.VerifyCredentials())
        {
            // ...
        }
    }
}
```

## O: Princípio Aberto/Fechado

Conforme declarado por Bertrand Meyer:
> Entidades de software (classes, módulos, funções, etc.) devem ser abertas para extensão, mas fechadas para modificação.

Mas o que isso significa? Basicamente, esse princípio diz que você deve permitir que os usuários (do código) adicionem funcionalidades sem modificar o código existente.

**Ruim**

```cs
abstract class AdapterBase
{
    protected string Name;

    public string GetName()
    {
        return Name;
    }
}

class AjaxAdapter : AdapterBase
{
    public AjaxAdapter()
    {
        Name = "ajaxAdapter";
    }
}

class NodeAdapter : AdapterBase
{
    public NodeAdapter()
    {
        Name = "nodeAdapter";
    }
}

class HttpRequester : AdapterBase
{
    private readonly AdapterBase Adapter;

    public HttpRequester(AdapterBase adapter)
    {
        Adapter = adapter;
    }

    public bool Fetch(string url)
    {
        var adapterName = Adapter.GetName();

        if (adapterName == "ajaxAdapter")
        {
            return MakeAjaxCall(url);
        }
        else if (adapterName == "httpNodeAdapter")
        {
            return MakeHttpCall(url);
        }
    }

    private bool MakeAjaxCall(string url)
    {
        // solicita e retorna uma promise
    }

    private bool MakeHttpCall(string url)
    {
        // solicita e retorna uma promise
    }
}
```

**Bom**

```cs
interface IAdapter
{
    bool Request(string url);
}

class AjaxAdapter : IAdapter
{
    public bool Request(string url)
    {
        // solicita e retorna uma promise
    }
}

class NodeAdapter : IAdapter
{
    public bool Request(string url)
    {
        // solicita e retorna uma promise
    }
}

class HttpRequester
{
    private readonly IAdapter Adapter;

    public HttpRequester(IAdapter adapter)
    {
        Adapter = adapter;
    }

    public bool Fetch(string url)
    {
        return Adapter.Request(url);
    }
}
```

## L: Princípio de Substituição de Liskov

Esse é um termo assustador para um conceito bem simples. Ele é definido formalmente por:

*Se S é um subtipo de T, então objetos do tipo T podem ser substituídos por objetos do tipo S sem que isso altere qualquer propriedade desejável do programa (corretude, tarefa executada, etc.).*

A definição é mais assustadora ainda.

A melhor explicação para isso é: se você tem uma classe base e uma classe derivada, então a classe base e a derivada podem ser usadas de forma intercambiável sem obter resultados incorretos. Isso pode parecer confuso, então vamos olhar o exemplo clássico do quadrado-retângulo. Matematicamente o quadrado é um retângulo, mas se você modela usando um relacionamento "é-um" por herança, então temos imediatamente um problema.

**Ruim**

```cs
class Rectangle
{
    protected double Width = 0;
    protected double Height = 0;

    public Drawable Render(double area)
    {
        // ...
    }

    public void SetWidth(double width)
    {
        Width = width;
    }

    public void SetHeight(double height)
    {
        Height = height;
    }

    public double GetArea()
    {
        return Width * Height;
    }
}

class Square : Rectangle
{
    public double SetWidth(double width)
    {
        Width = Height = width;
    }

    public double SetHeight(double height)
    {
        Width = Height = height;
    }
}

Drawable RenderLargeRectangles(Rectangle rectangles)
{
    foreach (rectangle in rectangles)
    {
        rectangle.SetWidth(4);
        rectangle.SetHeight(5);
        var area = rectangle.GetArea(); // Ruim: retornará 25 para o quadrado. Deveria ser 20.
        rectangle.Render(area);
    }
}

var rectangles = new[] { new Rectangle(), new Rectangle(), new Square() };
RenderLargeRectangles(rectangles);
```

**Bom**

```cs
abstract class ShapeBase
{
    protected double Width = 0;
    protected double Height = 0;

    abstract public double GetArea();

    public Drawable Render(double area)
    {
        // ...
    }
}

class Rectangle : ShapeBase
{
    public void SetWidth(double width)
    {
        Width = width;
    }

    public void SetHeight(double height)
    {
        Height = height;
    }

    public double GetArea()
    {
        return Width * Height;
    }
}

class Square : ShapeBase
{
    private double Length = 0;

    public double SetLength(double length)
    {
        Length = length;
    }

    public double GetArea()
    {
        return Math.Pow(Length, 2);
    }
}

Drawable RenderLargeRectangles(Rectangle rectangles)
{
    foreach (rectangle in rectangles)
    {
        if (rectangle is Square)
        {
            rectangle.SetLength(5);
        }
        else if (rectangle is Rectangle)
        {
            rectangle.SetWidth(4);
            rectangle.SetHeight(5);
        }

        var area = rectangle.GetArea();
        rectangle.Render(area);
    }
}

var shapes = new[] { new Rectangle(), new Rectangle(), new Square() };
RenderLargeRectangles(shapes);
```
## I: Princípio da Separação em interfaces

O princípio da separação em interfaces declara que:
> Clientes não devem ser forçados a depender de uma interface que eles não usam.

Um bom exemplo de que demonstra esse princípio é para classes que necessitam uma quantidade grande de objetos de configuração. É benéfico que o cliente não precise configurar um monte de opções, pois na maioria das vezes não são necessárias todas as configurações. Fazer com que elas sejam opcionais previne ter uma interface muito grande.

**Ruim**

```cs
public interface IEmployee
{
    void Work();
    void Eat();
}

public class Human : IEmployee
{
    public void Work()
    {
        // trabalhando...
    }

    public void Eat()
    {
        // comendo no intervalo...
    }
}

public class Robot : IEmployee
{
    public void Work()
    {
        //trabalhando muito mais
    }

    public void Eat()
    {
        //Robôs não comem, mas sou obrigado a implementar este método
    }
}
```

**Bom**

Nem todo trabalhador é empregado, mas todo empregado é trabalhador.

```cs
public interface IWorkable
{
    void Work();
}

public interface IFeedable
{
    void Eat();
}

public interface IEmployee : IFeedable, IWorkable
{
}

public class Human : IEmployee
{
    public void Work()
    {
        // trabalhando...
    }

    public void Eat()
    {
        //Comendo no intervalo
    }
}

// robôs somente trabalham
public class Robot : IWorkable
{
    public void Work()
    {
        //trabalhando...
    }
}
```

## D: Princípio de Inversão de Dependência

Esse princípio declara duas coisas essenciais:

1. Módulos de alto nível não devem depender de módulos de baixo nível.
1. Abstrações não devem depender de detalhes. Detalhes devem depender de abstrações.

A princípio, pode parecer difícil de entender, mas se você já trabalhou com .NET/.NET Core, você já viu a implementação desse princípio na forma de Injeção de Dependência. Embora não sejam conceitos idênticos, a Inversão de Dependência evita que módulos de alto nível saibam detalhes de módulos de baixo nível ou de como configurá-los e isso pode ser alcançado com Injeção de Dependência. Além disso, conseguimos evitar o acoplamento de módulos, visto que alto acoplamento é um mau hábito de desenvolvimento porque dificulta a refatoração de código.

**Ruim**

```cs
public abstract class EmployeeBase
{
    protected virtual void Work()
    {
        // trabalhando...
    }
}

public class Human : EmployeeBase
{
    public override void Work()
    {
        //trabalhando mais...
    }
}

public class Robot : EmployeeBase
{
    public override void Work()
    {
        //trabalhando muito mais...
    }
}

public class Manager
{
    private readonly Robot _robot;
    private readonly Human _human;

    public Manager(Robot robot, Human human)
    {
        _robot = robot;
        _human = human;
    }

    public void Manage()
    {
        _robot.Work();
        _human.Work();
    }
}
```

**Bom**

```cs
public interface IEmployee
{
    void Work();
}

public class Human : IEmployee
{
    public void Work()
    {
        // trabalhando...
    }
}

public class Robot : IEmployee
{
    public void Work()
    {
        //trabalhando mais...
    }
}

public class Manager
{
    private readonly IEnumerable<IEmployee> _employees;

    public Manager(IEnumerable<IEmployee> employees)
    {
        _employees = employees;
    }

    public void Manage()
    {
        foreach (var employee in _employees)
        {
            _employee.Work();
        }
    }
}
```

## D: Não se repita

Faça o seu melhor para evitar código duplicado. Código duplicado é ruim porque significa que existe mais de um lugar para alterar alguma coisa se você precisa modificar alguma lógica.

Às vezes temos código duplicado porque precisamos fazer duas coisas ligeiramente diferentes, mas que compartilham um monte em comum, mas as diferenças te forçam a ter duas ou mais funções separadas que fazem um monte da mesma coisa. Remover código duplicado significa criar uma abstração que possa lidar com essas diferenças com apenas uma função/classe/módulo.

Acertar na abstração é crítico e, por isso, você deve seguir os conceitos do SOLID explicados na seção **Classes**. Má abstração pode levar a mais código duplicado.

**Ruim**

```cs
public List<EmployeeData> ShowDeveloperList(Developers developers)
{
    foreach (var developers in developer)
    {
        var expectedSalary = developer.CalculateExpectedSalary();
        var experience = developer.GetExperience();
        var githubLink = developer.GetGithubLink();
        var data = new[] {
            expectedSalary,
            experience,
            githubLink
        };

        Render(data);
    }
}

public List<ManagerData> ShowManagerList(Manager managers)
{
    foreach (var manager in managers)
    {
        var expectedSalary = manager.CalculateExpectedSalary();
        var experience = manager.GetExperience();
        var githubLink = manager.GetGithubLink();
        var data =
        new[] {
            expectedSalary,
            experience,
            githubLink
        };

        render(data);
    }
}
```

**Bom**

```cs
public List<EmployeeData> ShowList(Employee employees)
{
    foreach (var employee in employees)
    {
        var expectedSalary = employees.CalculateExpectedSalary();
        var experience = employees.GetExperience();
        var githubLink = employees.GetGithubLink();
        var data =
        new[] {
            expectedSalary,
            experience,
            githubLink
        };

        render(data);
    }
}
```

**Muito bom**

```cs
public List<EmployeeData> ShowList(Employee employees)
{
    foreach (var employee in employees)
    {
        render(new[] {
            employee.CalculateExpectedSalary(),
            employee.GetExperience(),
            employee.GetGithubLink()
        });
    }
}
```
---

[Início](csharp.md)