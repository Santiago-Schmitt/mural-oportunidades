# Mural de Oportunidades

API REST para um mural de oportunidades acadêmicas e profissionais — estágios, cursos e eventos. Desenvolvida em Node.js com Express e SQLite, expõe os registros cadastrados para consumo por um front-end.

## Sumário

- [Tecnologias](#tecnologias)
- [Estrutura do projeto](#estrutura-do-projeto)
- [Pré-requisitos](#pré-requisitos)
- [Instalação](#instalação)
- [Como executar](#como-executar)
- [Carga inicial de dados (seed)](#carga-inicial-de-dados-seed)
- [Endpoints da API](#endpoints-da-api)
- [Modelo de dados](#modelo-de-dados)
- [Banco de dados](#banco-de-dados)
- [Scripts disponíveis](#scripts-disponíveis)

## Tecnologias

| Tecnologia | Versão | Uso |
| --- | --- | --- |
| [Node.js](https://nodejs.org/) | 24.x | Runtime JavaScript |
| [Express](https://expressjs.com/) | ^5.3.0 | Framework HTTP e roteamento |
| [better-sqlite3](https://github.com/WiseLibs/better-sqlite3) | ^13.0.3 | Driver SQLite síncrono |
| [CORS](https://github.com/expressjs/cors) | ^2.8.6 | Liberação de acesso cross-origin |

## Estrutura do projeto

```
mural/
├── .gitignore
├── README.md
└── mural-api/
    ├── .gitignore
    ├── package.json
    ├── package-lock.json
    ├── mural.db              # gerado automaticamente (não versionado)
    └── src/
        ├── server.js         # inicialização do Express e rotas
        ├── database.js       # conexão SQLite e criação da tabela
        ├── seed.js           # carga inicial com 60 registros de exemplo
        ├── routes/
        │   └── oportunidades.js  # rotas de /oportunidades
        └── validacao/        # (reservado para validações)
```

## Pré-requisitos

- Node.js 18 ou superior (recomendado 24.x)
- npm 9 ou superior

O `better-sqlite3` é um módulo nativo e compila durante a instalação. Em Windows, caso a instalação falhe, instale as [Build Tools do Visual Studio](https://github.com/nodejs/node-gyp#on-windows) ou execute:

```bash
npm install --global windows-build-tools
```

## Instalação

```bash
cd mural-api
npm install
```

## Como executar

Modo desenvolvimento (reinicia automaticamente a cada alteração):

```bash
cd mural-api
npm run dev
```

Modo produção:

```bash
cd mural-api
npm start
```

O servidor sobe em **http://localhost:3000**.

```bash
curl http://localhost:3000/oportunidades
```

## Carga inicial de dados (seed)

O banco é criado automaticamente na primeira execução (tabela `oportunidades`). Para popular com 60 registros de exemplo — 24 modelos de estágios, cursos e eventos distribuídos entre 8 instituições e 3 modalidades, com prazos variados (inclusive já encerrados):

```bash
cd mural-api
npm run seed
```

> ⚠️ **Atenção:** o seed executa `DELETE FROM oportunidades` antes de inserir. Isso apaga todos os registros existentes e reinicia o contador de IDs.

## Endpoints da API

Base URL: `http://localhost:3000`

| Método | Rota | Descrição | Resposta |
| --- | --- | --- | --- |
| `GET` | `/` | Identifica a API | `{ "mensagem": "API do Mural de Oportunidades" }` |
| `GET` | `/oportunidades` | Lista todas as oportunidades, ordenadas por prazo (mais próximas primeiro) | Array de objetos |

### Exemplo de resposta — `GET /oportunidades`

```json
[
  {
    "id": 1,
    "titulo": "Estágio em Desenvolvimento Web",
    "tipo": "estagio",
    "instituicao": "TechSul Sistemas",
    "modalidade": "presencial",
    "prazo": "2026-11-15",
    "link": "https://exemplo.com/inscricao/1",
    "descricao": "Apoio no desenvolvimento de sistemas com HTML, CSS e JavaScript.",
    "criado_em": "2026-10-27 14:32:10"
  }
]
```

### Exemplo com `curl`

```bash
# Identificação do serviço
curl http://localhost:3000/

# Listagem de oportunidades
curl http://localhost:3000/oportunidades
```

## Modelo de dados

Tabela `oportunidades` (criada em `src/database.js`):

| Coluna | Tipo | Restrições | Descrição |
| --- | --- | --- | --- |
| `id` | INTEGER | PK, AUTOINCREMENT | Identificador único |
| `titulo` | TEXT | NOT NULL | Título da oportunidade |
| `tipo` | TEXT | NOT NULL, CHECK | `estagio`, `curso` ou `evento` |
| `instituicao` | TEXT | NOT NULL | Empresa ou instituição responsável |
| `modalidade` | TEXT | NOT NULL, CHECK | `presencial`, `online` ou `hibrido` |
| `prazo` | TEXT | NOT NULL | Data limite no formato `AAAA-MM-DD` |
| `link` | TEXT | — | URL de inscrição (opcional) |
| `descricao` | TEXT | — | Descrição livre (opcional) |
| `criado_em` | TEXT | NOT NULL, DEFAULT | Data/hora local de criação |

## Banco de dados

- Arquivo: `mural-api/mural.db` (SQLite).
- Criado automaticamente na primeira execução, caso não exista.
- O arquivo e seus auxiliares (`-wal`, `-shm`, `-journal`) estão no `.gitignore` — não são versionados.
- Para começar do zero, pare o servidor e exclua `mural.db`; ele será recriado vazio (rode o seed em seguida, se quiser os dados de exemplo).

## Scripts disponíveis

Executados dentro de `mural-api/`:

| Script | Comando | Descrição |
| --- | --- | --- |
| `npm run dev` | `node --watch src/server.js` | Sobe a API com reload automático |
| `npm start` | `node src/server.js` | Sobe a API em modo normal |
| `npm run seed` | `node src/seed.js` | Recria a carga inicial de 60 oportunidades |
