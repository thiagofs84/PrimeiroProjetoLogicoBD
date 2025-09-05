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
USE ecommerce;
```

### seleção simples dos nomes de cliente

```sql
select
PNome,
NomeMeioInicial,
Sobrenome
from cliente;
```
#### retorno:
![](https://github.com/thiagofs84/PrimeiroProjetoLogicoBD/blob/main/1-sele%C3%A7%C3%A3o%20simples%20dos%20nomes%20de%20cliente.png)

### nome completo e tipo de cliente
```sql
select
concat(
	PNome, " ",	NomeMeioInicial, " ", Sobrenome
) as "Nome Completo",
Pessoa
from cliente;
```
#### retorno:
![](https://github.com/thiagofs84/PrimeiroProjetoLogicoBD/blob/main/2-nome%20completo%20e%20tipo%20de%20cliente.png)

### Quantidade de pedidos por cliente
```sql
select
    CONCAT(PNome, ' ', NomeMeioInicial, ' ', Sobrenome
    ) as `Nome Completo`,
    count(pedido.idPedido) as `Qtdd de Pedidos`
from cliente
inner join pedido 
    on cliente.idCliente = pedido.cliente_idCliente
group by cliente.idCliente, PNome, NomeMeioInicial, Sobrenome
order by concat(PNome," ",NomeMeioInicial," ",Sobrenome);
```
#### retorno:
![](https://github.com/thiagofs84/PrimeiroProjetoLogicoBD/blob/main/3-Quantidade%20de%20pedidos%20por%20cliente.png)

### Relação Produto, fornecedor e estoque
```sql
select
  produto.idProduto,
  produto.NomeProduto,
  fornecedor.idFornecedor,
  fornecedor.RazaoSocial,
  estoque.idEstoque,
  coalesce(produto_estoque.localizacao, estoque.localizacao) as Localização,
  produto_estoque.quantidade
from produto
join produto_fornecedor
  on produto_fornecedor.Produto_idProduto = produto.idProduto
	join fornecedor
	on fornecedor.idFornecedor = produto_fornecedor.Fornecedor_idFornecedor
		join produto_estoque
		on produto_estoque.Produto_idProduto = produto.idProduto
			join estoque
			on estoque.idEstoque = produto_estoque.Estoque_idEstoque
order by Localização, produto.NomeProduto, fornecedor.RazaoSocial;
```
#### retorno:
![](https://github.com/thiagofs84/PrimeiroProjetoLogicoBD/blob/main/4-Rela%C3%A7%C3%A3o%20Produto-fornecedor-estoque.png)

### pedidos entregues
```sql
select *
from pedido
where pedido.Situação = "Entregue";
```
#### retorno:
![](https://github.com/thiagofs84/PrimeiroProjetoLogicoBD/blob/main/5-pedidos%20entregues.png)

### tempo de entrega por pedido
```sql
select
idPedido,
DataEntrega - DataPedido
from pedido
where pedido.Situação = "Entregue";
```
#### retorno:
![](https://github.com/thiagofs84/PrimeiroProjetoLogicoBD/blob/main/6-tempo%20de%20entrega%20por%20pedido.png)

### média de tempo de entrega mensal
```sql
select 
    year(DataPedido) as Ano,
    month(DataPedido) as Mes,
    round(avg(datediff(DataEntrega, DataPedido)),1) as "Média Mensal de Tempo de Entrega"
from pedido
where pedido.Situação = 'Entregue'
group by year(DataPedido), month(DataPedido)
order by Ano, Mes;
```
#### retorno:
![](https://github.com/thiagofs84/PrimeiroProjetoLogicoBD/blob/main/7-media%20de%20tempo%20de%20entrega%20mensal.png)

### pedidos em atraso
```sql
 SELECT 
    idPedido,
    DataPedido,
    DATEDIFF(CURDATE(), DataPedido) AS DiasEmAberto,
    Situação
FROM pedido
WHERE pedido.Situação <> 'Entregue'
  AND DATEDIFF(CURDATE(), DataPedido) > (
      SELECT AVG(DATEDIFF(DataEntrega, DataPedido))
      FROM pedido
      WHERE pedido.Situação = 'Entregue'
  )
ORDER BY DiasEmAberto DESC;
```
#### retorno:
![](https://github.com/thiagofs84/PrimeiroProjetoLogicoBD/blob/main/8-pedidos%20em%20atraso.png)







