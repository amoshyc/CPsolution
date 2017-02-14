amoshyc's CPsolution
##########################

This repo contains some solutions to the problem of some online judges and competitive programming contest. The source code is mainly writtne in C++, sometimes Python. This is a new repo of the `old one <https://github.com/amoshyc/ojsolution>`_, which is maintained now. My codeforces account is ``amoshuangyc``, and poj's is ``amoshuang``.

This repo is written in rst + sphinx and be hold on `Github Pages <https://amoshyc.github.io/CPsolution/>`_. If you find any mistake, it's welcome to create an issue here at this repo or email me.

這個 repo 是用來記錄我在程式競賽中與 online judge 上解題的題解。程式語言是以 C++ 為主，有時會用 python。部份內容是移自這個 `repo <https://github.com/amoshyc/ojsolution>`_，目前該 repo 已廢止，之後所有題解都發佈在這。我 codeforces 的帳號是 ``amoshuangyc``，poj 是 ``amoshuang``。

這些筆記我是以 rst + sphinx 來撰寫，最後顯示結果是在 `Github Pages <https://amoshyc.github.io/CPsolution/>`_ 上。如果你發現任何錯誤，歡迎在這個 repo 發 issue 或傳 email 給我。最後在這註記一下安裝與設定的過程

Requirements
========================

1. python3-sphinx (``sudo dnf install python3-sphinx``)
2. rtd-theme (``pip3 install sphinx_rtd_theme``)

Make a similar repo
========================

1. ``git clone <new repo> && cd <new repo>``
2. ``sphinx-quickstart .``
3. Append following code to makefile.

.. code-block:: make

    .PHONY: upload
    MSG = "Build at $(shell /bin/date '+%Y-%m-%d %H-%M-%S')"
    upload:
        make clean
        make html
        @echo
        rm -rf ./docs/*
        cp -r ./build/html/* ./docs/
        touch ./docs/.nojekyll
        @echo
        git add -A
        git commit -m $(MSG)
        git push origin master

4. Append/modify following code to conf.py

.. code-block:: python

    import sphinx_rtd_theme

    def setup(app):
        app.add_stylesheet('css/mine.css')

    suppress_warnings = ['image.nonlocal_uri']

    html_theme = 'sphinx_rtd_theme'
    html_theme_path = [sphinx_rtd_theme.get_html_theme_path()]

5. Set the github repo to generate github pages from master/docs/

6. You can now use ``make html`` to build and ``make upload`` to upload repo to github. You can find your output at ``https://<username>.github.io/<new repo>/``
