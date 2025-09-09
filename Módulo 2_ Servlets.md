# Módulo 2: Servlets

Este módulo explora os Servlets, componentes Java que estendem as capacidades de servidores web, permitindo a criação de aplicações web dinâmicas.

## Conteúdo

3. Servlets
    3.1 Definição
    3.2 Ciclo de Vida
    3.3 Tratamento de Solicitações GET e POST
    3.4 Parâmetros e Respostas
    3.5 Redirecionamento e Encaminhamento
    3.6 Seções




### 3.1 Definição

**Servlets** são componentes Java que estendem as capacidades de servidores web, fornecendo uma maneira poderosa de criar aplicações web dinâmicas. Eles são programas Java que rodam em um servidor web (ou servidor de aplicação, como o GlassFish) e respondem a requisições de clientes, geralmente navegadores web. Diferente dos applets (que rodam no lado do cliente), os servlets rodam no lado do servidor, o que os torna ideais para processar dados de formulários, acessar bancos de dados, gerar conteúdo dinâmico e gerenciar sessões de usuário.

Em essência, um servlet atua como um intermediário entre uma requisição HTTP de um navegador e um banco de dados ou aplicação no servidor. Quando uma requisição chega ao servidor, o servidor a encaminha para o servlet apropriado, que então processa a requisição e gera uma resposta, que é enviada de volta ao cliente.

**Finalidade dos Servlets:**

*   **Processamento de Requisições HTTP**: Servlets são projetados para lidar com requisições HTTP, permitindo que as aplicações web respondam a diferentes tipos de solicitações (GET, POST, etc.).
*   **Geração de Conteúdo Dinâmico**: Eles podem gerar páginas HTML, XML ou outros tipos de conteúdo 


dinâmico com base em dados de bancos de dados, entrada do usuário ou outras fontes.
*   **Acesso a Bancos de Dados**: Servlets podem interagir com bancos de dados para armazenar e recuperar informações, sendo a base para aplicações web orientadas a dados.
*   **Gerenciamento de Sessão**: Eles fornecem mecanismos para manter o estado do usuário entre múltiplas requisições, o que é essencial para funcionalidades como carrinhos de compra, logins de usuário e personalização.
*   **Integração com Tecnologias Java EE**: Servlets são uma parte fundamental da plataforma Java EE, permitindo a integração com outras tecnologias como JSP, EJB, JMS, etc.

Servlets são a espinha dorsal de muitas aplicações web Java e entender seu funcionamento é crucial para o desenvolvimento web robusto e escalável.




### 3.2 Ciclo de Vida

O ciclo de vida de um Servlet é gerenciado pelo contêiner de Servlets (como o GlassFish). Compreender esse ciclo é fundamental para desenvolver Servlets eficientes e robustos. As principais fases do ciclo de vida de um Servlet são:

1.  **Carregamento e Instanciação**: Quando o contêiner de Servlets é iniciado ou quando a primeira requisição para um Servlet específico é recebida, o contêiner carrega a classe do Servlet e cria uma instância dele.

2.  **Inicialização (`init()` método)**: Após a instanciação, o contêiner chama o método `init()` do Servlet. Este método é chamado **apenas uma vez** durante todo o ciclo de vida do Servlet. É o local ideal para realizar tarefas de inicialização, como carregar configurações, estabelecer conexões com bancos de dados ou inicializar recursos que serão usados por todas as requisições. O método `init()` recebe um objeto `ServletConfig` que pode ser usado para obter parâmetros de inicialização definidos no `web.xml`.

    ```java
    public void init(ServletConfig config) throws ServletException {
        super.init(config);
        // Código de inicialização aqui
        System.out.println("Servlet inicializado!");
    }
    ```

