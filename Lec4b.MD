# design generic operator to operates in different( data direct programming

(define (attach-type type contents)
  (cons type contents))
(define (type datum)
  (car datum))
(define (contents datum)
  (cdr datum))

## type predict
```
(define (real-part z)
  (cond ((rectangular? z)
          (real-part-rectangular (contents z)))
        ((polar? z)
         (real-part-polar
          (contents z)))))   
```
any one who bring some kind of implementation must write his thing in table
installing the rectangular operation in the table
```
 (put key1 key2 value)
 (get key1 key2 value)
 (put 'rectangular 'real-part
      real-part-rectangular)
 (put 'rectangular 'imag-part
      imag-part-rectangular)
 (put 'rectangular 'magnitude
       magnitude-rectangular)
 (put 'rectangular 'angle
       angle-rectangular)
```
(define (operate op OBJ)
   (Let ((proc (get (type OJB) op))) //look at table to find a procedure to operate obj)))
     (if (not (null? proc))
         (proc (contents obj))
         (Error 'underfined op')

(real-part z)
(operate 'real-part z) //partter
((get 'polar 'real-part) (contents z)) //instaniliaze
```

## generic operator
```
 (define (add x y)
   (operate-2 'add x y)
(define (operate-2 op arg1 arg2)
  (if
   (eq? (type arg1) (type arg2))
   (let ((proc (get (type arge1)op)
     (if (not (null? proc))
         (proc (contents arg1)
               (contents arg2))
         (error
          "Op underfined on type")))
  (error "Args not same type")))

```
```
(define (make-polynomial var term-list)
  (attach-type 'polynomial
                (cons var term-list)))
(define ('poly p1 p2)
  (if (same-var? (var p1) (var p2))
      (make-polynomial
        (var pl)
        (+terms (terms-list p1)
                (terms-list p2)))
       (error "polys not in same var")))
(put 'polynomial 'add 'poly)
 
```



```
(define (+terms l1 l2)
  (cond ((empty-termlist? L1)L2)
        
```
