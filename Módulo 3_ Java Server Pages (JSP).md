# Módulo 3: Java Server Pages (JSP)

Este módulo aborda as Java Server Pages (JSP), uma tecnologia que permite a criação de conteúdo web dinâmico de forma mais fácil e intuitiva do que os Servlets puros, especialmente para a camada de apresentação.

## Conteúdo

4. Java Server Pages (JSP) – Parte 1
    4.1 Scriptlets
    4.2 Declarações
    4.3 Expressões
    4.4 Objetos Implícitos
5. Java Server Pages (JSP) – Parte 2
    5.1 Diretivas
    5.2 Ações
    5.3 Linguagem de Expressão – EL
    5.4 Biblioteca de Tags Padrão – JSTL




### 4.1 Scriptlets

**Scriptlets** são blocos de código Java embutidos diretamente em uma página JSP. Eles permitem que você execute lógica de negócios, manipule dados e interaja com objetos Java dentro do contexto da página web. Embora sejam poderosos, o uso excessivo de scriptlets é geralmente desencorajado em favor de abordagens mais limpas e separadas, como EL, JSTL e JavaBeans, que veremos mais adiante. No entanto, entender scriptlets é fundamental para compreender como o JSP funciona e para manter ou migrar sistemas legados.

Um scriptlet é delimitado pelas tags `<%` e `%>`.

**Sintaxe:**

```jsp
<% // Código Java aqui %>
```

**Exemplo de Uso de Scriptlet:**

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Exemplo de Scriptlet</title>
</head>
<body>
    <h1>Usando Scriptlets em JSP</h1>
    <% 
        // Declaração de variáveis e lógica Java
        String nome = "Aluno";
        int hora = new java.util.Date().getHours();
        String saudacao;

        if (hora < 12) {
            saudacao = "Bom dia";
        } else if (hora < 18) {
            saudacao = "Boa tarde";
        } else {
            saudacao = "Boa noite";
        }
    %>

    <p><%= saudacao %>, <%= nome %>!</p>

    <% 
        // Loop para exibir números
        for (int i = 1; i <= 5; i++) {
            out.println("<p>Número: " + i + "</p>");
        }
    %>

    <p>A data e hora atuais são: <%= new java.util.Date() %></p>
</body>
</html>
```

**Pontos Importantes sobre Scriptlets:**

*   **Variáveis Locais**: Variáveis declaradas dentro de um scriptlet são locais ao método `_jspService()` que o contêiner JSP gera a partir da página JSP. Isso significa que elas não são acessíveis de outros scriptlets ou expressões diretamente, a menos que sejam passadas como parâmetros ou armazenadas em escopos (request, session, application).
*   **Objetos Implícitos**: Dentro de um scriptlet, você tem acesso direto a vários objetos implícitos fornecidos pelo contêiner JSP, como `request`, `response`, `session`, `application`, `out`, `pageContext`, `config`, `page`, `exception` (se `isErrorPage` for true) e `response`. Veremos mais sobre eles na seção 4.4.
*   **Geração de Conteúdo**: Você pode usar `out.println()` para escrever conteúdo diretamente na resposta HTTP, mas é mais comum misturar o código Java com o HTML/XHTML diretamente, como mostrado no exemplo acima com `<%= saudacao %>`.
*   **Mistura de Lógica e Apresentação**: O principal problema com scriptlets é que eles misturam a lógica de negócios (Java) com a camada de apresentação (HTML/XHTML), o que torna o código difícil de ler, manter e depurar. Isso viola o princípio da separação de preocupações.

Embora scriptlets sejam uma forma de adicionar dinamismo às páginas JSP, a evolução da tecnologia JSP trouxe alternativas mais elegantes e eficientes para a maioria das tarefas, como a Linguagem de Expressão (EL) e a JavaServer Pages Standard Tag Library (JSTL), que promovem uma melhor separação entre a lógica e a apresentação. No entanto, para entender o funcionamento interno do JSP e para lidar com código legado, o conhecimento de scriptlets é indispensável.




### 4.2 Declarações

**Declarações** em JSP são blocos de código Java que permitem definir variáveis e métodos que serão acessíveis em toda a página JSP, incluindo scriptlets e expressões. Diferente dos scriptlets, que são executados dentro do método `_jspService()` gerado pelo contêiner JSP, as declarações são inseridas na classe do Servlet gerada, fora de qualquer método de serviço. Isso significa que variáveis declaradas são variáveis de instância da classe do Servlet, e métodos declarados são métodos da classe do Servlet.

Uma declaração é delimitada pelas tags `<%! ` e `%>`.

**Sintaxe:**

```jsp
<%! // Declarações de variáveis e métodos Java aqui %>
```

**Exemplo de Uso de Declarações:**

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Exemplo de Declaração</title>
</head>
<body>
    <h1>Usando Declarações em JSP</h1>

    <%! 
        // Variável de instância (acessível por todas as requisições)
        private int contadorGlobal = 0;

        // Método de instância (pode ser chamado de scriptlets ou expressões)
        public String getMensagemBoasVindas(String nome) {
            return "Olá, " + nome + "! Bem-vindo ao nosso site.";
        }
    %>

    <% 
        // Incrementa a variável de instância
        contadorGlobal++;
    %>

    <p>Esta página foi visitada <%= contadorGlobal %> vezes.</p>
    <p><%= getMensagemBoasVindas("Visitante") %></p>

    <%! 
        // Outra variável de instância
        private String ultimaVisita = "Nunca";

        // Método para atualizar a última visita
        public void atualizarUltimaVisita() {
            ultimaVisita = new java.util.Date().toString();
        }
    %>

    <% 
        // Chama o método declarado
        atualizarUltimaVisita();
    %>

    <p>Última visita registrada: <%= ultimaVisita %></p>

</body>
</html>
```