3.  **Processamento de Requisições (`service()` método)**: Após a inicialização, o Servlet está pronto para processar requisições. Para cada requisição de cliente, o contêiner de Servlets chama o método `service()` do Servlet. Este método é responsável por despachar a requisição para os métodos específicos de tratamento de HTTP (como `doGet()`, `doPost()`, `doPut()`, `doDelete()`, etc.) com base no método HTTP da requisição. O método `service()` recebe dois objetos: `ServletRequest` (ou `HttpServletRequest` para requisições HTTP) e `ServletResponse` (ou `HttpServletResponse` para respostas HTTP).

    ```java
    public void service(ServletRequest request, ServletResponse response)
            throws ServletException, IOException {
        // O método service geralmente não é sobrescrito diretamente em HttpServlet
        // Em HttpServlet, ele despacha para doGet, doPost, etc.
        System.out.println("Requisição recebida!");
        super.service(request, response); // Chama o método service da superclasse
    }
    ```

    **Observação**: Ao estender `HttpServlet`, você geralmente sobrescreve `doGet()` e `doPost()` em vez de `service()`, pois `HttpServlet` já implementa `service()` para despachar para os métodos `doXxx()` apropriados.

4.  **Destruição (`destroy()` método)**: Quando o contêiner de Servlets decide remover um Servlet (por exemplo, quando o servidor é desligado ou a aplicação é desativada), ele chama o método `destroy()` do Servlet. Este método é chamado **apenas uma vez** antes que o Servlet seja removido da memória. É o local para liberar recursos, como fechar conexões de banco de dados, salvar estados ou realizar qualquer limpeza necessária.

    ```java
    public void destroy() {
        // Código de limpeza aqui
        System.out.println("Servlet destruído!");
    }
    ```

**Diagrama Simplificado do Ciclo de Vida do Servlet:**

```mermaid
graph TD
    A[Contêiner Inicia/Primeira Requisição] --> B{Servlet Carregado e Instanciado}
    B --> C[init() é Chamado (uma vez)]
    C --> D{Pronto para Processar Requisições}
    D -- Para cada Requisição --> E[service() é Chamado]
    E -- Despacha para --> F{doGet()/doPost()/etc. é Chamado}
    F -- Resposta Gerada --> D
    D -- Contêiner Desliga/Remove Servlet --> G[destroy() é Chamado (uma vez)]
    G --> H[Servlet Removido da Memória]
```

Compreender este ciclo é vital para gerenciar recursos e garantir o comportamento correto do seu Servlet em diferentes cenários de uso.




### 3.3 Tratamento de Solicitações GET e POST

Em Servlets, as requisições HTTP são tratadas por métodos específicos que correspondem aos verbos HTTP. Os mais comuns são `doGet()` e `doPost()`, que são métodos da classe `HttpServlet` (a classe que a maioria dos Servlets estende).

Ambos os métodos recebem dois parâmetros:

*   `HttpServletRequest request`: Um objeto que encapsula todas as informações da requisição HTTP do cliente, como parâmetros, cabeçalhos, cookies, etc.
*   `HttpServletResponse response`: Um objeto que permite ao Servlet construir e enviar a resposta HTTP de volta ao cliente, definindo cabeçalhos, tipo de conteúdo e escrevendo o corpo da resposta.

#### `doGet()`

O método `doGet()` é invocado pelo contêiner de Servlets quando uma requisição HTTP `GET` é recebida. Requisições GET são tipicamente usadas para:

*   **Recuperar dados**: Quando um usuário digita uma URL diretamente no navegador, clica em um link, ou envia um formulário com `method="get"`.
*   **Não alterar o estado do servidor**: Requisições GET devem ser idempotentes e seguras, ou seja, múltiplas chamadas não devem ter efeitos colaterais além da recuperação de dados.
*   **Parâmetros na URL**: Os parâmetros são anexados à URL após um `?` e separados por `&` (ex: `pagina.html?param1=valor1&param2=valor2`).

**Exemplo de `doGet()`:**

```java
import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/MeuServletGet")
public class MeuServletGet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Servlet GET</title>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h1>Olá do Servlet GET!</h1>");
            String nome = request.getParameter("nome");
            if (nome != null && !nome.isEmpty()) {
                out.println("<p>Bem-vindo, " + nome + "!</p>");
            }
            out.println("</body>");
            out.println("</html>");
        }
    }
}
```

