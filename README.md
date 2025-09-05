# PrimeiroProjetoLogicoBD
Construindo seu Primeiro Projeto Lógico de Banco de Dados

## Modelo
![](https://github.com/thiagofs84/PrimeiroProjetoLogicoBD/blob/main/ModeloEcommerce.png)

## Scripts para criação do BD e tabelas

```sql
-- criação do banco de dados para o cenário de E-commerce
CREATE DATABASE ecommerce;
USE ecommerce;

-- criar tabela cliente
CREATE TABLE cliente(
    idCliente INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    PNome VARCHAR(15),
    NomeMeioInicial VARCHAR(3),
    Sobrenome VARCHAR(25),
    CPF CHAR(11) NOT NULL,
    Endereço VARCHAR(45),
    DataNascto DATE,
    Pessoa ENUM('Fisica', 'Juridica') NOT NULL DEFAULT 'Fisica',
    CONSTRAINT unique_cpf_cliente UNIQUE (CPF)
);
ALTER TABLE cliente AUTO_INCREMENT = 1;

-- criar tabela produto
CREATE TABLE produto(
    idProduto INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    NomeProduto VARCHAR(15) NOT NULL,
    Vendedor VARCHAR(45),
    Categoria ENUM('Eletronico', 'Vestimenta', 'Brinquedos', 'Cosméticos') NOT NULL,
    Descrição VARCHAR(45) NOT NULL,
    Valor FLOAT NOT NULL,
    Dimensão FLOAT
);
ALTER TABLE produto AUTO_INCREMENT = 1;

-- criar tabela pedido
CREATE TABLE pedido(
    idPedido INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    cliente_idCliente INT,
    ValorFrete FLOAT,
    DataPedido DATE,
    DataEntrega DATE,
    Situação ENUM('Em andamento', 'Processando', 'Enviado', 'Entregue') NOT NULL DEFAULT 'Processando',
    Descrição VARCHAR(45),
    Rastreamento VARCHAR(255),
    forma_pagamento ENUM('cartão de crédito', 'pix', 'boleto') NOT NULL DEFAULT 'cartão de crédito',
    CONSTRAINT fk_pedidos_cliente FOREIGN KEY (cliente_idCliente) REFERENCES cliente(idCliente)
);
ALTER TABLE pedido AUTO_INCREMENT = 1;

-- criar tabela estoque
CREATE TABLE estoque(
    idEstoque INT PRIMARY KEY NOT NULL AUTO_INCREMENT,
    localizacao VARCHAR(255),
    quantidade FLOAT DEFAULT 0
);
ALTER TABLE estoque AUTO_INCREMENT = 1;

-- criar tabela fornecedor
CREATE TABLE fornecedor(
    idFornecedor INT PRIMARY KEY NOT NULL,
    RazaoSocial VARCHAR(50) NOT NULL,
    CNPJ VARCHAR(14) NOT NULL,
    contato VARCHAR(12) NOT NULL,
    CONSTRAINT unico_fornecedor UNIQUE (CNPJ)
);
ALTER TABLE fornecedor AUTO_INCREMENT = 1;

-- criar tabela parceiro
CREATE TABLE parceiro(
    idParceiro INT PRIMARY KEY NOT NULL AUTO_INCREMENT,
    RazaoSocial VARCHAR(45) NOT NULL UNIQUE,
    CNPJ VARCHAR(14),
    CPF VARCHAR(9),
    contato VARCHAR(12) NOT NULL,
    endereco VARCHAR(255),
    NomeFantasia VARCHAR(45) NOT NULL,
    CONSTRAINT unico_parceiro_cnpj UNIQUE (CNPJ),
    CONSTRAINT unico_parceiro_cpf UNIQUE (CPF)
);
ALTER TABLE parceiro AUTO_INCREMENT = 1;

-- criar tabela produto_parceiro
CREATE TABLE produto_parceiro(
    Parceiro_idParceiro INT,
    Produto_idProduto INT,
    Quantidade INT DEFAULT 1,
    PRIMARY KEY (Parceiro_idParceiro, Produto_idProduto),
    CONSTRAINT fk_parceiro FOREIGN KEY (Parceiro_idParceiro) REFERENCES parceiro(idParceiro),
    CONSTRAINT fk_produto_parceiro FOREIGN KEY (Produto_idProduto) REFERENCES produto(idProduto)
);
ALTER TABLE produto_parceiro AUTO_INCREMENT = 1;

-- criar tabela produto_pedido
CREATE TABLE produto_pedido(
    Pedido_idPedido INT,
    Produto_idProduto INT,
    Quantidade INT DEFAULT 1,
    Status ENUM('Disponivel', 'Sem estoque') DEFAULT 'Disponivel',
    PRIMARY KEY (Pedido_idPedido, Produto_idProduto),
    CONSTRAINT fk_pedido FOREIGN KEY (Pedido_idPedido) REFERENCES pedido(idPedido),
    CONSTRAINT fk_produto_pedido FOREIGN KEY (Produto_idProduto) REFERENCES produto(idProduto)
);
ALTER TABLE produto_pedido AUTO_INCREMENT = 1;

-- criar tabela produto_estoque
CREATE TABLE produto_estoque(
    Estoque_idEstoque INT,
    Produto_idProduto INT,
    localizacao VARCHAR(255) NOT NULL,
    quantidade FLOAT,
    PRIMARY KEY (Estoque_idEstoque, Produto_idProduto),
    CONSTRAINT fk_estoque FOREIGN KEY (Estoque_idEstoque) REFERENCES estoque(idEstoque),
    CONSTRAINT fk_produto_estoque FOREIGN KEY (Produto_idProduto) REFERENCES produto(idProduto)
);
ALTER TABLE produto_estoque AUTO_INCREMENT = 1;

-- criar tabela produto_fornecedor
CREATE TABLE produto_fornecedor(
    Fornecedor_idFornecedor INT,
    Produto_idProduto INT,
    RazaoSocial VARCHAR(50),
    PRIMARY KEY (Fornecedor_idFornecedor, Produto_idProduto),
    CONSTRAINT fk_fornecedor FOREIGN KEY (Fornecedor_idFornecedor) REFERENCES fornecedor(idFornecedor),
    CONSTRAINT fk_produto_fornecedor FOREIGN KEY (Produto_idProduto) REFERENCES produto(idProduto)
);
ALTER TABLE produto_fornecedor AUTO_INCREMENT = 1;
```

## Analises

### utilização do banco de dados

```sql
-- criação do banco de dados para o cenário de E-commerce
USE ecommerce;
```

###seleção simples dos nomes de cliente
'''sql
select
PNome,
NomeMeioInicial,
Sobrenome
from cliente;
'''