**Pontos Importantes sobre Declarações:**

*   **Escopo da Classe**: Variáveis e métodos declarados em um bloco `<%! ... %>` têm escopo de classe. Isso significa que eles são compartilhados por todas as requisições que acessam essa página JSP. Para variáveis, isso pode levar a problemas de concorrência se não forem tratadas adequadamente (por exemplo, usando `synchronized` ou preferindo variáveis de escopo de requisição/sessão).
*   **Inicialização Única**: Variáveis de instância declaradas são inicializadas apenas uma vez, quando a JSP é carregada e compilada em um Servlet. Métodos declarados estão disponíveis para serem chamados a qualquer momento.
*   **Uso para Lógica Compartilhada**: Declarações são úteis para definir métodos auxiliares ou variáveis que precisam ser acessadas por múltiplos scriptlets ou para manter um estado que persiste entre requisições (com cautela para concorrência).
*   **Evitar Uso Excessivo**: Assim como scriptlets, o uso excessivo de declarações para lógica de negócios complexa pode obscurecer a separação de preocupações. É preferível mover a lógica de negócios para JavaBeans ou classes de utilidade Java separadas e acessá-las a partir da JSP.

Em resumo, as declarações fornecem uma maneira de adicionar membros (variáveis e métodos) à classe do Servlet gerada a partir da JSP. Embora úteis para certas situações, como métodos utilitários, é importante estar ciente de suas implicações de escopo e concorrência, e preferir outras abordagens para a lógica de negócios sempre que possível.




### 4.3 Expressões

**Expressões** em JSP são usadas para exibir o valor de uma variável ou o resultado de uma expressão Java diretamente na saída HTML/XHTML da página. Elas são uma forma concisa de inserir dados dinâmicos no conteúdo da página, sem a necessidade de usar `out.println()` dentro de um scriptlet.

Uma expressão é delimitada pelas tags `<%= ` e `%>`.

**Sintaxe:**

```jsp
<%= expressao_java %>
```

O valor da `expressao_java` é convertido para uma `String` e inserido na saída da página. A expressão pode ser qualquer expressão Java válida que resulte em um valor.

**Exemplo de Uso de Expressões:**

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Exemplo de Expressão</title>
</head>
<body>
    <h1>Usando Expressões em JSP</h1>

    <% 
        String nomeProduto = "Notebook";
        double preco = 2500.75;
        int quantidade = 2;
    %>

    <p>Nome do Produto: <%= nomeProduto %></p>
    <p>Preço Unitário: R$ <%= preco %></p>
    <p>Quantidade em Estoque: <%= quantidade %></p>
    <p>Valor Total: R$ <%= preco * quantidade %></p>

    <p>A data e hora atuais são: <%= new java.util.Date() %></p>

    <%! 
        public String getStatus(boolean ativo) {
            return ativo ? "Ativo" : "Inativo";
        }
    %>

    <p>Status do Produto: <%= getStatus(true) %></p>

