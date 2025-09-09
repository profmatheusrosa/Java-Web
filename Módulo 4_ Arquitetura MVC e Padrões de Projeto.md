# Módulo 4: Arquitetura MVC e Padrões de Projeto

Este módulo explora a Arquitetura Model-View-Controller (MVC) e padrões de projeto relacionados, que são fundamentais para o desenvolvimento de aplicações web robustas, escaláveis e de fácil manutenção.

## Conteúdo

6. Arquitetura MVC e Padrões de Projeto
    6.1 Visão Geral
    6.2 Camada Modelo
    6.3 Camada de Acesso a Dados (DAO)
    6.4 Camada Visão
    6.5 Camada Controle




### 6.1 Visão Geral

A **Arquitetura Model-View-Controller (MVC)** é um padrão de arquitetura de software que separa a lógica de uma aplicação em três componentes interconectados: **Model (Modelo)**, **View (Visão)** e **Controller (Controlador)**. O objetivo principal do MVC é promover a separação de preocupações, tornando o código mais organizado, modular, fácil de manter e de testar.

#### Conceito

*   **Model (Modelo)**: Representa os dados da aplicação e as regras de negócio. É responsável por gerenciar o estado dos dados, a lógica de negócios e a interação com o banco de dados. O Modelo é independente da interface do usuário e não sabe como os dados serão apresentados.

*   **View (Visão)**: É a camada de apresentação da aplicação. Ela exibe os dados do Modelo para o usuário e é responsável pela interface gráfica. A Visão não contém lógica de negócios; ela apenas apresenta o que o Modelo fornece. Em aplicações web Java, as JSPs (JavaServer Pages) são frequentemente usadas como Views.

*   **Controller (Controlador)**: Atua como um intermediário entre o Modelo e a Visão. Ele recebe as requisições do usuário (geralmente via navegador), interpreta-as, interage com o Modelo para processar a lógica de negócios e, em seguida, seleciona a Visão apropriada para exibir a resposta ao usuário. Em aplicações web Java, os Servlets são comumente usados como Controladores.

#### Fluxo de Interação no MVC

O fluxo típico de interação em uma aplicação MVC é o seguinte:

1.  **O Usuário Interage com a Visão**: O usuário realiza uma ação na interface (ex: clica em um botão, envia um formulário).
2.  **A Requisição é Enviada ao Controlador**: A ação do usuário é capturada e enviada ao Controlador (ex: um Servlet).
3.  **O Controlador Processa a Requisição**: O Controlador recebe a requisição, extrai os dados necessários e decide qual lógica de negócios deve ser executada.
4.  **O Controlador Interage com o Modelo**: O Controlador invoca métodos no Modelo para realizar operações de negócios (ex: salvar dados, buscar informações no banco de dados).
5.  **O Modelo Processa a Lógica de Negócios**: O Modelo executa a lógica de negócios, atualiza seu estado e, se necessário, interage com o banco de dados.
6.  **O Modelo Retorna Dados ao Controlador**: O Modelo retorna os resultados da operação ao Controlador.
7.  **O Controlador Seleciona a Visão**: Com base nos resultados do Modelo, o Controlador decide qual Visão deve ser apresentada ao usuário.
8.  **O Controlador Encaminha Dados para a Visão**: O Controlador passa os dados do Modelo para a Visão (geralmente através de atributos na requisição ou sessão).
9.  **A Visão Renderiza a Resposta**: A Visão utiliza os dados fornecidos pelo Controlador para gerar a interface do usuário (ex: uma página HTML) e a envia de volta ao navegador do usuário.

```mermaid
graph TD
    A[Usuário] --> B(Requisição HTTP)
    B --> C{Controlador (Servlet)}
    C --> D[Chama Lógica de Negócios]
    D --> E[Modelo (JavaBeans/Classes de Negócio)]
    E --> F[Acessa Dados (DAO/Banco de Dados)]
    F --> E
    E --> C
    C --> G[Seleciona Visão]
    G --> H[Visão (JSP)]
    H --> I(Resposta HTTP)
    I --> A
```

