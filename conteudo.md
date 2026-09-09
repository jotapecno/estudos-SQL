// ==================================================
// DDL
// ==================================================


// ==================================================
// TABELAS
// ==================================================

// Excluir tabela caso ela exista
DROP TABLE IF EXISTS produtos;
DROP TABLE IF EXISTS categorias;

// Criar nova tabela
CREATE TABLE produtos (
    codigo INT PRIMARY KEY,
    descricao VARCHAR(255) NOT NULL,
    preco DECIMAL(16, 2) NOT NULL,
    ativo BOOLEAN NOT NULL,
    data_cadastro TIMESTAMP NOT NULL
);


// ==================================================
// ALTERAÇÃO DE TABELAS
// ==================================================

// Adicionar uma nova coluna
ALTER TABLE produtos
ADD COLUMN categoria VARCHAR(100);

// Excluir uma coluna
ALTER TABLE produtos
DROP COLUMN categoria;

// Adicionar código da categoria
ALTER TABLE produtos
ADD COLUMN categoria_codigo INT;


// ==================================================
// Criar tabela de categorias
// ==================================================

CREATE TABLE categorias (
    codigo INT PRIMARY KEY,
    descricao VARCHAR(100) NOT NULL
);


// ==================================================
// DML
// ==================================================


// ==================================================
// INSERT
// ==================================================

// Inserir um registro
INSERT INTO produtos 
(codigo, descricao, preco, ativo, data_cadastro)
VALUES
(10, 'placa ovo', 18.99, TRUE, CURRENT_TIMESTAMP);

// Inserir outro registro
INSERT INTO produtos 
(codigo, descricao, preco, ativo, data_cadastro)
VALUES
(20, 'peito de frango kg', 27.99, TRUE, CURRENT_TIMESTAMP);

// Inserir outro registro
INSERT INTO produtos 
(codigo, descricao, preco, ativo, data_cadastro)
VALUES
(30, 'peito de frango kg', 23.99, TRUE, CURRENT_TIMESTAMP);


// ==================================================
// Inserir categorias
// ==================================================

INSERT INTO categorias
(codigo, descricao)
VALUES
(1, 'Alimentos'),
(2, 'Bebidas'),
(3, 'Higiene');


// ==================================================
// UPDATE
// ==================================================

// Atualizar o preço de um produto
UPDATE produtos
SET preco = 16.25
WHERE codigo = 10;


// Associar produtos às categorias
UPDATE produtos
SET categoria_codigo = 1
WHERE codigo IN (10, 30);


// ==================================================
// DELETE
// ==================================================

// Excluir um produto
DELETE FROM produtos
WHERE codigo = 20;


// ==================================================
// DQL
// ==================================================


// ==================================================
// SELECT
// ==================================================

// Mostrar todos os registros
SELECT *
FROM produtos;

// Mostrar registros com código diferente de 0
SELECT *
FROM produtos
WHERE codigo != 0;

// Mostrar registros ordenados pela descrição em ordem decrescente
SELECT *
FROM produtos
WHERE codigo != 0
ORDER BY descricao DESC;


// ==================================================
// DISTINCT
// ==================================================

// Mostrar descrições sem repetir valores
SELECT DISTINCT descricao
FROM produtos p;


// ==================================================
// ORDER BY + LIMIT
// ==================================================

// Mostrar os 2 produtos com maior código
SELECT *
FROM produtos p
ORDER BY codigo DESC
LIMIT 2;


// ==================================================
// ALIAS
// ==================================================

// Utilizar um apelido para a tabela
SELECT
    prod.codigo,
    prod.descricao,
    prod.preco
FROM
    produtos prod;

// Utilizar apelidos para as colunas
SELECT
    prod.codigo AS cod,
    prod.descricao AS descr,
    prod.preco AS prec
FROM
    produtos prod;


// ==================================================
// INNER JOIN
// ==================================================

// Mostrar produtos junto com suas categorias
SELECT
    p.codigo,
    p.descricao AS produto,
    p.preco,
    c.descricao AS categoria
FROM produtos p
INNER JOIN categorias c
    ON p.categoria_codigo = c.codigo;