</body>
</html>
```

**Pontos Importantes sobre Expressões:**

*   **Não Terminadas com Ponto e Vírgula**: Diferente dos scriptlets e declarações, as expressões **não** devem ser terminadas com ponto e vírgula (`;`).
*   **Apenas Expressões, Não Declarações ou Lógica de Controle**: Você não pode declarar variáveis ou usar estruturas de controle (como `if`, `for`, `while`) diretamente dentro de uma expressão. Elas são estritamente para exibir valores.
*   **Conversão para String**: O resultado da expressão é automaticamente convertido para uma `String` antes de ser inserido na saída. Isso significa que você pode exibir diretamente números, booleanos, objetos, etc.
*   **Objetos Implícitos**: Assim como nos scriptlets, você tem acesso aos objetos implícitos dentro de uma expressão (ex: `request.getParameter("param")`).
*   **Melhor Separação**: Expressões são uma forma mais limpa de exibir dados dinâmicos do que usar `out.println()` em scriptlets, pois mantêm o código Java mais conciso e focado na obtenção do valor a ser exibido.

Embora as expressões sejam úteis para exibir dados, a **Linguagem de Expressão (EL)**, que será abordada posteriormente, oferece uma sintaxe ainda mais simples e poderosa para acessar propriedades de JavaBeans e outros objetos, sendo a abordagem preferencial para a maioria das tarefas de exibição de dados em JSPs modernas.




### 4.4 Objetos Implícitos

**Objetos Implícitos** são objetos Java que o contêiner JSP automaticamente disponibiliza para uso em scriptlets, expressões e declarações, sem a necessidade de declará-los explicitamente. Eles representam informações e funcionalidades essenciais para o processamento de requisições e a geração de respostas em uma página JSP. Compreender esses objetos é fundamental para interagir com o ambiente do servidor e o cliente.

Os principais objetos implícitos em JSP são:

1.  **`request` (Tipo: `HttpServletRequest`)**
    *   Representa a requisição HTTP do cliente para o servidor.
    *   Permite acessar parâmetros de formulário (`getParameter()`, `getParameterValues()`), cabeçalhos (`getHeader()`), cookies (`getCookies()`), atributos (`getAttribute()`, `setAttribute()`) e informações sobre a URL da requisição.
    *   **Escopo**: Requisição (os atributos definidos neste objeto são válidos apenas para a requisição atual e são perdidos após a resposta ser enviada).

    **Exemplo:**
    ```jsp
    <p>Parâmetro 'nome': <%= request.getParameter("nome") %></p>
    <% 
        String userAgent = request.getHeader("User-Agent");
        request.setAttribute("infoNavegador", userAgent);
    %>
    ```

2.  **`response` (Tipo: `HttpServletResponse`)**
    *   Representa a resposta HTTP que o servidor enviará de volta ao cliente.
    *   Permite definir o tipo de conteúdo (`setContentType()`), adicionar cabeçalhos (`addHeader()`), adicionar cookies (`addCookie()`) e realizar redirecionamentos (`sendRedirect()`).
    *   **Escopo**: Requisição.

    **Exemplo:**
    ```jsp
    <% 
        response.setContentType("text/html;charset=UTF-8");
        // Não é comum usar response.sendRedirect() diretamente em JSP para controle de fluxo
        // mas é possível.
    %>
    ```

3.  **`out` (Tipo: `JspWriter`)**
    *   Usado para escrever conteúdo diretamente na saída da resposta HTTP do cliente.
    *   É o equivalente ao `PrintWriter` de um Servlet, mas otimizado para JSPs.
    *   **Escopo**: Requisição.

    **Exemplo:**
    ```jsp
    <% 
        out.println("<h2>Conteúdo gerado por out.println</h2>");
    %>
    ```

4.  **`session` (Tipo: `HttpSession`)**
    *   Representa a sessão do usuário. Permite armazenar e recuperar informações que persistem entre múltiplas requisições do mesmo usuário.
    *   Útil para carrinhos de compra, informações de login, preferências do usuário.
    *   **Escopo**: Sessão (os atributos persistem enquanto a sessão estiver ativa).

    **Exemplo:**
    ```jsp
    <% 
        Integer contador = (Integer) session.getAttribute("contadorSessao");
        if (contador == null) {
            contador = 1;
        } else {
            contador++;
        }
        session.setAttribute("contadorSessao", contador);
    %>
    <p>Você visitou esta página <%= contador %> vezes nesta sessão.</p>
    ```

5.  **`application` (Tipo: `ServletContext`)**
    *   Representa o contexto da aplicação web inteira. Permite armazenar e recuperar informações que são compartilhadas por todos os usuários e todas as requisições da aplicação.
    *   Útil para configurações globais, contadores de acesso à aplicação, etc.
    *   **Escopo**: Aplicação (os atributos persistem enquanto a aplicação estiver em execução).

    **Exemplo:**
    ```jsp
    <% 
        Integer totalVisitas = (Integer) application.getAttribute("totalVisitasApp");
        if (totalVisitas == null) {
            totalVisitas = 1;
        } else {
            totalVisitas++;
        }
        application.setAttribute("totalVisitasApp", totalVisitas);
    %>
    <p>Total de visitas à aplicação: <%= totalVisitas %></p>
    ```

6.  **`pageContext` (Tipo: `PageContext`)**
    *   Um objeto que fornece acesso a todos os outros objetos implícitos e a informações sobre a página JSP atual.
    *   Atua como um ponto de acesso centralizado para os diferentes escopos (page, request, session, application).
    *   **Escopo**: Página.

    **Exemplo:**
    ```jsp
    <p>Nome do Servlet gerado: <%= pageContext.getServletConfig().getServletName() %></p>
    ```

7.  **`page` (Tipo: `Object`)**
    *   Representa a instância do Servlet gerada a partir da página JSP atual. É o próprio objeto `this` dentro da JSP.
    *   Geralmente não é usado diretamente, pois `pageContext` oferece uma interface mais conveniente.
    *   **Escopo**: Página.

8.  **`config` (Tipo: `ServletConfig`)**
    *   Representa o objeto de configuração do Servlet para a página JSP atual.
    *   Permite acessar parâmetros de inicialização específicos da JSP definidos no `web.xml`.
    *   **Escopo**: Página.

    **Exemplo:**
    ```jsp
    <p>Parâmetro de inicialização 'meuParam': <%= config.getInitParameter("meuParam") %></p>
    ```

9.  **`exception` (Tipo: `Throwable`)**
    *   Disponível apenas em páginas JSP que são designadas como páginas de erro (`isErrorPage="true"`).
    *   Contém a exceção que foi lançada e causou o erro.
    *   **Escopo**: Página (apenas em páginas de erro).

    **Exemplo (em `errorPage.jsp` com `isErrorPage="true"`):
    ```jsp
    <p>Ocorreu um erro: <%= exception.getMessage() %></p>
    ```

Compreender o propósito e o escopo de cada objeto implícito é crucial para gerenciar o estado da aplicação e do usuário de forma eficaz em suas páginas JSP.




## 5. Java Server Pages (JSP) – Parte 2

### 5.1 Diretivas

**Diretivas** em JSP são instruções que fornecem informações ao contêiner JSP sobre como processar a página. Elas não geram nenhuma saída diretamente na resposta do cliente, mas afetam a forma como o Servlet correspondente é gerado ou como outros recursos são incluídos. As diretivas são processadas no momento da tradução da JSP para um Servlet Java.

Uma diretiva é delimitada pelas tags `<%@` e `%>`.

**Sintaxe:**

```jsp
<%@ diretiva atributo="valor" %>
```

Existem três tipos principais de diretivas:

1.  **`page` Diretiva**
    *   Define atributos que se aplicam a toda a página JSP. Pode ser usada em qualquer lugar da página, mas é uma boa prática colocá-la no início.
    *   **Atributos comuns:**
        *   `language`: Define a linguagem de script a ser usada na página (padrão é `java`).
        *   `contentType`: Define o tipo MIME e o conjunto de caracteres da resposta (ex: `text/html;charset=UTF-8`).
        *   `pageEncoding`: Define o conjunto de caracteres da própria página JSP.
        *   `import`: Importa classes e pacotes Java, similar à declaração `import` em Java. Pode ter múltiplos `import` separados por vírgula.
        *   `session`: Define se a página JSP participa de uma sessão HTTP (padrão é `true`).
        *   `isErrorPage`: Indica se a página é uma página de erro (padrão é `false`). Se `true`, o objeto implícito `exception` estará disponível.
        *   `errorPage`: Especifica a URL de uma página de erro para onde a requisição será encaminhada se ocorrer uma exceção não tratada nesta página.
        *   `buffer`: Define o tamanho do buffer de saída (padrão é `8kb`).
        *   `autoFlush`: Define se o buffer deve ser automaticamente descarregado quando cheio (padrão é `true`).

    **Exemplo de `page` diretiva:**
    ```jsp
    <%@page language="java" contentType="text/html; charset=UTF-8"
            pageEncoding="UTF-8" import="java.util.Date, java.text.SimpleDateFormat" %>
    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Exemplo de Diretiva Page</title>
    </head>
    <body>
        <h1>Data e Hora Atuais: <%= new SimpleDateFormat("dd/MM/yyyy HH:mm:ss").format(new Date()) %></h1>
    </body>
    </html>
    ```

2.  **`include` Diretiva**
    *   Inclui o conteúdo de outro arquivo (JSP, HTML, texto) no momento da **tradução** da página JSP. O conteúdo do arquivo incluído é inserido diretamente na página principal antes que ela seja compilada em um Servlet.
    *   É útil para reutilizar cabeçalhos, rodapés ou barras de navegação que são estáticos ou que não precisam de processamento dinâmico complexo.
    *   **Sintaxe:** `<%@ include file="caminho/para/arquivo.jsp" %>`

    **Exemplo de `include` diretiva:**
    Suponha que você tenha um arquivo `cabecalho.html`:
    ```html
    <!-- cabecalho.html -->
    <header>
        <h1>Meu Site Dinâmico</h1>
        <nav>
            <a href="index.jsp">Home</a> |
            <a href="sobre.jsp">Sobre</a>
        </nav>
    </header>
    <hr/>
    ```
    E um arquivo `rodape.html`:
    ```html
    <!-- rodape.html -->
    <hr/>
    <footer>
        <p>&copy; 2025 Meu Curso. Todos os direitos reservados.</p>
    </footer>
    ```
    Você pode incluí-los em `minhaPagina.jsp`:
    ```jsp
    <%@page contentType="text/html" pageEncoding="UTF-8"%>
    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Minha Página</title>
    </head>
    <body>
        <%@ include file="cabecalho.html" %>

        <main>
            <h2>Bem-vindo à minha página principal!</h2>
            <p>Este é o conteúdo dinâmico da página.</p>
        </main>

        <%@ include file="rodape.html" %>
    </body>
    </html>
    ```
    **Importante**: Se o conteúdo incluído mudar, a página principal precisará ser recompilada. Para inclusões dinâmicas que são processadas em tempo de execução, use a ação `<jsp:include>` (veremos na próxima seção).

3.  **`taglib` Diretiva**
    *   Declara que a página JSP usará uma biblioteca de tags personalizada (como JSTL ou uma biblioteca de tags que você mesmo criou).
    *   Permite que você use tags personalizadas para encapsular lógica complexa, tornando a JSP mais limpa e legível.
    *   **Atributos:**
        *   `uri`: O URI (Uniform Resource Identifier) que identifica a biblioteca de tags.
        *   `prefix`: Um prefixo que será usado para as tags da biblioteca na página JSP.

    **Exemplo de `taglib` diretiva (para JSTL Core):**
    ```jsp
    <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
    <!DOCTYPE html>
    <html>
    <head>
        <title>Exemplo de Diretiva Taglib</title>
    </head>
    <body>
        <c:set var="nome" value="Mundo" />
        <p>Olá, <c:out value="${nome}" />!</p>
    </body>
    </html>
    ```
    A diretiva `taglib` é fundamental para usar bibliotecas de tags que promovem a separação de lógica e apresentação, um conceito chave no desenvolvimento JSP moderno.

As diretivas são ferramentas poderosas para configurar o comportamento de uma página JSP e para reutilizar conteúdo de forma eficiente, contribuindo para a modularidade e manutenibilidade das aplicações web.




### 5.2 Ações

**Ações** em JSP são tags XML que controlam o comportamento do servidor em tempo de execução. Elas são mais flexíveis que as diretivas, pois seu processamento ocorre no momento em que a página é requisitada, e não no momento da compilação. As ações permitem a interação com JavaBeans, a inclusão dinâmica de recursos e o encaminhamento de requisições, entre outras funcionalidades.

As ações JSP são prefixadas com `jsp:` e seguem a sintaxe XML.

#### Principais Ações JSP

1.  **`<jsp:include>`**
    *   Inclui o conteúdo de outro recurso (JSP, HTML, Servlet) na resposta da página atual em tempo de execução. O recurso incluído é processado independentemente e sua saída é inserida na página principal.
    *   **Diferença para a diretiva `include`**: A diretiva `include` é estática (tempo de compilação), enquanto a ação `jsp:include` é dinâmica (tempo de execução). Isso significa que a ação `jsp:include` permite que o recurso incluído seja processado a cada requisição, e se o recurso incluído for um Servlet ou JSP, ele pode ter sua própria lógica e acessar seus próprios objetos implícitos.
    *   **Atributos:**
        *   `page`: O caminho relativo do recurso a ser incluído.
        *   `flush`: Booleano, indica se o buffer de saída deve ser descarregado antes da inclusão (padrão é `false`).

    **Exemplo de `<jsp:include>`:**
    Suponha que `dataAtual.jsp` seja:
    ```jsp
    <%-- dataAtual.jsp --%>
    <p>Data e Hora Atual: <%= new java.util.Date() %></p>
    ```
    E `minhaPaginaDinamica.jsp`:
    ```jsp
    <%@page contentType="text/html" pageEncoding="UTF-8"%>
    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Página Dinâmica com Include</title>
    </head>
    <body>
        <h1>Bem-vindo!</h1>
        <jsp:include page="dataAtual.jsp" />
        <p>Este é o conteúdo principal da página.</p>
    </body>
    </html>
    ```

2.  **`<jsp:forward>`**
    *   Encaminha a requisição e a resposta para outro recurso (JSP, HTML, Servlet) no mesmo servidor. O processamento da página atual é interrompido e o controle é transferido para o recurso especificado.
    *   **Diferença para `response.sendRedirect()`**: `jsp:forward` é uma operação do lado do servidor (a URL no navegador não muda e os atributos da requisição são preservados), enquanto `sendRedirect()` é do lado do cliente (nova requisição, URL muda, atributos perdidos).
    *   **Atributos:**
        *   `page`: O caminho relativo do recurso para o qual a requisição será encaminhada.

    **Exemplo de `<jsp:forward>`:**
    ```jsp
    <%@page contentType="text/html" pageEncoding="UTF-8"%>
    <% 
        // Lógica de processamento
        String usuario = request.getParameter("usuario");
        if (usuario == null || usuario.isEmpty()) {
            // Se o usuário não for fornecido, encaminha para uma página de erro ou formulário
            request.setAttribute("erro", "Nome de usuário é obrigatório.");
            // Encaminha para uma JSP de erro
            %><jsp:forward page="erro.jsp" /><% 
        } else {
            request.setAttribute("mensagem", "Olá, " + usuario + "!");
            // Encaminha para uma JSP de sucesso
            %><jsp:forward page="sucesso.jsp" /><% 
        }
    %>
    ```

3.  **`<jsp:useBean>`**
    *   Localiza ou instancia um JavaBean e o associa a uma variável de script com um determinado escopo. JavaBeans são classes Java simples que seguem certas convenções (construtor sem argumentos, getters e setters para propriedades).
    *   É fundamental para o padrão MVC, permitindo que a camada de apresentação (JSP) interaja com a camada de modelo (JavaBeans).
    *   **Atributos:**
        *   `id`: O nome da variável de script que será usada para referenciar o JavaBean.
        *   `class`: O nome completo da classe do JavaBean.
        *   `scope`: O escopo no qual o JavaBean será armazenado (`page`, `request`, `session`, `application`). Padrão é `page`.
        *   `type`: O tipo da variável de script (útil para polimorfismo).

    **Exemplo de `<jsp:useBean>`:**
    Suponha que você tenha uma classe `Pessoa.java`:
    ```java
    // src/main/java/com/example/model/Pessoa.java
    package com.example.model;

    public class Pessoa {
        private String nome;
        private int idade;

        public Pessoa() { /* Construtor padrão */ }

        public String getNome() { return nome; }
        public void setNome(String nome) { this.nome = nome; }

        public int getIdade() { return idade; }
        public void setIdade(int idade) { this.idade = idade; }
    }
    ```
    Em sua JSP:
    ```jsp
    <%@page contentType="text/html" pageEncoding="UTF-8"%>
    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Exemplo de jsp:useBean</title>
    </head>
    <body>
        <h1>Usando JavaBeans em JSP</h1>

        <jsp:useBean id="minhaPessoa" class="com.example.model.Pessoa" scope="session" />

        <p>Nome da Pessoa (inicial): <%= minhaPessoa.getNome() %></p>
        <p>Idade da Pessoa (inicial): <%= minhaPessoa.getIdade() %></p>

        <% 
            // Você pode acessar o bean diretamente via scriptlet
            minhaPessoa.setNome("Maria");
            minhaPessoa.setIdade(30);
        %>

        <p>Nome da Pessoa (após scriptlet): <%= minhaPessoa.getNome() %></p>
        <p>Idade da Pessoa (após scriptlet): <%= minhaPessoa.getIdade() %></p>

    </body>
    </html>
    ```

4.  **`<jsp:setProperty>`**
    *   Define o valor de uma propriedade de um JavaBean. Pode ser usado para preencher propriedades de um bean a partir de parâmetros da requisição ou de um valor específico.
    *   **Atributos:**
        *   `name`: O `id` do JavaBean definido com `<jsp:useBean>`.
        *   `property`: O nome da propriedade do JavaBean a ser definida.
        *   `value`: O valor a ser atribuído à propriedade. Se omitido, o valor é pego de um parâmetro da requisição com o mesmo nome da propriedade.
        *   `param`: O nome do parâmetro da requisição a ser usado para definir a propriedade.

    **Exemplo de `<jsp:setProperty>`:**
    ```jsp
    <%@page contentType="text/html" pageEncoding="UTF-8"%>
    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Exemplo de jsp:setProperty</title>
    </head>
    <body>
        <h1>Definindo Propriedades de JavaBeans</h1>

        <jsp:useBean id="minhaPessoa" class="com.example.model.Pessoa" scope="request" />

        <%-- Define a propriedade 'nome' com o valor do parâmetro 'nome' da requisição --%>
        <jsp:setProperty name="minhaPessoa" property="nome" />
        <%-- Define a propriedade 'idade' com o valor do parâmetro 'idade' da requisição --%>
        <jsp:setProperty name="minhaPessoa" property="idade" />

        <p>Nome: <%= minhaPessoa.getNome() %></p>
        <p>Idade: <%= minhaPessoa.getIdade() %></p>

        <%-- Exemplo de como seria o formulário HTML para enviar os dados --%>
        <%-- 
        <form action="exemplo.jsp" method="post">
            Nome: <input type="text" name="nome" /><br>
            Idade: <input type="text" name="idade" /><br>
            <input type="submit" value="Enviar" />
        </form>
        --%>

    </body>
    </html>
    ```

5.  **`<jsp:getProperty>`**
    *   Obtém o valor de uma propriedade de um JavaBean e o exibe na saída da página.
    *   **Atributos:**
        *   `name`: O `id` do JavaBean.
        *   `property`: O nome da propriedade do JavaBean a ser obtida.

    **Exemplo de `<jsp:getProperty>`:**
    ```jsp
    <%@page contentType="text/html" pageEncoding="UTF-8"%>
    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Exemplo de jsp:getProperty</title>
    </head>
    <body>
        <h1>Obtendo Propriedades de JavaBeans</h1>

        <jsp:useBean id="minhaPessoa" class="com.example.model.Pessoa" scope="request" />
        <jsp:setProperty name="minhaPessoa" property="nome" value="João" />
        <jsp:setProperty name="minhaPessoa" property="idade" value="25" />

        <p>Nome: <jsp:getProperty name="minhaPessoa" property="nome" /></p>
        <p>Idade: <jsp:getProperty name="minhaPessoa" property="idade" /></p>

    </body>
    </html>
    ```

As ações JSP são ferramentas importantes para interagir com componentes Java e gerenciar o fluxo de controle em suas páginas, promovendo uma abordagem mais estruturada e menos dependente de scriptlets.




### 5.3 Linguagem de Expressão – EL

A **Linguagem de Expressão (EL)** é uma linguagem simples e poderosa introduzida no JSP 2.0 que facilita o acesso a dados armazenados em JavaBeans, mapas, listas, arrays e objetos implícitos. A EL foi criada para simplificar a camada de apresentação, permitindo que os desenvolvedores acessem dados de forma concisa e legível, sem a necessidade de scriptlets ou tags `jsp:getProperty`.

A sintaxe básica da EL é `${expressao}`.

**Sintaxe:**

```jsp
${expressao}
```

#### Acessando Propriedades de JavaBeans

A EL permite acessar propriedades de JavaBeans usando a notação de ponto (`.`) ou colchetes (`[]`).

Suponha que você tenha um JavaBean `Pessoa` com propriedades `nome` e `idade` (com seus respectivos getters e setters), e que uma instância desse JavaBean esteja armazenada no escopo da requisição com o nome `minhaPessoa`.

```java
// Em um Servlet ou em outro lugar antes de encaminhar para a JSP
Pessoa p = new Pessoa();
p.setNome("Carlos");
p.setIdade(28);
request.setAttribute("minhaPessoa", p);
```

Na JSP, você pode acessar as propriedades assim:

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Exemplo de EL - JavaBeans</title>
</head>
<body>
    <h1>Acessando JavaBeans com EL</h1>

    <p>Nome: ${minhaPessoa.nome}</p>
    <p>Idade: ${minhaPessoa.idade}</p>

    <%-- Equivalente a: --%>
    <%-- <p>Nome: <%= ((com.example.model.Pessoa)request.getAttribute("minhaPessoa")).getNome() %></p> --%>
    <%-- <p>Idade: <%= ((com.example.model.Pessoa)request.getAttribute("minhaPessoa")).getIdade() %></p> --%>
</body>
</html>
```

