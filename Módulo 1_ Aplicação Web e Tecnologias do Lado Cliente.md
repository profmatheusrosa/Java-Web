# Módulo 1: Aplicação Web e Tecnologias do Lado Cliente

Este módulo aborda os conceitos fundamentais de aplicações web e as tecnologias essenciais do lado do cliente.

## Conteúdo

1. Aplicação Web
    1.1 Arquitetura e Funcionamento
    1.2 Cliente Web
    1.3 Servidor Web
    1.4 Protocolo HTTP
2. Tecnologias do Lado Cliente
    2.1 Marcação XHTML
    2.2 Estilos CSS
    2.3 Scripts JavaScript




### 1.1 Arquitetura e Funcionamento

Uma aplicação web é um sistema de software que funciona em um servidor web e é acessível através de um navegador web. A interação básica em uma aplicação web ocorre entre um **cliente** (geralmente um navegador web) e um **servidor** (onde a aplicação web está hospedada). Quando um usuário digita um endereço web (URL) no navegador, o navegador envia uma **requisição** para o servidor. O servidor processa essa requisição e envia uma **resposta** de volta para o navegador, que então exibe o conteúdo ao usuário.

Essa interação cliente-servidor é a base de como a web funciona. O cliente solicita recursos (como páginas HTML, imagens, vídeos) e o servidor os fornece. Essa comunicação é padronizada por protocolos, sendo o mais comum o HTTP (Hypertext Transfer Protocol).




### 1.2 Cliente Web

O **Cliente Web** é o programa que o usuário utiliza para acessar e interagir com as aplicações web. O exemplo mais comum de cliente web é o **navegador web** (como Google Chrome, Mozilla Firefox, Microsoft Edge, Safari). Os navegadores são responsáveis por:

*   **Enviar requisições HTTP**: Quando você digita uma URL ou clica em um link, o navegador traduz essa ação em uma requisição HTTP e a envia para o servidor.
*   **Receber respostas HTTP**: O navegador recebe a resposta do servidor, que geralmente contém código HTML, CSS e JavaScript.
*   **Renderizar o conteúdo**: O navegador interpreta o código HTML para estruturar a página, o CSS para estilizá-la e o JavaScript para adicionar interatividade, exibindo a página web de forma visualmente agradável e funcional para o usuário.
*   **Gerenciar sessões**: Navegadores também lidam com cookies e outras tecnologias para gerenciar o estado da sessão do usuário.

Em resumo, o cliente web é a interface através da qual o usuário final experimenta a aplicação web.




### 1.3 Servidor Web

O **Servidor Web** é um programa de computador que armazena os arquivos de um site (como documentos HTML, imagens, folhas de estilo, scripts JavaScript) e os entrega aos clientes web (navegadores) quando solicitados. As principais funções de um servidor web incluem:

*   **Armazenamento de Conteúdo**: Guarda todos os arquivos que compõem uma aplicação web.
*   **Processamento de Requisições**: Recebe as requisições HTTP dos clientes e as processa.
*   **Envio de Respostas**: Envia as respostas HTTP de volta para os clientes, contendo o conteúdo solicitado.
*   **Execução de Aplicações**: Muitos servidores web também são capazes de executar aplicações do lado do servidor (como Servlets e JSPs em Java, PHP, Python, Node.js) para gerar conteúdo dinâmico antes de enviá-lo ao cliente.

Exemplos populares de servidores web incluem Apache HTTP Server, Nginx, Microsoft IIS e, no contexto Java, servidores de aplicação como **Apache Tomcat** e **GlassFish**. O GlassFish, que utilizaremos neste curso, é um servidor de aplicação Java EE (Enterprise Edition) que fornece um ambiente robusto para a execução de aplicações web Java.

A **hospedagem** de um servidor web refere-se ao local físico onde o servidor está localizado e conectado à internet, tornando a aplicação acessível globalmente. Isso pode ser feito em servidores próprios (on-premise) ou, mais comumente, através de serviços de hospedagem na nuvem (como AWS, Google Cloud, Azure).




### 1.4 Protocolo HTTP