#### Benefícios da Arquitetura MVC

*   **Separação de Preocupações**: Cada componente tem uma responsabilidade clara e única, o que torna o código mais fácil de entender e gerenciar.
*   **Reusabilidade**: Os componentes (especialmente o Modelo) podem ser reutilizados em diferentes Visões ou Controladores.
*   **Manutenibilidade**: Alterações em uma camada (ex: mudar o design da Visão) geralmente não afetam as outras camadas (Modelo ou Controlador), facilitando a manutenção.
*   **Testabilidade**: A separação permite que cada componente seja testado de forma independente, o que melhora a qualidade do software.
*   **Flexibilidade**: Facilita a adaptação da aplicação a novas tecnologias ou requisitos, como a mudança de um banco de dados ou a adição de uma nova interface de usuário.
*   **Desenvolvimento em Equipe**: Diferentes membros da equipe podem trabalhar em diferentes camadas simultaneamente sem grandes conflitos.

O padrão MVC é amplamente adotado no desenvolvimento web e é a base para muitos frameworks modernos (como Spring MVC, Struts). Compreendê-lo é essencial para construir aplicações web Java escaláveis e de alta qualidade.




### 6.2 Camada Modelo

A **Camada Modelo** é o coração da arquitetura MVC. Ela é responsável por representar os dados da aplicação, implementar a lógica de negócios e gerenciar o estado da aplicação. O Modelo é completamente independente da interface do usuário e não tem conhecimento de como os dados serão exibidos ou como as requisições serão recebidas. Sua principal função é garantir a integridade e a consistência dos dados.

#### Componentes da Camada Modelo

A camada Modelo geralmente é composta por:

1.  **Classes Java (POJOs/JavaBeans)**: São classes simples que representam as entidades de negócio da aplicação. Por exemplo, em um sistema de e-commerce, você teria classes como `Produto`, `Cliente`, `Pedido`, etc. Essas classes contêm atributos (propriedades) que armazenam os dados e métodos (getters e setters) para acessá-los e modificá-los. Elas também podem conter métodos para validação de dados ou outras lógicas de negócio básicas relacionadas à própria entidade.

    **Exemplo de uma classe `Produto` (POJO - Plain Old Java Object):**

    ```java
    // src/main/java/com/example/model/Produto.java
    package com.example.model;

    public class Produto {
        private int id;
        private String nome;
        private double preco;
        private int estoque;

        public Produto() { /* Construtor padrão */ }

        public Produto(int id, String nome, double preco, int estoque) {
            this.id = id;
            this.nome = nome;
            this.preco = preco;
            this.estoque = estoque;
        }

        // Getters e Setters
        public int getId() {
            return id;
        }

        public void setId(int id) {
            this.id = id;
        }

        public String getNome() {
            return nome;
        }

        public void setNome(String nome) {
            this.nome = nome;
        }

        public double getPreco() {
            return preco;
        }

        public void setPreco(double preco) {
            this.preco = preco;
        }

        public int getEstoque() {
            return estoque;
        }

        public void setEstoque(int estoque) {
            this.estoque = estoque;
        }

        // Lógica de negócio relacionada ao produto
        public boolean isDisponivel() {
            return this.estoque > 0;
        }

        public void reduzirEstoque(int quantidade) {
            if (this.estoque >= quantidade) {
                this.estoque -= quantidade;
            } else {
                throw new IllegalArgumentException("Estoque insuficiente para a quantidade solicitada.");
            }
        }
    }
    ```