#### `doPost()`

O método `doPost()` é invocado pelo contêiner de Servlets quando uma requisição HTTP `POST` é recebida. Requisições POST são tipicamente usadas para:

*   **Enviar dados para o servidor**: Quando um usuário envia um formulário com `method="post"`.
*   **Alterar o estado do servidor**: Requisições POST podem ter efeitos colaterais, como criar um novo registro em um banco de dados, atualizar informações, etc.
*   **Parâmetros no corpo da requisição**: Os parâmetros não são visíveis na URL, sendo enviados no corpo da requisição HTTP, o que é mais seguro para dados sensíveis e permite o envio de maior volume de dados.

**Exemplo de `doPost()`:**

```java
import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/MeuServletPost")
public class MeuServletPost extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Servlet POST</title>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h1>Dados Recebidos via POST:</h1>");
            String usuario = request.getParameter("usuario");
            String senha = request.getParameter("senha");
            out.println("<p>Usuário: " + usuario + "</p>");
            out.println("<p>Senha: " + senha + "</p>");
            out.println("</body>");
            out.println("</html>");
        }
    }
}
```

#### Quando usar GET vs. POST?

*   **GET**: Use para recuperar dados. É adequado para buscas, links e quando a requisição não altera o estado do servidor. Os dados são visíveis na URL e podem ser marcados como favoritos ou compartilhados.
*   **POST**: Use para enviar dados que alteram o estado do servidor (criação, atualização, exclusão) ou quando os dados são sensíveis (senhas) ou muito grandes. Os dados não são visíveis na URL e não devem ser marcados como favoritos ou compartilhados.

É uma boa prática que um Servlet que lida com formulários implemente ambos os métodos, `doGet()` para exibir o formulário inicialmente e `doPost()` para processar os dados quando o formulário é enviado. Se um formulário POST for acessado via GET, ou vice-versa, o método `service()` padrão de `HttpServlet` retornará um erro 405 (Method Not Allowed), a menos que você sobrescreva esse comportamento.


### 3.4 Parâmetros e Respostas

Em Servlets, a interação com o cliente envolve a obtenção de parâmetros enviados na requisição e o envio de respostas adequadas. O objeto `HttpServletRequest` é usado para acessar os parâmetros da requisição, enquanto o `HttpServletResponse` é usado para construir a resposta.

#### Obtenção de Parâmetros

Os parâmetros são informações enviadas pelo cliente ao servidor, geralmente através de formulários HTML ou diretamente na URL (para requisições GET). O objeto `HttpServletRequest` fornece métodos para acessar esses parâmetros:

*   **`request.getParameter(String name)`**: Retorna o valor do parâmetro especificado como uma `String`. Se o parâmetro não existir, retorna `null`. Se houver múltiplos valores para o mesmo nome de parâmetro (ex: checkboxes), ele retorna apenas o primeiro valor.

    ```java
    String nomeUsuario = request.getParameter("nome");
    String idadeStr = request.getParameter("idade");
    int idade = 0;
    if (idadeStr != null && !idadeStr.isEmpty()) {
        try {
            idade = Integer.parseInt(idadeStr);
        } catch (NumberFormatException e) {
            // Tratar erro de conversão
            System.err.println("Idade inválida: " + idadeStr);
        }
    }
    ```

*   **`request.getParameterValues(String name)`**: Retorna um array de `String` contendo todos os valores para o parâmetro especificado. Útil para campos de formulário que podem ter múltiplos valores, como caixas de seleção (`<input type="checkbox">`) ou listas de seleção múltipla (`<select multiple>`).

    ```java
    String[] interesses = request.getParameterValues("interesse");
    if (interesses != null) {
        for (String interesse : interesses) {
            System.out.println("Interesse: " + interesse);
        }
    }
    ```

