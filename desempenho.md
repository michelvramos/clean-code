# Desempenho

## Coloque a condição impeditiva antes do trabalho pesado

Se você precisa validar dados, faça isso antes do trabalho pesado evitando, assim, alocação desnecessária de recursos de memória, processador e rede.

**Ruim**

```cs
void UpdatePersonEmail(int id, string emailAddress) 
{
    Person person = db.Person.find(id);// Desnecessário

    if(!isValidEmailAddress(emailAddress)) 
    {
        throw new Exception("E-mail inválido");
    }

    person.Email = emailAddress;
    db.Person.Update(person);
    db.SaveChanges();
}
```

**Bom**

```cs
void UpdatePersonEmail(int id, string emailAddress) 
{    
    if( !isValidEmailAddress(emailAddress)) 
    {
        throw new Exception("E-mail inválido");
    }

    Person person = db.Person.find(id);
    person.Email = emailAddress;
    db.Person.Update(person);
    db.SaveChanges();
}
```

## Coloque as condições mais frequentes primeiro

Ao escrever um bloco `if/else` com múltiplos blocos, coloque as condições que ocorrem com maior frequência primeiro.

**Ruim**

```cs
void CheckAge(int age)
{
    if(age < 6) 
    {
        throw new InvalidAgeException();
    }
    else if (age >= 13 && age < 18)
    {
        handleTeenager();
    }
    else if (age >= 6 && age < 13)
    {
        handleChild();
    }
    else if (age >= 18)
    {
        handleAdult();
    }
}
```

**Bom**

```cs
void CheckAge(int age)
{
    if(age >= 18)// a maioria dos cientes são adultos
    {
        handleAdults();
        return;
    }
    
    if (age >= 13)
    {
        handleTeenager();
        return;
    }
    
    if (age >= 6)
    {
        handleChild();
        return;
    }
    
    throw new InvalidAgeException();    
}
```

## Evite múltiplas chamadas ao banco de dados

Obter dados de um banco de dados envolve diversas tarefas internas tais como alocar de memória, abrir conexão de rede, converter fluxos de bytes em objetos, etc. Se você tem uma lista de id de objetos que precisa obter do banco para fazer alguma tarefa sobre eles, busque todos em uma chamada e não um por um dentro de um loop. Essa pode ser a diferença entre abrir e fechar 5 mil conexões de rede ou abrir e fechar somente uma conexão.

**Ruim**

```cs
void UpdateTax (int[] employeeList)
{
    foreach( int id in employeeList)
    {
        //fará uma chamada para cada item na lista
        Person person = db.Person.Find(id);
        taxService.CalculateTax(person.Salary);
    }
}
```

**Bom**

```cs
void UpdateTax (int[] employeeList)
{
    //obterá todos os empregados em uma única chamada
    List<Person> personList = db.Person.Where(x=>employeeList.Contains(x.Id)).ToList();

    foreach( Person person in personList)
    {        
        taxService.CalculateTax(person.Salary);
    }
}
```

Internamente, o Entity Framework (ou outro ORM qualquer) irá converter a chamada ao banco nas seguintes instruções SQL:

**Ruim**

```sql
select column1, column2..., columnN from Person p where p.id = @id;
```

**Bom**

```sql
select column1, column2..., columnN from Person p where p.id in ( id1, id2, id3 ... idN );
```

## Não seja redundante

Não teste se um objeto é `null` para retornar `null`. Se o método retorna `Task<SomeClass>` e a única chamada no corpo do método também retona `Task<SomeClass>`, então não use `await`, apenas remova o `async` da assinatura e retorne a `Task` para o chamador.

**Ruim**

```cs
public async Task<List<Rules>> GetRules(string ruleType)
{
    List<Rules> rules = await repository.GetRulesByTypeAsync(ruleType);

    if (rules == null)
    {
        return null;
    }

    return rules;
}
```

**Bom**

```cs
public Task<List<Rules>> GetRules(strin ruleType)
{
    return repository.GetRulesByTypeAsync(ruleType);    
}
```

---

[Início](csharp.md)