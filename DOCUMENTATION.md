# Documentation

1. Columns
   * [Defining Columns](#defining-columns)
       + [Column Conventions](#column-conventions)
   * [Sortable Columns](#sortable-columns)
   * [Searchable Columns](#searchable-columns)
   * [Filterable Columns](#filterable-columns)
   * [Column Types](#column-types)
       + [Text Column](#text-column)
       + [Number Column](#number-column)
       + [Decimal Column](#decimal-column)
       + [Text Column](#text-column)
       + [Percantage Column](#percentage-column)
       + [Boolean Column](#boolean-column)
       + [Date Column](#date-column)
       + [Computed Column](#computed-column)

## Columns

### Defining Columns

When you use `InteractsWithVueGoodTable` trait in your controller, it requires implementation of `getColumns` method.
Vue good table supports columns: number, decimal, percentage, boolean, date.

To add a columns, we can simply add it to the controllers's `getColumns` method.
Typically, columns may be created using their static make method.
This method accepts several arguments; however, you usually only need to pass the "human readable" name of the field.
Lvgt will automatically "snake case" this string to determine the underlying database column:


```php
use LaravelVueGoodTable\Columns\Number;
use LaravelVueGoodTable\Columns\Text;

/**
 * Get the columns displayed by the table.
 *
 * @return array
 */
public function getColumns: array
{
    return [
         Number::make('ID')
              ->sortable(),
                
         Text::make('Name'),
    ];
}
```

#### Column Conventions
As noted above, Lvgt will "snake case" the displayable name of the column to determine the underlying database column.
However, if necessary, you may pass the column name as the second argument to the field's make method:

```php
Text::make('Name', 'name_column')
```

### Sortable Columns
When attaching a column to a table, you may use the sortable method to indicate that the table may be sorted by the given column:
```php
Text::make('Name')->sortable()
```

### Searchable Columns
When attaching a column to a table, you may use the searchable method to indicate that the table may be filtered by search query entering by the given column:
```php
Text::make('Name')->searchable()
```

### Filterable Columns
When attaching a column to a table, you may use the filterable method to indicate that the table may be filtered by value in this column.

Simple input:
```php
Text::make('Id')->filterable(true, 'Type id')
```

Select:
```php
Text::make('Status')->filterable(true, 'Choose status', ['offline', 'online'])
```

Multiselect:
```php
Text::make('Hobby')->filterable(true, 'Choose hobbies', ['Sport', 'Dancing', 'PC-Gaming', 'Cooking'])
```

### Column Types
Let's explore all of the available types and their options. Most of them just extend `Text` column behavior.

#### Text Column
Takes attribute value from database result and put into table.
```php
use LaravelVueGoodTable\Columns\Text;

Text::make('Name')
```

You can set width of table column:
```php
Text::make('Name')->width('100px')
```

You can use display raw html inside the table cell
```php
Text::make('Name')->html()
```

If you need prepare value before displaying, use method `displayUsing`:
```php
Text::make('Category', 'category_id')
    ->displayUsing(function ($value, $row) {
        return "<a href='/categories/{$value}'>{$row->category_name}</a>";
    })
    ->html()
```

#### Number Column
Number - right aligned

#### Decimal Column
Decimal - right aligned, 2 decimal places

#### Percentage Column
Percentage - expects a decimal like 0.03 and formats it as 3.00%

#### Boolean Column
Boolean - right aligned

#### Date Column
Date - expects a string representation of date eg '20170530'. You should also specify `dateInputFormat` and `dateOutputFormat`.

```php
use LaravelVueGoodTable\Columns\Date;

Date::make('Created At', 'created_at')
    ->dateOutputFormat('dd.MM.yyyy HH:mm:ss')
```

Vue-good-table uses date-fns for date parsing. [Check out their formats here](https://date-fns.org/v2.0.0-beta.4/docs/parse)

#### Computed Column
If you need some extra field to put there you own value, no problem, use `ComputedField`:
```php
ComputedColumn::make('Random value', 'random', function ($resource) {
    return random(0, 100);
})
```
*Notice: You can't make computed column searchable or sortable.*