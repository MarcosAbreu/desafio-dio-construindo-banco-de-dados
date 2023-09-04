# Desafio DIO - Potência Tech powered by iFood | Ciência de Dados - Construindo seu Primeiro Projeto Lógico de Banco de Dados

Código para certificação DIO Potência Tech powered by iFood | Ciência de Dados. Módulo: Construindo seu Primeiro Projeto Lógico de Banco de Dados

## Desafio
Replicar a modelagem do projeto lógico de banco de dados para o cenário de e-commerce e desenvolver queries que atendam as seguintes clausulas:

* Recuperações simples com SELECT Statement
* Filtros com WHERE Statement
* Crie expressões para gerar atributos derivados
* Defina ordenações dos dados com ORDER BY
* Condições de filtros aos grupos – HAVING Statement
* Crie junções entre tabelas para fornecer uma perspectiva mais complexa dos dados

Para atender estes requisitos, devem ser elaboradas perguntas os quais as queries responderão.

## Ferramentas
* [MySQL Workbench](https://www.mysql.com/products/workbench/)
* [GitHub](https://github.com)

## Objetivo do Modelo

O modelo é na verdade um sistema de marketplace onde  N empresas com M produtos e X usuários possam ser cadastrados. Estas entidades, tem relação entre si como demonstrado no item **Modelo EER**

## Modelo EER

O modelo EER foi desenvolvido com base nas vídeo-aulas do desafio, sendo alterado para cumprir as requisições do desafio. Abaixo esta o screenshoot do modelo. O arquivo do modelo esta na localizado em `modelo/ecommerce.mwb`.


![Screenshot do Modelo](https://github.com/MarcosAbreu/desafio-dio-construindo-banco-de-dados/blob/main/modelo/modelo-screenshoot.png)

## Implementação SQL

O Banco de Dados foi implementado no MySQL Workbench seguindo as diretrizes de criação do Schema, Criação de Tabelas, Inserção de Dados para teste e Teste. As queries estão todas no arquivo `desafio-dio.sql`.

## Resultado do Desafio

A seguir, estão as perguntas realizadas, queries e resultados retornados.


### 1) Quais são os pedidos do usuário de ID = 2?

#### Query: 

~~~sql
SELECT  `e-commerce-dio`.`product`.`description` as `productDescription`,`e-commerce-dio`.`product`.`value`,
        `e-commerce-dio`.`product`.`category`,`e-commerce-dio`.`order`.`description` as `orderDescription`,
        `e-commerce-dio`.`order`.`status`, `e-commerce-dio`.`order`.`deliveryCost`

    FROM `e-commerce-dio`.`product_has_order`

    INNER JOIN `e-commerce-dio`.`order`
        ON `e-commerce-dio`.`product_has_order`.`Order_idOrder` = `e-commerce-dio`.`order`.`idOrder`

    INNER JOIN `e-commerce-dio`.`product`
        ON `e-commerce-dio`.`product_has_order`.`Product_idProduct` = `e-commerce-dio`.`product`.`idProduct`

    INNER JOIN `e-commerce-dio`.`client`
        ON `e-commerce-dio`.`order`.`Client_idClient` = `e-commerce-dio`.`client`.`idClient`

    WHERE `e-commerce-dio`.`client`.`idClient` = 2;
~~~

#### Resultado: 

productDescription | value | category | orderDescription | status | deliveryCost 
:--- | :---: | :---: | :---: | :---: | :---: |
Leite Santa Clara Integral 1L | 3,69 | Laticíneos | compra via aplicativo | Em Andamento | 50 |
Coco seco unidade | 8,90 | Hortifruti | compra via website | Processando | 150 |

<br>

### 2) Em quais fornecedores posso encontrar 'Alface crespa' ?

#### Query: 

~~~sql
SELECT `e-commerce-dio`.`product`.`description`, GROUP_CONCAT(`e-commerce-dio`.`supplier`.`corporate name`)
	
    FROM `e-commerce-dio`.`supplier_has_product`
    
    INNER join `e-commerce-dio`.`supplier`
        ON `e-commerce-dio`.`supplier_has_product`.`Supplier_idSupplier` = `e-commerce-dio`.`supplier`.`idSupplier`
    
    INNER join `e-commerce-dio`.`product`
        ON `e-commerce-dio`.`supplier_has_product`.`Product_idProduct` = `e-commerce-dio`.`product`.`idProduct`
    
    GROUP BY `e-commerce-dio`.`product`.`description`
    
    HAVING `e-commerce-dio`.`product`.`description` = 'Alface crespa';
~~~

#### Resultado: 

description | GROUP_CONCAT(e-commerce-dio.supplier.corporate name)
:--- | :---: 
Alface crespa | AMAZON,Shoppee

<br>

### 3) Quais os produtos disponíveis ordenados por categoria? 

#### Query: 

~~~sql
SELECT  `e-commerce-dio`.`product`.`description`,
        `e-commerce-dio`.`product`.`category`,
        `e-commerce-dio`.`product`.`value` 

    FROM `product` 
    ORDER BY `category`;
~~~


#### Resultado: 

description | category | value
:--- | :---: | :---:
Arroz Blue ville 1 Kg | Cereais | 4,79
Coco seco unidade | Hortifruti | 8,90
Alface crespa | Hortifruti | 1,99
Saco de Terra Preta 20 Kg | Jardim | 25,90
Leite Santa Clara Integral 1L | Laticíneos | 3,69
Yogurt Natural Batavo 200 mL | Laticíneos | 6,30
Desinfetante Veja 900mL | Limpeza | 14,79
Óleo de soja Leve 900 mL | Óleos |6,79

<br>

### 4) Qual é o detalhamento de entrega do pedido com o código de rastreio '20230801CX95PI021'? 

#### Query: 

~~~sql
SELECT `description`, `trackerCode`,`time`, `place` 
    
    FROM (
        SELECT  `e-commerce-dio`.`product_has_order`.`order_idOrder`,
                `e-commerce-dio`.`product_has_order`.`amount`,
                `e-commerce-dio`.`product`.`description`

            FROM `e-commerce-dio`.`product_has_order`

            INNER JOIN `e-commerce-dio`.`order`
                ON `e-commerce-dio`.`product_has_order`.`Order_idOrder` = `e-commerce-dio`.`order`.`idOrder`

            INNER JOIN `e-commerce-dio`.`product`
                ON `e-commerce-dio`.`product_has_order`.`Product_idProduct` = `e-commerce-dio`.`product`.`idProduct`
        ) `aninhada`

    INNER JOIN `e-commerce-dio`.`tracker`
	    ON `aninhada`.`Order_idOrder` = `e-commerce-dio`.`tracker`.`Order_idOrder`
    
    WHERE `trackerCode` = '20230801CX95PI021'
    ORDER BY `time` DESC;
~~~


#### Resultado: 

description | trackerCode | time | place
:--- | :---: | :---: | :---:
Óleo de soja Leve 900 mL | 20230801CX95PI021 | 2023-08-02 13:15:03 | Gravataí/RS
Óleo de soja Leve 900 mL | 20230801CX95PI021 | 2023-08-01 00:25:56 | Barueri/SP

<br>
