# 仮想リンクによる拘束を付けた逆運動学計算

``` lisp
(require :pr2-interface "package://pr2eus/pr2-interface.l")
(load "models/room73b2-bottle-object.l")

;; setup robot / object
(pr2-init)
(setq b (room73b2-bottle))
(send b :translate (float-vector 700 0 800))
(objects (list *pr2* b))

;; pre grasp pose
(with-assoc-move-target
    (mt :move-target
        (send (send *pr2* :rarm :end-coords :copy-worldcoords)
              :translate (float-vector 100 0 0))
        :parent-link (send *pr2* :rarm :end-coords :parent))
  (send *pr2* :inverse-kinematics
        (car (send b :handle))
        :move-target (car mt)
        :debug-view :no-message
        :rotation-axis :z
        :link-list (send *pr2* :link-list (send (car mt) :parent))))
(send *ri* :angle-vector (send *pr2* :angle-vector))
(send *ri* :wait-interpolation)

;; open gripper
(send *ri* :stop-grasp :rarm :wait t)

;; grasp
(send *pr2* :rarm :move-end-pos #f(100 0 0))
(send *ri* :angle-vector (send *pr2* :angle-vector))
(send *ri* :wait-interpolation)
```
