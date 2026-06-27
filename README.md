# DevForum

A web forum application built with pure Java EE — no frameworks. Users can register, log in, create topics, comment, and compete in a points-based ranking.

![Java](https://img.shields.io/badge/Java-20-orange?style=flat-square&logo=openjdk)
![Jakarta EE](https://img.shields.io/badge/Jakarta%20EE-10-blue?style=flat-square)
![MySQL](https://img.shields.io/badge/MySQL-8.0-blue?style=flat-square&logo=mysql)
![GlassFish](https://img.shields.io/badge/GlassFish-7-lightgrey?style=flat-square)

---

## Features

- User registration and login (email + password)
- Create and browse forum topics
- Comment on topics
- Points system: +10 pts per topic, +3 pts per comment
- Live ranking board
- DBUnit integration tests for all DAO classes
- Selenium functional tests covering multi-screen navigation

---

## Architecture

The project follows the **MVC pattern** without any external frameworks:

```
┌─────────────────────────────────────────────┐
│                   Browser                   │
└──────────────┬──────────────────────────────┘
               │ HTTP
┌──────────────▼──────────────────────────────┐
│          View Layer  (JSP)                  │
│  index.jsp  topicos.jsp  exibe-topico.jsp   │
│  cadastro.jsp  insere-topico.jsp  ranking.jsp│
└──────────────┬──────────────────────────────┘
               │ forward / redirect
┌──────────────▼──────────────────────────────┐
│        Controller Layer  (Servlets)         │
│  Login  Cadastrar  Topicos  ExibirTopico    │
│  AdicionarTopico  AdicionarComentario       │
│  Ranking                                    │
└──────────────┬──────────────────────────────┘
               │ method calls
┌──────────────▼──────────────────────────────┐
│           DAO Layer  (JDBC)                 │
│  UsuarioDAO  TopicosDAO  ComentarioDAO      │
└──────────────┬──────────────────────────────┘
               │ SQL
┌──────────────▼──────────────────────────────┐
│              MySQL Database                 │
│  usuario   topico   comentario              │
└─────────────────────────────────────────────┘
```

### Package structure

```
src/java/
├── servlets/          # Controllers — one servlet per use case
│   ├── Login.java
│   ├── Cadastrar.java
│   ├── Topicos.java
│   ├── ExibirTopico.java
│   ├── AdicionarTopico.java
│   ├── AdicionarComentario.java
│   └── Ranking.java
├── DAOs/              # Data access — plain JDBC, PreparedStatement
│   ├── UsuarioDAO.java
│   ├── TopicosDAO.java
│   └── ComentarioDAO.java
├── modelos/           # Plain Java model classes
│   ├── Usuario.java
│   ├── Topico.java
│   └── Comentario.java
└── JDBC/
    └── ConnectionFactory.java   # Single connection factory

web/
├── index.jsp          # Login screen
├── cadastro.jsp       # Registration screen
├── topicos.jsp        # Topics list
├── exibe-topico.jsp   # Topic detail + comments
├── insere-topico.jsp  # New topic form
├── ranking.jsp        # Leaderboard
└── style.css          # Global stylesheet

test/
├── TestUsuario.java   # DBUnit tests for UsuarioDAO
├── TestTopico.java    # DBUnit tests for TopicosDAO
├── TestComentario.java# DBUnit tests for ComentarioDAO
└── TestSelenium.java  # Selenium functional tests
```

---

## Tech Stack

| Layer | Technology | Notes |
|---|---|---|
| Language | Java 20 | |
| Server-side view | JSP (Jakarta Server Pages) | JSTL core tag library |
| Controllers | Jakarta Servlets 6.0 | `@WebServlet` annotation, no `web.xml` mapping needed |
| Data access | JDBC | `PreparedStatement` only — no ORM |
| Database | MySQL 8 | Via XAMPP or standalone |
| JDBC Driver | MySQL Connector/J 8.0.33 | |
| Application server | GlassFish 7 | Jakarta EE 10 compatible |
| Unit testing | JUnit 4.13.2 + DBUnit 2.7.3 | DAO layer tests |
| Functional testing | Selenium 4.10.0 | Edge WebDriver |
| Build tool | Apache Ant (NetBeans project) | |

---

## Database Schema

```sql
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

> **Note:** The `login` column is populated with the user's email address, which acts as the unique identifier throughout the application.

---

## Getting Started

### Prerequisites

- Java 20+
- NetBeans IDE (or Eclipse with WTP)
- GlassFish 7 ([download](https://glassfish.org/download))
- MySQL 8 (XAMPP recommended for local development)
- msedgedriver matching your Edge version (for Selenium tests)

### Database setup

Start MySQL and run the schema above in any client (HeidiSQL, MySQL Workbench, etc.) targeting a database named `forum`:

```sql
CREATE DATABASE forum;
USE forum;
-- paste schema above
```

### Running the application

1. Open the project in NetBeans: **File → Open Project** → select `Week4/Forum`
2. Register GlassFish 7 under **Tools → Servers** if not already configured
3. Right-click the project → **Run** (F6)
4. The browser opens at `http://localhost:8080/Forum/`

### Running the tests

**DAO tests (JUnit + DBUnit):**

Right-click `TestUsuario.java`, `TestTopico.java` or `TestComentario.java` → **Test File**

The `@Before` method resets the database to a known state before each test using JDBC directly, then uses `DBUnit DatabaseConnection` to verify the final state.

**Selenium tests:**

1. Make sure the application is deployed and running
2. Download [msedgedriver](https://developer.microsoft.com/microsoft-edge/tools/webdriver/) matching your Edge version
3. Place it at `C:/Users/<your-user>/.drivers/edgedriver_win64/msedgedriver.exe`
4. Update the path in `TestSelenium.java` if needed
5. Right-click `TestSelenium.java` → **Test File**

The three tests cover:
- `verRanking` — login → navigate to ranking screen
- `verTopico` — login → open first topic
- `cadastrarUsuario` — register a new user → login → verify ranking entry

---

## How the Points System Works

| Action | Points |
|---|---|
| Create a topic | +10 |
| Add a comment | +3 |

Points are updated via `UPDATE usuario SET pontos = pontos + ? WHERE login = ?` inside `TopicosDAO.adiconarTopico()` and `ComentarioDAO.adicionaComentario()`.

---

## Design

The UI uses a **pixel-art / retro game** aesthetic inspired by RPG interfaces:

- **Press Start 2P** for headings and buttons (Google Fonts)
- **VT323** for body text
- Fluid typography via CSS `clamp()` — scales with viewport width
- Scanline overlay effect
- Responsive layout with `min(960px, 94vw)` container

---

## License

MIT