*   **`request.getParameterNames()`**: Retorna uma `Enumeration` de `String` contendo os nomes de todos os parâmetros enviados na requisição. Útil para iterar sobre todos os parâmetros sem saber seus nomes antecipadamente.

    ```java
    Enumeration<String> nomesParametros = request.getParameterNames();
    while (nomesParametros.hasMoreElements()) {
        String nome = nomesParametros.nextElement();
        String valor = request.getParameter(nome);
        System.out.println("Parâmetro: " + nome + " = " + valor);
    }
    ```

*   **`request.getParameterMap()`**: Retorna um `Map<String, String[]>` de todos os parâmetros da requisição, onde a chave é o nome do parâmetro e o valor é um array de strings contendo todos os valores para aquele parâmetro.

    ```java
    Map<String, String[]> parametros = request.getParameterMap();
    for (Map.Entry<String, String[]> entry : parametros.entrySet()) {
        String nome = entry.getKey();
        String[] valores = entry.getValue();
        System.out.println("Parâmetro: " + nome + ", Valores: " + Arrays.toString(valores));
    }
    ```

#### Envio de Respostas

Após processar a requisição, o Servlet deve enviar uma resposta de volta ao cliente. O objeto `HttpServletResponse` é usado para construir essa resposta. As etapas típicas incluem:

1.  **Definir o Tipo de Conteúdo (`response.setContentType()`):** Informa ao navegador o tipo de conteúdo que está sendo enviado (ex: HTML, texto puro, JSON, imagem). É crucial para que o navegador renderize a resposta corretamente.

    ```java
    response.setContentType("text/html;charset=UTF-8");
    // ou
    response.setContentType("application/json");
    ```

2.  **Obter o Objeto de Escrita (`response.getWriter()` ou `response.getOutputStream()`):**

    *   **`PrintWriter out = response.getWriter()`**: Usado para enviar dados de texto (HTML, XML, JSON, texto puro) para o cliente. É o mais comum para gerar páginas web.
    *   **`ServletOutputStream out = response.getOutputStream()`**: Usado para enviar dados binários (imagens, arquivos PDF, etc.).

3.  **Escrever o Conteúdo da Resposta:** Use o objeto `PrintWriter` ou `ServletOutputStream` para escrever o corpo da resposta.

    ```java
    // Exemplo com PrintWriter
    try (PrintWriter out = response.getWriter()) {
        out.println("<!DOCTYPE html>");
        out.println("<html>");
        out.println("<head><title>Resposta do Servlet</title></head>");
        out.println("<body>");
        out.println("<h1>Olá, " + nomeUsuario + "!</h1>");
        out.println("</body>");
        out.println("</html>");
    }
    ```

**Exemplo Completo de Servlet processando parâmetros e enviando resposta:**

```java
import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/ProcessaDadosServlet")
public class ProcessaDadosServlet extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");

        // Obtenção de parâmetros
        String nome = request.getParameter("nome");
        String email = request.getParameter("email");
        String[] preferencias = request.getParameterValues("preferencia");

        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Dados Recebidos</title>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h1>Dados Recebidos com Sucesso!</h1>");
            out.println("<p>Nome: " + (nome != null ? nome : "N/A") + "</p>");
            out.println("<p>Email: " + (email != null ? email : "N/A") + "</p>");

            if (preferencias != null && preferencias.length > 0) {
                out.println("<p>Preferências:</p>");
                out.println("<ul>");
                for (String pref : preferencias) {
                    out.println("<li>" + pref + "</li>");
                }
                out.println("</ul>");
            } else {
                out.println("<p>Nenhuma preferência selecionada.</p>");
            }

            out.println("</body>");
            out.println("</html>");
        }
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // Se alguém tentar acessar via GET, redirecionar ou mostrar um formulário
        response.setContentType("text/html;charset=UTF-8");
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head><title>Formulário</title></head>");
            out.println("<body>");
            out.println("<h1>Por favor, preencha o formulário:</h1>");
            out.println("<form action=\"ProcessaDadosServlet\" method=\"post\">");
            out.println("Nome: <input type=\"text\" name=\"nome\"><br>");
            out.println("Email: <input type=\"email\" name=\"email\"><br>");
            out.println("Preferências:<br>");
            out.println("<input type=\"checkbox\" name=\"preferencia\" value=\"Java\"> Java<br>");
            out.println("<input type=\"checkbox\" name=\"preferencia\" value=\"Web\"> Web<br>");
            out.println("<input type=\"checkbox\" name=\"preferencia\" value=\"Banco de Dados\"> Banco de Dados<br>");
            out.println("<input type=\"submit\" value=\"Enviar\">");
            out.println("</form>");
            out.println("</body>");
            out.println("</html>");
        }
    }
}
```