2.  **Classes de Lógica de Negócios (Business Logic/Service Layer)**: Para aplicações mais complexas, a lógica de negócios que envolve múltiplas entidades ou operações mais elaboradas é encapsulada em classes de serviço separadas. Essas classes orquestram as operações, aplicando regras de negócio, realizando validações complexas e coordenando as interações com a camada de acesso a dados.

    **Exemplo de uma classe `ProdutoService`:**

    ```java
    // src/main/java/com/example/service/ProdutoService.java
    package com.example.service;

    import com.example.model.Produto;
    import com.example.dao.ProdutoDAO; // Assumindo a existência de um DAO

    public class ProdutoService {
        private ProdutoDAO produtoDAO; // Injeção de dependência ou instanciamento

        public ProdutoService() {
            this.produtoDAO = new ProdutoDAO(); // Exemplo simples, em apps reais use injeção
        }

        public Produto getProdutoPorId(int id) {
            // Pode adicionar lógica de validação ou cache aqui
            return produtoDAO.buscarPorId(id);
        }

        public void adicionarProduto(Produto produto) {
            // Validações de negócio antes de salvar
            if (produto.getPreco() <= 0) {
                throw new IllegalArgumentException("Preço do produto deve ser positivo.");
            }
            if (produto.getEstoque() < 0) {
                throw new IllegalArgumentException("Estoque não pode ser negativo.");
            }
            // Outras regras de negócio...
            produtoDAO.salvar(produto);
        }

        public void atualizarEstoque(int produtoId, int quantidade) {
            Produto produto = produtoDAO.buscarPorId(produtoId);
            if (produto == null) {
                throw new IllegalArgumentException("Produto não encontrado.");
            }
            produto.reduzirEstoque(quantidade); // Lógica de negócio na entidade
            produtoDAO.atualizar(produto);
        }

        // Outros métodos de lógica de negócio
    }
    ```

#### Responsabilidades da Camada Modelo

*   **Representação de Dados**: Definir a estrutura dos dados da aplicação (entidades).
*   **Lógica de Negócios**: Implementar as regras e validações que governam o comportamento dos dados e as operações da aplicação.
*   **Gerenciamento de Estado**: Manter o estado dos dados e garantir sua consistência.
*   **Persistência (Indireta)**: Embora o Modelo não lide diretamente com o banco de dados, ele define as operações que precisam ser realizadas e delega a responsabilidade de persistência a uma camada de acesso a dados (DAO), que veremos a seguir.
*   **Notificação de Mudanças (Opcional)**: Em algumas implementações MVC, o Modelo pode notificar a Visão ou o Controlador sobre mudanças em seu estado, embora em aplicações web baseadas em requisição/resposta, isso seja menos comum do que em aplicações desktop.

Ao manter a lógica de negócios e a representação de dados separadas da interface do usuário, a camada Modelo se torna altamente reutilizável e testável, contribuindo significativamente para a robustez e manutenibilidade da aplicação.


### 6.3 Camada de Acesso a Dados (DAO)

A **Camada de Acesso a Dados (DAO - Data Access Object)** é um padrão de projeto que abstrai e encapsula todo o acesso a dados de uma aplicação. Seu principal objetivo é separar a lógica de negócios da lógica de persistência, tornando a aplicação mais modular, flexível e fácil de manter. Ao usar o padrão DAO, as classes de negócio não precisam saber os detalhes de como os dados são armazenados ou recuperados (seja de um banco de dados relacional, um arquivo XML, um serviço web, etc.).

#### Conceito e Finalidade

O padrão DAO define uma interface para as operações de acesso a dados (CRUD - Create, Read, Update, Delete) para uma entidade específica. A implementação dessa interface contém o código real para interagir com a fonte de dados. Isso traz vários benefícios:

*   **Separação de Preocupações**: A lógica de persistência é isolada da lógica de negócios.
*   **Flexibilidade**: Se a tecnologia de persistência mudar (ex: de JDBC para JPA, ou de um banco de dados MySQL para PostgreSQL), apenas a implementação do DAO precisa ser alterada, e não as classes de negócio.
*   **Manutenibilidade**: O código de acesso a dados é centralizado e mais fácil de gerenciar.
*   **Testabilidade**: As classes de negócio podem ser testadas independentemente da fonte de dados real, usando implementações mock do DAO.