#### Acessando Elementos de Coleções (Listas, Arrays, Mapas)

A EL também simplifica o acesso a elementos de coleções.

**Listas e Arrays:**

```java
// Em um Servlet
List<String> frutas = new ArrayList<>();
frutas.add("Maçã");
frutas.add("Banana");
frutas.add("Laranja");
request.setAttribute("listaFrutas", frutas);

String[] cores = {"Vermelho", "Verde", "Azul"};
request.setAttribute("arrayCores", cores);
```

Na JSP:

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Exemplo de EL - Coleções</title>
</head>
<body>
    <h1>Acessando Coleções com EL</h1>

    <h2>Lista de Frutas:</h2>
    <ul>
        <li>${listaFrutas[0]}</li>
        <li>${listaFrutas[1]}</li>
        <li>${listaFrutas[2]}</li>
    </ul>

    <h2>Array de Cores:</h2>
    <ul>
        <li>${arrayCores[0]}</li>
        <li>${arrayCores[1]}</li>
        <li>${arrayCores[2]}</li>
    </ul>
</body>
</html>
```

**Mapas:**

```java
// Em um Servlet
Map<String, String> configuracoes = new HashMap<>();
configuracoes.put("tema", "escuro");
configuracoes.put("idioma", "pt_BR");
request.setAttribute("configuracoesApp", configuracoes);
```

Na JSP:

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Exemplo de EL - Mapas</title>
</head>
<body>
    <h1>Acessando Mapas com EL</h1>

    <p>Tema: ${configuracoesApp.tema}</p>
    <p>Idioma: ${configuracoesApp["idioma"]}</p> <%-- Use colchetes para chaves com caracteres especiais ou espaços --%>
</body>
</html>
```