Este exemplo demonstra como um Servlet pode receber dados de um formulário (via POST) e exibir esses dados de volta ao usuário, além de fornecer um formulário simples para entrada de dados (via GET).




### 3.5 Redirecionamento e Encaminhamento

Em aplicações web, é comum a necessidade de transferir o controle de uma requisição de um recurso para outro. Em Servlets, isso pode ser feito de duas maneiras principais: **redirecionamento** e **encaminhamento**. Embora ambos resultem na exibição de uma nova página ao usuário, eles funcionam de maneiras fundamentalmente diferentes e são usados em cenários distintos.

#### Redirecionamento (`sendRedirect()`)

O redirecionamento é uma operação do lado do cliente. Quando um Servlet chama `response.sendRedirect(URL)`, o servidor envia uma resposta HTTP para o navegador do cliente com um código de status de redirecionamento (geralmente 302 Found ou 303 See Other) e a nova URL para a qual o navegador deve ir. O navegador, então, faz uma **nova requisição** para a nova URL.

**Características do Redirecionamento:**

*   **Múltiplas Requisições**: Envolve duas requisições HTTP separadas (a original para o Servlet e a nova para a URL de redirecionamento).
*   **Mudança de URL**: A URL na barra de endereços do navegador muda para a nova URL.
*   **Perda de Atributos da Requisição**: Os atributos definidos no objeto `request` da requisição original são perdidos, pois uma nova requisição é criada.
*   **Pode Redirecionar para Qualquer URL**: Pode redirecionar para recursos dentro ou fora da aplicação web atual.
*   **Método HTTP**: A nova requisição geralmente é um GET, mesmo que a requisição original tenha sido um POST (a menos que o código de status seja 307 Temporary Redirect ou 308 Permanent Redirect, que preservam o método).

**Quando usar `sendRedirect()`:**

*   Após o envio de um formulário POST bem-sucedido (padrão PRG - Post/Redirect/Get) para evitar o reenvio do formulário se o usuário atualizar a página.
*   Para redirecionar o usuário para uma página de login após uma tentativa de acesso a uma área restrita.
*   Para enviar o usuário para um site externo.

**Exemplo de `sendRedirect()`:**

```java
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/RedirecionarServlet")
public class RedirecionarServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // Redireciona para outra página dentro da mesma aplicação
        response.sendRedirect("paginaSucesso.html");
        // Ou para um Servlet diferente
        // response.sendRedirect("OutroServlet");
        // Ou para um site externo
        // response.sendRedirect("https://www.google.com");
    }
}
```

#### Encaminhamento (`forward()`)

O encaminhamento é uma operação do lado do servidor. Quando um Servlet chama `request.getRequestDispatcher("URL").forward(request, response)`, o controle da requisição é transferido internamente para outro recurso (outro Servlet, JSP ou HTML) no mesmo servidor, **sem que o navegador do cliente seja notificado**. O recurso para o qual a requisição foi encaminhada é responsável por gerar a resposta final.

**Características do Encaminhamento:**

