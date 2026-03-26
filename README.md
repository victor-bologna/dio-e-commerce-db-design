# E-commerce Database – README

Este projeto descreve o modelo de banco de dados de um sistema de **e‑commerce**, cobrindo cadastro de clientes (PF/PJ), pedidos, produtos, estoque, pagamentos, entregas, vendedores e fornecedores.

***

## Visão geral

O objetivo do modelo é permitir:

- Cadastro de clientes pessoa física e jurídica.  
- Registro de pedidos com múltiplos produtos.  
- Controle de estoque por região.  
- Registro de pagamentos em diferentes meios (crédito, débito, pix).  
- Controle de entregas associadas aos pedidos.  
- Cadastro de vendedores e fornecedores e seus produtos.

***

## Entidades principais

### CUSTOMER

Representa o cliente genérico do sistema.

- `id` (PK)  
- `address`  
- `id_payment` – forma de pagamento associada (opcional, dependendo da regra de negócio)  
- `customer_type` – indica se é pessoa física ou jurídica  

Relacionamentos:

- 1:N com `ORDER` (um cliente pode ter vários pedidos).  
- 1:1 com `PERSON` (quando PF).  
- 1:1 com `COMPANY` (quando PJ).

***

### PERSON (Pessoa Física)

Especialização de `CUSTOMER` para pessoa física.

- `id` (PK)  
- `id_customer` (FK → CUSTOMER.id)  
- `cpf`
- `telephone`

Cada registro em `PERSON` corresponde a um cliente PF.

***

### COMPANY (Pessoa Jurídica)

Especialização de `CUSTOMER` para pessoa jurídica.

- `id` (PK)  
- `id_customer` (FK → CUSTOMER.id)  
- `cnpj`  
- `fantasy_name`
- `telephone`

Cada registro em `COMPANY` corresponde a um cliente PJ.

***

### ORDER

Representa um pedido realizado por um cliente.

- `id` (PK)  
- `status`  
- `description`  
- `id_customer` (FK → CUSTOMER.id)  
- `id_payment` – (FK → PAYMENT.id)
- `total_amount`

Relacionamentos:

- N:N com `PRODUCT` via `ORDER_HAS_PRODUCTS`.  
- 1:N com `ORDER_DELIVERY` (caso existam múltiplas entregas/vinculações).
- 1:1 com `PAYMENT`

***

### PRODUCT

Representa um produto disponível para venda.

- `id` (PK)  
- `category`  
- `description`  
- `value`  

Relacionamentos:

- N:N com `ORDER` via `ORDER_HAS_PRODUCTS`.  
- N:N com `STOCK` via `PRODUCT_HAS_STOCK`.  
- N:N com `SELLER` via `SELLER_SELLS_PRODUCT`.  
- N:N com `SUPPLIER` via `SUPPLIER_OFFERS_PRODUCT`.

***

## Entidades de associação

### ORDER_HAS_PRODUCTS

Associa produtos a pedidos (itens do pedido).

- `id` (PK)  
- `id_order` (FK → ORDER.id)  
- `id_product` (FK → PRODUCT.id)  
- `quantity`  

### PRODUCT_HAS_STOCK

Associa produtos a estoques regionais.

- `id` (PK)  
- `id_product` (FK → PRODUCT.id)  
- `id_stock` (FK → STOCK.id)  
- `quantity`  

### SELLER_SELLS_PRODUCT

Associa vendedores aos produtos que eles vendem.

- `id` (PK)  
- `id_product` (FK → PRODUCT.id)  
- `id_seller` (FK → SELLER.id)  
- `quantity` (pode representar disponibilidade ou limite, conforme regra do sistema)

### SUPPLIER_OFFERS_PRODUCT

Associa fornecedores aos produtos que fornecem.

- `id` (PK)  
- `id_product` (FK → PRODUCT.id)  
- `id_supplier` (FK → SUPPLIER.id)  

***

## Estoque

### STOCK

Representa um estoque físico ou regional.

- `id` (PK)  
- `region` – identificador/região do estoque  

Relacionamentos:

- N:N com `PRODUCT` via `PRODUCT_HAS_STOCK`.

***

## Pagamentos

### PAYMENT

Entidade genérica para meios de pagamento.

- `id` (PK)  
- `type` – tipo de pagamento (ex.: “credit”, “debit”, “pix”)  

Relacionamentos:

- 1:N com `CUSTOMER` (ou com `ORDER`, dependendo da evolução do modelo).

## Entregas

### DELIVERY

Representa uma entrega vinculada a uma empresa de entrega.

- `id` (PK)  
- `id_company` (FK → COMPANY.id) – empresa responsável pela entrega  

### ORDER_DELIVERY

Associação entre pedidos e entregas.

- `id` (PK)  
- `id_delivery` (FK → DELIVERY.id)  
- `id_order` (FK → ORDER.id)  
- `id_tracking` – código de rastreio da entrega  

***

## Vendedores e Fornecedores

### SELLER

Representa um vendedor no sistema.

- `id` (PK)  

Relacionamentos:

- N:N com `PRODUCT` via `SELLER_SELLS_PRODUCT`.

### SUPPLIER

Representa um fornecedor de produtos.

- `id` (PK)  

Relacionamentos:

- N:N com `PRODUCT` via `SUPPLIER_OFFERS_PRODUCT`.

***

## Possíveis melhorias / extensões

- Adicionar campos de auditoria (`created_at`, `updated_at`).  
- Definir tamanhos e tipos mais específicos (por exemplo, `VARCHAR(100)`, `DECIMAL(10,2)`).  
- Criar constraints de unicidade para `cpf` e `cnpj`.  
- Refinar a ligação de `PAYMENT` para estar diretamente associada ao `ORDER`, caso cada pedido tenha seu próprio pagamento.  

Este README serve como documentação inicial do modelo de dados e pode ser usado como base para geração do esquema físico em um SGBD como MySQL ou PostgreSQL.
