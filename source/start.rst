.. _setup:

環境のセットアップ
==================

当然使うにはPandasモジュールをいれておかないといけません。

.. code-block:: bash

    $ pip install -U pandas

あと、使う機会があるみたいなので、必要に応じて同様に :code:`numpy` もいれておくと良いと思います。

環境の仮想化を考えてる場合は
==============================

個人の権限で使いたいときは、venv(virtualenv)やpyenvを使うというお約束になると思います。
ただ、venvを使うと、モジュールビルドの際にネイティブコードのビルドでコケるのを何度か見てるので、私は
`pyenv <https://github.com/pyenv/pyenv>` を使うことが多いです。


