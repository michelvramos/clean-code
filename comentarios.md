# Comentários

## Evite marcadores de posição

Geralmente apenas adicionam poluição. Deixe com que os nomes de variáveis junto com a indentação e formatação corretas mostrem a estrutura visual do seu código.

**Ruim**

```cs
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
var model = new[]
{
    menu: 'foo',
    nav: 'bar'
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
void Actions()
{
    // ...
};
```

**Ruim**

```cs
#region Scope Model Instantiation

var model = {
    menu: 'foo',
    nav: 'bar'
};

#endregion

#region Action setup

void Actions() {
    // ...
};

#endregion
```

**Bom**

```cs
var model = new[]
{
    menu: 'foo',
    nav: 'bar'
};

void Actions()
{
    // ...
};
```

## Não deixe código comentado no fonte

Controle de versão existe por um motivo. Deixe código velho no histórico.

**Ruim**

```cs
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Bom**

```cs
doStuff();
```

## Não faça comentários de histórico

Lembre-se, controle de versão. Não há necessidade para código morto e, especialmente, comentários de histórico. Use o `git log` para isso.

**Ruim**

```cs
/**
 * 2018-12-20: Removido mônadas, pois não entendo (RM)
 * 2017-10-01: Melhorado usando mônadas especiais (JP)
 * 2016-02-03: Removido verificação de tipo (LI)
 * 2015-03-14: Adicionado Combine com verificação de tipo (JR)
 */
public int Combine(int a,int b)
{
    return a + b;
}
```

**Bom**

```cs
public int Combine(int a,int b)
{
    return a + b;
}
```

## Comente coisas que possuem regras de negócio complexas

Comentários são pedidos de desculpas, não um requisito. Um código bom praticamente se auto documenta.

**Ruim**

```cs
public int HashIt(string data)
{
    // hash
    var hash = 0;

    // tamanho da string
    var length = data.length;

    // Loop em cada carctere em data
    for (var i = 0; i < length; i++)
    {
        // Obtém o código do caractere.
        const char = data.charCodeAt(i);
        // Faz o hash
        hash = ((hash << 5) - hash) + char;
        // converter para inteiro de 32 bits
        hash &= hash;
    }
}
```

**Melhor, mas ainda ruim**

```cs
public int HashIt(string data)
{
    var hash = 0;
    var length = data.length;
    for (var i = 0; i < length; i++)
    {
        const char = data.charCodeAt(i);
        hash = ((hash << 5) - hash) + char;

        // Converte para inteiro de 32 bits
        hash &= hash;
    }
}
```

Se o comentário explica O QUÊ o código está fazendo, provavelmente é um comentário desnecessário e pode ser realizado com uma boa nomenclatura de variáveis. Por outro lado, seria difícil expressar porque o desenvolvedor escolheu `djb2` em vez de `sha-1` ou outra função hash. Nesse caso, um comentário é aceitável.

**Bom**

```cs
public int Hash(string data)
{
    var hash = 0;
    var length = data.length;

    for (var i = 0; i < length; i++)
    {
        var character = data[i];
        // Usei hash djb2 porque é um bom compromisso
        // entre velocidade e baixa colisão com implementação simples
        hash = ((hash << 5) - hash) + character;

        hash = ConvertTo32BitInt(hash);
    }
    return hash;
}

private int ConvertTo32BitInt(int value)
{
    return value & value;
}
```

---

[Início](csharp.md)