O **Protocolo de Transferência de Hipertexto (HTTP)** é o protocolo fundamental para a comunicação de dados na World Wide Web. Ele define como os clientes (navegadores) e os servidores se comunicam, trocando mensagens que contêm requisições e respostas. O HTTP é um protocolo **sem estado**, o que significa que cada requisição é independente da anterior, embora mecanismos como cookies e sessões sejam usados para manter o estado em aplicações web.

#### Métodos HTTP

Os métodos HTTP (também conhecidos como verbos HTTP) indicam a ação desejada a ser realizada no recurso identificado pela URL. Os mais comuns são:

*   **GET**: Solicita uma representação do recurso especificado. Deve ser usado apenas para recuperar dados e não deve ter efeitos colaterais no servidor (ou seja, não deve alterar o estado do servidor).
*   **POST**: Envia dados para o servidor para criar um novo recurso ou enviar dados para processamento. Geralmente usado para enviar dados de formulários.
*   **PUT**: Envia dados para o servidor para atualizar um recurso existente ou criar um novo se ele não existir.
*   **DELETE**: Solicita a remoção do recurso especificado.

#### Cabeçalhos HTTP

Os cabeçalhos HTTP são pares nome-valor que contêm informações adicionais sobre a requisição ou a resposta. Eles fornecem metadados que auxiliam na comunicação entre cliente e servidor. Exemplos:

*   **Requisição**: `User-Agent` (identifica o navegador), `Accept` (tipos de mídia aceitos), `Content-Type` (tipo de mídia do corpo da requisição).
*   **Resposta**: `Content-Type` (tipo de mídia do corpo da resposta), `Content-Length` (tamanho do corpo), `Set-Cookie` (define um cookie no cliente).

#### Requisição e Resposta HTTP

Uma **requisição HTTP** é composta por:

1.  **Linha de Requisição**: Contém o método HTTP (GET, POST, etc.), a URL do recurso e a versão do protocolo HTTP (ex: `GET /index.html HTTP/1.1`).
2.  **Cabeçalhos de Requisição**: Informações adicionais sobre a requisição e o cliente.
3.  **Corpo da Requisição (opcional)**: Dados enviados ao servidor, comum em requisições POST.

Uma **resposta HTTP** é composta por:

1.  **Linha de Status**: Contém a versão do protocolo HTTP, um código de status numérico e uma frase de status (ex: `HTTP/1.1 200 OK`).
2.  **Cabeçalhos de Resposta**: Informações adicionais sobre a resposta e o servidor.
3.  **Corpo da Resposta (opcional)**: O conteúdo do recurso solicitado (ex: código HTML de uma página web).

**Exemplo de Requisição GET:**

```
GET /pagina.html HTTP/1.1
Host: www.exemplo.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
```

**Exemplo de Resposta 200 OK:**

```
HTTP/1.1 200 OK
Date: Tue, 10 Sep 2025 10:00:00 GMT
Server: Apache/2.4.41 (Unix)
Content-Type: text/html; charset=UTF-8
Content-Length: 1234

<!DOCTYPE html>
<html>
<head>
    <title>Minha Página</title>
</head>
<body>
    <h1>Bem-vindo!</h1>
</body>
</html>
```

Compreender o HTTP é crucial para desenvolver aplicações web eficazes, pois ele dita as regras de comunicação entre cliente e servidor.




## 2. Tecnologias do Lado Cliente

As tecnologias do lado do cliente são executadas diretamente no navegador do usuário e são responsáveis pela apresentação e interatividade da interface da aplicação web.

### 2.1 Marcação XHTML

**XHTML (Extensible Hypertext Markup Language)** é uma linguagem de marcação que é uma reformulação do HTML 4.01 como uma aplicação XML. Isso significa que o XHTML segue as regras mais rigorosas do XML, o que o torna mais bem-formado e fácil de ser processado por máquinas. Embora o HTML5 seja a versão mais recente e amplamente utilizada do HTML, o XHTML ainda é importante para entender os fundamentos de marcação web e as boas práticas de escrita de código estruturado.

#### Estrutura Básica de um Documento XHTML

Um documento XHTML bem-formado segue uma estrutura específica:

