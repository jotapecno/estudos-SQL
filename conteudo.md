# DDL, DML e DQL - SQL

## DDL

### Tabelas

```sql
-- Excluir tabela caso ela exista
DROP TABLE IF EXISTS produtos;
DROP TABLE IF EXISTS categorias;

-- Criar nova tabela
CREATE TABLE produtos (
    codigo INT PRIMARY KEY,
    descricao VARCHAR(255) NOT NULL,
    preco DECIMAL(16, 2) NOT NULL,
    ativo BOOLEAN NOT NULL,
    data_cadastro TIMESTAMP NOT NULL
);
```

O `DROP TABLE IF EXISTS` remove a tabela informada caso ela já exista no banco, evitando erro quando ela ainda não foi criada. O `CREATE TABLE` cria a estrutura `produtos`, definindo `codigo` como chave primária, `descricao` como texto obrigatório, `preco` como valor decimal com até 16 dígitos e 2 casas decimais, `ativo` como campo booleano e `data_cadastro` como data/hora, ambos também obrigatórios (`NOT NULL`).

### Alteração de Tabelas

```sql
-- Adicionar uma nova coluna
ALTER TABLE produtos ADD COLUMN categoria VARCHAR(100);

-- Excluir uma coluna
ALTER TABLE produtos DROP COLUMN categoria;

-- Adicionar código da categoria
ALTER TABLE produtos ADD COLUMN categoria_codigo INT;
```

O `ALTER TABLE ... ADD COLUMN` adiciona uma nova coluna a uma tabela já existente, como `categoria` e, mais adiante, `categoria_codigo`. Já o `ALTER TABLE ... DROP COLUMN` remove uma coluna da tabela — nesse caso, a coluna `categoria` é removida antes de ser substituída pela abordagem com `categoria_codigo`.

### Criar tabela de categorias

```sql
CREATE TABLE categorias (
    codigo INT PRIMARY KEY,
    descricao VARCHAR(100) NOT NULL
);
```

Cria a tabela `categorias`, com `codigo` como chave primária e `descricao` como campo de texto obrigatório, que será usada posteriormente para relacionar produtos às suas categorias.

**Comandos utilizados em DDL**

| Comando | Descrição |
|---|---|
| DROP TABLE IF EXISTS | Remove uma tabela, caso ela exista, sem gerar erro se ela não existir |
| CREATE TABLE | Cria uma nova tabela, definindo colunas e tipos de dados |
| ALTER TABLE ... ADD COLUMN | Adiciona uma nova coluna a uma tabela já existente |
| ALTER TABLE ... DROP COLUMN | Remove uma coluna existente de uma tabela |
| PRIMARY KEY | Define a chave primária da tabela (identificador único) |
| NOT NULL | Torna o preenchimento do campo obrigatório |

---

## DML

### Insert

```sql
-- Inserir um registro
INSERT INTO produtos (codigo, descricao, preco, ativo, data_cadastro)
VALUES (10, 'placa ovo', 18.99, TRUE, CURRENT_TIMESTAMP);

-- Inserir outro registro
INSERT INTO produtos (codigo, descricao, preco, ativo, data_cadastro)
VALUES (20, 'peito de frango kg', 27.99, TRUE, CURRENT_TIMESTAMP);

-- Inserir outro registro
INSERT INTO produtos (codigo, descricao, preco, ativo, data_cadastro)
VALUES (30, 'peito de frango kg', 23.99, TRUE, CURRENT_TIMESTAMP);
```

O `INSERT INTO ... VALUES` adiciona um novo registro à tabela `produtos`, informando o valor de cada coluna listada. O campo `data_cadastro` recebe `CURRENT_TIMESTAMP`, que preenche automaticamente com a data e hora atuais do momento da inserção.

```sql
-- Inserir categorias
INSERT INTO categorias (codigo, descricao)
VALUES (1, 'Alimentos'), (2, 'Bebidas'), (3, 'Higiene');
```

Aqui vários registros são inseridos em uma única instrução, listando conjuntos de valores separados por vírgula — de forma equivalente a um `insertMany()` no MongoDB.

**Comandos utilizados em Insert**

| Comando | Descrição |
|---|---|
| INSERT INTO ... VALUES | Insere um novo registro (linha) em uma tabela |
| INSERT INTO ... VALUES (...), (...), (...) | Insere vários registros de uma só vez em uma única instrução |

### Update

