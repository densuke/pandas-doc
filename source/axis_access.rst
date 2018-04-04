.. _axis-access:

ちょっとしたデータの読み出し
=============================

読み込んだデータは、データフレーム(DataFrame)というクラスのインスタンスとして処理されます。

.. code-block:: python

    >>> import pandas as pd
    >>> data = pd.read_csv("static/sample.csv") # データフレームとして取り込み
    >>> data
        社員ID     氏名  部署    入社日
    0  1432554  風雅 放下  開発   4月5日
    1  1332217  愚派 藻我  総務   4月2日
    2  5532289  吐血 安全  営業  8月20日

見てのとおり、データフレームを単純に評価させると、表っぽく吐き出してくれます。
行(row)単位でのアクセスは、自動的にインデックス(index)が付き、デフォルト0から付けてくれます。
最初の行はデフォルトとして、カラム名として機能します。

.. _access_row:

行単位のアクセス
---------------------

単純に行がほしいときは、インデックス指定でアクセスできるように見えます。
でも、できません。

.. code-block:: python3

    >>> data[0]
    Traceback (most recent call last):
    File "/usr/local/lib/python3.6/site-packages/pandas/core/indexes/base.py", line 2525, in get_loc
        return self._engine.get_loc(key)
    File "pandas/_libs/index.pyx", line 117, in pandas._libs.index.IndexEngine.get_loc
    File "pandas/_libs/index.pyx", line 139, in pandas._libs.index.IndexEngine.get_loc
    File "pandas/_libs/hashtable_class_helper.pxi", line 1265, in pandas._libs.hashtable.PyObjectHashTable.get_item
    File "pandas/_libs/hashtable_class_helper.pxi", line 1273, in pandas._libs.hashtable.PyObjectHashTable.get_item
    KeyError: 0

    During handling of the above exception, another exception occurred:

    Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
    File "/usr/local/lib/python3.6/site-packages/pandas/core/frame.py", line 2139, in __getitem__
        return self._getitem_column(key)
    File "/usr/local/lib/python3.6/site-packages/pandas/core/frame.py", line 2146, in _getitem_column
        return self._get_item_cache(key)
    File "/usr/local/lib/python3.6/site-packages/pandas/core/generic.py", line 1842, in _get_item_cache
        values = self._data.get(item)
    File "/usr/local/lib/python3.6/site-packages/pandas/core/internals.py", line 3843, in get
        loc = self.items.get_loc(item)
    File "/usr/local/lib/python3.6/site-packages/pandas/core/indexes/base.py", line 2527, in get_loc
        return self._engine.get_loc(self._maybe_cast_indexer(key))
    File "pandas/_libs/index.pyx", line 117, in pandas._libs.index.IndexEngine.get_loc
    File "pandas/_libs/index.pyx", line 139, in pandas._libs.index.IndexEngine.get_loc
    File "pandas/_libs/hashtable_class_helper.pxi", line 1265, in pandas._libs.hashtable.PyObjectHashTable.get_item
    File "pandas/_libs/hashtable_class_helper.pxi", line 1273, in pandas._libs.hashtable.PyObjectHashTable.get_item
    KeyError: 0

実はPandasでは、データが時系列に入ってくるといった感じで、列指向になってるんですね。
そのため、行で取得するには :code:`loc` などのアクセスメソッドを使います。他に :code:`at` といったのもあるみたいです。

Pythonの内部構造がわかってない私にとってはコレ自体がけっこう鬼門で、()ではダメで、[]で取得なんですよね。

.. code:: python

    # メソッドとして呼ぶとその場所を示すオブジェクトが返る(これで後から呼び出せる?)
    >>> data.loc(0)
    <pandas.core.indexing._LocIndexer object at 0x10eb526d8>

    # []を使うと指定行の値が得られる、正確にはSeriesというクラスのインスタンス
    >>> type(data.loc[0])
    <class 'pandas.core.series.Series'>
    >>> data.loc[0]
    社員ID    1432554
    氏名        風雅 放下
    部署           開発
    入社日        4月5日
    Name: 0, dtype: object

.. _access_numrows:

行数を知るには?
-----------------

行数の把握は、 :code:`__len__()` を使いましょう(マテ)、
いえいえ、 :code:`len()` 関数(というかアダプター)を使うことになるでしょう。

.. code-block:: python

    >>> len(data)
    3

.. _access_column:

列指向のアクセス
--------------------

列でのアクセスは、配列(辞書)要素でのアクセスと、DataFrameクラスが自動生成したアクセサを用いたアクセスが用意されています。

.. code-block:: python3

    # 配列(辞書)っぽいアクセス
    >>> data['社員ID']
    0    1432554
    1    1332217
    2    5532289
    Name: 社員ID, dtype: int64

    # アクセサメソッドを用いたアクセス
    >>> data.氏名
    0    風雅 放下
    1    愚派 藻我
    2    吐血 安全
    Name: 氏名, dtype: object

データとしての意味はありませんが、得られたデータに対して集合演算とか可能です。一番単純なのは :code:`sum()` でしょうか。

.. code-block:: python3

    >>> data.社員ID.sum()
    8297060

.. _rename_columns:

カラム名の操作周辺
-----------------------

Excel由来のデータだと、1行目(カラム名扱い)が日本語になってる場合が多く、途中で日本語変換をいれまくらないといけないのがすごく腹立たしいです。

取得、カラム数
....................

まず、カラム名については、 :code:`columns` アクセサで何があるかを返してくれます。
このオブジェクトは長さも取得できるようにサポートされてるので、 :code:`len()` が使えます。

.. code-block:: python3

    >>> data.columns
    Index(['社員ID', '氏名', '部署', '入社日'], dtype='object')

    >>> len(data.columns)
    4

変更
........

日本語のカラム名はキモいので、書き換えましょう。
:code:`rename()` が使えます。 :code:`columns` 引数に辞書の形で渡せば行えます。

.. code-block:: python3

    >>> data.rename(columns={'社員ID': 'id'})
            id     氏名  部署    入社日
    0  1432554  風雅 放下  開発   4月5日
    1  1332217  愚派 藻我  総務   4月2日
    2  5532289  吐血 安全  営業  8月20日

ただし元データは操作せず、書き換えた新しいDataFrameを返す仕様です。
そのため、カラム名が変わったものを受け取り直すか、:code:`inplace` 引数を使うといいでしょう。

.. code-block:: python3

    # 新しい変数で受け取る例
    >>> newdata = data.rename(columns={'社員ID': 'id'})
    >>> data
        社員ID     氏名  部署    入社日  # ←変わってない
    0  1432554  風雅 放下  開発   4月5日
    1  1332217  愚派 藻我  総務   4月2日
    2  5532289  吐血 安全  営業  8月20日
    >>> newdata
            id     氏名  部署    入社日   # ←こちらは書き換えられたものが入ってる
    0  1432554  風雅 放下  開発   4月5日
    1  1332217  愚派 藻我  総務   4月2日
    2  5532289  吐血 安全  営業  8月20日

    # inplaceで直接書き換えてみる
    >>> newdata2 = data.rename(columns={'社員ID': 'id'}, inplace=True)
    >>> data
            id     氏名  部署    入社日  # ← 書き換わりました
    0  1432554  風雅 放下  開発   4月5日
    1  1332217  愚派 藻我  総務   4月2日
    2  5532289  吐血 安全  営業  8月20日
    >>> newdata2
    >>>                                # ← data.rename()がなにも返してないため空

