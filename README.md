# Construindo-seu-Primeiro-Projeto-L-gico-de-Banco-de-Dados

-- Criação das tabelas

CREATE TABLE Cliente (
    id_cliente INT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    tipo CHAR(2) CHECK (tipo IN ('PF', 'PJ')),
    cpf VARCHAR(14) UNIQUE,
    cnpj VARCHAR(18) UNIQUE
);

CREATE TABLE Endereco (
    id_endereco INT PRIMARY KEY,
    id_cliente INT NOT NULL,
    logradouro VARCHAR(200) NOT NULL,
    numero VARCHAR(10) NOT NULL,
    complemento VARCHAR(50),
    bairro VARCHAR(100) NOT NULL,
    cidade VARCHAR(100) NOT NULL,
    estado CHAR(2) NOT NULL,
    cep VARCHAR(9) NOT NULL,
    FOREIGN KEY (id_cliente) REFERENCES Cliente(id_cliente)
);

CREATE TABLE Telefone (
    id_telefone INT PRIMARY KEY,
    id_cliente INT NOT NULL,
    ddd VARCHAR(3) NOT NULL,
    numero VARCHAR(9) NOT NULL,
    FOREIGN KEY (id_cliente) REFERENCES Cliente(id_cliente)
);

CREATE TABLE Pagamento (
    id_pagamento INT PRIMARY KEY,
    id_cliente INT NOT NULL,
    forma_pagamento VARCHAR(50) NOT NULL,
    FOREIGN KEY (id_cliente) REFERENCES Cliente(id_cliente)
);

CREATE TABLE Produto (
    id_produto INT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    descricao TEXT,
    preco DECIMAL(10, 2) NOT NULL,
    estoque INT NOT NULL
);

CREATE TABLE Categoria (
    id_categoria INT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL
);

CREATE TABLE ProdutoCategoria (
    id_produto INT NOT NULL,
    id_categoria INT NOT NULL,
    PRIMARY KEY (id_produto, id_categoria),
    FOREIGN KEY (id_produto) REFERENCES Produto(id_produto),
    FOREIGN KEY (id_categoria) REFERENCES Categoria(id_categoria)
);

CREATE TABLE Pedido (
    id_pedido INT PRIMARY KEY,
    id_cliente INT NOT NULL,
    id_endereco INT NOT NULL,
    id_pagamento INT NOT NULL,
    data_pedido DATE NOT NULL,
    status VARCHAR(20) NOT NULL,
    codigo_rastreio VARCHAR(50),
    FOREIGN KEY (id_cliente) REFERENCES Cliente(id_cliente),
    FOREIGN KEY (id_endereco) REFERENCES Endereco(id_endereco),
    FOREIGN KEY (id_pagamento) REFERENCES Pagamento(id_pagamento)
);

CREATE TABLE PedidoItem (
    id_pedido INT NOT NULL,
    id_produto INT NOT NULL,
    quantidade INT NOT NULL,
    valor_unitario DECIMAL(10, 2) NOT NULL,
    PRIMARY KEY (id_pedido, id_produto),
    FOREIGN KEY (id_pedido) REFERENCES Pedido(id_pedido),
    FOREIGN KEY (id_produto) REFERENCES Produto(id_produto)
);

-- Inserção de dados de exemplo

INSERT INTO Cliente (id_cliente, nome, tipo, cpf, cnpj)
VALUES
    (1, 'João Silva', 'PF', '123.456.789-00', NULL),
    (2, 'Maria Souza', 'PF', '987.654.321-00', NULL),
    (3, 'Empresa LTDA', 'PJ', NULL, '12.345.678/0001-90');

INSERT INTO Endereco (id_endereco, id_cliente, logradouro, numero, complemento, bairro, cidade, estado, cep)
VALUES
    (1, 1, 'Rua A', '123', 'Apto 101', 'Centro', 'São Paulo', 'SP', '01234-567'),
    (2, 2, 'Avenida B', '456', NULL, 'Jardins', 'Rio de Janeiro', 'RJ', '98765-432'),
    (3, 3, 'Rua C', '789', 'Sala 10', 'Barra Funda', 'São Paulo', 'SP', '12345-678');

INSERT INTO Telefone (id_telefone, id_cliente, ddd, numero)
VALUES
    (1, 1, '011', '12345678'),
    (2, 2, '021', '87654321'),
    (3, 3, '011', '99887766');

INSERT INTO Pagamento (id_pagamento, id_cliente, forma_pagamento)
VALUES
    (1, 1, 'Cartão de Crédito'),
    (2, 2, 'Boleto Bancário'),
    (3, 3, 'Transferência Bancária');

INSERT INTO Produto (id_produto, nome, descricao, preco, estoque)
VALUES
    (1, 'Camiseta', 'Camiseta de algodão', 29.90, 100),
    (2, 'Calça Jeans', 'Calça Jeans básica', 89.90, 50),
    (3, 'Tênis Esportivo', 'Tênis para corrida e caminhada', 149.90, 25);

INSERT INTO Categoria (id_categoria, nome)
VALUES
    (1, 'Vestuário'),
    (2, 'Calçados');

INSERT INTO ProdutoCategoria (id_produto, id_categoria)
VALUES
    (1, 1),
    (2, 1),
    (3, 2);

INSERT INTO Pedido (id_pedido, id_cliente, id_endereco, id_pagamento, data_pedido, status, codigo_rastreio)
VALUES
    (1, 1, 1, 1, '2023-05-01', 'Entregue', 'ABC123'),
    (2, 2, 2, 2, '2023-05-02', 'Enviado', 'DEF456'),
    (3, 3, 3, 3, '2023-05-03', 'Processando', NULL);

INSERT INTO PedidoItem (id_pedido, id_produto, quantidade, valor_unitario)
VALUES
    (1, 1, 2, 29.90),
    (1, 3, 1, 149.90),
    (2, 2, 1, 89.90),
    (3, 1, 3, 29.90),
    (3, 2, 2, 89.90);

-- Queries

-- Recuperações simples com SELECT Statement
SELECT * FROM Cliente;
SELECT nome, preco FROM Produto;

-- Filtros com WHERE Statement
SELECT * FROM Pedido WHERE status = 'Entregue';
SELECT * FROM Cliente WHERE tipo = 'PF';

-- Expressões para gerar atributos derivados
SELECT id_pedido, SUM(quantidade * valor_unitario) AS total_pedido
FROM PedidoItem
GROUP BY id_pedido;

-- Ordenações dos dados com ORDER BY
SELECT * FROM Produto ORDER BY preco DESC;
SELECT nome, data_pedido FROM Pedido ORDER BY data_pedido ASC;

-- Condições de filtros aos grupos - HAVING Statement
SELECT id_cliente, COUNT(*) AS total_pedidos
FROM Pedido
GROUP BY id_cliente
HAVING COUNT(*) > 1;

-- Junções entre tabelas para fornecer uma perspectiva mais complexa dos dados
SELECT c.nome AS cliente, p.nome AS produto, pi.quantidade, pi.valor_unitario, (pi.quantidade * pi.valor_unitario) AS total_item
FROM Cliente c
JOIN Pedido pe ON c.id_cliente = pe.id_cliente
JOIN PedidoItem pi ON pe.id_pedido = pi.id_pedido
JOIN Produto p ON pi.id_produto = p.id_produto;
