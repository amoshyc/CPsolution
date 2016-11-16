###################################################
2D 陣列對角線
###################################################

.. sidebar:: Tags

    - ``tag_math``
    - ``tag_template``

.. contents:: TOC
    :depth: 2

************************
程式碼
************************

:math:`N \times M` 的矩陣中，元素 :math:`(r, c)`，主副對解線編號公式為：

.. math::

    id_1 &= N - r - 1 + c \\
    id_2 &= r + c     \\

兩者的範圍都是半開區間 :math:`[0,\, N + M - 1)`

主對角線迭代

.. code-block:: cpp

    for (int id = 0; id < N + M - 1; id++) {
        int r = N - ((id < N) ? id : N - 1) - 1;
        int c = id - (N - r - 1);
        while (r < N && c < M) {
            cout << data[r][c] << " ";
            r++;
            c++;
        }
    }

副對角線迭代

.. code-block:: cpp

    for (int id = 0; id < N + M - 1; id++) {
        int r = ((id < N) ? id : N - 1);
        int c = id - r;
        while (r >= 0 && c < M) {
            cout << data[r][c] << " ";
            r--;
            c++;
        }
    }

基本想法是主對角線就是副對角線縱軸反轉，即 r' = N - r - 1
副對角線則有 r + c = id 的性質

--------------

例如 4x5 的陣列，各項的主對角線編號為

.. math::

    \begin{pmatrix}
    3 & 4 & 5 & 6 & 7 \\
    2 & 3 & 4 & 5 & 6 \\
    1 & 2 & 3 & 4 & 5 \\
    0 & 1 & 2 & 3 & 4 \\
    \end{pmatrix}

副對角線為

.. math::

    \begin{pmatrix}
    0 & 1 & 2 & 3 & 4 \\
    1 & 2 & 3 & 4 & 5 \\
    2 & 3 & 4 & 5 & 6 \\
    3 & 4 & 5 & 6 & 7 \\
    \end{pmatrix}


************************
模板驗證
************************
