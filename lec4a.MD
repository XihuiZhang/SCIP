# pattern matching && build a language

patterns-----------rule---------------->skeleton  
|                                         |  
|                                         |instantiatian  
match                                     |  
|                                         |  
|                                         |  
Expression source--------------—-> expression target  

# 求导 
(define deriv-rules
 (
  ((dd (? c c) (? v))                   0)  // 以问号开头的叫做pattern varible  
  ((dd (? v v) (? v))                   1)  // c 是constant match v是变量 match  
  ((dd (? v u) (? v))                   0)  // :号作用：表示被代替的对象
  
  ((dd (+ (? x1) (? x2)) (? v))  
    (+ (dd (: x1) (: v))    
       (dd (: x2) (: v)） 
       
       
### pattern match
foo             ————match exactly it self foo
(f a b)        -----match a list whose first element is f, second element a ,.....
(? x)          -----match anything call it x
(?v x)        ------match  var
(?c x)        ------match constance

### skeleton
foo           ------instantiates itself  
(f a b)       ------instantiates list with three elements 
(: x)         ------instantiate value of x as in the matched pattern  


## something like part of complier

```
(define (match pat exp dict) // atom?是否是原子操作：pattern是原子操作说明 pattern是由primitive thing construct的 not blackbox
  (cond ((eq? dict 'failed') 'failed)  //atom 不是由其它模式构成的模式 ： foo  return foo
        ((atom? pat)
          *** Atomic patterns)
          *** Pattern variable clauses
          ((atom? exp) 'failed)
          (else
            (match (cdr pat)
                   (cdr exp)
                   (match (car pat)
                          (car exp)
                          dict)))))
                          
                          
    ((atom? pat) 
      (if (atom? exp)
        (if (eq? pat exp)
            dict
            'failed)
            'failed))
 ``` 
 
 ## （? x） (?c x) (?v x)三种匹配模式
 
 ```
 ((arbitrary-constant? pat)
  (if (constant? exp)
      (extend-dict pat exp dict)
      'failed))

((arbitrary-variable？ pat)
  (if (variable? exp)
      (extend-dict pat exp dict
      'failed))

((arbitrary-expression? pat)
  (extend-dict pat exp dict))
 ```
 ### 实例化一个 patterns with dict 就是对skeleton进行递归的做树的遍历
 ```
  (define (instantiate skeleton dict)
    (define (loop s)
      (cond ((atom? s) s)
        ((skeleton-evaluation? s)
          (evaluate (eval-exp s) dict))
        (else (cons (loop (car s))
                    (loop (cdr s))))))
  (loop skeleton)
 ```

### simplifier 
```
(define (simplifier the-rules)
  (define (simplify-exp exp)            // sim-exp && sim-parts are used  to recursively simplify the exp
    ***)                                // sim-exp simlify any whole exp and sim-parts sim part
  (define (simplify-parts exp)
    ***)
  (define (try-rules exp)
    ***)
  simplify-exp)
 ( define (try-rule exp)
   (define scan rules)
    ***)
   (scan rules))
```


```
(define (simplify-exp exp)
  (try-rules (if (compound? exp)
                 (simplify-parts exp)
                 exp)))
                 
(define (simplify-parts exp)
  (if (null? exp)
    '()
    (cond (simplify-exp (car exp))
          (simplify-parts (cdr exp))))
``` 
 
```
 (define (simplify-exp exp)
   (try-rules
     (if (compound exp?)
         (map simplify-exp exp)
         exp)))
```
