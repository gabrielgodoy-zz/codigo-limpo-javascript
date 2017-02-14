Repositório original: [ryanmcdermott/clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)

# codigo-limpo-javascript

## Índice
  1. [Introdução](#introdução)
  2. [Variáveis](#váriaveis)
  3. [Funções](#funções)
  4. [Objetos e estrutura dos dados](#objetos-e-estrutura-dos-dados)
  5. [Classes](#classes)
  6. [Testes](#testes)
  7. [Concorrência e assincronia](#concorrência-e-assincronia)
  8. [Tratamento de erros](#tratamento-de-erros)
  9. [Formatação](#formatação)
  10. [Comentários](#comentários)

## Introdução
![Imagem humorística sobre a estimativa de qualidade do código sendo o número de vezes que você xinga lendo um código](http://www.osnews.com/images/comics/wtfm.jpg)

Princípios de Engenharia de Software, do livro de Robert C. Martin [*Código Limpo*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), adaptado para o Javascript. Isso não é um guia de estilo. É um guia para produzir código legível, reutilizável e software Javascript refatorável.

Nem todo princípio aqui precisa ser seguido ao pé da letra, e alguns listados aqui não são universalmente aceitos. São somente guias e nada mais. Mas essas ideias se desenvolveram ao longo de anos de experiência em desenvolvimento pelos autores do livro *Código Limpo*. 

Nossa profissão de engenheiro de software tem pouco mais de 50 anos, e ainda estamos aprendendo muito. Quando arquitetura de software for tão velha quanto arquitetura em si é hoje, talvez teremos regras mais sólidas para seguir. Por agora, deixe essas diretrizes servirem como ponto de apoio para avaliar a qualidade do código que você e a sua equipe produz.

Mais uma coisa: Entendendo essas regras não vai fazer de você um desenvolvedor melhor do dia pra noite, e aplicar essas regras por anos a fio não vai impedir que você cometa erros. Cada pedaço de código começa como um rascunho inicial, como argila fresca que vai ser moldada no seu formato final. Por último nós lapidamos as imperfeições quando revisamos o código com nosso time. Não se culpe por criar códigos iniciais que precisam de melhoria.   

##**Váriaveis**
### Utilize nomes fáceis e com significado

**Ruim:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**Bom:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```
**[⬆ voltar para o topo](#Índice)**

### Utilize o mesmo vocabulário quando variáveis tiverem relação, forem do mesmo tipo

**Ruim:**
```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Bom:**
```javascript
getUser();
```
**[⬆ voltar para o topo](#Índice)**

### Utilize nomes fáceis de se buscar
Nós desenvolvedores iremos ler mais código do que escrever. É importante que o código que escrevemos seja legível e de fácil localização. Usando nomes difíceis para variáveis estaremos prejudicando a leitura de outros desenvolvedores. Ferramentas que podem ajudar nesse processo:

[buddy.js](https://github.com/danielstjules/buddy.js) e
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)

**Ruim:**
```javascript
// O que seria 86400000? Só quem escreveu que sabe
setTimeout(blastOff, 86400000);

```

**Bom:**
```javascript
// Declare esse número em maiúsculo como uma constante (const)
const MILLISECONDS_IN_A_DAY = 86400000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);

```
**[⬆ voltar para o topo](#Índice)**

### Utilize variáveis auto-explicativas
**Ruim:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(address.match(cityZipCodeRegex)[1], address.match(cityZipCodeRegex)[2]);
```

**Bom:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```
**[⬆ voltar para o topo](#Índice)**

### Escreva códigos descritivos
Código explícito e mais fácil de ler que código implícito

**Ruim:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Wait, what is `l` for again?
  dispatch(l);
});
```

**Bom:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```
**[⬆ voltar para o topo](#Índice)**

### Não crie contextos redundantes
Se sua classe ou objeto pai já descreve seu assunto, não repita no nome das variáveis internas

**Ruim:**
```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}
```

**Bom:**
```javascript
const Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red';
}
```
**[⬆ voltar para o topo](#Índice)**

### Utilize parâmetros predefinidos nas funções ao invés de condicionais de checagem
Parâmetros predefinidos são mais limpos do que a criaçao de condicionais para checar se um argumento foi passado ou não por exemplo. Saiba que utilizando parâmetros predefinidos, sua função somente vai oferecer argumentos predefinidos para argumentos `undefined`. Outros valores falsos como `''`, `""`, `false`, `null`, `0`, e `NaN`, não serão substituídos por um valor predefinido.

**Ruim:**
```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.';
  // ...
}

```

**Bom:**
```javascript
function createMicrobrewery(breweryName = 'Hipster Brew Co.') {
  // ...
}

```
**[⬆ voltar para o topo](#Índice)**

##**Funções**
### Parâmetros em uma função (2 ou menos é o ideal)
Limitando a quantidade de parâmetros numa função é muito importante porque isso torna seu código mais fácil de ser testado. Geralmente quanto mais parâmetros existirem mais serão os casos de testes que terão que ser feitos com todas suas possíveis combinações.

Um ou dois parâmetros é o cenário ideal, e três parâmetros devem ser evitados se possível. Qualquer função com mais de três argumentos deve ser repensada. Geralmente, se você tem mais de dois argumentos sua função está fazendo coisa demais. Caso não esteja, é sempre possível inserir um objeto como parâmetro da função que dentro dele terão outras variáveis.  

Já que o Javascript permite a criação de objetos dinamicamente, você pode usar uma objeto em caso de precisar de muitos argumentos.

Para tornar óbvio quais parâmetros uma função espera, você pode usar a sintaxe de [destructuring do ES6](http://www.jstips.co/en/use-destructuring-in-function-parameters/). Essa sintaxe tem algumas vantagens:

1. Quando alguém olha para sua função fica claro quais propriedades estão sendo usadas.

2. A técnica de destructuring também clona os valores primitivos que são passados como argumento para a função. Isso ajuda a prevenir efeitos colaterais que a sua função possa causar. Observação: Quando se utiliza destructuring em objetos e arrays eles não são clonados. 

3. Linters podem acusar erros em propriedades que não são usadas quando se utiliza destructuring, o que não poderia ser possível antes.

**Ruim:**
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Bom:**
```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```
**[⬆ voltar para o topo](#Índice)**


### Funções devem fazer somente uma coisa

Essa regra é a mais importante em engenharia de software. Quando funções fazem mais de uma coisa, ela são mais difíceis de serem reutilizadas, testadas e ficam mais complicadas de entender. Quando você isola uma função para somente uma ação, ela pode ser refatorada facilmente e o seu código será mais limpo. Se você tiver que absorver somente uma regra nesse guia, essa é a regra que você deve levar para toda a sua vida. Isso já vai deixá-lo a frente de muitos desenvolvedores.

**Ruim:**
```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Bom:**
```javascript
function emailClients(clients) {
  clients
    .filter(isClientActive)
    .forEach(email);
}

function isClientActive(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```
**[⬆ voltar para o topo](#Índice)**


### Os nomes das funções devem dizer o que elas fazem

**Ruim:**
```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// Nesse caso abaixo, é difícil dizer pelo nome da função o que é adicionado
addToDate(date, 1);
```

**Bom:**
```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```
**[⬆ voltar para o topo](#Índice)**

### Funções devem ter somente um nível de abstração

Geralmente, quando você tem mais de um nível de abstração, sua função está fazendo muita coisa sozinha. Dividir essa função em funções menores facilidade sua reutilização e é mais fácil de testar. 

**Ruim:**
```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}
```

**Bom:**
```javascript
function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function lexer(tokens) {
  const ast = [];
  tokens.forEach((token) => {
    ast.push( /* ... */ );
  });

  return ast;
}

function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const ast = lexer(tokens);
  ast.forEach((node) => {
    // parse...
  });
}
```
**[⬆ voltar para o topo](#Índice)**

### Remove código duplicado

Faça o seu melhor para evitar código duplicado. Código duplicado é ruim porque significa que existe mais de um lugar onde você precisará atualizar alguma coisa caso alguma lógica mude.

Imagine que você administra um restaurante e você deve controlar seu estoque: Todos seus tomates, cebolas, alho e pimentas, etc. 
Se você tem múltiplas listas que você deve observar, então cada vez que você serve um prato todas essas listas terão que ser atualizadas. Se você tiver somente uma lista, é só ela que você tem que atualizar!

De vez em quando você tem código duplicado porque você tem duas ou três coisas que tem pequenas mudanças entre elas, 
mas que compartilham muito em comum, mas suas diferenças te forçam a ter duas ou três funções separadas que fazem quase a mesma coisa. 
Remover código duplicado nesse caso significa criar uma camada de abstração que possa lidar com essas pequenas diferenças com uma só função/módulo/classe.  


Executar essa abstração de forma correta é essencial, por isso deve-se seguir os princípios SOLID descritos na seção *Classes*. 
Abstrações ruins podem ser pior que código duplicado, então tome cuidado! Dito isso, se você pode fazer uma boa abstração, faça! 
Não se repita, caso contrário você vai se pegar atualizando código em múltiplos lugares toda vez que uma mudança tenha que ser feita.

**Ruim:**
```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Bom:**
```javascript
function showEmployeeList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    let portfolio = employee.getGithubLink();

    if (employee.type === 'manager') {
      portfolio = employee.getMBAProjects();
    }

    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```
**[⬆ voltar para o topo](#Índice)**

### Crie objetos default com Object.assign()

**Ruim:**
```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable === undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Bom:**
```javascript
const menuConfig = {
  title: 'Order',
  // Usuário não incluiu a chave 'body'
  buttonText: 'Send',
  cancellable: true
};

function createMenu(config) {
  config = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // config agora é: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```
**[⬆ voltar para o topo](#Índice)**


### Não utilize flags como parâmetros de funções para sinalizar alguma condicional

Flags automaticamente dizem que essa função faz mais de uma coisa. Funções devem fazer somente uma coisa. 
Divida suas funções caso ela esteja seguindo caminhos diferentes no código baseado em um cooleano como flag.

**Ruim:**
```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Bom:**
```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```
**[⬆ voltar para o topo](#Índice)**

### Evite efeitos colaterais (parte 1)

Uma função produz um efeito colateral caso ela fazer qualquer outra coisa do que pegar uma valor e retornar outro valor, ou valores.
Um efeito colateral pode ser escrever em um arquivo, modificar uma variável global, ou acidentalmente enviar todo seu dinheiro para um estranho.

Em certos momentos você vai precisar ter efeitos colaterais. Como no exemplo anterior, você pode precisar escrever em um arquivo. 
O que é necessário fazer é centralizar onde você vai realizar isso. Não tenha várias funções e classes que escrevem para um determinado arquivo. 
Tenha um único serviço centralizado para fazer isso. Um e somente um.

O ponto central é evitar problemas comuns como compartilhar estados entre objetos sem nenhuma estrutura, 
usando tipos de dados mutáveis que podem ser escritos por qualquer coisa, e não centralizando onde você deseja que os efeitos colaterais devem ocorrer. 
Se você conseguir centralizar esse efeitos colaterais você será mais feliz que a maioria dos programadores.

**Ruim:**
```javascript
// Variável global referenciada pela função abaixo.
// Se tivéssemos uma outra função que usasse esse nome, agora teríamos um array e o código poderia quebrar.
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Bom:**
```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```
**[⬆ voltar para o topo](#Índice)**

### Evite efeitos colaterais (parte 2)

Em Javascript, valores primitivos são passados como valor, e objetos e array são passados como referência.
No caso de objetos e arrays, se a nossa função faz uma mudança em um array num carrinho de compras, por exemplo, adicionado um item para compra, 
então qualquer outra função que utilize esse array do 'carrinho' será afetada como essa adição. Isso pode ser ótimo, mas pode ser ruim também.

Vamos imaginar uma situação ruim:

O usuário clica em "Comprar", botão que chama uma função comprar() que inicia uma requisição e envia para o servidor um array chamado 'carrinho'. 
Caso a conexão a internet do usuário esteja ruim, a função comprar() teve de ficar tentando várias vezes executar a requisição ao servidor.

Agora, e se nesse meio-tempo o usuário acidentalmente clicou no botão "Adicionar ao carrinho" em um item que ele na verdade não quer, 
antes da requisição ao servidor começar?

Se isso acontece e a requisição ao servidor começa, então a função 'comprar' irá enviar o item escolhido acidentalmente, porque ele tem uma referência a um array 'carrinho' que outra função chamada 'adicionarAoCarrinho' modificou ao adicionar o item indesejado.

Uma solução seria que a função 'adicionarAoCarrinho' sempre clonasse o array 'carrinho', editasse ele, e retornasse o clone. 
Isso iria garantir que nenhuma outra função que está guardando a referência do 'carrinho' seja afetada por uma mudança em outro lugar.

Dois problemas com essa solução:

1. Existirão casos onde você de fato irá querer modificar esse objeto e não clonar ele, mas quando você adota essa prática de programação você irá perceber que esses casos são raros. A maioria das coisas podem ser refatoras para evitar efeitos colaterais!

2. Clonar objetos muito grandes pode ser peado em termos de performance. 
Por sorte isso não é um grande problema na prática porque existem [boas bibliotecas](https://facebook.github.io/immutable-js/) 
que permitem que essa abordagem de programação seja rápida e não seja tão intensiva em questão de memória como seria caso se clonasse objetos e arrays grandes manualmente.

**Ruim:**
```javascript
const adicionarAoCarrinho = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Bom:**
```javascript
const adicionarAoCarrinho = (cart, item) => {
  return [...cart, { item, date : Date.now() }];
};
```

**[⬆ voltar para o topo](#Índice)**

### Não escreva funções globais

Poluir o escopo global é uma prática ruim no Javascript, porque você pode causar conflito com alguma outra biblioteca 
e o usuário da sua API ou do seu código pode acabar com um erro em produção. 
 
Um exemplo, e se você quisesse estender o objeto nativo Array para ter um método 'diff' que poderia mostrar a diferença entre duas arrays?
Você poderia escrever sua nova função para o 'Array.prototype', mas isso poderia conflitar com outra biblioteca que faz o mesmo. 

E se outra biblioteca estiver usando o termo 'diff' para achar a diferença entre o primeiro e o último elemento da array? 
É por isso que seria bem melhor somente usar classes ES2015/ES6 e simplesmente estender o 'Array' global. 

**Ruim:**
```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Bom:**
```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```
**[⬆ voltar para o topo](#Índice)**

### Dê preferência para a programação funcional ao invés da imperativa
Javascript não é uma linguagem funcional do jeito que o Haskell é, mas ainda assim tem seu toque funcional.
Linguagens funcionais são mais limpas e mais fáceis de testar. Favoreça esse estilo de programação quando puder.

**Ruim:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Bom:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const INITIAL_VALUE = 0;

const totalOutput = programmerOutput
  .map((programmer) => programmer.linesOfCode)
  .reduce((acc, linesOfCode) => acc + linesOfCode, INITIAL_VALUE);
```
**[⬆ voltar para o topo](#Índice)**


### Encapsule suas condicionais

**Ruim:**
```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**Bom:**
```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```
**[⬆ voltar para o topo](#Índice)**

### Evite condicionais negativas

**Ruim:**
```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Bom:**
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```
**[⬆ voltar para o topo](#Índice)**


### Evite condicionais
Isso parece uma tarefa impossível. Quando se escuta isso pela primeira vez, a maioria das pessoas se pergunta, 
"Como eu vou poder fazer qualquer coisa sem um if ou else?". A resposta é que você pode utilizar polimorfismo, ou seja, criando classes diferentes, derivadas de uma mesma classe pai, com comportamentos diferentes definidos em cada uma dessas classes.
A segunda pergunta é geralmente, "ok, porque eu deveria fazer isso?". A resposta vem de um princípio que foi dito antes. 
Uma função deve fazer somente uma coisa. Quando você tem classes e funções que tem 'if', você está dizendo que ao usuário que sua função faz mais de uma coisa.
Lembre-se, faça somente uma coisa.

**Ruim:**
```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Bom:**
```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```
**[⬆ voltar para o topo](#Índice)**


### Evite checagem de tipo (parte 1)
Javascript não é tipificado, o que significa que sua função pode aceitar qualquer tipo de argumento.
Às vezes essa liberdade pode ser ruim e você pode ficar tentado a escrever uma checagem de tipo ns suas funções.
Existem várias maneiras diferentes para evitar que você tenha que fazer essa checagem.
A primeira coisa a considerar são API consistentes.

**Ruim:**
```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
```

**Bom:**
```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```
**[⬆ voltar para o topo](#Índice)**


### Evite checagem de tipo (parte 2)
Se você está trabalhando com valores primitivos como strings e números você não pode usar polimorfismo 
mas você sentirá a necessidade de checar o tipo dos valores, você deve considerar usar Typescript. 
É uma excelente alternativa ao Javascript normal, porque provê tipagem em cima da sintaxe normal do Javascript. 
O problema em fazer a checagem de tipos manualmente como Javascript normal é que fazer de uma maneira correta obriga você a escrever muito código extra
 fazer você perder a legibilidade da lógica que realmente interessa. 
 Mantenha seu Javascript limpo, escreva bons testes, e tenha boas revisões de código. Caso contrário, faça tudo isso com Typescript (que como foi dito, é uma boa alternativa).

**Ruim:**
```javascript
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
```

**Bom:**
```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```
**[⬆ voltar para o topo](#Índice)**

### Não otimize demais
Browsers modernos fazem várias otimizações por debaixo dos panos em tempo de execução. 
Muitas vezes, se você está otimizando, você está apenas perdendo seu tempo. [Existem boas fontes](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers) para checar
onde otimização ainda pode ser feita. Tenha esse pontos como seu foco, até que sejam sanados, caso sejam.

**Ruim:**
```javascript

// Em browsers antigos, cada iteração com `list.length` não cacheado será custoso
// porque `list.length` será recomputado. Em browsers modernos, isso já é otimizado.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Bom:**
```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```
**[⬆ voltar para o topo](#Índice)**

### Remova código morto
Código morto é tão ruim quanto código duplicado. Não existe razão para tê-lo em sua base de código. 
Se não está sendo chamado, remova ele! Ainda poderá ser resgatado no seu versionamento caso você ainda precise dele.

**Ruim:**
```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');

```

**Bom:**
```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```
**[⬆ voltar para o topo](#Índice)**

##**Objetos e estrutura dos dados**
### Utilize getters e setters

Utilizando getters e setters para acessar dados em objetos pode ser melhor do que simplesmente olhar pela propriedade em um objeto.
"Porque?", você pode perguntar. 
Bem, aqui está uma lista não ordenada de motivos:

* Quando você quer fazer mais do que simplesmente pegar uma propriedade do objeto, você não tem que olhar e mudar todo mundo que acessar aquele objeto
* Adicionar uma validação fica fácil ao dar um 'set'
* Encapsula a representação interna do objeto
* Fica fácil adicionar log e tratamento de erros ao dar get and set
* Você pode fazer lazy loading com as propriedades do seu objeto, como é o caso de pegar essa propriedade no servidor

**Ruim:**
```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0,
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Bom:**
```javascript
function makeBankAccount() {
  // Propriedade abaixo é privada
  let balance = 0;

  // Um 'getter', tornado público através to objeto retornado abaixo
  function getBalance() {
    return balance;
  }

  // Um 'setter', tornado público através to objeto retornado abaixo
  function setBalance(amount) {
    // é possível validar antes de atualizar a propriedade 'balance'
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance,
  };
}

const account = makeBankAccount();
account.setBalance(100);
```
**[⬆ voltar para o topo](#Índice)**

### Faça seus objetos terem membros privados
Isso pode ser feito através de closures (para ES5 e abaixo).

**Ruim:**
```javascript

const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Bom:**
```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    },
  };
}

const employee = makeEmployee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```
**[⬆ voltar para o topo](#Índice)**


##**Classes**

### Princípio de uma só responsabilidade 

Como dito no livro Código Limpo, "Nunca deve existir mais de uma razão para uma classe mudar". 
É tentador embutir muita funcionalidade em uma classe só, como embutir tudo em uma mala quando só se pode levar uma mala no avião. 
O problema com isso é que a sua classe não será coesa conceitualmente e isso vai te dar muitas razões para mudar a classe toda hora. 
Minimizar as vezes que você precisa mudar uma classe é importante. 
É importante porque se muita funcionalidade está em uma única classe, e você modifica uma parte dela, pode ser difícil de entender como isso vai afetar outros módulos que dependem dela.   


**Ruim:**
```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Bom:**
```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}


class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```
**[⬆ voltar para o topo](#Índice)**

### Princípio Aberto/Fechado

Como foi dito por Bertrand Meyer, "entidades de software (classes, módulos, funções, etc.) devem estar abertas para extensões, mas fechadas para modificação."
O que isso significa? Esse princípio basicamente diz que você deve permitir que usuários adicionem novas funcionalidades sem poder alterar código existente.

**Ruim:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === 'ajaxAdapter') {
      return makeAjaxCall(url).then((response) => {
        // transform response and return
      });
    } else if (this.adapter.name === 'httpNodeAdapter') {
      return makeHttpCall(url).then((response) => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Bom:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then((response) => {
      // transform response and return
    });
  }
}
```
**[⬆ voltar para o topo](#Índice)**


Princípio da substituição de Liskov

Este é um termo que assusta mas o conceito é bem simples. 
É formalmente definido como "Se S é um subtipo de T, então objetos do tipo T podem ser substituídos com objetos do tipo S
(objetos do tipo S podem substituir objetos do tipo T) sem alterar qualquer propriedade do programa (exatidão, tarefa executada, etc.)" 
Essa definição assusta ainda mais. 

A melhor explicação para esse princípio é, se você tem uma classe pai e uma classe filho, 
então a classe pai ou a classe filho podem ser usadas sem obter resultados incorretos. Isso ainda pode parecer confuso, então vamos olhar o clássico exemplo do Retângulo-Quadrado.
 
Matematicamente, um quadrado é um retângulo, mas se o seu modelo está usando a relação "é um" via hereditariedade, você pode ter problemas.  

**Ruim:**
```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach((rectangle) => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // RUIM: Vai retornar 25 para quadrado, deveria ser 20
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Bom:**
```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach((shape) => {
      const area = shape.getArea();
      shape.render(area);
    });
  }

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```
**[⬆ voltar para o topo](#Índice)**

### Princípio de segregação de interface
Javascript não tem interfaces, então esse princípio não se aplica tão estritamente quanto outras linguagens que tem interface implementada.
Entretanto, é importante e relevante até mesmo por causa da falta de tipagem do Javascript.

Esse princípio diz que "Clientes não devem ser forçados a depender de interfaces que eles não usam." 
Interfaces são contratos implícitos em Javascript por causa do Duck Typing (Duck typing é um termo usado para se referir a linguagens dinâmicas que não tem tipagem forte)

Um bom exemplo para olhar isso e que exemplifica esse princípio é para classes que requerem objetos com configuração grande.

Não obbrigar a seus clientes a configurar muitas opçòes é benéfico, porque na maioria do tempo eles não vão precisar de todas as configurações.
Tornando elas opcionais ajuda a prevenir uma interface muito gorda.

**Ruim:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  animationModule() {} // Na maioria do tempo, nós não vamos precisar animar enquando se executa essa função
  // ...
});

```

**Bom:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule() {}
  }
});
```
**[⬆ voltar para o topo](#Índice)**


### Princípio da inversão de dependências

Esse princípio diz 2 coisas essenciais:

1. Módulos de alto nível não devem depender de módulos baixo-nível. Os dois devem depender de abstrações.

2. Abstrações não devem depender de detalhes. Detalhes devem depender de abstrações


Isso pode ser difícil à primeira vista, mas se você já trabalhou com Angular.js, você já viu uma implementação desse princípio na forma de 
Injeção de dependências (DI). Enquanto eles não são conceitos idênticos, o princípio da inversão de dependências previne módulos alto-nível 
de saberem os detalhes dos módulos baixo-nível e de mexerem neles. Isso pode ser alcançado através da injeção de dependências. 
Um grande benefício disso é que isso reduz a interdepência entre módulos. Interdepência é um padrão muito ruim porque torna seu código mais difícil de ser refatorado.

Como dito anteriormente, Javascript não tem interfaces, então as abstrações que das quais se depende são contratos implícitos. 
No caso, os métodos e propriedades que um objeto/classe expõe para outro objeto/classe. 
No exemplo abaixo, o contrato implícito é que qualquer módulo Request para um `InventoryTracker` vai ter um método `requestItems`.  

**Ruim:**
```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // RUIM: Nós criamos uma dependência em uma implementação de request específica
    // Nós devemos ter somente requestItems dependendo de um método request: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(['apples', 'bananas']);
inventoryTracker.requestItems();
```

**Bom:**
```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS'];
  }

  requestItem(item) {
    // ...
  }
}

// Ao construir nossa dependências externamente e injetando elas, nós podemos facilmente
// substituir nosso módulo de request por um que utiliza websocket por exemplo.
const inventoryTracker = new InventoryTracker(['apples', 'bananas'], new InventoryRequesterV2());
inventoryTracker.requestItems();
```
**[⬆ voltar para o topo](#Índice)**


### Prefira classes ES2015/ES6 ao invés de simples funções ES5 
É bem difícil de ter herança de classes, construtores, e definições de métodos bem legíveis para 'classes' em ES5.
Caso você necessite herança (e veja que você pode não precisar), então prefira classes. Entretanto, prefira funções pequenas ao invés de classes até você 
sentir a necessidade de objetos maiores e mais complexos.

**Ruim:**
```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error('Instantiate Animal with `new`');
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error('Instantiate Mammal with `new`');
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error('Instantiate Human with `new`');
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Bom:**
```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() { /* ... */ }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() { /* ... */ }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() { /* ... */ }
}
```
**[⬆ voltar para o topo](#Índice)**


### Utilize métodos em cadeia, em sequência

Esse padrão é muito útil em Javascript e você vai vê-lo em tantas bibliotecas como jQuery e Lodash.
Ele permite que seu código seja mais expressivo, e menos verboso. Por essa razão, eu digo, use métodos em cadeia e veja como seu código vai ser limpo.
Nas suas funções de classe, simplesmente retorne `this` no final de toda função, e você poderá criar uma cadeia de métodos em sequência.

**Ruim:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car();
car.setColor('pink');
car.setMake('Ford');
car.setModel('F-150');
car.save();
```

**Bom:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car()
  .setColor('pink')
  .setMake('Ford')
  .setModel('F-150')
  .save();
```
**[⬆ voltar para o topo](#Índice)**

### Prefira composição ao invés de herança

Como foi dito no livro [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) pela "Gangue dos 4",
você deve preferir composições ao invés de herança sempre que puder. Existem bons motivos para usar herança e muitos para usar composição.

O ponto principal é que se seu gosto é naturalmente utilizar herança, tente pensar se composição irá resolver seu problema de forma melhor. 
Em alguns casos ela pode.

Você pode se questionar, "Quando devo usar herança?". Depende do problema que você tem em mãos, mas a lista abaixo mostra casos onde implementar herança é vantajoso:

1. Sua herança representa a relação 'é-um' e não 'tem-um' (Humano->Animal vs. Usuario->detalhesUsuario)
2. Você pode reutilizar código das classes bases (Humanos podem se mover como todos os animais)
3. Você quer fazer mudanças globais para classes derivadas mudando a classes base. (Mudar o gasto calórico de todos os animais quando se movem).

**Ruim:**
```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Ruim porque Employees "tem" o dado 'tax'. EmployeeTaxData não é um tipo de Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Bom:**
```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```
**[⬆ voltar para o topo](#Índice)**

##**Testes**
Testar é mais importante que entregar. Se você não tem testes ou uma quantidade inadequada, então toda vez que você faz uma entrega de código você não vai ter certeza se quebrou algo.
Decidir o quanto se deve testar é medido pelo time, mas ter 100% de cobertura (todas funções e todos os caminhos das condicionais) é como você e seu time irão atingir confiança e paz de espírito.
Isso significa que além de ter um bom framework de testes, você também precisa usar uma [boa ferramenta de cobertura](http://gotwarlost.github.io/istanbul/)

Não tem desculpa para não escrever testes. Tem um [monte de bons frameworks de teste](http://jstherightway.org/#testing-tools),
então ache um que seu time prefere. Quando você achar um que funcione para seu time, então prefira sempre escrever testes para cada feature/módulo que surja. 
Se seu método de preferência é TDD (Test Driven Development), ótimo, mas o ponto principal é ter certeza que se está atingindo as metas de cobertura definidas antes de se lançar uma feature nova, ou refatorando uma que já existe.

### Conceito único por teste

**Ruim:**
```javascript
const assert = require('assert');

describe('MakeMomentJSGreatAgain', () => {
  it('handles date boundaries', () => {
    let date;

    date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);

    date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);

    date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

**Bom:**
```javascript
const assert = require('assert');

describe('MakeMomentJSGreatAgain', () => {
  it('handles 30-day months', () => {
    const date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);
  });

  it('handles leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);
  });

  it('handles non-leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```
**[⬆ voltar para o topo](#Índice)**

##**Concorrência e assincronia**
### Utilize Promises, evite callbacks

Callbacks não são limpos, e eles causam um aninhamento excessivo. 
Com ES2015/ES6, Promises são objetos globais nativos. Use-os!  

**Ruim:**
```javascript
require('request').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', (requestErr, response) => {
  if (requestErr) {
    console.error(requestErr);
  } else {
    require('fs').writeFile('article.html', response.body, (writeErr) => {
      if (writeErr) {
        console.error(writeErr);
      } else {
        console.log('File written');
      }
    });
  }
});

```

**Bom:**
```javascript
require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return require('fs-promise').writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```
**[⬆ voltar para o topo](#Índice)**

### Async/Await são ainda mais limpos que Promises
Promises são uma alternativa bem limpa se comparadas a callbacks, mas ES2017/ES8 traz async e await
que oferecem uma solução ainda mais limpa. Tudo que você precisa é uma função prefixada com o termo 'async', e então você
pode escrever sua lógica de maneira ordenada e imperativa sem um encadeamento de `then`. 
Use isso caso você possa usar as vantangens das feature do ES2017/ES8, compilando o código babeljs por exemplo.

**Ruim:**
```javascript
require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return require('fs-promise').writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```

**Bom:**
```javascript
async function getCleanCodeArticle() {
  try {
    const response = await require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin');
    await require('fs-promise').writeFile('article.html', response);
    console.log('File written');
  } catch(err) {
    console.error(err);
  }
}
```
**[⬆ voltar para o topo](#Índice)**

##**Tratamento de erros**

Exibir erros com throw é uma coisa boa! Isso sinaliza que sua aplicação tem um erro e faz você saber disso parando a execução do script, matando o processo (em Node),
e notificando no seu console.

### Não ignore erros que caem no block de 'catch'

Não fazendo nada com o erro que cai no catch não te traz a habilidade de consertar esse error ou reagir mediante um erro.
Logar esse erro no console (`console.log`) não é muito melhor, porque muitas vezes esses logs somem no mar de coisas printadas no console. 
Se você vai encapsular um código num bloco try/catch isso significa que um erro pode vir a ocorrer e pra isso você deve ter um plano, ou criar um caminho no código para quando isso ocorrer.

**Ruim:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Bom:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Não ignore Promises rejeitadas
Pela mesma razão que você não deve ignorar erros de blocos `try/catch`.

**Ruim:**
```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  console.log(error);
});
```

**Bom:**
```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
});
```

**[⬆ voltar para o topo](#Índice)**

##**Formatação**

Formatação é subjetivo. Assim como muitas regras expostas aqui, não existe uma regra rápida e sólida para ser seguida.
O ponto principal é NÃO ARGUMENTE em cima de regras de formatação.

Existem muitas [ferramentas que automatizam esse processo](http://standardjs.com/rules.html) de checagem de formatação.
Utilize uma! É uma perda de tempo e dinheiro para engenheiros em argumentar em cima de formatação.

Para coisas que não se enquandram embaixo do padrão de formatação automática (identação, tabs vs. espaços, aspas duplas ou simples) procure orientação de outros desenvolvedores.

### Utilize maiúsculas de forma consistente

Javascript não possui tipagem, então capitalização irá dizer muito sobre o significado das suas variáveis, funções, etc. 
Essas regras são subjetivas, então seu time pode escolher qualquer uma que desejam. 
O ponto central é, não interessa qual convenção escolher, apenas seja consistente.

**Ruim:**
```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const Artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Bom:**
```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```
**[⬆ voltar para o topo](#Índice)**

### Funções que chamam ou que são chamadas estar próximas

Se uma função chama uma outra, mantenha essas funções perto uma das outras no código. 
Idealmente mantenha a função que chama em cima da função que é chamada. Nós tendemos a ler código de cima-pra-baixo, como um jornal.
Por causa disso, faça seu código ser lido desse jeito.

**Ruim:**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(user);
review.perfReview();
```

**Bom:**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ voltar para o topo](#Índice)**


##**Comentários**

Somente comente coisas que necessitam explicação de regras de negócio complexas.
Comentários são desculpas, não algo obrigatório. Bom código na maioria das vezes se auto-documenta.

**Ruim:**
```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = ((hash << 5) - hash) + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Bom:**
```javascript

function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}

```
**[⬆ voltar para o topo](#Índice)**

### Não deixe código comentado na sua base de código
Controle de versão existe para uma razão. Deixe código antigo na história do controle de versão.

**Ruim:**
```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Bom:**
```javascript
doStuff();
```
**[⬆ voltar para o topo](#Índice)**

### Não utilize comentários que parecem changelogs
Lembre-se, use controle de versão! Não existe a necessidade de código morto, código comentado, ou comentários do que foi mudado. 
Utilize `git log` para visualizar a história do código.

**Ruim:**
```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Bom:**
```javascript
function combine(a, b) {
  return a + b;
}
```
**[⬆ voltar para o topo](#Índice)**

### Evite blocos de comentários que indicam seções no seu código

Esses blocos geralmente adicionam ruído no seu código. 
Deixe os nomes das funções e das variáveis estuturarem o código, assim como identação e formatação corretas.

**Ruim:**
```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Bom:**
```javascript
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

const actions = function() {
  // ...
};
```

**[⬆ voltar para o topo](#Índice)**
