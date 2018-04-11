# Viewerの画像を取得する

``` lisp
(require :pr2 "package://pr2eus/pr2.l")

(setq *pr2* (pr2))

(objects (list *pr2*))

(send (send *irtviewer* :viewer :viewsurface) :write-to-image-file "/tmp/pr2.png")
```
