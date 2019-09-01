# compare-mysql-tables
Script that compare two entities of the table and show the diff

I use that script to compare data changes between operations on entities in Magento. Eg compare orders before and after credit memo.

Next configuration, where 3120,3121 are orders id and first has credit memo and second no 
```
$compareOrderIds = [3120, 3121];

$tablesToDiff = [
    'sales_order' => [
        'entity_pk' => 'entity_id',
        'entity_ids' => $compareOrderIds,
        'column_include' => ['entity_id', 'increment_id', 'state', 'status'],
    ],
    'sales_creditmemo' => [
        'entity_pk' => 'order_id',
        'entity_ids' => $compareOrderIds,
        'column_include' => ['entity_id', 'order_id', 'increment_id'],
    ],
];
```
Will output
```
$ php data-diff.php 

TABLE: sales_order
----------------------------------------
entity_id    | 3120       | 3121       |
----------------------------------------
entity_id    | 3120       | 3121       |
state        | complete   | processing |
status       | complete   | processing |
increment_id | 0000002674 | 0000002675 |
========================================

TABLE: sales_creditmemo
----------------------------------
order_id     | 3120       | 3121 |
----------------------------------
entity_id    | 357        |      |
order_id     | 3120       |      |
increment_id | 0000000341 |      |
==================================

```
