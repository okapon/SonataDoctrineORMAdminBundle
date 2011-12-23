Audit
=====

The bundle provides a support for the ``EntityAuditBundle`` from https://github.com/simplethings/EntityAudit.
この(Audit)バンドルは https://github.com/simplethings/EntityAudit による ``EntityAuditBundle`` の支援を提供します。

How it works
------------

**Extract from the original documentation**
元のドキュメントからの引用です

There are a bunch of different approaches to auditing or versioning of database tables. This extension creates a
mirroring table for each audited entities table that is suffixed with "_audit". Besides all the columns of the
audited entity there are two additional fields:
これはデータベーステーブルの監査とバージョン管理を行うための一連の異なった試みです。このエクステンションは"_audit"という接尾辞が付けたれた監査されるエンティティテーブルごとにミラーリングテーブルを作成します。加えて監査されるエンティティの全てのカラムには２つの追加フィールドがあります。

- rev : Contains the global revision number generated from a "revisions" table.
- revtype : Contains one of 'INS', 'UPD' or 'DEL' as an information to which type of database operation caused
  this revision log entry.

- rev : "revisions"テーブルにより生成された全体のリビジョン番号を含んでいます。
- revtype : このリビジョンのログエントリーでどんなデータベース操作が行われるかの情報として、'INS', 'UPD', 'DEL'のどれか１つを含んでいる

The global revision table contains an id, timestamp, username and change comment field.
全体のリビジョンテーブルは id, timestamp, username,change commentフィールドを持っています。

With this approach it is possible to version an application with its changes to associations at the particular
points in time.
points in time.
この方法により特定時点におけるアプリケーションのバージョンと関連付けることができます。

This extension hooks into the SchemaTool generation process so that it will automatically create the necessary
DDL statements for your audited entities.
スキーマツールの生成プロセスにこのエクステンションのフックを入れることにより、あなたの管理されたエンティティ向けに必要なDDLステートメントが自動的に作られます。


Configuration
-------------

If the ``EntityAuditBundle`` is enabled, then all entities managed by the ``DoctrineORMAdminBundle`` will be audited.

It is possible to disable an entity to be audited with the attribute audit="false" in services.xml
For instance :
.. code-block:: xml

    <service id="tutorial.blog.admin.post" class="Tutorial\BlogBundle\Admin\PostAdmin">
        <tag name="sonata.admin" manager_type="orm" audit="false" group="tutorial_blog" label="post"/>

        <argument/>
        <argument>Tutorial\BlogBundle\Entity\Post</argument>
        <argument>TutorialBlogBundle:PostAdmin</argument>
    </service>


Usage
-----

Once the ``EntityAuditBundle`` is set, then 2 new actions are availables :

 - /admin/vendor/entity/{id}/history : display the history list
 - /admin/vendor/entity/{id}/history/{revision} : display the object at a specific revision

These actions are available in the ``view`` and ``edit`` action. Please note the current implementation uses
the ``show`` definition to display the revision.
