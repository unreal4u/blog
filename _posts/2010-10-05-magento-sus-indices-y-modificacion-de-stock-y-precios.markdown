---
author: admin
comments: true
date: 2010-10-05 22:31:14+00:00
layout: post
slug: magento-sus-indices-y-modificacion-de-stock-y-precios
title: 'Magento: sus índices y modificación de stock y precios'
wordpress_id: 454
categories:
- Mundo Web
- PHP
---

Tuve la suerte de conocer este año Magento: una plataforma de  e-commerce, muy completa y que apenas lleva un año rondando en la Web.  Sin embargo, ha sido por un lado una fortuna y por otro, una des-fortuna  conocer este CMS.
Fortuna debido a que está muy bien hecho, 100% orientado a objetos y  realizado con el framework de Zend. Tiene algunas opciones muy avanzadas  y en general el planteamiento mismo de la base está muy bien hecho. Así  por ejemplo, la base de datos implementa un [esquema tipo EAV](http://en.wikipedia.org/wiki/Entity-attribute-value_model) el cual, en un principio, puede parecer complicada. Sin embargo, con el  tiempo uno va viendo que es mejor así. Lo otro que también está muy  bien hecho, es su sistema de templates.

Sin embargo, también ha sido una des-fortuna debido a su complejidad  inicial y su pobre documentación. No existe un lugar centralizado que  sea bueno donde hayan tips, consejos, descripción de métodos y otros y  mucha información uno la puede ir sacando a través de los distintos  blogs que existen.
<!-- more -->

Por lo mismo, en esta entrada compartiré con ustedes cómo hacer una  modificación externa de los precios y stock (en la empresa donde trabajo  hay un ERP con el que se trabaja y es, en estricto rigor, el que  manda), para que de esta forma, Magento pueda estar al tanto de todas  las modificaciones de precios y por supuesto, de stock.


## Rápido barniz


Para actualizar el stock y el precio de forma externa, primero  debemos actualizar la base de datos. Esto se hace con las siguientes  consultas:
[sql]-- Precio:
UPDATE catalog_product_entity_decimal SET value = '43000.00' WHERE attribute_id = 64 AND entity_id = $a['entity_id']
-- Stock:
UPDATE cataloginventory_stock_item SET qty = '23',is_in_stock = '.$is_in_stock.' WHERE product_id = $a['entity_id']
[/sql]
Dos notas:
1.- $a['entity_id'] se refiere a la id interna que Magento le asigna al  producto en sí. No es difícil de sacar, involucra estas tablas:
`catalog_product_entity
catalog_product_entity_decimal
cataloginventory_stock_item`
2.- $is_in_stock puede tener dos valores: 0 si el stock es igual o menor a 0, ó 1 si es mayor que este valor. En breve:

    
    if ($b['qty'] > 0) $is_in_stock = 1;
    else $is_in_stock = 0;
    


Hasta ahí todo bien: en el administrador podemos darnos cuenta de que  los productos con stock tienen ingresado el nuevo stock y que también  tienen sus precios al día. Sin embargo, aquí empieza lo bueno: ¡en el  frontend no hay ningún cambio!
Afortunadamente, [leí en un blog](http://www.mrnordstrom.com/2010/06/09/day-9-magento-rebuild-the-search-index-automatically/) que se puede reconstruir el índice de forma automática y que (mejor aún) existe un programa para hacer esto: shell/indexer.php.

En breve:
[bash]crontab -e
# una vez dentro
*/30 * * * * /usr/bin/php /home/[usuario]/erp/actualiza.php &amp;&amp; /usr/bin/php /home/[usuario]/public_html/shell/indexer.php reindexall >/dev/null 2>&1
[/bash]
La última parte (`>/dev/null 2>&1`) es para que  no se mande un mail al usuario cada vez que se ejecute un cron. En este  mail irá toda la salida que se produzca así que puede ser bueno eliminar  ese código por motivos de depuración.

Y aquí es donde entra la curiosidad: indexer.php, al pasarle el  parámetro reindexall reindexa absolutamente todos los índices... no  sería mejor reconstruir sólo el del stock? Me puse a investigar y me  encontré con el comando `indexer.php --info`, que retorna la siguiente salida:

    
    catalog_product_attribute     Product Attributes
    catalog_product_price         Product Prices
    catalog_url                   Catalog URL Rewrites
    catalog_product_flat          Product Flat Data
    catalog_category_flat         Category Flat Data
    catalog_category_product      Category Products
    catalogsearch_fulltext        Catalog Search Index
    tag_summary                   Tag Aggregation Data
    cataloginventory_stock        Stock Status
    


Todos esos atributos podemos pasarle al script para que reindexe sólo uno de los índices y alivianar la pega del servidor.

Y eso sería todo por hoy, espero que les haya servido :) Quizás, pero muy quizás en una próxima entrega, explicaré un poco el asunto de los templates en Magento.
