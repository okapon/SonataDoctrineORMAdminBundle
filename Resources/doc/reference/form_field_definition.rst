Form field definition
=====================

Example
-------

.. code-block:: php

    <?php
    namespace Sonta\NewsBundle\Admin;

    use Sonata\AdminBundle\Admin\Admin;
    use Sonata\AdminBundle\Form\FormMapper;
    use Sonata\AdminBundle\Datagrid\DatagridMapper;
    use Sonata\AdminBundle\Datagrid\ListMapper;
    use Sonata\AdminBundle\Show\ShowMapper;
    use Sonata\AdminBundle\Validator\ErrorElement;

    class PostAdmin extends Admin
    {
        protected function configureFormFields(FormMapper $formMapper)
        {
            $formMapper
                ->add('author', 'sonata_type_model', array(), array('edit' => 'list'))
                ->add('enabled')
                ->add('title')
                ->add('abtract', null, array('required' => false))
                ->add('content')

                // you can define help messages like this
                ->setHelps(array(
                   'title' => $this->trans('help_post_title')
                ));

        }

        public function validate(ErrorElement $errorElement, $object)
        {
            // conditional validation, see the related section for more information
            if ($object->getEnabled()) {
                // abstract cannot be empty when the post is enabled
                $errorElement
                    ->with('abtract')
                        ->assertNotBlank()
                        ->assertNotNull()
                    ->end()
                ;
            }
        }
    }

.. note::

    By default, the form framework always sets ``required=true`` for each
    field. This can be an issue for HTML5 browsers as they provide client-side
    validation.
    デフォルトでフォームフレームワークは全てのフィールドに対していつも ``required=true`` をセットします。これは、クライアントサイドバリデーションを提供しているHTML5ブラウザにおける問題解決になることでしょう。

Types available
---------------

    - array
    - checkbox
    - choice
    - datetime
    - decimal
    - integer
    - text
    - date
    - time
    - datetime

If no type is set, the Admin class will use the one set in the doctrine mapping
definition.
もしタイプがセットされなければ ``Admin`` クラスはdoctrineのマッピング定義により決められたタイプを使います。

Advanced Usage: File Management
-------------------------------

If you want to use custom types from the Form framework you must use the
``addType`` method. (The ``add`` method uses the information provided by the
model definition).
もしフォームフレームワークによるカスタムタイプを使いたければ、 ``addType`` メソッドを使わなければならない。（ ``add`` メソッドはモデル定義で提供された情報を追加います）

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
        protected function configureFormFields(FormMapper $form)
        {
            $formMapper
                ->add('name', null, array('required' => false))
                ->add('enabled', null, array('required' => false))
                ->add('authorName', null, array('required' => false))
                ->add('cdnIsFlushable', null, array('required' => false))
                ->add('description', null, array('required' => false))
                ->add('copyright', null, array('required' => false))
                ->add('binaryContent', 'file', array('required' => false));
        }
  }

.. note::

    By setting ``type=false`` in the file definition, the Form framework will
    provide an instance of ``UploadedFile`` for the ``Media::setBinaryContent``
    method. Otherwise, the full path will be provided.
    file定義で ``type=false`` と設定することで、フォームフレームワークは ``Media::setBinaryContent`` メソッドのために ``UploadedFile`` インスタンスを提供します。フルパスが提供されるのは別の方法で。

Advanced Usage: Many-to-one
---------------------------

If you have many ``Post``s linked to one ``User``, then the ``Post`` form should
display a ``User`` field.
もしたくさんの ``Post'' が１人の ''User'' に関連性を持っていたら、 ``Post`` フォームは ``User`` フィールドを表示すべきである。

The AdminBundle provides 3 edit options:
AdminBundleは３つの編集オプションを提供しています。

 - ``standard``: default value, the ``User`` list is set in a select widget
 - ``list``: the ``User`` list is set in a model where you can search and select a user
 - ``inline``: embed the ``User`` form into the ``Post`` form, great for one-to-one, or if your want to allow the user to edit the ``User`` information.

 - ``standard``: デフォルト値で、 ``User`` リストはセレクトウィジェットでセットされます。
 - ``list``:  ``User`` リストはモデルから探してユーザー選択して設定できます。
 - ``inline``:  ``Post`` フォーム内に ``User`` フォームを埋め込むのは、一対一に最適であり、そうでなければ使用者に ``User`` 情報を編集することを許可させます。

With the ``standard`` and ``list`` options, you can create a new ``User`` by clicking on the "+" icon.
 ``standard`` と ``list`` オプションを用いた場合、 "+" のアイコンをクリックすることで新規に ``User`` を作成することができます。

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

Advanced Usage: One-to-many
---------------------------

Let's say you have a ``Gallery`` that links to some ``Media``s with a join table
``galleryHasMedias``. You can easily add a new ``galleryHasMedias`` row by
defining one of these options:
 ``Gallery`` が ``galleryHasMedias`` 結合テーブルで、いつくかの ``Media`` に関連しているとしましょう。以下のオプションのうち１つを定義することで ``galleryHasMedias`` 行を簡単に追加できます。

  - ``edit``: ``inline|standard``, the inline mode allows you to add new rows
  - ``inline``: ``table|standard``, the fields are displayed into table
  - ``sortable``: if the model has a position field, you can enable a drag and
    drop sortable effect by setting ``sortable=field_name``

  - ``edit``: ``inline|standard``, inlineiモードでは新しい行を追加することができます
  - ``inline``: ``table|standard``, フィールドがテーブル内に表示されます
  - ``sortable``: もしモデルが位置フィールドを持っていたら、 ``sortable=field_name`` と設定することでドラッグ＆ドロップで入れ替えできます

.. code-block:: php

    <?php
    namespace Sonata\MediaBundle\Admin;

    use Sonata\AdminBundle\Admin\Admin;
    use Sonata\AdminBundle\Form\FormMapper;
    use Sonata\AdminBundle\Datagrid\DatagridMapper;
    use Sonata\AdminBundle\Datagrid\ListMapper;

    class GalleryAdmin extends Admin
    {
        protected function configureFormFields(FormMapper $formMapper)
        {
            $formMapper
                ->add('code')
                ->add('enabled')
                ->add('name')
                ->add('defaultFormat')
                ->add('galleryHasMedias', 'sonata_type_collection', array(), array(
                    'edit' => 'inline',
                    'inline' => 'table',
                    'sortable'  => 'position'
                ))
            ;
        }
    }