#### Objetos Implícitos da EL

A EL também fornece seus próprios objetos implícitos, que são diferentes dos objetos implícitos do JSP, mas fornecem acesso a informações semelhantes de forma mais conveniente. Eles são prefixados com `pageScope`, `requestScope`, `sessionScope`, `applicationScope` para acessar atributos em seus respectivos escopos.

*   **`pageContext`**: Acessa o objeto `PageContext`.
*   **`pageScope`**: Mapa de atributos no escopo da página.
*   **`requestScope`**: Mapa de atributos no escopo da requisição.
*   **`sessionScope`**: Mapa de atributos no escopo da sessão.
*   **`applicationScope`**: Mapa de atributos no escopo da aplicação.
*   **`param`**: Mapa de parâmetros da requisição (retorna o primeiro valor se houver múltiplos).
*   **`paramValues`**: Mapa de parâmetros da requisição (retorna um array de todos os valores).
*   **`header`**: Mapa de cabeçalhos da requisição (retorna o primeiro valor).
*   **`headerValues`**: Mapa de cabeçalhos da requisição (retorna um array de todos os valores).
*   **`cookie`**: Mapa de cookies da requisição.
*   **`initParam`**: Mapa de parâmetros de inicialização do contexto da aplicação.

**Exemplo de Objetos Implícitos da EL:**

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Exemplo de EL - Objetos Implícitos</title>
</head>
<body>
    <h1>Objetos Implícitos da EL</h1>

    <p>Parâmetro 'nome' da requisição: ${param.nome}</p>
    <p>User-Agent do cabeçalho: ${header["User-Agent"]}</p>
    <p>ID da Sessão: ${sessionScope.id}</p>
    <p>Nome do Contexto da Aplicação: ${applicationScope.contextPath}</p>

    <%-- Exemplo de como seria o formulário HTML para enviar o parâmetro 'nome' --%>
    <%-- 
    <form action="exemplo.jsp" method="get">
        Nome: <input type="text" name="nome" /><br>
        <input type="submit" value="Enviar" />
    </form>
    --%>