*   **Uma Única Requisição**: Envolve apenas uma requisição HTTP do cliente. A transferência de controle ocorre internamente no servidor.
*   **URL Inalterada**: A URL na barra de endereços do navegador permanece a mesma da requisição original.
*   **Preservação de Atributos da Requisição**: Os atributos definidos no objeto `request` são preservados e podem ser acessados pelo recurso para o qual a requisição foi encaminhada. Isso é muito útil para passar dados entre Servlets e JSPs.
*   **Apenas para Recursos Internos**: Só pode encaminhar para recursos dentro da mesma aplicação web no mesmo servidor.
*   **Método HTTP**: O método HTTP original (GET ou POST) é preservado.

**Quando usar `forward()`:**

*   Para passar o controle de um Servlet (que processa a lógica de negócios) para um JSP (que exibe a interface do usuário), passando dados processados.
*   Para modularizar a aplicação, onde diferentes Servlets ou JSPs lidam com partes específicas do processamento da requisição.
*   Para implementar o padrão MVC (Model-View-Controller), onde o Controller (Servlet) encaminha para a View (JSP).

**Exemplo de `forward()`:**

```java
import java.io.IOException;
import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/EncaminharServlet")
public class EncaminharServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // Adiciona um atributo à requisição para ser acessado pela página de destino
        request.setAttribute("mensagem", "Dados enviados via encaminhamento!");

        // Encaminha a requisição para uma JSP
        RequestDispatcher dispatcher = request.getRequestDispatcher("paginaDestino.jsp");
        dispatcher.forward(request, response);

        // Ou para outro Servlet
        // RequestDispatcher dispatcher = request.getRequestDispatcher("OutroServlet");
        // dispatcher.forward(request, response);
    }
}
```

#### Diferenças Chave: Redirecionamento vs. Encaminhamento

| Característica         | `sendRedirect()` (Redirecionamento)       | `forward()` (Encaminhamento)                                |
| :--------------------- | :---------------------------------------- | :---------------------------------------------------------- |
| **Tipo de Operação**   | Lado do Cliente                           | Lado do Servidor                                            |
| **Requisições HTTP**   | Duas (original e nova)                    | Uma                                                         |
| **URL no Navegador**   | Muda para a nova URL                      | Permanece a mesma                                           |
| **Atributos da Requisição** | Perdidos                                  | Preservados                                                 |
| **Escopo**             | Pode ir para qualquer URL (interna/externa) | Apenas para recursos dentro da mesma aplicação no servidor |
| **Método HTTP**        | Nova requisição geralmente GET            | Preserva o método HTTP original                             |

Escolher entre redirecionamento e encaminhamento depende do fluxo de controle desejado e da necessidade de preservar ou descartar os dados da requisição original. Em geral, use `sendRedirect()` para fluxos de trabalho que envolvem mudanças de estado (como após um POST) e `forward()` para delegar a geração da view a outro componente (como um JSP) dentro do mesmo ciclo de requisição/resposta.


### 3.6 Seções

O protocolo HTTP é **sem estado**, o que significa que cada requisição de um cliente para um servidor é tratada de forma independente, sem que o servidor 


lembre-se de requisições anteriores do mesmo cliente. No entanto, para a maioria das aplicações web, é essencial manter o estado do usuário entre múltiplas requisições. Por exemplo, um carrinho de compras, um usuário logado ou preferências de navegação precisam ser persistidos durante a sessão do usuário. É aqui que o conceito de **sessões** se torna fundamental.

#### O que é uma Sessão?

A **sessão** em aplicações web refere-se a um período de interação entre um cliente e um servidor. Durante uma sessão, o servidor pode armazenar informações específicas do usuário, que podem ser acessadas e modificadas em requisições subsequentes. O contêiner de Servlets (como o GlassFish) gerencia as sessões, associando um identificador único (ID de sessão) a cada sessão.

#### Criação e Uso de Sessões (`HttpSession`)

Em Java Servlets, o objeto `HttpSession` é a principal interface para gerenciar sessões. Uma sessão é criada automaticamente pelo contêiner quando você a solicita pela primeira vez, geralmente através do objeto `HttpServletRequest`.

