# コンパイルに関する考察

ベンチマークをとってみた



```lisp:transpose-image.l
(defun transpose-in-eus (img)
  (let ((width (send img :width))
        (height (send img :height))
        (imgbuf (send img :entity)))
    (let ((b (make-string (* width height 3))))
    (dotimes (x width)
      (dotimes (y height)
        (dotimes (z 3)
          (setf (elt b (+ (* (- height y 1) width 3) (* x 3) z))
                (elt imgbuf (+ (* y width 3) (* x 3) z))))))
    (instance image::color-image24 :init width height b))
    ))
```

```lisp:bench-transpose-image.l
(compiler::compile-file-if-src-newer "transpose-image")

(load "transpose-image")

(setq *img* (instance image::color-image24 :init 640 480 (make-string (* 3 640 480))))
(setq *cnt* 10)
(when (compiled-function-p #'transpose-in-eus)
  (warning-message 3 "~%~%===== ~A transpose in eus (compiled) ======~%" *cnt*)
  (bench (dotimes (i *cnt*) (transpose-in-eus *img*))))

(load "transpose_image.l")
(unless (compiled-function-p #'transpose-in-eus)
  (warning-message 3 "~%~%===== ~A transpose in eus (not compiled) ======~%" *cnt*)
  (bench (dotimes (i *cnt*) (transpose-in-eus *img*))))

(warning-message 3 "~%===== ~A transpose in C ======~%" *cnt*)
(bench (dotimes (i *cnt*) (gl::transpose-image-rows *img*)))
```

```lisp
===== 10 transpose in eus (compiled) ======
;; time -> 1.73079[s]

===== 10 transpose in eus (not compiled) ======
;; time -> 25.5784[s]

===== 10 transpose in C ======
;; time -> 0.000631[s]
```

| Code        | Time (s)          | Comparison  |
| ------------- |-------------| -----|
| Lisp      | 25.5784 | 1 |
| Lisp (Compiled)      | 1.73079      |  2742  |
| C (defforeign) | 0.000631      |    40536 |
