# 衝突回避拘束を入れて逆運動学計算

``` lisp
(require :pr2 "package://pr2eus/pr2.l")
(pr2)
(objects (list *pr2*)) ;; init pr2 robot visualizer

(send *pr2* :reset-pose)

(send *pr2* :inverse-kinematics
      (list (make-coords :pos (float-vector 370 -200 770))  ;; goal position for left arm
            (make-coords :pos (float-vector 370 200 790)))  ;; for right arm
      :move-target (list (send *pr2* :larm :end-coords) (send *pr2* :rarm :end-coords))  ;; use both arms
      :link-list (list ;; register links to move
                  (send *pr2* :link-list (send (send *pr2* :larm :end-coords) :parent))
                  (send *pr2* :link-list (send (send *pr2* :rarm :end-coords) :parent)))
      :rotation-axis (list nil nil)
      :avoid-collision-distance 100
      :avoid-collision-null-gain 5.0 ;; this parameter affects how solver take efforts to find collision free pose
      :avoid-collision-joint-gain 0.8
      :collision-avoidance-link-pair ;; register link pairs to take account for each links.
      (reduce #'append
              (mapcar #'(lambda (rarm-link)
                          (mapcar #'(lambda (larm-link) (list larm-link rarm-link))
                                  (send *pr2* :larm :links)))
                      (send *pr2* :rarm :links)))
      :debug-view :no-messge ;; show results for each iteration to viewer
      :stop 100  ;; iteration count to solve inverse-kinematics
      )
```
