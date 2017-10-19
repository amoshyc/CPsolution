###################################################
矩形距離與重疊面積
###################################################

.. sidebar:: Tags

    - ``tag_geometry``
    - ``tag_template``

.. contents:: TOC
    :depth: 2

************************
程式碼
************************

.. code-block:: cpp
    :linenos:

    struct Rect {
        db x1, y1, x2, y2;
        db overlap_area(const Rect& r) const {
            db dx = min(x2, r.x2) - max(x1, r.x1);
            db dy = min(y1, r.y1) - max(y2, r.y2);
            return ((dx > eps && dy > eps) ? dx * dy : 0.0);
        }
        bool overlap(const Rect& r) const {
            return overlap_area(r) > eps;
        }
        bool overlap_x(const Rect& r) const {
            Rect r1 = Rect{x1, +1, x2, -1};
            Rect r2 = Rect{r.x1, +1, r.x2, -1};
            return r1.overlap(r2);
        }
        bool overlap_y(const Rect& r) const {
            Rect r1 = Rect{-1, y1, +1, y2};
            Rect r2 = Rect{-1, r.y1, +1, r.y2};
            return r1.overlap(r2);
        }
        db dis(const Rect& r) const {
            if (overlap_area(r) > eps) {
                return 0;
            }
            if (overlap_x(r)) {
                return min(abs(y1 - r.y2), abs(y2 - r.y1)); // dy
            }
            if (overlap_y(r)) {
                return min(abs(x1 - r.x2), abs(x2 - r.x1)); // dx
            }
            db mn = INF;
            db xs1[4] = {x1, x1, x2, x2};
            db ys1[4] = {y1, y2, y1, y2};
            db xs2[4] = {r.x1, r.x1, r.x2, r.x2};
            db ys2[4] = {r.y1, r.y2, r.y1, r.y2};
            for (int i = 0; i < 4; i++) {
                for (int j = 0; j < 4; j++) {
                    db dx = (xs1[i] - xs2[j]);
                    db dy = (ys1[i] - ys2[j]);
                    mn = min(mn, dx * dx + dy * dy);
                }
            }
            return sqrt(mn);
        }
    };

************************
模板驗證
************************

待測
