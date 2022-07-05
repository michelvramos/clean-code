# Tratamento de Exceções

## Afinal, o que é exceção?

Em termos de linguagens de programação, uma exceção é um desvio condicional, exatamente como `if/else,switch/case`, com o diferencial que ela carrega consigo a pilha de execução. No C#, quando uma exceção é disparada, o método atual é interrompido e a execução continua no ponto onde o método foi chamado. Se o ponto onde o método for chamado não estiver dentro de um bloco `try/catch`, então o método é interrompido e retorna para o ponto onde foi chamado, e assim sucessivamente até que seja encontrado um bloco `try/catch`. Se chegarmos no topo da árvore de chamadas e não houver tratamento de exceção, o programa é terminado.


## Quando disparar exceção?

Resposta: quando não for possível continuar a partir do ponto atual no código, seja porque um serviço não está disponível, uma falha na leitura de um arquivo, um parâmetro de entrada inválido na função, etc. Pense no seguinte: a regra é que o método deve executar até o final com as informações que ele possui. Se ele não pode executar até o final, isso é uma exceção, afinal, você não pode enviar um e-mail sem o endereço do destinatário, por exemplo. Resumo: sempre que a função/método não for capaz de cumprir a sua tarefa, dispare uma exceção.

**Ruim**

```cs
Person UpdatePersonEmail(int personId, string emailAddress)
{    
    Person person = db.person.find(personId);

    if(person == null)
    {
        return null;
    }

    person.email = emailAdress;
    db.person.Update(person);

    return person;
}

person = UpdatePersonEmail(-1,"michel.ramos@deliverit.com.br");
//agora pessoa é null
```

**Bom**

```cs
Person UpdatePersonEmail( int personId, string emailAddress)
{    
    Person person = db.person.find(personId);

    if( person == null )
    {
        throw new Exception("Pessoa não encontrada.");
    }

    person.email = emailAdress;
    db.person.Update(person);

    return person;
}

try 
{
    person = UpdatePersonEmail(-1,"michel.ramos@deliverit.com.br");
} 
catch (Exception ex) {
    //executar limpeza e sair do método...
    //ou continuar sem enviar e-mail...
}

```

## Não use `throw ex` no bloco `catch`

Se você precisar disparar a mesma exceção, apena use `throw`. Dessa forma a pilha de execução será preservada, caso contrário, será perdida.

**Ruim**

```cs
try
{
    // Faça alguma coisa...
}
catch (Exception ex)
{
    // Qualquer ação, como desfazer alterações, fazer log.
    throw ex;
}
```

**Pior**

```cs
try
{
    // Faça alguma coisa...
}
catch (Exception ex)
{
    // Qualquer ação, como desfazer alterações, fazer log.
    throw new Exception(ex.Message);
}
```

**Bom**

```cs
try
{
    // Faça alguma coisa...
}
catch (Exception ex)
{
   // Qualquer ação, como desfazer alterações, fazer log.
    throw;
}
```

## Não ignore erros pegos

Fazer nada com um erro pego não lhe dá a habilidade de corrigir ou reagir a um erro. Relançar uma exceção não é muito melhor do que isso, pois pode se perder em meio a uma enxurrada de mensagens no console. Se você coloca um código dentro do `try/catch`, isso significa que você acha que uma exceção pode ser disparada e você deve ter um plano ou um caminho alternativo no código para isso.

**Ruim**

```cs
try
{
    FunctionThatMightThrow();
}
catch (Exception ex)
{
    // erro silencioso
}
```

**Bom**

```cs
try
{
    FunctionThatMightThrow();
}
catch (Exception error)
{
    NotifyUserOfError(error);

    // Outra opção
    ReportErrorToService(error);
}
```

## Use múltiplos blocos `catch` em vez de condicionais `if`

Se você precisa executar uma ação de acordo com o tipo de exceção, use múltiplos blocos `catch`.

**Ruim**

```cs
try
{
    // Faça algo...
}
catch (Exception ex)
{

    if (ex is TaskCanceledException)
    {
        // Faça uma ação para TaskCanceledException
    }
    else if (ex is TaskSchedulerException)
    {
        // Faça uma ação para TaskSchedulerException
    }
}
```

**Bom**

```cs
try
{
    // Faça algo
}
catch (TaskCanceledException ex)
{
    // Faça uma ação para TaskCanceledException
}
catch (TaskSchedulerException ex)
{
    // Faça uma ação para TaskSchedulerException
}
```

## Mantenha a pilha de exceções quando relançar uma exceção

C# permite que uma exceção seja relançada usando a palavra chave `throw`. É uma má prática lançar uma exceção pega usando `throw ex;`, pois isso redefine a pilha de exceções. Em vez disso, use `throw`, dessa forma, mantendo a pilha de exceções. Uma alternativa é lançar uma `throw new CustomException( "alguma informação", ex);` e atribuir `ex` a propriedade `InnerException`. Fazer log de exceções é uma boa prática, porém o objetivo é fazer o log e depois utilizar `throw`.

**Ruim**

```cs
try
{
    FunctionThatMightThrow();
}
catch (Exception ex)
{
    logger.LogInfo(ex);
    throw ex;
}
```

**Bom**

```cs
try
{
    FunctionThatMightThrow();
}
catch (Exception error)
{
    logger.LogInfo(error);
    throw;
}
```

---

[Início](csharp.md)