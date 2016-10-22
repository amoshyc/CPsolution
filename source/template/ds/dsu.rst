###################################################
Disjoint Set 並查集
###################################################

.. sidebar:: Tags

    - ``tag_dsu``
    - ``tag_template``

.. contents:: TOC
    :depth: 2

************************
程式碼
************************

.. code-block:: cpp
    :linenos:

    const int MAX_V = ...;
    int V;
    int par[MAX_V];

    void init(int V) {
        fill(par, par + V, -1);
    }

    int root(int a) {
        if (par[a] < 0) return a;
        return (par[a] = root(par[a]));
    }

    void unite(int a, int b) {
        a = root(a);
        b = root(b);
        if (a == b) return; // already in same set
        if (-par[a] > -par[b]) swap(a, b); // if (size[a] > size[b])
        par[b] += par[a]; // size[b] += size[a]
        par[a] = b;
    }

    bool same(int a, int b) {
        return root(a) == root(b);
    }

    int size(int a) { // correct if a is root
        return -par[a];
    }

************************
模板驗證
************************

`cf 346 e <../../cf/cf346/pe.html>`_
