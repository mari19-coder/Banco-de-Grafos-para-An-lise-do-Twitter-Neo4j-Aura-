# 📊 Projeto – Banco de Grafos Twitter (Neo4j Aura)

Dataset: twitter_training.csv (Kaggle – Twitter Sentiment)


## 🎯 Objetivo do Produto

### Criar um banco de dados em grafos capaz de responder perguntas complexas sobre:

Engajamento entre usuários

Conteúdos mais populares

Comunidades de interesse

Influenciadores e conexões relevantes

Tecnologia principal: Neo4j Aura (Cloud)

## 🧠 1️⃣ Modelagem do Grafo (Twitter)
### 🎭 Nós (Nodes)
Label	Propriedades
User	id, username, followers_count
Tweet	id, text, created_at, likes
Hashtag	tag
### 🔗 Relacionamentos (Relationships)
Relação	Origem → Destino	Significado
FOLLOWS	User → User	Usuário segue outro
POSTED	User → Tweet	Usuário publicou tweet
LIKED	User → Tweet	Usuário curtiu tweet
RETWEETED	User → Tweet	Retweet
HAS_HASHTAG	Tweet → Hashtag	Hashtag usada
## 🧭 2️⃣ Diagrama Conceitual do Grafo
(:User {id, username, followers_count})
   │
   │ POSTED
   ▼
(:Tweet {id, text, created_at, likes})
   │
   │ HAS_HASHTAG
   ▼
(:Hashtag {tag})

(:User)-[:FOLLOWS]->(:User)
(:User)-[:LIKED]->(:Tweet)
(:User)-[:RETWEETED]->(:Tweet)

## ☁️ 3️⃣ Constraints (Neo4j Aura / Neo4j 5+)

### ⚠️ Executar antes da carga de dados

CREATE CONSTRAINT user_id_unique IF NOT EXISTS
FOR (u:User)
REQUIRE u.id IS UNIQUE;

CREATE CONSTRAINT tweet_id_unique IF NOT EXISTS
FOR (t:Tweet)
REQUIRE t.id IS UNIQUE;

CREATE CONSTRAINT hashtag_tag_unique IF NOT EXISTS
FOR (h:Hashtag)
REQUIRE h.tag IS UNIQUE;

## ⚡ 4️⃣ Índices (Performance de Consultas)
CREATE INDEX user_username_index IF NOT EXISTS
FOR (u:User)
ON (u.username);

CREATE INDEX tweet_created_at_index IF NOT EXISTS
FOR (t:Tweet)
ON (t.created_at);

CREATE INDEX tweet_likes_index IF NOT EXISTS
FOR (t:Tweet)
ON (t.likes);

CREATE INDEX hashtag_index IF NOT EXISTS
FOR (h:Hashtag)
ON (h.tag);

## ☁️ 5️⃣ Carga de Dados (Compatível com Neo4j Aura)

Neo4j Aura não permite file:///
Use URLs HTTPS (GitHub, Kaggle exportado, etc.)

### 👤 Usuários
LOAD CSV WITH HEADERS
FROM 'https://github.com/mari19-coder/Banco-de-Grafos-para-An-lise-do-Twitter-Neo4j-Aura-/blob/main/twitter_training.csv' AS row
MERGE (u:User {id: row.user_id})
SET u.username = row.username,
    u.followers_count = toInteger(row.followers);

### 🐦 Tweets
LOAD CSV WITH HEADERS
FROM 'https://github.com/mari19-coder/Banco-de-Grafos-para-An-lise-do-Twitter-Neo4j-Aura-/blob/main/twitter_training.csv' AS row
MERGE (t:Tweet {id: row.tweet_id})
SET t.text = row.text,
    t.created_at = row.created_at,
    t.likes = toInteger(row.likes);

### 🧑‍💻 Usuário → Tweet
LOAD CSV WITH HEADERS
FROM 'https://github.com/mari19-coder/Banco-de-Grafos-para-An-lise-do-Twitter-Neo4j-Aura-/blob/main/twitter_training.csv' AS row
MATCH (u:User {id: row.user_id})
MATCH (t:Tweet {id: row.tweet_id})
MERGE (u)-[:POSTED]->(t);

### 🔁 Seguidores
LOAD CSV WITH HEADERS
FROM 'https://github.com/mari19-coder/Banco-de-Grafos-para-An-lise-do-Twitter-Neo4j-Aura-/blob/main/twitter_training.csv' AS row
MATCH (a:User {id: row.follower_id})
MATCH (b:User {id: row.followed_id})
MERGE (a)-[:FOLLOWS]->(b);

### ❤️ Likes
LOAD CSV WITH HEADERS
FROM 'https://github.com/mari19-coder/Banco-de-Grafos-para-An-lise-do-Twitter-Neo4j-Aura-/blob/main/twitter_training.csv' AS row
MATCH (u:User {id: row.user_id})
MATCH (t:Tweet {id: row.tweet_id})
MERGE (u)-[:LIKED]->(t);

### 🔖 Hashtags
LOAD CSV WITH HEADERS
FROM 'https://github.com/mari19-coder/Banco-de-Grafos-para-An-lise-do-Twitter-Neo4j-Aura-/blob/main/twitter_training.csv' AS row
MERGE (h:Hashtag {tag: row.hashtag});

LOAD CSV WITH HEADERS
FROM 'https://github.com/mari19-coder/Banco-de-Grafos-para-An-lise-do-Twitter-Neo4j-Aura-/blob/main/twitter_training.csv' AS row
MATCH (t:Tweet {id: row.tweet_id})
MATCH (h:Hashtag {tag: row.hashtag})
MERGE (t)-[:HAS_HASHTAG]->(h);

## 🔍 6️⃣ Consultas de Negócio (Insights)
### 🔥 Tweets mais populares
MATCH (t:Tweet)
RETURN t.text, t.likes
ORDER BY t.likes DESC
LIMIT 10;

### 👑 Usuários mais influentes
MATCH (u:User)
RETURN u.username, u.followers_count
ORDER BY u.followers_count DESC
LIMIT 10;

### 🤝 Comunidades por hashtag
MATCH (u:User)-[:POSTED]->(:Tweet)-[:HAS_HASHTAG]->(h:Hashtag)
RETURN h.tag, count(DISTINCT u) AS usuarios
ORDER BY usuarios DESC;

### 🔗 Usuários mais conectados
MATCH (u:User)-[:FOLLOWS]->()
RETURN u.username, count(*) AS conexoes
ORDER BY conexoes DESC
LIMIT 10;

## 🧭 7️⃣ Diagrama Automático no Neo4j Aura

No Neo4j Browser:

CALL db.schema.visualization();


✔️ Mostra:

Labels

Relacionamentos

Constraints

Índices

## 🎨 8️⃣ Diagrama Visual (Mermaid – documentação)
graph TD
  User -->|POSTED| Tweet
  User -->|FOLLOWS| User
  User -->|LIKED| Tweet
  Tweet -->|HAS_HASHTAG| Hashtag

## 🚀 Conclusão

Este protótipo entrega:

✔️ Banco de grafos realista (Twitter)

✔️ Neo4j Aura (cloud-ready)

✔️ Constraints e índices

✔️ Consultas complexas

✔️ Visualização e diagramação

✔️ Base pronta para:

GDS (comunidades)

Embeddings

Recomendação

IA generativa

