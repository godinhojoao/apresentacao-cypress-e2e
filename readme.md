# Cypress - Comandos básicos e boas práticas

## Por que utilizar o cypress para realizar testes e2e e não outra ferramenta ?

- Baixa curva de aprendizado, fácil configuração e muito utilizado para testes e2e. Embora possa ser utilizado para realizar outros tipos de testes também.

### Pensado para ser executado durante o desenvolvimento, por conta disso contém diversas funcionalidades que são seu diferencial:

- watch mode - Após salvar os novos testes já re-executa os mesmos.
- time-travel - É como se pudéssemos voltar no tempo e ver o que houve de errado
- Screenshots - Podemos configurar para tirar screenshots caso o teste falhar
- Vídeos - Podemos configurar para salvar vídeos do fluxo
- Esperas automáticas - Por padrão o cypress já espera os elementos estarem visíveis

### Outros pontos positivos:

- Controle de tráfego de rede: Podemos interceptar requisições utilizando cy.intercept()
- Ótima documentação: O que facilita a resolução de dúvidas específicas referentes a comandos específicos.
- Comunidade: Uma das coisas mais importantes em qualquer tecnologia, para melhorar o projeto em si e até mesmo para ajudar outros desenvolvedores a corrigirem erros utilizando a tecnologia.

---

## Permanent trade-offs X Temporary trade-offs

### Permanentes: ( não irão ser “corrigidos” )

- Não é para um propósito geral, é focado para testes e2e.
- Não têm suporte para múltiplas tabs.
- Não acessa 2 urls de origens diferentes.

### Temporários ( serão “resolvidos” )

- cy.tab() : comando para rodar o “comando tab” do teclado, bom para testar acessibilidade
- Não têm nenhum suporte a eventos nativos mobile
- Não têm nenhum comando específico para upload de arquivos.
- Suporte a iframe é muito baixo, mas pelo menos existe.

---

## Como começar a utilizar cypress ?

- `npm install -D cypress`
- `npx cypress open` ( criar a estrutura padrão do cypress a 1a vez )
- Colocamos uma cypress.json na raiz que tem configurações como:
  - baseUrl: a url que será o início de todo cy.visit
  - caminho para os plugins, salvar screenshots, vídeos, etc..
  - entre outras coisas como setar as variáveis de ambiente ( env )
- Pronto, basta criar seus testes e rodar utilizando:
- cypress run ( rodar headless, sem visualizar os testes )
- cypress open ( abrir os testes e clicar no que deseja rodar visualizando )

---

## Alguns comandos básicos do cypress:

### Sintaxe básica no “mundo dos testes”:

- describe, it, beforeEach: Mesma coisa que qualquer outra ferramenta de testes.

### Sintaxe do próprio cypress:

- cy.visit(url) Para navegar para uma url
- cy.get(seletor) Pega todos elementos com esse seletor
- cy.get(seletor).first() para pegar apenas o primeiro elemento
- cy.get(seletor).click() para clicarmos em um elemento
- cy.get(seletor).type() para digitarmos em um input
- cy.get(seletor).trigger('mouseover') para simular um “hover”
- cy.wait(time), cy.wait(aliases): Espera um tempo específico ou espera um alias “existir”

- cy.get(seletor).should():
  - .should é utilizado para testar diversos fatores referentes ao elemento selecionado, a partir daqui vou ignorar o cy.get(), no entanto, ele sempre deve estar presente no ínicio.
  - .should(‘have.text’, ‘texto esperado’) para conferir o texto de um elemento
  - .should(‘have.length’, 2) para conferir quanto itens vieram em um get
  - .should(‘be.visible’) para verificar se um elemento está visível
  - .should(‘have.css’, ‘background-color’, 'rgb(255, 0, 0)');

---

## Forma para testar múltiplos elementos de forma mais “detalhada”

- Em algum momento podemos precisar testar o texto de 5 elementos agrupados, cinco elementos <li>
- Para isso podemos utilizar cy.get(‘li’).should(‘have.text’) e o texto irá vir todo bagunçado e agrupado, no entanto, temos uma forma mais adequada de testar isso:
- cy.get(‘li’).each((item, index, list) => {}) dentro dessa função podemos utilizar o expect para testar cada elemento com o valor que desejarmos. Podemos utilizar da mesma sintaxe do mocha nesse caso.

---