#### Estrutura do Padrão DAO

Normalmente, o padrão DAO envolve:

1.  **Interface DAO**: Define os métodos abstratos para as operações CRUD para uma entidade.
2.  **Classe de Implementação DAO**: Implementa a interface DAO, contendo o código específico para interagir com a fonte de dados (ex: usando JDBC para um banco de dados relacional).

#### Exemplo de Implementação DAO (com JDBC)

Vamos usar a entidade `Produto` do exemplo anterior. Precisaremos de uma interface `ProdutoDAO` e uma implementação `ProdutoDAOImpl`.

**1. Interface `ProdutoDAO`:**

```java
// src/main/java/com/example/dao/ProdutoDAO.java
package com.example.dao;

import com.example.model.Produto;
import java.util.List;

public interface ProdutoDAO {
    void salvar(Produto produto);
    Produto buscarPorId(int id);
    List<Produto> buscarTodos();
    void atualizar(Produto produto);
    void deletar(int id);
}
```

**2. Implementação `ProdutoDAOImpl` (usando JDBC):**

```java
// src/main/java/com/example/dao/impl/ProdutoDAOImpl.java
package com.example.dao.impl;

import com.example.dao.ProdutoDAO;
import com.example.model.Produto;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class ProdutoDAOImpl implements ProdutoDAO {

    // Configurações de conexão com o banco de dados
    private static final String JDBC_URL = "jdbc:mysql://localhost:3306/meubanco";
    private static final String JDBC_USER = "root";
    private static final String JDBC_PASSWORD = "password";

    private Connection getConnection() throws SQLException {
        // Em aplicações reais, use um pool de conexões (ex: C3P0, HikariCP)
        return DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD);
    }

    @Override
    public void salvar(Produto produto) {
        String sql = "INSERT INTO produtos (nome, preco, estoque) VALUES (?, ?, ?)";
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)) {

            stmt.setString(1, produto.getNome());
            stmt.setDouble(2, produto.getPreco());
            stmt.setInt(3, produto.getEstoque());
            stmt.executeUpdate();

            try (ResultSet generatedKeys = stmt.getGeneratedKeys()) {
                if (generatedKeys.next()) {
                    produto.setId(generatedKeys.getInt(1));
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
            // Tratar exceção (logar, relançar uma exceção de negócio, etc.)
        }
    }

    @Override
    public Produto buscarPorId(int id) {
        String sql = "SELECT id, nome, preco, estoque FROM produtos WHERE id = ?";
        Produto produto = null;
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, id);
            try (ResultSet rs = stmt.executeQuery()) {
                if (rs.next()) {
                    produto = new Produto();
                    produto.setId(rs.getInt("id"));
                    produto.setNome(rs.getString("nome"));
                    produto.setPreco(rs.getDouble("preco"));
                    produto.setEstoque(rs.getInt("estoque"));
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return produto;
    }

    @Override
    public List<Produto> buscarTodos() {
        String sql = "SELECT id, nome, preco, estoque FROM produtos";
        List<Produto> produtos = new ArrayList<>();
        try (Connection conn = getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {
                Produto produto = new Produto();
                produto.setId(rs.getInt("id"));
                produto.setNome(rs.getString("nome"));
                produto.setPreco(rs.getDouble("preco"));
                produto.setEstoque(rs.getInt("estoque"));
                produtos.add(produto);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return produtos;
    }

    @Override
    public void atualizar(Produto produto) {
        String sql = "UPDATE produtos SET nome = ?, preco = ?, estoque = ? WHERE id = ?";
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, produto.getNome());
            stmt.setDouble(2, produto.getPreco());
            stmt.setInt(3, produto.getEstoque());
            stmt.setInt(4, produto.getId());
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void deletar(int id) {
        String sql = "DELETE FROM produtos WHERE id = ?";
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, id);
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

#### Integração com a Camada Modelo (Serviço)

A camada de serviço (Business Logic) que vimos anteriormente (`ProdutoService`) utilizaria o DAO para realizar as operações de persistência, sem se preocupar com os detalhes de implementação do banco de dados.

```java
// Exemplo de uso no ProdutoService
// ...
public class ProdutoService {
    private ProdutoDAO produtoDAO; 

