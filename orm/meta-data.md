# Meta Data

Because the Entity doesn't extend any smart base class, we will have to tell Doctrine how to map the data from the database into the entity. There're multiple ways of doing this:

### Annotations

The annotation driver is set as default meta driver. It searches the entities in the `app` folder, but you can change this to whatever folder (or multiple folders) you like. Annotations means, that you will use docblocks to indicate the column mappings.

```php
<?php

use Doctrine\ORM\Mapping AS ORM;

/**
 * @ORM\Entity
 * @ORM\Table(name="articles")
 */
class Article
{
    /**
     * @ORM\Id
     * @ORM\GeneratedValue
     * @ORM\Column(type="integer")
     */
    protected $id;

    /**
     * @ORM\Column(type="string")
     */
    protected $title;

    public function getId()
    {
        return $this->id;
    }

    public function getTitle()
    {
        return $this->title;
    }

    public function setTitle($title)
    {
        $this->title = $title;
    }
}
```

More about the annotation driver: http://doctrine-orm.readthedocs.org/en/latest/reference/annotations-reference.html#

### YAML

If you prefer Yaml, you can easily switch the meta driver to `yaml`. It's better to change the meta data paths to something like `config_path('mappings')` instead of adding them all to the `app` folder.

```yaml
# App.Article.dcm.yml
App\Article:
  type: entity
  table: articles
  id:
    id:
      type: integer
      generator:
        strategy: AUTO
  fields:
    title:
      type: string
```

More about the YAML driver: http://doctrine-orm.readthedocs.org/en/latest/reference/yaml-mapping.html

### XML

Another option is to leverage XML mappings. Just like YAML it's better to change the meta data paths to something like `config_path('mappings')`.

```xml
// App.Article.dcm.xml
<?xml version="1.0" encoding="UTF-8"?>
<doctrine-mapping xmlns="http://doctrine-project.org/schemas/orm/doctrine-mapping"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://doctrine-project.org/schemas/orm/doctrine-mapping
                          http://raw.github.com/doctrine/doctrine2/master/doctrine-mapping.xsd">

    <entity name="App\Article" table="articles">

        <id name="id" type="integer" column="id">
            <generator strategy="AUTO"/>
            <sequence-generator sequence-name="tablename_seq" allocation-size="100" initial-value="1" />
        </id>

        <field name="title" column="title" type="string" />
    </entity>

</doctrine-mapping>
```

More information about XML mappings: http://doctrine-orm.readthedocs.org/en/latest/reference/xml-mapping.html

### Config files

This package adds another option, which leverages Laravel's config system. You could for example create a `config/mappings.php` file and set that inside the `mapping_file` setting.
The array structure is almost identical to the YAML one:

```php
<?php
return [
    'App\Article' => [
        'type'   => 'entity',
        'table'  => 'articles',
        'id'     => [
            'id' => [
                'type'     => 'integer',
                'strategy' => 'identity'
            ],
        ],
        'fields' => [
            'title' => [
                'type' => 'string'
            ]
        ]
    ]
];
```

### Static PHP

When you change the meta data driver setting to `static_php`, your entities will expect a `loadMetadata` method.

```php
<?php

class Article
{

    protected $id;
    protected $title;

    public static function loadMetadata(Doctrine\ORM\Mapping\ClassMetadata $metadata)
    {
        $metadata->mapField(array(
           'id'        => true,
           'fieldName' => 'id',
           'type'      => 'integer'
        ));

        $metadata->mapField(array(
           'fieldName' => 'title',
           'type'      => 'string'
        ));
    }
}
```

More on the StaticPHP driver: http://doctrine-orm.readthedocs.org/en/latest/reference/php-mapping.html
