# Clean Code

Como manter seu código limpo (Clean Code) seguindo algumas práticas sugeridas pelo Robert C. Martin (Uncle Bob).

## O que é o Clean Code?

Por que estamos falando tanto sobre código limpo (Clean Code) e por que isto é tão importante para nós? De fato a manutenção de um software é tão importante quanto sua construção.

Como relatado por Robert C. Martin em seu livro clássico, *Clean Code*, um Best Seller da nossa área, algumas práticas e visões são importantíssimas para mantermos a *vida* do nosso software.

> **IMPORTANTE** Este artigo não descarta a leitura do livro, que é muito mais denso e profundo sobre o assunto.

As empresas investem milhões em softwares todo ano, mas com tantas mudanças no time e nas tecnologias, como fazer este investimento durar? Como garantir uma boa manutenção, durabilidade, vida ao software? Segundo Uncle Bob, as práticas abaixo são o caminho.


## Regras gerais
### Siga as convenções
Se você começou agora em um projeto ou acabaram de definir suas convenções, siga-as! Se utilizam por exemplo constantes em maiúsculo, enumeradores com **E** como prefixo, não importa! Siga sempre os padrões do projeto.

### KISS
Mantenha as coisas simples! Este conceito vem até de outro livro, e particularmente acho que é a base de uma boa solução. Normalmente tendemos a complicar as coisas que poderiam ser muito mais simples.

Então, Keep It Stupid Simple (Mantenha isto estupidamente simples - KISS)!

### Regra do escoteiro
"Deixe sempre o acamapamento mais limpo do que você encontrou!" O mesmo vale para nosso código. Devolva (Check in) sempre o código melhor do que você o obteve. Se todo desenvolvedor no time tiver esta visão, e devolver um pedacinho de código melhor do que estava antes, em pouco temos teremos uma grande mudança.

### Causa raiz
Sempre procure a causa raiz do problema, nunca resolva as coisas superficialmente. No dia-a-dia, na correria, tendemos a corrigir os problemas superficialmente e não adentrar neles, o que muitas vezes causa o re-trabalho!

Tente sempre procurar a causa raiz e resolver assim o problema de uma vez por todas!

## Regras de design

### Mantenha dados de configuração em alto nível

Algo que toda aplicação tem são suas configurações, como as conhecidas **ConnectionStrings**. Tente sempre deixar estas configurações ou o *parse* delas em um nível mais alto possível.

Evite sobrescrever configurações em métodos dentro de **Controllers** ou algo do tipo. Se possível, mantenha esta passagem no método principal, no início da aplicação e não mexa mais nisto!

#### Exemplo

Em diversas aplicações que trabalho, crio sempre uma classe **Settings** no projeto base e depois no **Startup** das aplicações populo ela com as configurações. Isto garante que não teremos estas configurações sendo escritas em todo lugar e também que não precisaremos do **IConfiguration** que fica no **ASP.NET** em projetos que não são Web.

<small>**MeuProjeto.Domain**</small>
```csharp
public static class Settings {
    public static string ConnectionString { get; set; }
}
```

<small>**MeuProjeto.Api**</small>
```csharp
public Startup(IConfiguration configuration)
{
    Configuration = configuration;
    Settings.ConnectionString = Configuration.GetConnectionString("connectionString");
}
```

<small>**MeuProjeto.Infra**</small>
```csharp
using(var connection = new SqlConnection(Settings.ConnectionString)) {
    ...
}
```

### Polimorfismo no lugar de IFs

Um **IF** ou condicional, como o nome diz, traz uma tomada de decisão a nossa aplicação, o que implica no aumento da complexidade da mesma. No geral devemos evitar o uso excessivo destes.