    public ProdutoService() {
        this.produtoDAO = new ProdutoDAOImpl(); // Instancia a implementação do DAO
    }

    public void adicionarProduto(Produto produto) {
        // ... validações de negócio ...
        produtoDAO.salvar(produto); // Delega a persistência ao DAO
    }

    public Produto getProdutoPorId(int id) {
        return produtoDAO.buscarPorId(id); // Delega a busca ao DAO
    }
    // ...
}
```

Ao adotar o padrão DAO, a aplicação ganha em organização, flexibilidade e facilidade de manutenção, pois a complexidade do acesso a dados é encapsulada em uma camada dedicada. Isso é crucial para construir aplicações robustas e escaláveis.


### 6.4 Camada Visão

A **Camada Visão (View)** na arquitetura MVC é responsável por apresentar os dados ao usuário. Ela é a interface com a qual o usuário interage e exibe as informações que vêm do Modelo. A principal característica da Visão é que ela **não contém lógica de negócios**; seu papel é puramente de apresentação. Em aplicações web Java, as **JavaServer Pages (JSP)** são a tecnologia mais comum para implementar a camada de Visão.

#### Responsabilidades da Camada Visão

*   **Renderizar a Interface do Usuário**: Gerar o HTML, CSS e JavaScript necessários para exibir a página web no navegador do cliente.
*   **Exibir Dados do Modelo**: Receber os dados processados pelo Controlador (que os obteve do Modelo) e apresentá-los de forma legível e organizada ao usuário.
*   **Capturar Interações do Usuário**: Coletar a entrada do usuário (por exemplo, através de formulários) e enviá-la de volta ao Controlador para processamento.
*   **Não Conter Lógica de Negócios**: A Visão deve ser o mais "burra" possível, focando apenas na apresentação. Qualquer decisão sobre como os dados são obtidos ou manipulados deve ser delegada ao Controlador ou ao Modelo.

#### Implementação da Visão com JSP

As JSPs são ideais para a camada de Visão porque permitem a mistura de código HTML/XHTML estático com elementos dinâmicos, como expressões EL e tags JSTL, para exibir dados de forma eficiente. O contêiner JSP compila a JSP em um Servlet, que então gera a resposta HTML.

**Exemplo de uma JSP como Visão:**

Suponha que um Servlet (Controlador) tenha processado uma requisição e colocado uma lista de produtos (obtida do Modelo) no escopo da requisição. A JSP `listaProdutos.jsp` seria responsável por exibir esses produtos.

**Servlet (Controlador) - Exemplo Simplificado:**

```java
// Em um Servlet (Controlador)
// ...
import com.example.model.Produto;
import com.example.service.ProdutoService;
import java.io.IOException;
import java.util.List;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/produtos")
public class ProdutoController extends HttpServlet {

    private ProdutoService produtoService = new ProdutoService(); // Instancia o serviço

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        // 1. Interage com o Modelo (ProdutoService) para obter dados
        List<Produto> produtos = produtoService.buscarTodosProdutos(); // Supondo um método no serviço
        
        // 2. Coloca os dados no escopo da requisição para a Visão acessar
        request.setAttribute("listaDeProdutos", produtos);
        
