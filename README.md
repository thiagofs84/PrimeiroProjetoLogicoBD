# PrimeiroProjetoLogicoBD
Construindo seu Primeiro Projeto Lógico de Banco de Dados

## Modelo
![](https://github.com/thiagofs84/PrimeiroProjetoLogicoBD/blob/main/ModeloEcommerce.png)

## Scripts para criação do BD e tabelas
-- criação do banco de dados para o cenário de E-commerce
'''create database ecommerce;
use ecommerce;'''

-- criar tabela cliente
create table cliente(
idCliente int not null auto_increment primary key,
PNome varchar(15),
NomeMeioInicial varchar(3),
Sobrenome varchar (25),
CPF char(11) not null,
Endereço varchar(45),
DataNascto date,
Pessoa enum('Fisica', 'Juridica') not null default 'Fisica',
constraint unique_cpf_cliente unique (CPF)
);
alter table cliente auto_increment = 1;

-- criar tabela produto
create table produto(
idProduto int not null auto_increment primary key,
NomeProduto varchar(15) not null,
Vendedor varchar(45),
Categoria enum('Eletronico', 'Vestimenta', 'Brinquedos', 'Cosméticos') not null,
Descrição varchar (45) not null,
Valor float not null,
Dimensão float
);
alter table produto auto_increment = 1;

-- criar tabela pedido
create table pedido(
idPedido int not null auto_increment primary key,
cliente_idCliente int,
ValorFrete float,
DataPedido date,
DataEntrega date,
Situação enum('Em andamento', 'Processando', 'Enviado', 'Entregue') default('Processando') not null,
Descrição varchar(45),
Rastreamento varchar(255),
forma_pagamento enum('cartão de crédito', 'pix', 'boleto') default('cartão de crédito') not null,
constraint fk_pedidos_cliente foreign key (Cliente_idCliente) references cliente(idCliente)
);
alter table pedido auto_increment = 1;
-- criar tabela estoque

create table estoque(
idEstoque int primary key not null auto_increment,
localizacao varchar(255),
quantidade float default 0
);
-- criar tabela fornecedor
alter table estoque auto_increment = 1;

create table fornecedor(
idFornecedor int primary key not null,
RazaoSocial varchar(50) not null,
CNPJ varchar(14) not null,
contato varchar(12) not null,
constraint unico_fornecedor unique (CNPJ)
);
alter table fornecedor auto_increment = 1;

-- criar a tabela parceiro

create table parceiro(
idParceiro int primary key not null auto_increment,
RazaoSocial varchar(45) not null unique,
CNPJ varchar(14),
CPF varchar(9),
contato varchar(12) not null,
endereco varchar(255),
NomeFantasia varchar(45) not null,
constraint unico_parceiro_cnpj unique (CNPJ),
constraint unico_parceiro_cpf unique (CPF)
);
alter table parceiro auto_increment = 1;

-- criar a tabela Produto_Parceiro

create table produto_parceiro(
Parceiro_idParceiro int,
Produto_idProduto int,
Quantidade int default 1,
primary key (Parceiro_idParceiro, Produto_idProduto),
constraint fk_parceiro foreign key (Parceiro_idParceiro) references parceiro(idParceiro),
constraint fk_produto_parceiro foreign key (Produto_idProduto) references produto(idProduto)
);
alter table produto_parceiro auto_increment = 1;

-- criar a tabela Produto_Pedido

create table produto_pedido(
Pedido_idPedido int,
Produto_idProduto int,
Quantidade int default 1,
Status enum('Disponivel', 'Sem estoque') default'Disponivel',
primary key (Pedido_idPedido, Produto_idProduto),
constraint fk_pedido foreign key (Pedido_idPedido) references pedido(idPedido),
constraint fk_produto_pedido foreign key (Produto_idProduto) references produto(idProduto)
);
alter table produto_pedido auto_increment = 1;

-- criar a tabela Produto_Estoque

create table produto_estoque(
Estoque_idEstoque int,
Produto_idProduto int,
localizacao varchar(255) not null,
quantidade float,
primary key (Estoque_idEstoque, Produto_idProduto),
constraint fk_estoque foreign key (Estoque_idEstoque) references estoque(idEstoque),
constraint fk_produto_estoque foreign key (Produto_idProduto) references produto(idProduto)
);
alter table produto_estoque auto_increment = 1;

-- criar a tabela Produto_Fornecedor

create table produto_fornecedor(
Fornecedor_idFornecedor int,
Produto_idProduto int,
RazaoSocial varchar(50),
primary key (Fornecedor_idFornecedor, Produto_idProduto),
constraint fk_fornecedor foreign key (Fornecedor_idFornecedor) references fornecedor(idFornecedor),
constraint fk_produto_fornecedor foreign key (Produto_idProduto) references produto(idProduto)
);
alter table produto_fornecedor auto_increment = 1;