## Como testar os estilos de um elemento com cypress:

- Embora o cypress tenha o cy.get().should(‘have.css’) muitas vezes isso acaba sendo uma forma muito trabalhosa de testar os estilos de um elemento um por um, por conta disso podemos utilizar:
- cy.get().first().then((element) => {} ) dentro dessa função temos acesso ao elemento e podemos pegar todos estilos dele e verificar se está como desejamos em uma determinada situação.
- Isso pode ser muito útil para testar se os nossos estilos :focus estão funcionando como esperado e também :hover, etc… Ou até mesmo algum estilo condicional utilizando classes por exemplo.

---

## Como interceptar requisições http com cypress:

- Temos várias formas de validar qual requisição queremos interceptar essas são apenas duas formas:
  - cy.intercept('/users\*', { hostname: 'localhost' }, (req) => {})
  - cy.intercept('POST', '/users\*', {
    statusCode: 201,
    body: {
    name: 'Peter Pan'
    }
    })

---

## Boas práticas

### Coisas gerais que devemos seguir no cypress

- Sempre que possível realizar o mínimo de navegação possível, para tornar os testes mais ágeis.
  Definir a baseURL no cypress.json
- Utilizar de múltiplas asserções: testar vários "detalhes", por ex: have.length, have.text, etc...
- Escrever cenários com boa legibilidade, devemos especificar alguns pontos ao descrever um teste:

  - O que está sendo testado ? tabela, dialog, página, login, etc..
  - Em qual circunstância ? Quando estiver autenticado, quando estiver sem conexão…
  - Qual o resultado esperado ? Deve entrar no menu navegável.

- Describe: para colocar o nome da aplicação, ou página, por exemplo:
  - describe('Painel do gestor')
- Context: para colocar o nome da funcionalidade em específico a ser testada:
  - context('Login' ) — funcionalidade
- It: para colocar a descrição do texto seguindo o padrão: ( circunstância + resultado esperado )
  - it('Ao utilizar usuário e senha corretos deve realizar o login e ser redirecionado para tela de atividades')
- Seguir a convenção AAA no fluxo de teste:
  - Arrange - Preparação ( realizar o login )
  - Act - Ação ( acessar uma tela que necessita de autenticação para ser visualizada )
  - Assert - Validação do resultado ( verificar se realmente conseguimos acessar a rota )

---

## Como evitar falsos negativos em testes e2e ?

- Como um falso negativo pode ocorrer ao testar o front-end de uma tela ?
  - Caso um serviço externo ( API ) esteja com problemas, fora do ar, etc…
- Nesse caso o teste iria falhar, no entanto, o front-end da aplicação estaria funcionando da forma correta, o erro na verdade é no serviço externo.
- Para evitar isso podemos utilizar o cy.intercept() e interceptar requisições externas, dessa forma conseguimos ter confiabilidade no front-end e não levamos em consideração os serviços externos.

---

## Não devemos utilizar variáveis no cypress

- O cypress utiliza de uma forma para armazenar dados “própria”, que chamamos de aliases, basicamente não utilizamos o simples var, let, const do js para guardar algum dado.
- Para criar um alias podemos utilizar:
  - cy.get().first().as(myElement’)
- Para pegar um alias podemos utilizar:
  - cy.get(‘@myElement’) → mais utilizado
  - this.myElement

---

## De onde essas informações foram retiradas e onde buscar mais sobre o cypress:

- Pode parecer um clichê, mas já vi algumas documentações que achei confusas, complexas demais. E outras que só tratavam de detalhes técnicos esquecendo de levantar os pontos positivos e negativos de utilizar essa ferramenta e não outra.
- E a documentação do cypress é uma documentação muito completa, tanto em pontos técnicos, mas também em levantar trade-offs, etc…
- Fonte da apresentação e onde buscar mais informações:

  - https://docs.cypress.io/guides/overview/why-cypress
  - https://medium.com/@pablodarde/o-padr%C3%A3o-triple-a-arrange-act-assert-741e2a94cf88

- Link dos slides da apresentação:
  - https://docs.google.com/presentation/d/e/2PACX-1vRj5CBQyXkbnbl2hdK984DVHvnSzNDHHhN_GkBUGpMaT-i0LVvVeA1szQRF7aQvqAtgv4BLiLrNLSxv/pub?start=true&loop=false&delayms=60000
