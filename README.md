# 💬 DevForum - Fórum Web para Desenvolvedores

Este projeto consiste no desenvolvimento de uma **aplicação web de fórum** voltada para a comunidade de desenvolvedores. A aplicação permite que usuários se cadastrem, façam login, publiquem tópicos, comentem em discussões e disputem posições em um **ranking por pontuação** - tudo em uma interface com visual retrô/pixel art.

Desenvolvido como **projeto final do Curso de Java do ITA**, o sistema demonstra a construção de uma aplicação web completa utilizando **Java EE puro**, sem frameworks como Spring ou Hibernate.

![Java](https://img.shields.io/badge/Java-17-orange?style=flat-square&logo=openjdk)
![Jakarta EE](https://img.shields.io/badge/Jakarta%20EE-10-blue?style=flat-square)
![MySQL](https://img.shields.io/badge/MySQL-8.0-blue?style=flat-square&logo=mysql)
![GlassFish](https://img.shields.io/badge/GlassFish-7-lightgrey?style=flat-square)

---

## 🏷️ Palavras-chave

`Java EE` · `Jakarta EE` · `Servlets` · `JSP` · `JDBC` · `MySQL` · `MVC` · `DAO` · `CRUD` · `Aplicação Web` · `Autenticação` · `Sessão HTTP` · `JUnit` · `DBUnit` · `Selenium` · `Testes Automatizados` · `GlassFish` · `SQL` · `PreparedStatement`

---

## 💡 Arquitetura e Padrões de Projeto

O sistema é estruturado em **Java** e segue rigorosos padrões de desenvolvimento web:

* **MVC (Model-View-Controller):** Separação clara entre a camada de apresentação (JSP), controle de requisições (Servlets) e regras de negócio/dados (Model + DAO).
* **DAO (Data Access Object):** Utilizado para isolar e padronizar toda a comunicação com o banco de dados via JDBC.
* **Factory Pattern:** `ConnectionFactory` centraliza a criação de conexões com o MySQL, evitando duplicação de código.
* **Session Management:** Controle de sessão HTTP para manter o usuário autenticado durante a navegação.

```
Navegador  →  JSP (View)  →  Servlet (Controller)  →  DAO (JDBC)  →  MySQL
```

---

## ⚙️ Funcionalidades do Sistema

O fórum oferece as seguintes operações para os usuários:

* **Cadastro e Login** - autenticação por e-mail e senha
* **Tópicos** - criação e listagem de discussões
* **Comentários** - interação em tópicos existentes
* **Ranking** - placar de líderes com sistema de pontuação (+10 pts por tópico, +3 pts por comentário)
* **Sessão de usuário** - área restrita acessível apenas após o login

### Entidades gerenciadas

* **Usuários**
* **Tópicos**
* **Comentários**

---

## 🎯 Hard Skills Demonstradas

Competências técnicas valorizadas no mercado de desenvolvimento que este projeto coloca em prática:

| Área | Skill |
|---|---|
| Back-end | Desenvolvimento com **Java** e **Jakarta EE** |
| Web | **Servlets**, **JSP** e ciclo de requisição HTTP |
| Banco de dados | Modelagem relacional e consultas **SQL** com **MySQL** |
| Persistência | **JDBC** com `PreparedStatement` (sem ORM) |
| Arquitetura | Padrão **MVC** e camada **DAO** |
| Segurança | Autenticação de usuários e gerenciamento de **sessão HTTP** |
| Qualidade | Testes com **JUnit**, **DBUnit** e **Selenium** |
| DevOps básico | Deploy em **GlassFish** e build com **Apache Ant** |
| Front-end | **HTML**, **CSS** responsivo e design de interface |

---

## 🚀 Tecnologias Utilizadas

### 🖥️ Back-end & Persistência

* **Linguagem:** Java 17
* **Plataforma:** Jakarta EE 10
* **Controllers:** Jakarta Servlets 6.0 (`@WebServlet`)
* **Acesso a Dados:** JDBC + PreparedStatement
* **Banco de Dados:** MySQL 8
* **Driver JDBC:** MySQL Connector/J 8.0.33
* **Servidor de Aplicação:** GlassFish 7

### 🎨 Front-end & Interface

* **Views:** JSP (Jakarta Server Pages)
* **Estilização:** CSS3 (layout responsivo, tipografia fluida)
* **Estética:** Pixel art / retrô - fontes *Press Start 2P* e *VT323*

### 🧪 Testes & Qualidade

* **Testes Unitários/Integração:** JUnit 4.13.2 + DBUnit 2.7.3
* **Testes Funcionais (E2E):** Selenium 4.10.0 (Edge WebDriver)
* **Build:** Apache Ant (projeto NetBeans)

---

## 📂 Estrutura do Projeto

```
src/java/
├── servlets/       → Controllers (Login, Cadastrar, Topicos, Ranking...)
├── DAOs/           → Acesso ao banco (UsuarioDAO, TopicosDAO, ComentarioDAO)
├── modelos/        → Entidades (Usuario, Topico, Comentario)
└── JDBC/           → ConnectionFactory

web/
├── *.jsp           → Telas da aplicação
└── style.css       → Estilos globais

test/
├── TestUsuario.java      → Testes DBUnit do UsuarioDAO
├── TestTopico.java       → Testes DBUnit do TopicosDAO
├── TestComentario.java   → Testes DBUnit do ComentarioDAO
└── TestSelenium.java     → Testes funcionais E2E
```

---

## 🗄️ Banco de Dados

```sql
CREATE DATABASE IF NOT EXISTS forum;
USE forum;

CREATE TABLE usuario (
    login  VARCHAR(255) NOT NULL PRIMARY KEY,
    email  TEXT,
    nome   TEXT,
    senha  TEXT,
    pontos INTEGER
);

CREATE TABLE topico (
    id_topico INT AUTO_INCREMENT PRIMARY KEY,
    titulo    TEXT,
    conteudo  TEXT,
    login     VARCHAR(255),
    FOREIGN KEY (login) REFERENCES usuario(login)
);

CREATE TABLE comentario (
    id_comentario INT AUTO_INCREMENT PRIMARY KEY,
    comentario    TEXT,
    login         VARCHAR(255),
    id_topico     INT,
    FOREIGN KEY (login)     REFERENCES usuario(login),
    FOREIGN KEY (id_topico) REFERENCES topico(id_topico)
);
```

> A coluna `login` armazena o **e-mail** do usuário como identificador único.

---

## ▶️ Como Executar

### Pré-requisitos

* Java 17+
* NetBeans IDE
* GlassFish 7
* MySQL 8 (XAMPP recomendado)

### Passos

1. Execute o script SQL acima no MySQL
2. Abra o projeto no NetBeans: **Arquivo → Abrir Projeto**
3. Configure o GlassFish em **Ferramentas → Servidores**
4. Execute o projeto com **F6**
5. Acesse: `http://localhost:8080/Forum/`

> Credenciais do banco em `src/java/JDBC/ConnectionFactory.java` (padrão: `root` / senha vazia).


MIT
