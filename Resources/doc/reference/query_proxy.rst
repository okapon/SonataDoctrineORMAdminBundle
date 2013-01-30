Doctrine ORM Proxy Query
========================


The ``ProxyQuery`` object is used to add missing features from the original Doctrine Query builder :

 ``ProxyQuery`` オブジェクトはオリジナルのDoctrine Query builder になかった機能を追加する為に使われます。

  - ``execute`` method - no need to call the ``getQuery()`` method (``execute`` メソッドは ``getQuery()`` メソッドを呼ぶ必要がない)
  - add sort by and sort order options (sort by と sort順オプションを追加)
  - add preselect id query on left join query, so a limit query will be only applied on the left statement and
    not on the full select statement. This simulates the original Doctrine 1 behavior. ()


.. code-block:: php

    <?php
    use Sonata\AdminBundle\Datagrid\ORM\ProxyQuery;

    $queryBuilder = $this->em->createQueryBuilder();
    $queryBuilder->from('Post', 'p');

    $proxyQuery = new ProxyQuery($queryBuilder);
    $proxyQuery->leftJoin('p.tags', t);
    $proxyQuery->setSortBy('name');
    $proxyQuery->setMaxResults(10);

    $results = $proxyQuery->execute();