```xml
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" lang="pt-br" xml:lang="pt-br">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Título da Minha Página</title>
    <!-- Links para folhas de estilo CSS -->
    <link rel="stylesheet" type="text/css" href="estilos.css" />
    <!-- Scripts JavaScript -->
    <script type="text/javascript" src="script.js"></script>
</head>
<body>
    <!-- Conteúdo da página -->
    <h1>Bem-vindo ao Curso!</h1>
    <p>Esta é uma página de exemplo em XHTML.</p>
</body>
</html>
```

#### Sintaxe e Boas Práticas do XHTML

Para que um documento XHTML seja considerado bem-formado, ele deve aderir a algumas regras rigorosas:

*   **Todos os elementos devem ser fechados**: Cada tag de abertura deve ter uma tag de fechamento correspondente (ex: `<p>Conteúdo</p>`). Para elementos vazios (que não contêm conteúdo), como `<img>` ou `<br>`, a tag deve ser auto-fechada (ex: `<img src="imagem.jpg" alt="Descrição" />` ou `<br />`).
*   **Tags e atributos devem ser em minúsculas**: Diferente do HTML, o XHTML é *case-sensitive*. Assim, `<p>` é válido, mas `<P>` não é.
*   **Todos os valores de atributos devem estar entre aspas**: Mesmo para valores numéricos ou sem espaços, as aspas são obrigatórias (ex: `<img width="100" />`).
*   **Elementos devem ser aninhados corretamente**: A ordem de fechamento das tags deve ser inversa à ordem de abertura (ex: `<em><strong>Texto</strong></em>` é correto, mas `<em><strong>Texto</em></strong>` não é).
*   **O atributo `id` deve ser único**: Cada `id` em um documento deve ser usado apenas uma vez.

Seguir essas boas práticas não apenas garante a validade do XHTML, mas também promove um código mais limpo, legível e fácil de manter, o que é fundamental para o desenvolvimento web profissional.




### 2.2 Estilos CSS

**CSS (Cascading Style Sheets)** é uma linguagem de folha de estilo usada para descrever a apresentação de um documento escrito em uma linguagem de marcação, como HTML ou XHTML. O CSS permite separar a apresentação do conteúdo, o que oferece maior flexibilidade e controle sobre o layout, cores, fontes e outros aspectos visuais de uma página web. Com CSS, é possível criar designs responsivos que se adaptam a diferentes tamanhos de tela e dispositivos.

#### Como o CSS é Aplicado

Existem três maneiras principais de aplicar CSS a um documento HTML/XHTML:

1.  **Estilos Inline**: Aplicados diretamente a um elemento HTML usando o atributo `style`. Não é recomendado para grandes projetos devido à dificuldade de manutenção.
    ```html
    <p style="color: blue; font-size: 16px;">Este é um parágrafo azul.</p>
    ```
2.  **Estilos Internos (Embedded)**: Definidos dentro da tag `<style>` na seção `<head>` do documento. Afetam apenas a página em que estão definidos.
    ```html
    <head>
        <style type="text/css">
            p {
                color: green;
                font-family: Arial;
            }
        </style>
    </head>
    ```
3.  **Estilos Externos (Linked)**: Definidos em um arquivo `.css` separado e linkados ao documento HTML/XHTML usando a tag `<link>` na seção `<head>`. Esta é a abordagem mais recomendada, pois permite reutilizar os mesmos estilos em várias páginas e facilita a manutenção.
    ```html
    <head>
        <link rel="stylesheet" type="text/css" href="estilos.css" />
    </head>
    ```

#### Seletores CSS

Seletores são padrões usados para selecionar os elementos HTML/XHTML que você deseja estilizar. Os tipos mais comuns incluem:

*   **Seletor de Elemento**: Seleciona todos os elementos de um determinado tipo (ex: `p`, `h1`, `div`).
    ```css
    p {
        color: black;
    }
    ```
*   **Seletor de ID**: Seleciona um único elemento com um `id` específico (ex: `#meuId`). IDs devem ser únicos em uma página.
    ```css
    #cabecalho {
        background-color: lightgray;
    }
    ```
*   **Seletor de Classe**: Seleciona todos os elementos que possuem uma classe específica (ex: `.minhaClasse`). Um elemento pode ter múltiplas classes.
    ```css
    .destaque {
        font-weight: bold;
    }
    ```