        // 3. Encaminha para a Visão (JSP)
        request.getRequestDispatcher("/WEB-INF/views/listaProdutos.jsp").forward(request, response);
    }
}
```

**JSP (Visão) - `listaProdutos.jsp` (localizada em `/WEB-INF/views/` para segurança):**

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Lista de Produtos</title>
    <style>
        table {
            width: 100%;
            border-collapse: collapse;
        }
        th, td {
            border: 1px solid black;
            padding: 8px;
            text-align: left;
        }
        th {
            background-color: #f2f2f2;
        }
    </style>
</head>
<body>
    <h1>Produtos Disponíveis</h1>

    <c:if test="${empty listaDeProdutos}">
        <p>Nenhum produto encontrado.</p>
    </c:if>

    <c:if test="${not empty listaDeProdutos}">
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Nome</th>
                    <th>Preço</th>
                    <th>Estoque</th>
                </tr>
            </thead>
            <tbody>
                <c:forEach var="produto" items="${listaDeProdutos}">
                    <tr>
                        <td>${produto.id}</td>
                        <td>${produto.nome}</td>
                        <td>${produto.preco}</td>
                        <td>${produto.estoque}</td>
                    </tr>
                </c:forEach>
            </tbody>
        </table>
    </c:if>

    <p><a href="adicionarProduto.jsp">Adicionar Novo Produto</a></p>

</body>
</html>
```

Neste exemplo, a JSP `listaProdutos.jsp`:

*   Utiliza a diretiva `taglib` para importar a biblioteca JSTL Core (`c`).
*   Usa a tag `<c:if>` para verificar se a lista de produtos está vazia.
*   Usa a tag `<c:forEach>` para iterar sobre a `listaDeProdutos` (que foi colocada no `requestScope` pelo Servlet).
*   Acessa as propriedades de cada objeto `produto` usando a Linguagem de Expressão (EL), como `${produto.id}`, `${produto.nome}`, etc.
*   Contém apenas HTML e tags JSP/JSTL para apresentação, sem lógica de negócios complexa.

#### Boas Práticas para a Camada Visão

*   **Mínimo de Lógica**: Mantenha a lógica na Visão no mínimo essencial para apresentação (loops, condicionais simples para exibição). Evite cálculos complexos ou acesso direto a bancos de dados.
*   **Uso de EL e JSTL**: Prefira a Linguagem de Expressão (EL) e a JSTL em vez de scriptlets para acessar e exibir dados. Isso torna o código mais limpo e legível.
*   **Separação de Recursos**: Mantenha as JSPs em um diretório seguro (como `/WEB-INF/views/`) para que não possam ser acessadas diretamente pelo navegador, forçando todas as requisições a passarem pelo Controlador.
*   **Reutilização de Componentes**: Use `jsp:include` ou diretivas `include` para reutilizar partes da interface (cabeçalhos, rodapés, menus) em várias páginas.
*   **Validação do Lado do Cliente**: Use JavaScript para validações de formulário no lado do cliente para melhorar a experiência do usuário, mas sempre revalide no servidor.

Ao seguir esses princípios, a camada de Visão se torna eficiente, fácil de manter e de colaborar, complementando as outras camadas da arquitetura MVC para construir aplicações web robustas.


### 6.5 Camada Controle

A **Camada Controle (Controller)** é o componente central da arquitetura MVC em aplicações web. Ela atua como um intermediário entre a Visão e o Modelo, recebendo as requisições do cliente, processando-as, interagindo com o Modelo para executar a lógica de negócios e, finalmente, selecionando a Visão apropriada para gerar a resposta. Em aplicações web Java, os **Servlets** são a escolha natural para implementar a camada de Controle.

#### Responsabilidades da Camada Controle

