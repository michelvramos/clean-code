# Conceitos de Clean Code adaptados para .NET/.NET Core

Escrito em: 29/03/2022

Autor: Michel Vaz Ramos

Adaptado de experiências pessoais e das seguintes fontes:

- [Clean Code .NET](https://github.com/thangchung/clean-code-dotnet#introduction)
- [C# Coding Guidelines](https://csharpcodingguidelines.com/)


# Introdução

> "A única métrica válida para qualidade de código é: WTF/minuto" - ([HOLWERDA, Thom, 2008](https://www.osnews.com/story/19266/wtfsm/))

Esse documento contém **princípios de engenharia de software** retirados do livro [Clean Code de Robert C. Martin](#referências), adaptados para .NET/.NET Core.

**Esteja ciente que:**

1. Nem todos os princípios devem ser estritamente seguidos.
1. Poucos desses princípios são universalmente aceitos.
1. Esses princípios são nada mais além de guias, porém, foram **criados e aprimorados coletivamente através de anos de prática e experiência** pelos autores do livro.

# Por que usar esse guia se ele limita minha criatividade?

Você deve usar esse guia porque nem todo desenvolvedor:

* está ciente de que se lê código 10x mais do que se escreve código.
* está ciente das potenciais armadilhas de certas construções em C#.
* está atualizado sobre certas convenções do .NET Framework, tais como: `IDisposable, async / await` ou a natureza da execução adiada do LINQ.
* está ciente do impacto de usar (ou não usar) soluções específicas em aspectos como segurança, performance, suporte a multi-linguagem, etc.
* percebe que nem todo desenvolvedor é capacitado, habilidoso ou experiente para entender soluções elegantes, porém, potencialmente muito abstratas.

Este guia está subdividido nos tópicos a seguir:

1. [Nomes](nomes.md)
1. [Variáveis](variaveis.md)
1. [Funções](funcoes.md)
1. [Objetos e Estruturas de Dados](objetosEstruturas.md)
1. [Classes](classes.md)
1. [SOLID](solid.md)
1. [Tratamento de Exceções](erros.md)
1. [Comentários](comentarios.md)
1. [Desempenho](desempenho.md)

Espero que esse guia seja útil para quem está lendo tanto quanto foi útil para mim enquanto eu escrevia este documento.

# Referências

##### 1. Martin, R. C. (2019). Código Limpo: Habilidades Práticas do Agile Software. Brasil: Alta Books.