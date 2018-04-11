# 手先力の計算

`torque-vector`とモデルから得られるヤコビアンの擬似逆行列を使う。

``` lisp
(setq *robot* (instance pr2-robot :init))

(setq *J*
   (send *robot* :calc-jacobian-from-link-list
      (send *robot* :link-list (send (send *robot* :larm :end-coords) :parent)
                               (send (car (send *robot* :larm :joint-list)) :child-link))
       :move-target (send *robot* :larm :end-coords)))

(setq *torque-vector* (float-vector 5 5 5 5 5 5 5))
;; (send *ri* :state :torque-vector)から左腕だけ取ってくる
(setq *force* (transform (pseudo-inverse2 (transpose *J*)) *torque-vector*))
```

**NOTE** 便利メソッドを発見

``` lisp
(setq *force* (send *robot* :calc-force-from-joint-torque :larm *torque-vector*))
```

内部では`sr-inverse`を使っているようだ。