*   **Receber Requisições**: O Controlador é o primeiro ponto de contato para as requisições HTTP do cliente.
*   **Interpretar Requisições**: Analisar a requisição (URL, parâmetros, método HTTP) para determinar a ação a ser executada.
*   **Validar Entrada (Inicial)**: Realizar validações básicas na entrada do usuário antes de passá-la para o Modelo.
*   **Interagir com o Modelo**: Invocar métodos na camada de serviço (Modelo) para executar a lógica de negócios e obter ou manipular dados.
*   **Gerenciar o Fluxo**: Decidir qual Visão deve ser apresentada ao usuário com base no resultado da operação do Modelo. Isso pode envolver encaminhar (`forward`) para uma JSP ou redirecionar (`sendRedirect`) para outra URL.
*   **Preparar Dados para a Visão**: Colocar os dados necessários (obtidos do Modelo) no escopo da requisição ou sessão para que a Visão possa acessá-los e exibi-los.

#### Implementação do Controlador com Servlets

Servlets são ideais para a camada de Controle devido à sua capacidade de lidar com requisições HTTP, acessar parâmetros, gerenciar sessões e encaminhar para outras páginas. Eles atuam como "porteiros" que direcionam o fluxo da aplicação.

**Exemplo de um Servlet como Controlador:**

Vamos considerar um cenário onde um usuário envia um formulário de cadastro de produto. O Servlet `ProdutoController` receberia essa requisição.

**`ProdutoController.java`:**

```java
// src/main/java/com/example/controller/ProdutoController.java
package com.example.controller;

import com.example.model.Produto;
import com.example.service.ProdutoService;
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/produto") // Mapeia este Servlet para a URL /produto
public class ProdutoController extends HttpServlet {

    private ProdutoService produtoService; // Instância do serviço (Modelo)

    @Override
    public void init() throws ServletException {
        super.init();
        // Inicializa o serviço. Em aplicações maiores, use injeção de dependência.
        this.produtoService = new ProdutoService(); 
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        String acao = request.getParameter("acao");

        if (acao == null) {
            acao = "listar"; // Ação padrão
        }

        switch (acao) {
            case "listar":
                listarProdutos(request, response);
                break;
            case "formAdicionar":
                mostrarFormularioAdicionar(request, response);
                break;
            case "editar":
                mostrarFormularioEditar(request, response);
                break;
            case "deletar":
                deletarProduto(request, response);
                break;
            default:
                response.sendError(HttpServletResponse.SC_NOT_FOUND);
                break;
        }
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        String acao = request.getParameter("acao");

        if (acao == null) {
            acao = "adicionar"; // Ação padrão para POST
        }

        switch (acao) {
            case "adicionar":
                adicionarProduto(request, response);
                break;
            case "atualizar":
                atualizarProduto(request, response);
                break;
            default:
                response.sendError(HttpServletResponse.SC_NOT_FOUND);
                break;
        }
    }

    private void listarProdutos(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // 1. Interage com o Modelo para obter dados
        List<Produto> produtos = produtoService.buscarTodosProdutos();
        
        // 2. Prepara dados para a Visão
        request.setAttribute("listaDeProdutos", produtos);
        
        // 3. Encaminha para a Visão
        request.getRequestDispatcher("/WEB-INF/views/listaProdutos.jsp").forward(request, response);
    }

    private void mostrarFormularioAdicionar(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        request.getRequestDispatcher("/WEB-INF/views/formProduto.jsp").forward(request, response);
    }

    private void adicionarProduto(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // 1. Obtém parâmetros da requisição
        String nome = request.getParameter("nome");
        double preco = Double.parseDouble(request.getParameter("preco"));
        int estoque = Integer.parseInt(request.getParameter("estoque"));

        // 2. Cria objeto do Modelo
        Produto novoProduto = new Produto(0, nome, preco, estoque);

        try {
            // 3. Interage com o Modelo (serviço) para executar a lógica de negócios
            produtoService.adicionarProduto(novoProduto);
            // 4. Redireciona para evitar reenvio do formulário (PRG Pattern)
            response.sendRedirect(request.getContextPath() + "/produto?acao=listar");
        } catch (IllegalArgumentException e) {
            // Trata erros de validação ou negócio
            request.setAttribute("erro", e.getMessage());
            request.setAttribute("produto", novoProduto); // Mantém dados preenchidos
            request.getRequestDispatcher("/WEB-INF/views/formProduto.jsp").forward(request, response);
        }
    }

    private void mostrarFormularioEditar(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        int id = Integer.parseInt(request.getParameter("id"));
        Produto produto = produtoService.buscarPorId(id);
        if (produto != null) {
            request.setAttribute("produto", produto);
            request.getRequestDispatcher("/WEB-INF/views/formProduto.jsp").forward(request, response);
        } else {
            response.sendError(HttpServletResponse.SC_NOT_FOUND, "Produto não encontrado.");
        }
    }

    private void atualizarProduto(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        int id = Integer.parseInt(request.getParameter("id"));
        String nome = request.getParameter("nome");
        double preco = Double.parseDouble(request.getParameter("preco"));
        int estoque = Integer.parseInt(request.getParameter("estoque"));

        Produto produtoAtualizado = new Produto(id, nome, preco, estoque);

        try {
            produtoService.atualizarProduto(produtoAtualizado); // Supondo um método de atualização
            response.sendRedirect(request.getContextPath() + "/produto?acao=listar");
        } catch (IllegalArgumentException e) {
            request.setAttribute("erro", e.getMessage());
            request.setAttribute("produto", produtoAtualizado);
            request.getRequestDispatcher("/WEB-INF/views/formProduto.jsp").forward(request, response);
        }
    }

    private void deletarProduto(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        int id = Integer.parseInt(request.getParameter("id"));
        try {
            produtoService.deletarProduto(id); // Supondo um método de deleção
            response.sendRedirect(request.getContextPath() + "/produto?acao=listar");
        } catch (IllegalArgumentException e) {
            response.sendError(HttpServletResponse.SC_INTERNAL_SERVER_ERROR, e.getMessage());
        }
    }
}
```