</body>
</html>
```

#### Operadores da EL

A EL suporta vários operadores para realizar operações lógicas, aritméticas e de comparação:

*   **Aritméticos**: `+`, `-`, `*`, `/` (ou `div`), `%` (ou `mod`)
*   **Relacionais**: `==` (ou `eq`), `!=` (or `ne`), `<` (or `lt`), `>` (or `gt`), `<=` (or `le`), `>=` (or `ge`)
*   **Lógicos**: `&&` (or `and`), `||` (or `or`), `!` (or `not`)
*   **Condicional**: `? :` (operador ternário)
*   **Vazio**: `empty` (verifica se uma coleção, string ou array é nulo ou vazio)

**Exemplo de Operadores da EL:**

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Exemplo de EL - Operadores</title>
</head>
<body>
    <h1>Operadores da EL</h1>

    <% 
        request.setAttribute("num1", 10);
        request.setAttribute("num2", 5);
        request.setAttribute("texto", "");
    %>

    <p>Soma: ${num1 + num2}</p>
    <p>Subtração: ${num1 - num2}</p>
    <p>Multiplicação: ${num1 * num2}</p>
    <p>Divisão: ${num1 / num2}</p>
    <p>Resto: ${num1 % num2}</p>

    <p>num1 é maior que num2? ${num1 > num2}</p>
    <p>num1 é igual a num2? ${num1 == num2}</p>

    <p>O texto está vazio? ${empty texto}</p>
    <p>O número 1 é par? ${num1 % 2 == 0 ? "Sim" : "Não"}</p>
</body>
</html>
```

