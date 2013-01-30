List field definition
=====================

These fields are used to display the information inside the list table.

これらのフィールドはリストテーブル内に情報を表示する為に使われます。

Example
-------

.. code-block:: php

    <?php
    namespace Sonata\NewsBundle\Admin;

    use Sonata\AdminBundle\Admin\Admin;
    use Sonata\AdminBundle\Form\FormMapper;
    use Sonata\AdminBundle\Datagrid\DatagridMapper;
    use Sonata\AdminBundle\Datagrid\ListMapper;
    use Sonata\AdminBundle\Show\ShowMapper;

    class PostAdmin extends Admin
    {
        protected function configureListFields(ListMapper $listMapper)
        {
            $listMapper
                ->addIdentifier('title')
                ->add('author')
                ->add('enabled')
                ->add('tags')
                ->add('commentsEnabled')

                // add custom action links
                ->add('_action', 'actions', array(
                    'actions' => array(
                        'view' => array(),
                        'edit' => array(),
                    )
                ))
            ;
        }
    }

Types available
---------------

The most important option for each field is the ``type``: The available
types include:

それぞれのフィールドにおいて最も重要なオプションは ``type`` であり、以下のものが利用可能です。

* boolean
* datetime
* decimal
* identifier
* integer
* many_to_one : a link will be added to the related edit action (関連したeditアクションにリンクが追加される)
* string
* text
* date
* time

If no type is set, the ``Admin`` class will use the type defined in the doctrine
mapping definition.

もしタイプがセットされなければ ``Admin`` クラスはdoctrineのマッピング定義により決められたタイプを使います。

List Actions
------------

You can set actions for the list items by adding an '_action' field in ``configureListFields`` ( ``configureListFields`` 中に '_action' フィールドを追加することにより、リストアイテムにアクションをセットすることができます):

.. code-block:: php

    <?php
    $listMapper->add('_action', 'actions', array(
        'actions' => array(
            'view' => array(),
            'edit' => array(),
        )
    ))

Edit and delete actions are enabled in the default configuration. You can add
your own! Default template file is: ``SonataAdminBundle:CRUD:list__action_[ACTION_NAME].html.twig``

デフォルト設定ではeditとdeleteアクションは有効になっています。あなたは自分のをアクションを追加することができます。デフォルトテンプレートファイルは ``SonataAdminBundle:CRUD:list__action_[ACTION_NAME].html.twig`` です。

You can specify your own by setting up the 'template' option like so:

次のように 'テンプレート' オプションをセットアップすることで自身のテンプレートを記述することもできます。

.. code-block:: php

    <?php
    $listMapper->add('_action', 'actions', array(
        'actions' => array(
            'view' => array(),
            'edit' => array(),
            'delete' => array('template' => 'MyBundle:MyController:my_partial.html.twig'),
        )
    ))

Advance Usage
-------------

Displaying sub entity properties (子エンティティのプロパティを表示する)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you need to display only one field from a sub entity in a dedicated column,
you can simply use the dot-separated notation (note that this only makes sense
when the prefix path is made of entities, not collections):

もし、子エンティティから一つのフィールドだけ表示する必要があれば、単純にドットで区切った表記を使うことができます。
（接頭辞として付くパスが、collection型でなく、エンティティから作られている場合理にかなっています。）

.. code-block:: php

    <?php
    namespace Acme\AcmeBundle\Admin;

    use Sonata\AdminBundle\Admin\Admin;
    use Sonata\AdminBundle\Form\FormMapper;
    use Sonata\AdminBundle\Datagrid\DatagridMapper;
    use Sonata\AdminBundle\Datagrid\ListMapper;
    use Sonata\AdminBundle\Show\ShowMapper;

    class UserAdmin extends Admin
    {
        protected function configureListFields(ListMapper $listMapper)
        {
            $listMapper
                ->addIdentifier('id')
                ->addIdentifier('firstName')
                ->addIdentifier('lastName')
                ->addIdentifier('address.street')
                ->addIdentifier('address.ZIPCode')
                ->addIdentifier('address.town')
            ;
        }
    }


Custom template
^^^^^^^^^^^^^^^

If you need a specific layout for a row cell, you can define a custom template

もし行セルにたいして特別なレイアウトが必要であれば、カスタムテンプレートを定義することもできます。

.. code-block:: php

    <?php
    namespace Sonata\MediaBundle\Admin;

    use Sonata\AdminBundle\Admin\Admin;
    use Sonata\AdminBundle\Form\FormMapper;
    use Sonata\AdminBundle\Datagrid\DatagridMapper;
    use Sonata\AdminBundle\Datagrid\ListMapper;
    use Sonata\AdminBundle\Show\ShowMapper;

    class MediaAdmin extends Admin
    {
        protected function configureListFields(ListMapper $listMapper)
        {
            $listMapper
                ->addIdentifier('id')
                ->add('image', 'string', array('template' => 'SonataMediaBundle:MediaAdmin:list_image.html.twig'))
                ->add('custom', 'string', array('template' => 'SonataMediaBundle:MediaAdmin:list_custom.html.twig'))
            ;
        }
    }

The related template (関連したテンプレート) :

.. code-block:: jinja

    {% extends 'SonataAdminBundle:CRUD:base_list_field.html.twig' %}

    {% block field%}
        <div>
            <strong>{{ object.name }}</strong> <br />
            {{ object.providername}} : {{ object.width }}x{{ object.height }} <br />
        </div>
    {% endblock %}
