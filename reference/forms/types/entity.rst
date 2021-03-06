.. index::
   single: Forms; Fields; choice

entity Field Type
=================

A special ``choice`` field that's designed to load options from a Doctrine
entity. For example, if you have a ``Category`` entity, you could use this
field to display a ``select`` field of all, or some, of the ``Category``
objects from the database.

+-------------+------------------------------------------------------------------+
| Rendered as | can be various tags (see :ref:`forms-reference-choice-tags`)     |
+-------------+------------------------------------------------------------------+
| Options     | - `class`_                                                       |
|             | - `property`_                                                    |
|             | - `query_builder`_                                               |
|             | - `em`_                                                          |
+-------------+------------------------------------------------------------------+
| Overridden  | - `choices`                                                      |
| Options     | - `choice_list`                                                  |
+-------------+------------------------------------------------------------------+
| Inherited   | - `required`_                                                    |
| options     | - `label`_                                                       |
|             | - `multiple`_                                                    |
|             | - `expanded`_                                                    |
|             | - `preferred_choices`_                                           |
|             | - `empty_value`_                                                 |
|             | - `read_only`_                                                   |
|             | - `error_bubbling`_                                              |
+-------------+------------------------------------------------------------------+
| Parent type | :doc:`choice</reference/forms/types/choice>`                     |
+-------------+------------------------------------------------------------------+
| Class       | :class:`Symfony\\Bridge\\Doctrine\\Form\\Type\\EntityType`       |
+-------------+------------------------------------------------------------------+

Basic Usage
-----------

The ``entity`` type has just one required option: the entity which should
be listed inside the choice field::

    $builder->add('users', 'entity', array(
        'class' => 'AcmeHelloBundle:User',
        'property' => 'username',
    ));

In this case, all ``User`` objects will be loaded from the database and rendered
as either a ``select`` tag, a set or radio buttons or a series of checkboxes
(this depends on the ``multiple`` and ``expanded`` values).
If the entity object does not have a ``__toString()`` method the ``property`` option
is needed.

Using a Custom Query for the Entities
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you need to specify a custom query to use when fetching the entities (e.g.
you only want to return some entities, or need to order them), use the ``query_builder``
option. The easiest way to use the option is as follows::

    use Doctrine\ORM\EntityRepository;
    // ...

    $builder->add('users', 'entity', array(
        'class' => 'AcmeHelloBundle:User',
        'query_builder' => function(EntityRepository $er) {
            return $er->createQueryBuilder('u')
                ->orderBy('u.username', 'ASC');
        },
    ));

.. include:: /reference/forms/types/options/select_how_rendered.rst.inc

Field Options
-------------

class
~~~~~

**type**: ``string`` **required**

The class of your entity (e.g. ``AcmeStoreBundle:Category``). This can be
a fully-qualified class name (e.g. ``Acme\StoreBundle\Entity\Category``)
or the short alias name (as shown prior).

property
~~~~~~~~

**type**: ``string``

This is the property that should be used for displaying the entities
as text in the HTML element. If left blank, the entity object will be
cast into a string and so must have a ``__toString()`` method.

query_builder
~~~~~~~~~~~~~

**type**: ``Doctrine\ORM\QueryBuilder`` or a Closure

If specified, this is used to query the subset of options (and their
order) that should be used for the field. The value of this option can
either be a ``QueryBuilder`` object or a Closure. If using a Closure,
it should take a single argument, which is the ``EntityRepository`` of
the entity.

em
~~

**type**: ``string`` **default**: the default entity manager

If specified, the specified entity manager will be used to load the choices
instead of the default entity manager.

Overridden Options
------------------

choices
~~~~~~~

**default**: ``null``

choice_list
~~~~~~~~~~~

**default**: all entities selected

The choices will default to all entities selected with one of the options that
are documented above.

Inherited options
-----------------

These options inherit from the :doc:`choice</reference/forms/types/choice>` type:

.. include:: /reference/forms/types/options/multiple.rst.inc

.. note::
    
    If you are working with a collection of Doctrine entities, it will be helpful
    to read the documention for the :doc:`/reference/forms/types/collection`
    as well. In addition, there is a complete example in the cookbook article
    :doc:`/cookbook/form/form_collections`.
    
.. include:: /reference/forms/types/options/expanded.rst.inc

.. include:: /reference/forms/types/options/preferred_choices.rst.inc

.. include:: /reference/forms/types/options/empty_value.rst.inc

These options inherit from the :doc:`field</reference/forms/types/field>` type:

.. include:: /reference/forms/types/options/required.rst.inc

.. include:: /reference/forms/types/options/label.rst.inc

.. include:: /reference/forms/types/options/read_only.rst.inc

.. include:: /reference/forms/types/options/error_bubbling.rst.inc