#### Integração com JSP

A EL é projetada para ser usada em conjunto com as tags JSP e, especialmente, com a JSTL. Ela oferece uma maneira limpa e eficiente de acessar dados e exibir informações, reduzindo a necessidade de código Java embutido nas páginas JSP e promovendo uma melhor separação entre a lógica de negócios e a apresentação.

É a forma preferencial de acessar dados em JSPs modernas, tornando o código mais legível e fácil de manter.




### 5.4 Biblioteca de Tags Padrão – JSTL

A **JavaServer Pages Standard Tag Library (JSTL)** é um conjunto de tags padronizadas que estendem a funcionalidade do JSP, fornecendo uma maneira mais limpa e eficiente de realizar tarefas comuns em páginas web, como iteração, condicionais, formatação de dados e acesso a bancos de dados. A JSTL foi criada para reduzir a necessidade de scriptlets (código Java embutido) nas JSPs, promovendo uma melhor separação entre a lógica de negócios e a camada de apresentação.

A JSTL é dividida em várias bibliotecas de tags, cada uma com um propósito específico:

1.  **Core (`c`)**: Para tarefas de fluxo de controle (condicionais, laços), manipulação de URLs e variáveis.
2.  **Formatting (`fmt`)**: Para formatação de dados (números, datas, moedas) e internacionalização.
3.  **SQL (`sql`)**: Para interagir com bancos de dados diretamente da JSP (não recomendado para aplicações complexas, mas útil para prototipagem ou tarefas simples).
4.  **XML (`x`)**: Para manipulação de documentos XML.
5.  **Functions (`fn`)**: Para funções de string e coleção que podem ser usadas em expressões EL.

Para usar as tags JSTL em uma JSP, você precisa declará-las usando a diretiva `taglib` no início da página, especificando o URI da biblioteca e um prefixo.

#### Biblioteca Core (`c`)

É a biblioteca mais utilizada e fornece tags para controle de fluxo e manipulação de variáveis.

*   **`<c:set>`**: Define uma variável em um determinado escopo.
    ```jsp
    <c:set var="nomeUsuario" value="João Silva" scope="session" />
    <p>Usuário: ${sessionScope.nomeUsuario}</p>
    ```

*   **`<c:out>`**: Exibe o valor de uma expressão EL. É mais seguro que `<%= %>` porque escapa caracteres HTML por padrão, prevenindo ataques XSS.
    ```jsp
    <c:out value="${param.mensagem}" />
    <c:out value="${param.htmlContent}" escapeXml="false" /> <%-- Cuidado ao desabilitar o escape --%>
    ```