```sql
-- Atualizar o preço de um produto
UPDATE produtos SET preco = 16.25 WHERE codigo = 10;

-- Associar produtos às categorias
UPDATE produtos SET categoria_codigo = 1 WHERE codigo IN (10, 30);
```

O `UPDATE ... SET ... WHERE` localiza o(s) registro(s) que atendem à condição do `WHERE` e altera o valor da coluna indicada — no primeiro caso, o preço do produto de código 10. No segundo exemplo, a cláusula `IN` permite aplicar a mesma atualização a vários registros de uma vez, verificando se o `codigo` está presente na lista informada (10 ou 30).

**Comandos utilizados em Update**

| Comando | Descrição |
|---|---|
| UPDATE ... SET ... WHERE | Atualiza o valor de uma ou mais colunas nos registros que atendem à condição |
| IN | Verifica se o valor de uma coluna está presente em uma lista de valores |

### Delete

```sql
-- Excluir um produto
DELETE FROM produtos WHERE codigo = 20;
```

O `DELETE FROM ... WHERE` remove o(s) registro(s) que atendem à condição informada — nesse caso, o produto de código 20. Sem o `WHERE`, o comando removeria todos os registros da tabela.

**Comandos utilizados em Delete**

| Comando | Descrição |
|---|---|
| DELETE FROM ... WHERE | Remove os registros que atendem à condição informada |

---

## DQL

### Select

```sql
-- Mostrar todos os registros
SELECT * FROM produtos;

-- Mostrar registros com código diferente de 0
SELECT * FROM produtos WHERE codigo != 0;

-- Mostrar registros ordenados pela descrição em ordem decrescente
SELECT * FROM produtos WHERE codigo != 0 ORDER BY descricao DESC;
```

O `SELECT *` sem filtro retorna todos os registros e colunas da tabela `produtos`. Adicionando `WHERE codigo != 0`, apenas os registros cujo código é diferente de 0 são retornados. Combinando com `ORDER BY descricao DESC`, o resultado é ordenado pela coluna `descricao` em ordem decrescente.

### Distinct

```sql
SELECT DISTINCT descricao FROM produtos p;
```

O `DISTINCT` elimina valores repetidos do resultado, retornando cada `descricao` apenas uma vez, mesmo que existam produtos diferentes com a mesma descrição.

### Order By + Limit

```sql
SELECT * FROM produtos p ORDER BY codigo DESC LIMIT 2;
```

Combina `ORDER BY codigo DESC` para ordenar os produtos do maior para o menor código com `LIMIT 2`, que restringe o resultado às duas primeiras linhas retornadas — ou seja, os dois produtos de maior código.

### Alias

```sql
-- Utilizar um apelido para a tabela
SELECT prod.codigo, prod.descricao, prod.preco FROM produtos prod;

-- Utilizar apelidos para as colunas
SELECT prod.codigo AS cod, prod.descricao AS descr, prod.preco AS prec FROM produtos prod;
```

No primeiro exemplo, `prod` é um apelido (alias) para a tabela `produtos`, usado para referenciar as colunas de forma mais curta. No segundo, o `AS` cria apelidos para as próprias colunas retornadas (`cod`, `descr`, `prec`), alterando apenas o nome de exibição do resultado, sem alterar os dados.

### Inner Join

```sql
SELECT p.codigo, p.descricao AS produto, p.preco, c.descricao AS categoria
FROM produtos p
INNER JOIN categorias c ON p.categoria_codigo = c.codigo;
```

O `INNER JOIN` combina registros das tabelas `produtos` e `categorias`, relacionando-os pela condição `p.categoria_codigo = c.codigo`. Assim, o resultado traz cada produto junto com a descrição de sua categoria correspondente, retornando apenas os produtos que possuem uma categoria associada.

**Comandos e cláusulas utilizados em DQL**

| Comando/Cláusula | Descrição |
|---|---|
| SELECT | Consulta e retorna dados de uma ou mais tabelas |
| WHERE | Filtra os registros retornados de acordo com uma condição |
| != | Operador de diferença — retorna registros diferentes do valor informado |
| ORDER BY | Ordena os resultados por uma coluna, em ordem crescente (padrão) ou decrescente (`DESC`) |
| DISTINCT | Remove valores repetidos, retornando apenas ocorrências únicas |
| LIMIT | Limita a quantidade de registros retornados pela consulta |
| AS | Cria um apelido (alias) para tabela ou coluna |
| INNER JOIN ... ON | Combina registros de duas tabelas com base em uma condição de relacionamento |