**Obtendo ou Criando uma Sessão:**

Você pode obter o objeto `HttpSession` usando o método `getSession()` do `HttpServletRequest`:

```java
HttpSession session = request.getSession();
// ou
// HttpSession session = request.getSession(true); // O mesmo que o anterior, cria se não existir
```

Se uma sessão já existe para o cliente atual, `request.getSession()` retorna essa sessão. Se não existir, uma nova sessão é criada. Se você chamar `request.getSession(false)`, ele retornará a sessão existente ou `null` se nenhuma sessão existir (não criará uma nova).

**Armazenando e Recuperando Atributos na Sessão:**

Uma vez que você tem um objeto `HttpSession`, você pode armazenar e recuperar objetos nele usando métodos semelhantes aos de um `Map`:

*   **`session.setAttribute(String name, Object value)`**: Armazena um objeto na sessão com um nome específico.
*   **`Object value = session.getAttribute(String name)`**: Recupera um objeto da sessão pelo seu nome. Você precisará fazer um *cast* para o tipo correto.
*   **`session.removeAttribute(String name)`**: Remove um objeto da sessão.

**Exemplo de Uso de Sessão:**

```java
import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@WebServlet("/ContadorVisitasServlet")
public class ContadorVisitasServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");

        // Obtém a sessão atual ou cria uma nova se não existir
        HttpSession session = request.getSession();

        // Obtém o contador de visitas da sessão
        Integer contador = (Integer) session.getAttribute("contadorVisitas");

        // Se o contador for nulo (primeira visita na sessão), inicializa-o
        if (contador == null) {
            contador = 1;
        } else {
            // Caso contrário, incrementa o contador
            contador++;
        }

        // Armazena o contador atualizado na sessão
        session.setAttribute("contadorVisitas", contador);

        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Contador de Visitas</title>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h1>Você visitou esta página " + contador + " vezes nesta sessão.</h1>");
            out.println("<p>ID da Sessão: " + session.getId() + "</p>");
            out.println("<p>Tempo de Inatividade Máximo (segundos): " + session.getMaxInactiveInterval() + "</p>");
            out.println("</body>");
            out.println("</html>");
        }
    }
}
```

#### Gerenciamento de Sessão

O contêiner de Servlets gerencia as sessões de várias maneiras para garantir que elas funcionem corretamente e não consumam recursos desnecessariamente:

*   **ID de Sessão**: O contêiner atribui um ID de sessão único a cada nova sessão. Este ID é geralmente transmitido entre o cliente e o servidor via cookies (o método mais comum e preferencial) ou, em casos onde cookies estão desabilitados, via reescrita de URL (adicionando o ID da sessão à URL).

*   **Tempo de Inatividade (`session.setMaxInactiveInterval()`):** As sessões têm um tempo de vida limitado. Se um cliente não fizer nenhuma requisição para o servidor por um determinado período (tempo de inatividade), a sessão é automaticamente invalidada pelo contêiner para liberar recursos. Você pode configurar esse tempo programaticamente ou no arquivo `web.xml` da sua aplicação.

    ```java
    // Define o tempo máximo de inatividade para 30 minutos (1800 segundos)
    session.setMaxInactiveInterval(1800);
    ```

*   **Invalidação Explícita (`session.invalidate()`):** Você pode invalidar uma sessão programaticamente, o que remove todos os atributos da sessão e a torna inutilizável. Isso é crucial para segurança, especialmente após um logout de usuário.

    ```java
    // Após o logout do usuário
    session.invalidate();
    ```

*   **Eventos de Sessão**: O Java Servlet API permite que você crie *listeners* para eventos de sessão (como criação, destruição ou adição/remoção de atributos), o que pode ser útil para auditoria ou gerenciamento de recursos.

O gerenciamento eficaz de sessões é vital para a segurança e o desempenho de aplicações web, garantindo que os dados do usuário sejam mantidos de forma consistente e que os recursos do servidor sejam utilizados de forma otimizada.