*   **`<c:if>`**: Executa um bloco de código se uma condição for verdadeira.
    ```jsp
    <c:if test="${usuario.logado}">
        <p>Bem-vindo, ${usuario.nome}!</p>
    </c:if>
    ```

*   **`<c:choose>`, `<c:when>`, `<c:otherwise>`**: Estrutura condicional para múltiplos casos.
    ```jsp
    <c:choose>
        <c:when test="${score >= 90}">
            <p>Excelente!</p>
        </c:when>
        <c:when test="${score >= 70}">
            <p>Bom!</p>
        </c:when>
        <c:otherwise>
            <p>Precisa melhorar.</p>
        </c:otherwise>
    </c:choose>
    ```

*   **`<c:forEach>`**: Itera sobre coleções (Listas, Arrays, Mapas) ou executa um loop por um número fixo de vezes.
    ```jsp
    <% 
        List<String> produtos = new ArrayList<>();
        produtos.add("TV");
        produtos.add("Geladeira");
        produtos.add("Fogão");
        request.setAttribute("listaProdutos", produtos);
    %>

    <h2>Lista de Produtos:</h2>
    <ul>
        <c:forEach var="produto" items="${listaProdutos}">
            <li>${produto}</li>
        </c:forEach>
    </ul>

    <h2>Contagem de 1 a 5:</h2>
    <c:forEach var="i" begin="1" end="5">
        <p>Número: ${i}</p>
    </c:forEach>
    ```

*   **`<c:url>`**: Cria uma URL, adicionando automaticamente o contexto da aplicação e parâmetros, e codificando-os corretamente.
    ```jsp
    <a href="<c:url value="/detalhes.jsp">
        <c:param name="id" value="${produto.id}" />
        <c:param name="categoria" value="${produto.categoria}" />
    </c:url>">Ver Detalhes</a>
    ```

#### Biblioteca Formatting (`fmt`)

Usada para formatação de números, datas e internacionalização.

*   **`<fmt:formatNumber>`**: Formata números como moeda, porcentagem ou com um padrão específico.
    ```jsp
    <c:set var="preco" value="1234.567" />
    <p>Preço: <fmt:formatNumber value="${preco}" type="currency" currencySymbol="R$" /></p>
    <p>Porcentagem: <fmt:formatNumber value="0.75" type="percent" /></p>
    <p>Número com 2 casas decimais: <fmt:formatNumber value="${preco}" pattern="#.00" /></p>
    ```

*   **`<fmt:formatDate>`**: Formata objetos `java.util.Date` ou `java.util.Calendar`.
    ```jsp
    <c:set var="dataAtual" value="<%= new java.util.Date() %>" />
    <p>Data Completa: <fmt:formatDate value="${dataAtual}" type="both" dateStyle="full" timeStyle="long" /></p>
    <p>Apenas Data: <fmt:formatDate value="${dataAtual}" pattern="dd/MM/yyyy" /></p>
    ```

*   **`<fmt:setLocale>`**: Define o locale para a página.
*   **`<fmt:bundle>`, `<fmt:message>`**: Para internacionalização (i18n), buscando mensagens de arquivos de recursos.

#### Biblioteca SQL (`sql`)

Permite executar consultas SQL diretamente da JSP. **Embora seja parte da JSTL, seu uso em aplicações de produção é geralmente desencorajado em favor de uma camada de acesso a dados (DAO) separada, que promove melhor segurança, desempenho e separação de preocupações.**

*   **`<sql:setDataSource>`**: Configura uma fonte de dados.
*   **`<sql:query>`**: Executa uma consulta SELECT.
*   **`<sql:update>`**: Executa operações INSERT, UPDATE, DELETE.

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/sql" prefix="sql" %>

<sql:setDataSource var="dataSource" dataSource="jdbc/meuBanco" />

<sql:query var="resultados" dataSource="${dataSource}">
    SELECT id, nome FROM usuarios
</sql:query>

<h2>Usuários:</h2>
<ul>
    <c:forEach var="usuario" items="${resultados.rows}">
        <li>ID: ${usuario.id}, Nome: ${usuario.nome}</li>
    </c:forEach>
</ul>
```

#### Biblioteca Functions (`fn`)

Fornece funções utilitárias para manipulação de strings e coleções, que podem ser usadas dentro de expressões EL.

*   **`fn:length(collectionOrString)`**: Retorna o tamanho de uma coleção ou o comprimento de uma string.
*   **`fn:contains(string, substring)`**: Verifica se uma string contém outra substring.
*   **`fn:toUpperCase(string)`**, **`fn:toLowerCase(string)`**: Converte uma string para maiúsculas/minúsculas.
*   **`fn:substring(string, begin, end)`**: Extrai uma parte de uma string.

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/functions" prefix="fn" %>

<c:set var="minhaString" value="Hello World JSP" />
<c:set var="minhaLista" value="<%= new ArrayList<String>() {{ add("A"); add("B"); }} %>" />

<p>Comprimento da string: ${fn:length(minhaString)}</p>
<p>String em maiúsculas: ${fn:toUpperCase(minhaString)}</p>
<p>Contém "World"? ${fn:contains(minhaString, "World")}</p>
<p>Tamanho da lista: ${fn:length(minhaLista)}</p>
```

A JSTL, em conjunto com a EL, é a abordagem recomendada para a camada de apresentação em aplicações JSP modernas, pois promove um código mais limpo, legível e manutenível, separando efetivamente a lógica de apresentação da lógica de negócios. Isso é um passo importante em direção ao padrão de arquitetura MVC, que será abordado no próximo módulo.



