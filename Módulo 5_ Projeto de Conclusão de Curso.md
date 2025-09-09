# Módulo 5: Projeto de Conclusão de Curso

Este módulo é dedicado ao desenvolvimento de um projeto prático que integra todos os conceitos e tecnologias abordados nos módulos anteriores. O objetivo é consolidar o aprendizado e demonstrar a capacidade de construir uma aplicação web Java completa utilizando o padrão MVC, Servlets, JSP, DAO, e as tecnologias do lado do cliente.

## Conteúdo

7. Projeto de Conclusão de Curso
    7.1 Desenvolvimento




### 7.1 Desenvolvimento

O projeto de conclusão de curso será uma aplicação web de **Gerenciamento de Tarefas (To-Do List)**. Esta aplicação permitirá aos usuários adicionar, listar, editar e excluir tarefas, demonstrando a interação completa entre as camadas do MVC, o acesso a dados e a interface do usuário.

#### Requisitos Funcionais

*   **Adicionar Tarefa**: Permitir que o usuário insira uma nova tarefa com um título e uma descrição.
*   **Listar Tarefas**: Exibir todas as tarefas existentes em uma tabela, com opções para editar e excluir.
*   **Editar Tarefa**: Permitir que o usuário modifique o título e a descrição de uma tarefa existente.
*   **Excluir Tarefa**: Remover uma tarefa da lista.
*   **Marcar como Concluída/Pendente**: Adicionar um status para a tarefa e a capacidade de alterná-lo.

#### Tecnologias a Serem Utilizadas

*   **Backend**: Java, Servlets, JDBC
*   **Frontend**: JSP, HTML, CSS, JavaScript
*   **Banco de Dados**: MySQL (ou outro banco de dados relacional de sua escolha)
*   **Servidor de Aplicação**: GlassFish
*   **IDE**: NetBeans

#### Estrutura do Projeto (NetBeans)

Ao criar um novo projeto web no NetBeans, a estrutura será similar a esta:

```
MeuProjetoGerenciadorTarefas
├── src
│   └── main
│       └── java
│           └── com
│               └── exemplo
│                   ├── controller  (Servlets - Camada Controle)
│                   ├── model       (POJOs/JavaBeans - Camada Modelo)
│                   ├── service     (Lógica de Negócios - Camada Modelo)
│                   └── dao         (Interfaces DAO - Camada Modelo)
│                       └── impl    (Implementações DAO - Camada Modelo)
│       └── webapp
│           ├── WEB-INF
│           │   ├── web.xml
│           │   └── views       (JSPs - Camada Visão)
│           │       ├── listaTarefas.jsp
│           │       ├── formTarefa.jsp
│           │       └── error.jsp
│           ├── css
│           │   └── style.css
│           ├── js
│           │   └── script.js
│           └── index.html (ou index.jsp)
└── pom.xml (se for um projeto Maven)
```

#### Passos para o Desenvolvimento

1.  **Configuração do Ambiente**: Certifique-se de ter o NetBeans e o GlassFish instalados e configurados corretamente. Crie um novo projeto web no NetBeans.

2.  **Configuração do Banco de Dados**: Crie um banco de dados (ex: `gerenciador_tarefas`) e uma tabela `tarefas`.

    ```sql
    CREATE DATABASE IF NOT EXISTS gerenciador_tarefas;
    USE gerenciador_tarefas;

    CREATE TABLE IF NOT EXISTS tarefas (
        id INT AUTO_INCREMENT PRIMARY KEY,
        titulo VARCHAR(255) NOT NULL,
        descricao TEXT,
        concluida BOOLEAN DEFAULT FALSE
    );
    ```