*   **Seletores de Atributo**: Seleciona elementos com um atributo específico ou um atributo com um valor específico (ex: `[type="text"]`).
    ```css
    input[type="text"] {
        border: 1px solid gray;
    }
    ```
*   **Seletores Combinadores**: Permitem combinar seletores para selecionar elementos com base em seu relacionamento (ex: `div p` para parágrafos dentro de divs, `ul > li` para itens de lista que são filhos diretos de `ul`).

#### Propriedades CSS Comuns

As propriedades CSS controlam a aparência dos elementos. Algumas das mais utilizadas incluem:

*   **`color`**: Define a cor do texto.
*   **`background-color`**: Define a cor de fundo.
*   **`font-size`**: Define o tamanho da fonte.
*   **`font-family`**: Define a família da fonte.
*   **`text-align`**: Alinha o texto (left, right, center, justify).
*   **`margin`**: Define o espaço externo ao redor de um elemento.
*   **`padding`**: Define o espaço interno entre a borda de um elemento e seu conteúdo.
*   **`border`**: Define a borda de um elemento.
*   **`width`**, **`height`**: Definem a largura e altura de um elemento.
*   **`display`**: Controla como um elemento é exibido (block, inline, inline-block, none, flex, grid).

#### Layout com CSS

O CSS oferece diversas técnicas para controlar o layout de uma página. As mais importantes incluem:

*   **Modelo de Caixa (Box Model)**: Todo elemento HTML/XHTML é tratado como uma caixa retangular, composta por conteúdo, padding, borda e margem. Compreender o modelo de caixa é fundamental para posicionar e dimensionar elementos corretamente.
*   **`float`**: Usado para posicionar elementos lado a lado, como colunas de texto ou imagens que flutuam em torno do texto. Embora ainda seja usado, `float` é frequentemente substituído por Flexbox ou Grid para layouts mais complexos.
*   **`position`**: Permite posicionar elementos de forma mais precisa (static, relative, absolute, fixed, sticky).
*   **Flexbox (Flexible Box Layout)**: Um módulo de layout unidimensional que permite distribuir espaço entre itens em um contêiner e alinhá-los. É ideal para criar layouts de componentes ou pequenas seções de uma página.
*   **CSS Grid Layout**: Um sistema de layout bidimensional que permite organizar elementos em linhas e colunas. É perfeito para criar layouts de página inteira complexos e responsivos.

Dominar o CSS é essencial para criar interfaces de usuário atraentes e funcionais, garantindo que a aplicação web seja visualmente agradável e acessível em diferentes dispositivos.




### 2.3 Scripts JavaScript

**JavaScript** é uma linguagem de programação de alto nível, interpretada e multiparadigma, amplamente utilizada para tornar as páginas web interativas. Enquanto HTML/XHTML estrutura o conteúdo e CSS o estiliza, JavaScript adiciona comportamento dinâmico, permitindo que as páginas respondam às ações do usuário, manipulem dados e interajam com APIs.

#### Onde Inserir JavaScript

Assim como o CSS, o JavaScript pode ser inserido em um documento HTML/XHTML de algumas maneiras:

1.  **Inline**: Diretamente em um atributo de evento de um elemento HTML (não recomendado para a maioria dos casos).
    ```html
    <button onclick="alert('Olá, mundo!');">Clique-me</button>
    ```
2.  **Interno (Embedded)**: Dentro da tag `<script>` na seção `<head>` ou `<body>` do documento. Geralmente, é melhor colocá-lo no final do `<body>` para garantir que o HTML seja carregado antes que o script tente manipulá-lo.
    ```html
    <head>
        <script type="text/javascript">
            function saudacao() {
                alert('Bem-vindo!');
            }
        </script>
    </head>
    <body>
        <button onclick="saudacao();">Saudar</button>
    </body>
    ```
3.  **Externo (Linked)**: Em um arquivo `.js` separado, linkado ao documento HTML/XHTML usando a tag `<script>` com o atributo `src`. Esta é a abordagem mais recomendada para organização e reutilização de código.
    ```html
    <head>
        <script type="text/javascript" src="meu_script.js"></script>
    </head>
    ```

#### Manipulação de DOM (Document Object Model)

