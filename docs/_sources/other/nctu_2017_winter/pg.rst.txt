#####################################
[hdu5443] G. The Water Problem
#####################################

.. sidebar:: Tags

    - ``tag_easy``

.. contents:: TOC
    :depth: 2

*************************************************************
`題目 <https://vjudge.net/contest/147988>`_
*************************************************************

************************
Specification
************************

************************
分析
************************

.. note:: 水題

************************
AC Code
************************

.. code-block:: cpp
    :linenos:

    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <algorithm>
    using namespace std;

    int main() {
        int TC;
        scanf("%d", &TC);
        while (TC--) {
            int N; scanf("%d", &N);

            vector<int> a(N, 0);
            for (int i = 0; i < N; i++) {
                scanf("%d", &a[i]);
            }

            int Q;
            scanf("%d", &Q);
            while (Q--) {
                int l, r; scanf("%d %d", &l, &r);
                l--; r--;
                printf("%d\n", *max_element(a.begin() + l, a.begin() + r + 1));
            }
        }
        return 0;
    }
