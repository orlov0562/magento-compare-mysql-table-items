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

More complex configuration
```
$compareOrderIds = [3120, 3121];

$tablesToDiff = [
    'sales_order' => [
        'entity_pk' => 'entity_id',
        'entity_ids' => $compareOrderIds,
    ],
    'sales_order_address' => [
        'entity_pk' => 'parent_id',
        'entity_ids' => $compareOrderIds,
    ],
    'sales_order_grid' => [
        'entity_pk' => 'entity_id',
        'entity_ids' => $compareOrderIds,
    ],
    'sales_order_item' => [
        'entity_pk' => 'order_id',
        'entity_ids' => $compareOrderIds,
    ],
    'sales_order_payment' => [
        'entity_pk'=>'parent_id',
        'entity_ids' => $compareOrderIds,
    ],
    'sales_order_status_history' => [
        'entity_pk' => 'parent_id',
        'entity_ids' => $compareOrderIds,
    ],
    'sales_order_tax' => [
        'entity_pk' => 'order_id',
        'entity_ids' => $compareOrderIds,
    ],
    'sales_order_tax_item' => [
        'entity_pk' => 'order_id',
        'entity_ids' => $compareOrderIds,
        'select' => 'sot.order_id as order_id',
        'join' => 'LEFT JOIN sales_order_tax sot ON sot.tax_id = sales_order_tax_item.tax_id',
        'where' => 'sot.$entity_pk = "$entity_id"',
        'column_append' => ['order_id'],
    ],
    'sales_creditmemo' => [
        'entity_pk' => 'order_id',
        'entity_ids' => $compareOrderIds,
    ],
    'sales_creditmemo_grid' => [
        'entity_pk' => 'order_id',
        'entity_ids' => $compareOrderIds,
    ],
    'sales_creditmemo_item' => [
        'entity_pk' => 'order_id',
        'entity_ids' => $compareOrderIds,
        'select' => 'scm.order_id as order_id',
        'join' => 'LEFT JOIN sales_creditmemo scm ON scm.entity_id = sales_creditmemo_item.parent_id',
        'where' => 'scm.$entity_pk = "$entity_id"',
        'column_append' => ['order_id'],
    ],
];
```

- entity_pk = column to compare
- entity_ids = ids, to compare that will be used to select items
- select = allows to add custom columns to the select, for example in case of joins (not obvious)
- join = allows to append joins (not obvious)
- where = conditions to select entities (default: $entity_pk = "$entity_id")
- column_append = append column to the comparison (not obvious, by default all columns from the table will be used)
- column_include = include only this columns into comparison
- column_exclude = exclude columns from comparison
