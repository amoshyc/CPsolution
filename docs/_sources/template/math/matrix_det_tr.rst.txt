###################################################
矩陣 Determinant 與 Trace
###################################################

.. sidebar:: Tags

    - ``tag_gaussian``
    - ``tag_template``

.. contents:: TOC
    :depth: 2

************************
原理
************************

當矩陣中的每一項都是整數，矩陣的 trace 與 determinant 也會是整數。前者好求，但後者麻煩。
Determinant 一般都是透過 Row Operation 都矩陣上三角化，三角化的過程中：

.. math::

    &det(r_{ij}(A)) = -det(A) \\
    &det(r_i^{(k)}(A)) = k \cdot det(A) \\
    &det(r_{ij}^{(k)}(A)) = det(A)

三種列運算，第 1, 2 種會影響 determinant，要記得處理。不過本模板中只使用第 1, 3 種運算，所以只需考慮第 1 種列運算。

我們想避免使用浮點數來運算，因為會有誤差，我們可以使用輾轉相除法來代替，具體教學請參 `這裡`_ 。
基本想法就如同我們用下列程式碼來求 gcd:

.. code-block:: cpp
    :linenos: 

    int gcd(int a, int b) {
        while (b) {
            a = a % b;
            swap(a, b);
        }
        return a;
    }

只是我們把取餘數作用在整個 row 上。

.. _這裡: <http://blog.csdn.net/zhoufenqin/article/details/7779707

-----------------------

常見的考法為求矩陣特徵值的和與積，他們分別是該矩陣的 trace 與 determinant。

.. math::

    tr(A) &= \sum_{i} \lambda_i \\
    det(A) &= \prod_{i} \lambda_i

證明請參線代課本。

************************
程式碼
************************

.. code-block:: cpp
    :linenos:

    #define sz(x) (int(x.size()))
    typedef long long ll;
    typedef vector<ll> vec;
    typedef vector<vec> mat;

    ll trace(mat A) { // square matrix
        ll sum = 0;
        for (int i = 0; i < sz(A); i++) {
            sum += A[i][i];
        }
        return sum;
    }

    ll determinant(mat A) { // square matrix
        ll det = 1;
        for (int i = 0; i < sz(A); i++) {
            for (int j = i + 1; j < sz(A); j++) {
                int r1 = i, r2 = j;
                while (A[r2][i]) { // gcd
                    ll q = A[r1][i] / A[r2][i];
                    for (int k = 0; k < sz(A); k++)
                        A[r1][k] = A[r1][k] - A[r2][k] * q;
                    swap(r1, r2);
                }
                if (r1 != i) {
                    swap(A[i], A[j]);
                    det = -det;
                }
            }
            if (A[i][i] == 0)
                return 0;
            else
                det *= A[i][i];
        }
        return det;
    }

************************
模板驗證
************************

`uva684 <../../uva/p684.html>`_
`NCPC 2015 初賽 D <https://gist.github.com/anonymous/797dd10f4666f77588884908b76ebff0>`_