Nestes cenários, opte sempre pelo [polimorfismo](https://balta.io/blog/orientacao-a-objetos) ao invés de tomar decisão em todo método que cria. 

#### Exemplo

Vamos tomar como base uma classe **Pagamento**, onde temos pagamento via Boleto ou Cartão de Crédito, porém nos pagamentos via Boleto, caso o dia do vencimento seja sábado ou domingo (Final de semana), o mesmo pode ser pago no próximo dia útil.

> **IMPORTANTE** Esta regra não está 100% correta ou eficiente, é apenas uma demonstração

```csharp
public class Pagamento {
    public bool PodeSerPago() {
        if(tipo == ETipoPagamento.Boleto)
        {
            if(vencimento.Day != IsWeekend())
                return true;
        }

        if(tipo == ETipoPagamento.CartaoCredito)
            ...
    }
}
```

Note que temos duas tomadas de decisão dentro do método **PodeSerPago**, onde a primeira se refere apenas a pagamentos do tipo Boleto. Caso hajam mais formas de pagamento futuramente, como tratariamos este código? Encheriamos de **IF**?

A solução mais plausível é derivar da classe base **Pagamento** criando o **PagamentoBoleto** que sobrescreve o método **PodeSerPago**, dando uma nova funcionalidade a ele.

```csharp
public class Pagamento {
    public virtual bool PodeSerPago() {
        ...
    }
}

public class PagamentoBoleto : Pagamento {
    public override bool PodeSerPago() {
        if(vencimento.Day != IsWeekend())
            return true;
    }
}
```

### Mult-thread

Sempre que necessário utilize processamento em Threads separadas. Já temos suporte a multi-threads e paralelismo no C# faz um bom tempo e o próprio Async/Await já ajudam nisso.

#### Exemplo

<small><strong>Sem async/await</strong></small>

```csharp
[HttpGet("cursos")]
public IActionResult Index([FromServices] IContentRepository repository)
{
    ViewBag.Courses = repository.GetContents(EContentType.Course);
    return View();
}
```

<small><strong>Com async/await</strong></small>

```csharp
[HttpGet("cursos")]
public async Task<IActionResult> Index([FromServices] IContentRepository repository)
{
    ViewBag.Courses = await repository.GetContentsAsync(EContentType.Course);
    return View();
}
```

### Separe os códigos mult-thread

Seguindo o mesmo exemplo acima, é uma boa prática manter o que é assíncrono separado do que é síncrono, para não forçar um método a ser ou não assíncrono por conta de outro trecho de código.

#### Exemplo

```csharp
public async Task<IEnumerable<Model>> GetAsync()
{
    var model = new Model();
    model.Courses = await _context.Courses.ToListAsync();
    model.Tags = _context.Courses.ToList(); // Não async
    
    return model;
}
```

### Utilize Async como sufixo

Se um método é assíncrono, utilize sempre o sufixo **async** para identificá-lo.

```csharp
public async Task<IEnumerable<Model>> GetAsync()
```

### Exite configurações desnecessárias

Evite deixar configurações no sistema só por que alguém ainda não definiu como aquilo deve ser. Isto polui o código e traz uma complexidade desnecessária.

#### Exemplo
```csharp
public void ConfiguraUsoMySql() 
{
    // ainda não sabemos se vamos ou não suportar MySQL também
    throw new NotImplementedException();
}
```

### Utilize injeção de dependência

Sempre que possível utilize [injeção de dependência](https://balta.io/blog/dependency-injection), ele vai tornar seu código mais limpo e desacoplado.

#### Exemplo
[![Dependency Injection](https://img.youtube.com/vi/va9wX68lAfA/0.jpg)](https://www.youtube.com/watch?v=va9wX68lAfA "Dependency Injection")


### Lei de Demeter

A Lei de Demeter (LoD) ou princípio do menor conhecimento é um princípio que prega os seguintes pontos.

 * Cada unidade deve ter conhecimento limitado sobre outras unidades: apenas unidades próximas se relacionam. 
 * Cada unidade deve apenas conversar com seus amigos.
 * Não fale com estranhos, apenas fale com seus amigos imediatos

#### Exemplo

```csharp
public class Order() 
{ 
    public Discount Discount { get; set; }
}

public class Discount() 
{ 
    public decimal Amount { get; set; }

    public void Apply() { ... }
}
```

<small><strong>Mau exemplo</strong></small>

```csharp
public class OrderHandler() 
{
    var order = new Order();
    order.Discount.Apply(); // <-
}
```

<small><strong>Bom exemplo</strong></small>

```csharp
public class Order() 
{ 
    public Discount Discount { get; set; }

    public void Place() 
    {
        Discount?.Apply();
    }
}

public class OrderHandler() 
{
    var order = new Order();
    order.Place();
}
```

## Regras sobre entendimento do código

### Seja consistente
Se você executa algo de uma forma, execute todo o resto desta mesma forma. Seja consistente na forma com que aplica o código. Siga sempre o padrão definido.


#### Exemplo
```csharp
// Codificando em inglês
public class CustomerRepository { ... }

// Agora mudou para "portuglês"
public class ProdutoRepository { ... }

// Agora é português
public class RepositorioUnidadeMedida { ... }
```

```csharp
// Utilizou sufixo ASYNC no método assíncrono
public async Task<Product> GetAsync() { ... }

// Agora não usou mais =/
public async Task<Course> Get() { ... }
```

### Utilize variáveis concisas

Opte por variáveis concisas, mesmo que resultem em um nome maior. Elas devem ser auto-explicativas, sem a necessidade de comentários ou informações adicionais.

#### Exemplo
```csharp
// Total do que?
decimal total = 0;

// Total do carrinho de compras
decimal shoppingCartTotal = 0;
```

### Obsessão primitiva

No dia-a-dia tendemos a nos focar apenas em tipos primitivos (Built-in), causando uma obsessão pelos mesmos. Podemos criar e usar objetos de valor (Value Objects) para suprir melhor esta necessidade.

#### Exemplo

<small><strong>Mau exemplo</strong></small>

```csharp
public class Customer 
{
    public string Email { get; set; }

    public Customer 
    {
        // Valida E-mail
    }
}

public class Employee 
{
    public string Email { get; set; }

    public Customer 
    {
        // Valida E-mail novamente
    }
}
```

<small><strong>Bom exemplo</strong></small>

```csharp
// Value Object
public class Email 
{
    public string Address { get; set; }

    public Email 
    {
        // Valida E-mail
    }
}

public class Customer 
{
    public Email Email { get; set; }
}

public class Employee 
{
    public Email Email { get; set; }
}
```

### Evite dependências lógicas

Não escreva métodos cujo funcionamento correto dependa de algo contido em sua classe.

#### Exemplo

<small><strong>Mau exemplo</strong></small>

```csharp
public class Student 
{
    public bool IsSubscriber { get; set; }

    public void Xpto() 
    {
        if(IsSubscriber)
            ... // Só executa se for assinante   
    }
}
```

<small><strong>Bom exemplo</strong></small>

```csharp
public class Student 
{
    ...
}

public class Subscriber : Student
{
    public void Xpto() 
    {
        ...        
    }
}
```

### Evite condicionais negativas

No C# a negação é dada por um sinal de exclamação (**!**) que muitas vezes pode ser imperceptível, ocasionando na má leitura do código.

#### Exemplo

```csharp
// Evite
if(!IsSubscriber) { ... }

// Utilize
if(IsSubscriber) { ... }
```

## Regras de nomes

### Escolha nomes descritivos

Esolher bons nomes para classes, variáveis e métodos é essencial para um código limpo. Lembre-se que se você precisa explicar seu código, então algo pode ser melhorado nele.

#### Exemplo
```csharp
// Evite
var x = 256;

// Duração do que? Qual a métrica?
int duration = 25;

// Muito mais expressivo
int durationInMinutes = 25;
```

### Faça distinções significantes

Utilize sempre nomes nos quais quem estiver lendo seu código possa diferenciar seu significado de outros possíveis nomes.

#### Exemplo
```csharp
// Evite
var salario = 7500M;

// Tem um significado maior
var salarioEmReais = 7500M;
```

### Utilize nomes pronunciáveis e buscáveis

Evite utilizar nomes difíceis de pronunciar ou inventar nomes e conveções para variáveis, classes e métodos. Lembre-se sempre da linguagem ubíquoa e da importância dela no código.

#### Exemplo
```csharp
// Evite
var strTexto = "Meu texto aqui";

// Evite
public void GenerateBoletoInLote() {}

// Evite
public void Cadastry() {}
```

### Evite uso excessivo de strings

Quem nunca perdeu horas procurando um BUG que era apenas um problema de comparação de string? Evite digitar a mesma string várias vezes, utilize constantes para isto.

#### Exemplo

```csharp
// Evite
if(environment == "PROD")
    ...

// Utilize
const string ENV = "PROD";

if(environment == ENV)
    ...
```

### Não use prefixo ou caracteres especiais

Não utilize prefixo com o tipo da variável, classe ou método e NUNCA use espaços ou caracteres especiais nestes itens.

#### Exemplo

```csharp
// Evite
public class clsCustomer { ... }

// Evite
string strNome = "André";

// Evite
var situação - "Pendente";
```

## Functions rules
1. Small.
2. Do one thing.
3. Use descriptive names.
4. Prefer fewer arguments.
5. Have no side effects.
6. Don't use flag arguments. Split method into several independent methods that can be called from the client without the flag.

## Comments rules
1. Always try to explain yourself in code.
2. Don't be redundant.
3. Don't add obvious noise.
4. Don't use closing brace comments.
5. Don't comment out code. Just remove.
6. Use as explanation of intent.
7. Use as clarification of code.
8. Use as warning of consequences.

## Source code structure
1. Separate concepts vertically.
2. Related code should appear vertically dense.
3. Declare variables close to their usage.
4. Dependent functions should be close.
5. Similar functions should be close.
6. Place functions in the downward direction.
7. Keep lines short.
8. Don't use horizontal alignment.
9. Use white space to associate related things and disassociate weakly related.
10. Don't break indentation.

## Objects and data structures
1. Hide internal structure.
2. Prefer data structures.
3. Avoid hybrids structures (half object and half data).
4. Should be small.
5. Do one thing.
6. Small number of instance variables.
7. Base class should know nothing about their derivatives.
8. Better to have many functions than to pass some code into a function to select a behavior.
9. Prefer non-static methods to static methods.

## Tests
1. One assert per test.
2. Readable.
3. Fast.
4. Independent.
5. Repeatable.

## Code smells
1. Rigidity. The software is difficult to change. A small change causes a cascade of subsequent changes.
2. Fragility. The software breaks in many places due to a single change.
3. Immobility. You cannot reuse parts of the code in other projects because of involved risks and high effort.
4. Needless Complexity.
5. Needless Repetition.
6. Opacity. The code is hard to understand.

## Fonte
https://gist.github.com/wojteklu/73c6914cc446146b8b533c0988cf8d29

https://www.amazon.com.br/Clean-Code-Handbook-Software-Craftsmanship-ebook/dp/B001GSTOAM