**Pontos Chave na Implementação do Controlador:**

*   **Mapeamento de URL**: A anotação `@WebServlet("/produto")` mapeia o Servlet para a URL `/produto`, o que significa que todas as requisições para `/produto` serão tratadas por este Servlet.
*   **Métodos `doGet()` e `doPost()`**: O Controlador implementa os métodos `doGet()` e `doPost()` para lidar com diferentes tipos de requisições HTTP. Dentro deles, ele pode usar um parâmetro `acao` para rotear para diferentes métodos privados que executam a lógica específica.
*   **Interação com o Serviço (Modelo)**: O Controlador não acessa o DAO diretamente. Em vez disso, ele interage com a camada de serviço (`ProdutoService`), que por sua vez utiliza o DAO. Isso mantém a separação de responsabilidades.
*   **Preparação de Dados para a Visão**: Após a interação com o Modelo, o Controlador coloca os dados necessários (ex: `listaDeProdutos`, `produto`, `erro`) como atributos no objeto `request` (ou `session`, se necessário) para que a Visão possa acessá-los via EL.
*   **Encaminhamento (`forward`)**: Para exibir uma página JSP, o Controlador usa `request.getRequestDispatcher().forward(request, response)`. Isso transfere o controle para a JSP, mantendo os atributos da requisição e a URL no navegador.
*   **Redirecionamento (`sendRedirect`)**: Após operações que modificam o estado do servidor (como adicionar, atualizar, deletar), o Controlador geralmente usa `response.sendRedirect()`. Isso implementa o padrão PRG (Post/Redirect/Get), que evita o reenvio de formulários se o usuário atualizar a página e garante que a URL no navegador reflita o estado atual da aplicação.
*   **Tratamento de Erros**: O Controlador também é responsável por capturar exceções da camada de serviço e decidir como apresentá-las ao usuário (ex: encaminhar para uma página de erro, exibir mensagem na mesma página).

Ao centralizar o controle do fluxo da aplicação e a orquestração entre o Modelo e a Visão, a camada de Controle garante que a aplicação web seja organizada, responsiva e siga o padrão MVC de forma eficaz.



