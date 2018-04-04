.. _read:

ファイルのインポート
==============================

初期データを作るというのが多いですが、既存データの加工や利用のほうが個人的には圧倒的に多いので、そちら基準で。

.. _read_csv:

CSVファイルのインポート
---------------------------

一般的なデータはやはり読み書きを最低限テキストエディタででも読めるCSVでしょう。

.. code-block:: python

    >>> import pandas as pd
    >>> data = pd.read_csv("static/sample.csv")
    >>> data
        社員ID     氏名  部署    入社日
    0  1432554  風雅 放下  開発   4月5日
    1  1332217  愚派 藻我  総務   4月2日
    2  5532289  吐血 安全  営業  8月20日

Excelシートの読込
----------------------

仕事の上では残念ながらExcel形式(:code:`*.xls`, :code:`*.xlsx`)が多いのです。幸い読込サポートが入ってますが、
前提として :code:`xlrd` を最低限いれておく必要があります。

.. code-block:: bash

    $ pip install xlrd

あとは専用のメソッド :code:`read_excel()` を使います。

.. code-block:: python

    >>> import pandas as pd
    >>> data = pd.read_excel("static/sample.xls")
    >>> data
        社員ID     氏名  部署        入社日
    0  1432554  風雅 放下  開発 2017-04-05
    1  1332217  愚派 藻我  総務 2018-04-02
    2  5532289  吐血 安全  営業 2016-08-20

あ、 :code:`xlrd` のインポートは裏でやってくれるみたいなので明示しなくていいみたいです。

.. note:: 

    むしろ逆に :code:`xlrd` 無しで読もうとすると親切に教えてくれます。

    .. code-block:: python

        >>> import pandas as pd
        >>> data = pd.read_excel("static/sample.xls")
        Traceback (most recent call last):
        File "/usr/local/lib/python3.6/site-packages/pandas/io/excel.py", line 261, in __init__
            import xlrd
        ModuleNotFoundError: No module named 'xlrd'

        During handling of the above exception, another exception occurred:

        Traceback (most recent call last):
        File "<stdin>", line 1, in <module>
        File "/usr/local/lib/python3.6/site-packages/pandas/util/_decorators.py", line 118, in wrapper
            return func(*args, **kwargs)
        File "/usr/local/lib/python3.6/site-packages/pandas/io/excel.py", line 230, in read_excel
            io = ExcelFile(io, engine=engine)
        File "/usr/local/lib/python3.6/site-packages/pandas/io/excel.py", line 263, in __init__
            raise ImportError(err_msg)
        ImportError: Install xlrd >= 0.9.0 for Excel support