3.  **Camada Modelo (POJO)**: Crie a classe `Tarefa.java` no pacote `com.exemplo.model`.

    ```java
    // src/main/java/com/exemplo/model/Tarefa.java
    package com.exemplo.model;

    public class Tarefa {
        private int id;
        private String titulo;
        private String descricao;
        private boolean concluida;

        // Construtores
        public Tarefa() {}

        public Tarefa(int id, String titulo, String descricao, boolean concluida) {
            this.id = id;
            this.titulo = titulo;
            this.descricao = descricao;
            this.concluida = concluida;
        }

        // Getters e Setters
        public int getId() { return id; }
        public void setId(int id) { this.id = id; }
        public String getTitulo() { return titulo; }
        public void setTitulo(String titulo) { this.titulo = titulo; }
        public String getDescricao() { return descricao; }
        public void setDescricao(String descricao) { this.descricao = descricao; }
        public boolean isConcluida() { return concluida; }
        public void setConcluida(boolean concluida) { this.concluida = concluida; }
    }
    ```

4.  **Camada Modelo (DAO)**: Crie a interface `TarefaDAO.java` e sua implementação `TarefaDAOImpl.java` no pacote `com.exemplo.dao` e `com.exemplo.dao.impl`, respectivamente. Configure a conexão JDBC.

    ```java
    // src/main/java/com/exemplo/dao/TarefaDAO.java
    package com.exemplo.dao;

    import com.exemplo.model.Tarefa;
    import java.util.List;

    public interface TarefaDAO {
        void adicionarTarefa(Tarefa tarefa);
        Tarefa buscarTarefaPorId(int id);
        List<Tarefa> listarTodasTarefas();
        void atualizarTarefa(Tarefa tarefa);
        void deletarTarefa(int id);
    }
    ```

    ```java
    // src/main/java/com/exemplo/dao/impl/TarefaDAOImpl.java
    package com.exemplo.dao.impl;

    import com.exemplo.dao.TarefaDAO;
    import com.exemplo.model.Tarefa;
    import java.sql.*;
    import java.util.ArrayList;
    import java.util.List;

    public class TarefaDAOImpl implements TarefaDAO {
        private static final String JDBC_URL = "jdbc:mysql://localhost:3306/gerenciador_tarefas?useSSL=false&serverTimezone=UTC";
        private static final String JDBC_USER = "root";
        private static final String JDBC_PASSWORD = "password";

        private Connection getConnection() throws SQLException {
            return DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD);
        }

        @Override
        public void adicionarTarefa(Tarefa tarefa) {
            String sql = "INSERT INTO tarefas (titulo, descricao, concluida) VALUES (?, ?, ?)";
            try (Connection conn = getConnection();
                 PreparedStatement stmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)) {
                stmt.setString(1, tarefa.getTitulo());
                stmt.setString(2, tarefa.getDescricao());
                stmt.setBoolean(3, tarefa.isConcluida());
                stmt.executeUpdate();
                try (ResultSet generatedKeys = stmt.getGeneratedKeys()) {
                    if (generatedKeys.next()) {
                        tarefa.setId(generatedKeys.getInt(1));
                    }
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        @Override
        public Tarefa buscarTarefaPorId(int id) {
            String sql = "SELECT id, titulo, descricao, concluida FROM tarefas WHERE id = ?";
            Tarefa tarefa = null;
            try (Connection conn = getConnection();
                 PreparedStatement stmt = conn.prepareStatement(sql)) {
                stmt.setInt(1, id);
                try (ResultSet rs = stmt.executeQuery()) {
                    if (rs.next()) {
                        tarefa = new Tarefa();
                        tarefa.setId(rs.getInt("id"));
                        tarefa.setTitulo(rs.getString("titulo"));
                        tarefa.setDescricao(rs.getString("descricao"));
                        tarefa.setConcluida(rs.getBoolean("concluida"));
                    }
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            return tarefa;
        }

        @Override
        public List<Tarefa> listarTodasTarefas() {
            String sql = "SELECT id, titulo, descricao, concluida FROM tarefas";
            List<Tarefa> tarefas = new ArrayList<>();
            try (Connection conn = getConnection();
                 Statement stmt = conn.createStatement();
                 ResultSet rs = stmt.executeQuery(sql)) {
                while (rs.next()) {
                    Tarefa tarefa = new Tarefa();
                    tarefa.setId(rs.getInt("id"));
                    tarefa.setTitulo(rs.getString("titulo"));
                    tarefa.setDescricao(rs.getString("descricao"));
                    tarefa.setConcluida(rs.getBoolean("concluida"));
                    tarefas.add(tarefa);
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            return tarefas;
        }

        @Override
        public void atualizarTarefa(Tarefa tarefa) {
            String sql = "UPDATE tarefas SET titulo = ?, descricao = ?, concluida = ? WHERE id = ?";
            try (Connection conn = getConnection();
                 PreparedStatement stmt = conn.prepareStatement(sql)) {
                stmt.setString(1, tarefa.getTitulo());
                stmt.setString(2, tarefa.getDescricao());
                stmt.setBoolean(3, tarefa.isConcluida());
                stmt.setInt(4, tarefa.getId());
                stmt.executeUpdate();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        @Override
        public void deletarTarefa(int id) {
            String sql = "DELETE FROM tarefas WHERE id = ?";
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

5.  **Camada Modelo (Serviço)**: Crie a classe `TarefaService.java` no pacote `com.exemplo.service` para encapsular a lógica de negócios e interagir com o DAO.

    ```java
    // src/main/java/com/exemplo/service/TarefaService.java
    package com.exemplo.service;

    import com.exemplo.dao.TarefaDAO;
    import com.exemplo.dao.impl.TarefaDAOImpl;
    import com.exemplo.model.Tarefa;
    import java.util.List;

    public class TarefaService {
        private TarefaDAO tarefaDAO;

        public TarefaService() {
            this.tarefaDAO = new TarefaDAOImpl();
        }

        public void adicionarTarefa(Tarefa tarefa) {
            if (tarefa.getTitulo() == null || tarefa.getTitulo().trim().isEmpty()) {
                throw new IllegalArgumentException("O título da tarefa não pode ser vazio.");
            }
            tarefaDAO.adicionarTarefa(tarefa);
        }

        public Tarefa buscarTarefaPorId(int id) {
            return tarefaDAO.buscarTarefaPorId(id);
        }

        public List<Tarefa> listarTodasTarefas() {
            return tarefaDAO.listarTodasTarefas();
        }

        public void atualizarTarefa(Tarefa tarefa) {
            if (tarefa.getTitulo() == null || tarefa.getTitulo().trim().isEmpty()) {
                throw new IllegalArgumentException("O título da tarefa não pode ser vazio.");
            }
            tarefaDAO.atualizarTarefa(tarefa);
        }

        public void deletarTarefa(int id) {
            tarefaDAO.deletarTarefa(id);
        }

        public void alternarStatusTarefa(int id) {
            Tarefa tarefa = buscarTarefaPorId(id);
            if (tarefa != null) {
                tarefa.setConcluida(!tarefa.isConcluida());
                tarefaDAO.atualizarTarefa(tarefa);
            } else {
                throw new IllegalArgumentException("Tarefa não encontrada para alternar status.");
            }
        }
    }
    ```

6.  **Camada Controle (Servlet)**: Crie o Servlet `TarefaController.java` no pacote `com.exemplo.controller` para gerenciar as requisições.

    ```java
    // src/main/java/com/exemplo/controller/TarefaController.java
    package com.exemplo.controller;

    import com.exemplo.model.Tarefa;
    import com.exemplo.service.TarefaService;
    import java.io.IOException;
    import java.util.List;
    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    @WebServlet("/tarefas")
    public class TarefaController extends HttpServlet {
        private TarefaService tarefaService;

        @Override
        public void init() throws ServletException {
            super.init();
            tarefaService = new TarefaService();
        }

        @Override
        protected void doGet(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            String acao = request.getParameter("acao");
            if (acao == null) acao = "listar";

            switch (acao) {
                case "listar":
                    listarTarefas(request, response);
                    break;
                case "novo":
                    mostrarFormularioAdicionar(request, response);
                    break;
                case "editar":
                    mostrarFormularioEditar(request, response);
                    break;
                case "deletar":
                    deletarTarefa(request, response);
                    break;
                case "alternarStatus":
                    alternarStatusTarefa(request, response);
                    break;
                default:
                    listarTarefas(request, response);
                    break;
            }
        }

        @Override
        protected void doPost(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            String acao = request.getParameter("acao");
            if (acao == null) acao = "adicionar";

            switch (acao) {
                case "adicionar":
                    adicionarTarefa(request, response);
                    break;
                case "atualizar":
                    atualizarTarefa(request, response);
                    break;
                default:
                    response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Ação POST inválida.");
                    break;
            }
        }

        private void listarTarefas(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            List<Tarefa> tarefas = tarefaService.listarTodasTarefas();
            request.setAttribute("tarefas", tarefas);
            request.getRequestDispatcher("/WEB-INF/views/listaTarefas.jsp").forward(request, response);
        }

        private void mostrarFormularioAdicionar(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            request.getRequestDispatcher("/WEB-INF/views/formTarefa.jsp").forward(request, response);
        }

        private void adicionarTarefa(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            String titulo = request.getParameter("titulo");
            String descricao = request.getParameter("descricao");
            boolean concluida = "on".equals(request.getParameter("concluida"));

            Tarefa novaTarefa = new Tarefa(0, titulo, descricao, concluida);
            try {
                tarefaService.adicionarTarefa(novaTarefa);
                response.sendRedirect("tarefas?acao=listar");
            } catch (IllegalArgumentException e) {
                request.setAttribute("erro", e.getMessage());
                request.setAttribute("tarefa", novaTarefa);
                request.getRequestDispatcher("/WEB-INF/views/formTarefa.jsp").forward(request, response);
            }
        }

        private void mostrarFormularioEditar(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            int id = Integer.parseInt(request.getParameter("id"));
            Tarefa tarefa = tarefaService.buscarTarefaPorId(id);
            if (tarefa != null) {
                request.setAttribute("tarefa", tarefa);
                request.getRequestDispatcher("/WEB-INF/views/formTarefa.jsp").forward(request, response);
            } else {
                response.sendError(HttpServletResponse.SC_NOT_FOUND, "Tarefa não encontrada.");
            }
        }

        private void atualizarTarefa(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            int id = Integer.parseInt(request.getParameter("id"));
            String titulo = request.getParameter("titulo");
            String descricao = request.getParameter("descricao");
            boolean concluida = "on".equals(request.getParameter("concluida"));

            Tarefa tarefaAtualizada = new Tarefa(id, titulo, descricao, concluida);
            try {
                tarefaService.atualizarTarefa(tarefaAtualizada);
                response.sendRedirect("tarefas?acao=listar");
            } catch (IllegalArgumentException e) {
                request.setAttribute("erro", e.getMessage());
                request.setAttribute("tarefa", tarefaAtualizada);
                request.getRequestDispatcher("/WEB-INF/views/formTarefa.jsp").forward(request, response);
            }
        }

        private void deletarTarefa(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            int id = Integer.parseInt(request.getParameter("id"));
            tarefaService.deletarTarefa(id);
            response.sendRedirect("tarefas?acao=listar");
        }

        private void alternarStatusTarefa(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            int id = Integer.parseInt(request.getParameter("id"));
            try {
                tarefaService.alternarStatusTarefa(id);
                response.sendRedirect("tarefas?acao=listar");
            } catch (IllegalArgumentException e) {
                response.sendError(HttpServletResponse.SC_NOT_FOUND, e.getMessage());
            }
        }
    }
    ```

7.  **Camada Visão (JSP)**: Crie as JSPs `listaTarefas.jsp` e `formTarefa.jsp` no diretório `/WEB-INF/views/`.

    **`listaTarefas.jsp`:**

    ```jsp
    <%@page contentType="text/html" pageEncoding="UTF-8"%>
    <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Gerenciador de Tarefas</title>
        <style>
            body { font-family: Arial, sans-serif; margin: 20px; }
            table { width: 100%; border-collapse: collapse; margin-top: 20px; }
            th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
            th { background-color: #f2f2f2; }
            .concluida { text-decoration: line-through; color: gray; }
            .btn { padding: 5px 10px; margin: 2px; cursor: pointer; }
            .btn-add { background-color: #4CAF50; color: white; border: none; }
            .btn-edit { background-color: #008CBA; color: white; border: none; }
            .btn-delete { background-color: #f44336; color: white; border: none; }
            .btn-toggle { background-color: #555555; color: white; border: none; }
        </style>
    </head>
    <body>
        <h1>Minhas Tarefas</h1>

        <p><a href="tarefas?acao=novo" class="btn btn-add">Adicionar Nova Tarefa</a></p>

        <c:if test="${empty tarefas}">
            <p>Nenhuma tarefa encontrada. Adicione uma nova!</p>
        </c:if>

        <c:if test="${not empty tarefas}">
            <table>
                <thead>
                    <tr>
                        <th>ID</th>
                        <th>Título</th>
                        <th>Descrição</th>
                        <th>Status</th>
                        <th>Ações</th>
                    </tr>
                </thead>
                <tbody>
                    <c:forEach var="tarefa" items="${tarefas}">
                        <tr class="${tarefa.concluida ? 'concluida' : ''}">
                            <td>${tarefa.id}</td>
                            <td>${tarefa.titulo}</td>
                            <td>${tarefa.descricao}</td>
                            <td>
                                <c:if test="${tarefa.concluida}">Concluída</c:if>
                                <c:if test="${!tarefa.concluida}">Pendente</c:if>
                            </td>
                            <td>
                                <a href="tarefas?acao=editar&id=${tarefa.id}" class="btn btn-edit">Editar</a>
                                <a href="tarefas?acao=deletar&id=${tarefa.id}" class="btn btn-delete" onclick="return confirm('Tem certeza que deseja excluir esta tarefa?');">Excluir</a>
                                <a href="tarefas?acao=alternarStatus&id=${tarefa.id}" class="btn btn-toggle">Alternar Status</a>
                            </td>
                        </tr>
                    </c:forEach>
                </tbody>
            </table>
        </c:if>

    </body>
    </html>
    ```

    **`formTarefa.jsp`:**

    ```jsp
    <%@page contentType="text/html" pageEncoding="UTF-8"%>
    <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title><c:if test="${tarefa.id > 0}">Editar Tarefa</c:if><c:if test="${tarefa.id == 0 || empty tarefa}">Adicionar Nova Tarefa</c:if></title>
        <style>
            body { font-family: Arial, sans-serif; margin: 20px; }
            form { background-color: #f9f9f9; padding: 20px; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,.1); width: 400px; margin: 20px auto; }
            label { display: block; margin-bottom: 5px; font-weight: bold; }
            input[type="text"], textarea { width: calc(100% - 22px); padding: 10px; margin-bottom: 10px; border: 1px solid #ddd; border-radius: 4px; }
            input[type="checkbox"] { margin-right: 5px; }
            .error { color: red; margin-bottom: 10px; }
            .btn-submit { background-color: #007bff; color: white; padding: 10px 15px; border: none; border-radius: 4px; cursor: pointer; }
            .btn-submit:hover { background-color: #0056b3; }
            .btn-back { background-color: #6c757d; color: white; padding: 10px 15px; border: none; border-radius: 4px; cursor: pointer; margin-left: 10px; }
            .btn-back:hover { background-color: #5a6268; }
        </style>
    </head>
    <body>
        <h1><c:if test="${tarefa.id > 0}">Editar Tarefa</c:if><c:if test="${tarefa.id == 0 || empty tarefa}">Adicionar Nova Tarefa</c:if></h1>

        <c:if test="${not empty erro}">
            <p class="error">${erro}</p>
        </c:if>

        <form action="tarefas" method="post">
            <c:if test="${tarefa.id > 0}">
                <input type="hidden" name="acao" value="atualizar" />
                <input type="hidden" name="id" value="${tarefa.id}" />
            </c:if>
            <c:if test="${tarefa.id == 0 || empty tarefa}">
                <input type="hidden" name="acao" value="adicionar" />
            </c:if>

            <label for="titulo">Título:</label>
            <input type="text" id="titulo" name="titulo" value="${tarefa.titulo}" required />

            <label for="descricao">Descrição:</label>
            <textarea id="descricao" name="descricao" rows="5">${tarefa.descricao}</textarea>

            <label>
                <input type="checkbox" name="concluida" <c:if test="${tarefa.concluida}">checked</c:if> /> Tarefa Concluída
            </label>
            <br><br>

            <input type="submit" value="Salvar Tarefa" class="btn-submit" />
            <button type="button" onclick="window.location.href='tarefas?acao=listar'" class="btn-back">Voltar</button>
        </form>

    </body>
    </html>
    ```

8.  **Configuração do `web.xml`**: Verifique se o `web.xml` (localizado em `WEB-INF`) está configurado corretamente, embora com `@WebServlet` a configuração seja mínima. Você pode adicionar mapeamentos de erro, se desejar.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app version="3.1" xmlns="http://xmlns.jcp.org/xml/ns/javaee"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd">
        <session-config>
            <session-timeout>30</session-timeout>
        </session-config>
        
        <!-- Exemplo de página de erro global -->
        <error-page>
            <exception-type>java.lang.Exception</exception-type>
            <location>/WEB-INF/views/error.jsp</location>
        </error-page>
        <error-page>
            <error-code>404</error-code>
            <location>/WEB-INF/views/error.jsp</location>
        </error-page>
        <error-page>
            <error-code>500</error-code>
            <location>/WEB-INF/views/error.jsp</location>
        </error-page>
    </web-app>
    ```

    **`error.jsp` (exemplo simples):**

    ```jsp
    <%@page contentType="text/html" pageEncoding="UTF-8" isErrorPage="true"%>
    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Erro!</title>
        <style>
            body { font-family: Arial, sans-serif; margin: 20px; text-align: center; }
            h1 { color: red; }
            .error-details { background-color: #f8d7da; border: 1px solid #f5c6cb; color: #721c24; padding: 15px; margin-top: 20px; border-radius: 5px; display: inline-block; text-align: left; }
        </style>
    </head>
    <body>
        <h1>Ocorreu um Erro!</h1>
        <p>Lamentamos, mas algo inesperado aconteceu.</p>
        
        <c:if test="${not empty pageContext.exception}">
            <div class="error-details">
                <p><strong>Detalhes do Erro:</strong></p>
                <p>Mensagem: ${pageContext.exception.message}</p>
                <p>Tipo: ${pageContext.exception["class"].name}</p>
                <%-- Para depuração, você pode exibir o stack trace --%>
                <%-- 
                <pre>
                    <% pageContext.exception.printStackTrace(new java.io.PrintWriter(out)); %>
                </pre>
                --%>
            </div>
        </c:if>
        
        <p><a href="tarefas?acao=listar">Voltar para a lista de tarefas</a></p>
    </body>
    </html>
    ```

9.  **Recursos Estáticos**: Crie os diretórios `css` e `js` em `webapp` e adicione `style.css` e `script.js` conforme necessário para estilização e interatividade.

    **`webapp/css/style.css` (exemplo):**
    ```css
    /* Estilos básicos para o projeto */
    body {
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        margin: 0;
        padding: 20px;
        background-color: #f4f7f6;
        color: #333;
    }

    h1 {
        color: #0056b3;
        text-align: center;
        margin-bottom: 30px;
    }

    table {
        width: 80%;
        margin: 0 auto;
        border-collapse: collapse;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        background-color: #fff;
    }

    th, td {
        padding: 12px 15px;
        border: 1px solid #ddd;
        text-align: left;
    }

    th {
        background-color: #e9ecef;
        color: #495057;
        font-weight: bold;
    }

    tr:nth-child(even) {
        background-color: #f8f9fa;
    }

    tr:hover {
        background-color: #e2e6ea;
    }

    .concluida {
        text-decoration: line-through;
        color: #888;
        font-style: italic;
    }

    .btn {
        display: inline-block;
        padding: 8px 12px;
        margin: 5px 2px;
        border-radius: 5px;
        text-decoration: none;
        color: white;
        font-size: 0.9em;
        transition: background-color 0.3s ease;
    }

    .btn-add {
        background-color: #28a745;
    }

    .btn-add:hover {
        background-color: #218838;
    }

    .btn-edit {
        background-color: #007bff;
    }

    .btn-edit:hover {
        background-color: #0056b3;
    }

    .btn-delete {
        background-color: #dc3545;
    }

    .btn-delete:hover {
        background-color: #c82333;
    }

    .btn-toggle {
        background-color: #6c757d;
    }

    .btn-toggle:hover {
        background-color: #5a6268;
    }

    form {
        background-color: #fff;
        padding: 30px;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0,0,0,.1);
        width: 500px;
        margin: 30px auto;
    }

    label {
        display: block;
        margin-bottom: 8px;
        font-weight: bold;
        color: #555;
    }

    input[type="text"],
    textarea {
        width: calc(100% - 24px);
        padding: 12px;
        margin-bottom: 15px;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-sizing: border-box;
    }

    input[type="checkbox"] {
        margin-right: 8px;
    }

    .error {
        color: #dc3545;
        background-color: #f8d7da;
        border: 1px solid #f5c6cb;
        padding: 10px;
        border-radius: 5px;
        margin-bottom: 15px;
    }

    .btn-submit {
        background-color: #007bff;
        color: white;
        padding: 10px 20px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 1em;
        transition: background-color 0.3s ease;
    }

    .btn-submit:hover {
        background-color: #0056b3;
    }

    .btn-back {
        background-color: #6c757d;
        color: white;
        padding: 10px 20px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 1em;
        margin-left: 10px;
        transition: background-color 0.3s ease;
    }

    .btn-back:hover {
        background-color: #5a6268;
    }
    ```

    **`webapp/js/script.js` (exemplo para futuras validações ou interatividade):**
    ```javascript
    // script.js
    document.addEventListener('DOMContentLoaded', function() {
        // Exemplo de validação de formulário no lado do cliente
        const formTarefa = document.querySelector('form');
        if (formTarefa) {
            formTarefa.addEventListener('submit', function(event) {
                const tituloInput = document.getElementById('titulo');
                if (tituloInput.value.trim() === '') {
                    alert('O título da tarefa é obrigatório!');
                    tituloInput.focus();
                    event.preventDefault(); // Impede o envio do formulário
                }
            });
        }
    });
    ```

10. **Testar e Refinar**: Compile e execute o projeto no GlassFish via NetBeans. Teste todas as funcionalidades (adicionar, listar, editar, excluir, alternar status). Refine a interface do usuário e a lógica conforme necessário.

Este projeto de gerenciamento de tarefas serve como um excelente ponto de partida para aplicar e solidificar todos os conhecimentos adquiridos sobre desenvolvimento web Java com Servlets, JSP, MVC e acesso a dados. Ao completá-lo, você terá uma compreensão prática e aprofundada de como construir aplicações web robustas e bem estruturadas. 