O **DOM** é uma interface de programação para documentos HTML e XML. Ele representa a estrutura de um documento como uma árvore de objetos, onde cada nó é um elemento, atributo ou texto. JavaScript pode usar o DOM para acessar, modificar e manipular o conteúdo, estrutura e estilo de uma página web em tempo real. Isso significa que você pode, por exemplo, alterar o texto de um parágrafo, adicionar novos elementos à página ou mudar a cor de um botão, tudo isso após a página ter sido carregada.

**Exemplos de Manipulação de DOM:**

*   **Selecionar Elementos:**
    ```javascript
    // Seleciona um elemento pelo ID
    const meuParagrafo = document.getElementById('meuId');

    // Seleciona elementos pela classe
    const elementosComClasse = document.getElementsByClassName('minhaClasse');

    // Seleciona o primeiro elemento que corresponde a um seletor CSS
    const primeiroElemento = document.querySelector('div p');

    // Seleciona todos os elementos que correspondem a um seletor CSS
    const todosElementos = document.querySelectorAll('.item');
    ```
*   **Modificar Conteúdo:**
    ```javascript
    meuParagrafo.textContent = 'Novo texto para o parágrafo.';
    meuParagrafo.innerHTML = '<strong>Texto em negrito</strong> e normal.';
    ```
*   **Modificar Estilos:**
    ```javascript
    meuParagrafo.style.color = 'red';
    meuParagrafo.style.fontSize = '20px';
    ```
*   **Adicionar/Remover Classes:**
    ```javascript
    meuParagrafo.classList.add('novaClasse');
    meuParagrafo.classList.remove('classeAntiga');
    meuParagrafo.classList.toggle('classeAtiva'); // Adiciona se não tiver, remove se tiver
    ```
*   **Criar e Adicionar Elementos:**
    ```javascript
    const novoElemento = document.createElement('li');
    novoElemento.textContent = 'Novo item de lista';
    document.getElementById('minhaLista').appendChild(novoElemento);
    ```

#### Eventos

Eventos são ações que acontecem em um documento HTML/XHTML, como um clique do mouse, o carregamento de uma página, o envio de um formulário, o pressionar de uma tecla, etc. JavaScript permite que você 


responda a esses eventos, executando funções JavaScript quando eles ocorrem.

**Exemplos de Eventos Comuns:**

*   `onclick`: Quando um elemento é clicado.
*   `onmouseover`: Quando o ponteiro do mouse passa sobre um elemento.
*   `onkeydown`: Quando uma tecla é pressionada.
*   `onsubmit`: Quando um formulário é enviado.
*   `onload`: Quando a página ou um recurso (como uma imagem) é completamente carregado.

**Adicionando Listeners de Eventos:**

A forma mais moderna e recomendada de lidar com eventos é usando `addEventListener()`:

```javascript
const meuBotao = document.getElementById("meuBotao");
meuBotao.addEventListener("click", function() {
    alert("Botão clicado!");
});

// Ou com uma função nomeada
function lidarComClique() {
    console.log("Clique detectado!");
}
meuBotao.addEventListener("click", lidarComClique);
```

#### Validações de Formulário

JavaScript é amplamente utilizado para realizar **validações de formulário** no lado do cliente. Isso significa que os dados inseridos pelo usuário podem ser verificados antes de serem enviados ao servidor, proporcionando feedback instantâneo e melhorando a experiência do usuário. Exemplos de validações incluem:

*   Verificar se campos obrigatórios foram preenchidos.
*   Validar formatos de e-mail, CPF, telefone.
*   Comparar senhas para garantir que são iguais.
*   Verificar se um número está dentro de um determinado intervalo.

**Exemplo Básico de Validação:**

```javascript
function validarFormulario() {
    const nome = document.getElementById("nome").value;
    if (nome === "") {
        alert("O campo Nome é obrigatório!");
        return false; // Impede o envio do formulário
    }
    return true; // Permite o envio do formulário
}

// No HTML:
// <form onsubmit="return validarFormulario();">
//    <input type="text" id="nome" />
//    <button type="submit">Enviar</button>
// </form>
```

O JavaScript é uma ferramenta poderosa para criar interfaces de usuário dinâmicas e responsivas, sendo um pilar fundamental do desenvolvimento web front-end.



