Form types and data transformers
================================

The AdminBundle is shipped with custom form types and data transfomers in order
to handle the different model's workflows and lifecycle.

異なったモデルのワークフローやライフサイクルを対処するため、AdminBundleはカスタムフォームタイプとデータトランスフォーマーを備えて出荷されている（世に送り出されている）

Form types
----------

    - ``sonata_type_admin`` : this type is linked to an Admin class and the field construction is
      delegated to an Admin class. (このタイプはAdminクラスに関連付けられ、フィールド構造はAdminクラスに委任されている)

    - ``sonata_type_collection`` : this type works like the native ``CollectionType`` but contains two extra
      features (このタイプのは元々の ``CollectionType`` に似ているが、２つの追加機能を備えている): the data layer is abstracted to work with any implemented layer and a delete option is added
      so a collection entry can be deleted. (データ層はいかなる実行層とでも働くように抽象化され、削除オプションが追加された。よって、コレクションエントリは削除することができます。 「implemented layerは実行層という訳でいいのか？」)

    - ``sonata_type_model`` : this type works like the native ``EntityType`` but this internal is abstracted
      to work with any implemented layer. (このタイプのは元々の ``EntityType`` ににているが、この内部は実行層と連携するために抽象化されている。)

    - ``sonata_type_immutable_array``: this type allows to edit a fixed array, like a settings array. (このタイプは固定化された配列を配列設定のように編集することができます。)

Let's say, the object has settings properties

仮に、オブジェクトがプロパティ設定を持つとします。

.. code-block:: php

    <?php
    class Page
    {
        public $settings = array(
            'content'   => 'default content',
            'public'    => true,
            'type'      => 1
        );
    }

Now you can edit the settings array with :

次のように書くことで、配列を編集できるようになります。

.. code-block:: php

    <?php
    namespace Sonata\PageBundle\Admin;

    use Sonata\AdminBundle\Admin\Admin;
    use Sonata\AdminBundle\Form\FormMapper;
    use Sonata\AdminBundle\Datagrid\DatagridMapper;
    use Sonata\AdminBundle\Datagrid\ListMapper;
    use Sonata\AdminBundle\Show\ShowMapper;
    use Sonata\AdminBundle\Validator\ErrorElement;

    class PageAdmin extends Admin
    {

        protected function configureFormFields(FormMapper $formMapper)
        {
            $formMapper
                ->add('enabled')
                ->add('settings', 'sonata_type_immutable_array', array(
                    'keys' => array(
                        array('content', 'textarea', array()),
                        array('public', 'checkbox', array()),
                        array('type', 'choice', array('choices' => array(1 => 'type 1', 2 => 'type 2')))
                    )
            );
        }
    ));

the output will be :

表示は次のようになります。

.. image:: ../images/sonata_type_immutable_array.png
           :alt: Immutable Array Type
           :width: 460

Other options :

.. code-block:: php

    <?php
    namespace Sonata\NewsBundle\Admin;

    use Sonata\AdminBundle\Admin\Admin;
    use Sonata\AdminBundle\Form\FormMapper;
    use Sonata\AdminBundle\Datagrid\DatagridMapper;
    use Sonata\AdminBundle\Datagrid\ListMapper;
    use Sonata\AdminBundle\Show\ShowMapper;

    use Application\Sonata\NewsBundle\Entity\Comment;

    class PostAdmin extends Admin
    {
        protected function configureFormFields(FormMapper $formMapper)
        {
            $formMapper
                ->with('General')
                    ->add('enabled', null, array('required' => false))
                    ->add('author', 'sonata_type_model', array(), array('edit' => 'list'))
                    ->add('title')
                    ->add('abstract')
                    ->add('content')
                ->end()
                ->with('Tags')
                    ->add('tags', 'sonata_type_model', array('expanded' => true))
                ->end()
                ->with('Options', array('collapsed' => true))
                    ->add('commentsCloseAt')
                    ->add('commentsEnabled', null, array('required' => false))
                    ->add('commentsDefaultStatus', 'choice', array('choices' => Comment::getStatusList()))
                ->end()
            ;
        }
    }


Datatransformer
---------------

    - ``ArrayToModelTransformer`` : transform an array to an object (配列からオブジェクトへ変換する)
    - ``ModelsToArrayTransformer`` : transform a collection of array into a collection of object (配列のコレクションからオブジェクトのコレクションへ変換する)
    - ``ModelToIdTransformater`` : transform an ``id`` into an object ( ``id`` からオブジェクトへ変換する)
