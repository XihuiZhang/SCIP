1：list操作中的map，filter and reduce

map（list，func）/// mapping all the element in list to func() and return the newlist with value func(x)
这任务的基本方法是用过 cdr+cons的递归实现的

car取pair的第一个元素
cdr取pair的第二个元素
cons生成一个pair，
list是由n个pair嵌套的结果，最后一个pair的第二个元素是nil，其他pair的第二个元素都是剩下递归定义的pair）

(define(map funcli) 
  (if (null? li)nil 
  (cons (func (car li)) (map func (cdr li))) ))                     
  
这里我们定义最后返回的nil为空list：(define nil '())

检验这个函数的效果：（map（lambda（x）*x x）list 1 2 3 4 5 6）  return (1 4 9 16 25 36)


(filter(filter condition)(list))

filter 接受俩个参数————list和用来过滤list的filt（）
基本方法和上面一样都是用过cdr+cons的递归 和map唯一不同的是，你可以认为                         
filter中的func 是（lambda（x）x）而且filter需要考虑通过和不通过filt（）             

(define (filter filt li) 
  (cond ((null? li) nil) 
    ((filt (car li)) (cons (car li) (filter filt (cdr li))) ) 
      (else (filter filt (cdr li)) )))
 ////////////////////////////////////////////////////////// cond使用的例子     
如果a>=100，则b=2；如果10<=a<100,则b=1；如果a<10,b=0 
       (cond 
         ((>= a 100) (setq b 2)) 
         ((>= a 10) (setq b 1)) 
         (t (setq b 0)) 
         ）
////////////////////////////////////////////////////////
 定义一个检验整数是否是奇数的
  filter：(define (odd? x) (= (remainder x 2) 1))
    (filter odd? test)
    return：'(1 3 5 9)        
    
    
reduce （accumulate) 接受三个参数   list————binary op————null（也就是当list为空的时候返回值）。
递归定义
(define (reduce op null li) 
  (if (null? li) null
    (op (car li) (reduce op null (cdr li)))))
 此procedure的递归含义十分显然：如果list是空（null?判断）
 则递归返回null值；如果不是，则返回通过op计算的，list的第一个元素的值和剩余元素reduce的值。
很容易检验这个reduce函数的工作效果：

(reduce + 0 test)

返回：52


(reduce * 1 test)

返回：777600

也就是我们计算了test 所有元素的和以及积

实际上，我们的这个op 操作可以变得十分一般化，例如：

(reduce cons nil test)

返回：'(1 2 3 4 5 6 9 10 12) 重现了我们输入的list


reduce 的高阶运算操作
reduce + 0 (map square (filter odd? test)))


用reduce acuumulate 实现序列的map append 和 length
reduce 序列的操作实际上是一个递归的，reduce(list[i:])=op(list[i], reduce(list[i+1:])) 的过程
 
1：map
 reduce和map 的结构是完全一样的 只要将op改成二元操作函数
 op 应该是 lambda（x y）（cons （func x） y）
 
（define（mapversion2 fun li）（reduce（lambda（x y）cons（func x）y）ni li））

用reduce实现append
reduce实现 append

所谓append，就是把第二个list 放在第一个list后面，合并成一个新的list。问题是我们的reduce只能操作一个list，第二个list如何操作？

注意到在append中，我们的确只需要操作一个list，另外一个list只需要原封不同的返回即可。实际上，如果第一个list是空的话，我们直接返回第二个list。如果第一个list不为空，我们递归的找到他的末尾（空），再返回第二个list即可。每次递归都和之前的结果组成一个新的list即可。由此我们可以设想：

op应该是 (lambda ( x y) (cons x y)), null设成list2

无非就是找到list1 末尾的时候，不返回nil而是返回list2。

这样我们就可以定义我们的append函数为：

(define (append list1 list2) (reduce (lambda ( x y) (cons x y)) list2 list1))

(append (list 1 2 3) (list 4 5 6))
返回：'(1 2 3 4 5 6)

用reduce实现 length

返回list长度也是一个重要操作，如何通过设定op 来完成这一步呢？

我们注意到list中每一个元素都贡献1的长度，而且长度的相加具有递归性：

length(a[i:])=1+length(a[i+1:])

length([])=0

我们可以构建

op= (lambda (x y) (+ 1 y))

null=0

这样我们就可以定义length函数为：

(define (length list1) (reduce (lambda (x y) (+ 1 y)) 0 list1))

(length test)
返回：9
由此我们可以看到高阶抽象函数的强大。
3. reduce (accumulate)的其他实例：

1） 计算polynomial：

这是一个经典的数学问题，也是一个经典的递归问题：

因为注意到：a_n x^n+a_{n-1} x^{n+1} + ... + a_{1} x^{1} +a_0 
=(a_0 +x (a_1 + x( a_2 +...))

(horner's formula)

所以我们很容易通过reduce实现这一功能：

让op=(lambda (current rest) (+ current (* x rest))) 并且 null=0

(define (polynomial x list) (reduce (lambda (current rest) (+ current (* x rest))) 0 list ))

计算3+2x +5 x^2 +10 x^4 +x^5 , 且 x=10

(polynomial 10 (list 3 2 5 0 10 1 ))
返回：200523

(polynomial 0 (list 3 2 5 0 10 1 ))
返回：3
3. reduce (accumulate)的其他实例：

1） 计算polynomial：

这是一个经典的数学问题，也是一个经典的递归问题：

因为注意到：a_n x^n+a_{n-1} x^{n+1} + ... + a_{1} x^{1} +a_0 
=(a_0 +x (a_1 + x( a_2 +...))

(horner's formula)

所以我们很容易通过reduce实现这一功能：

让op=(lambda (current rest) (+ current (* x rest))) 并且 null=0

(define (polynomial x list) (reduce (lambda (current rest) (+ current (* x rest))) 0 list ))

计算3+2x +5 x^2 +10 x^4 +x^5 , 且 x=10

(polynomial 10 (list 3 2 5 0 10 1 ))
返回：200523

(polynomial 0 (list 3 2 5 0 10 1 ))
返回：3
2）计算nested-list的元素个数
在lisp语言当中，tree的表示是通过nested-list而来。在mathematica中这无非是说list的depth大于1，比如(1 2 3 4 5 6)是一个flattened list。但一般来说，list可以表示成 (1 2 3 (2 3) (1) (5 (6 (0) ) 7) 9) 这样，可以嵌套多层，其递归的结构可以用来表示树（树就是一个递归结构），而每一个数字都是树的leaf。

拓展上述的length 函数，我们可以计算树的“长度”。对于 (1 2 3 (2 3) (1) (5 (6 (0) ) 7) 9) 来说，如果使用length，我们会得到8这样一个错误的结果，而实际上应该是11。

如何利用reduce和递归来进行正确的计算呢？我们已经知道reduce具备enumerate list 每一个元素的功能，我们需要做的是判断这个元素是一个数（leaf） 还是嵌套元素（list）：

op= (lambda (this rest) (if (pair? this) (+ recursive-stuff-here rest) (+ 1 rest)))

那么这个recursive-stuff-here 就是要重新再次调用算tree leaf个数的函数。

实际上最后我们这个算tree长度的函数应该长成这个样子：

(define (length-tree t) (reduce (lambda (this rest) (if (pair? this) (+ (length-tree this) rest) (+ 1 rest))) 0 t))

(define tree (list 1 2 3 (list 2 3) (list 1) (list 5 (list 6 (list 0))) 7 9))

tree
'(1 2 3 (2 3) (1) (5 (6 (0))) 7 9)
(length-tree tree)
11
3）扩展到level-2 的 nested list

reduce 现在只能处理一个flattened list。如果list是一个levet=2的嵌套list,每一个子list等长，那么我们应该也可以处理reduce，只不过每次取每一个子列的第一，第二，第三，第k个元素出来组成flattened list，再把这些结果组成一个嵌套list。

例如：

对于 ((1 2 3) (4 5 6) (7 8 9) (10 11 12)) 求 和的reduce， 等于(22 26 30)

如何求出所有子列的第一个元素呢？

可以使用map，（因为map可以遍历所有元素），且令

func1＝(lambda (sublist) (car sublist))

这样就把第一个元素取出来了。

那么如何求出所有剩下的元素呢？

很显然

令func2＝(lambda (sublist) (cdr sublist))

即可。

这样通过对list取元素进行reduce递归，我们就可以实现扩展的reduce-n：

(define (reduce-n op null lists) (if (null? (car lists)) nil (cons (reduce op null (map func1 lists)) (reduce-n op null (map func2 lists)) ) ))

注意func1 取的就是一列（第一元素），func2取的是一列（剩余子列）

例如对于 a=((1 2 3) (4 5 6) (7 8 9) (10 11 12)) ,

(map func1 a) 返回 (1 4 7 10)

(map func2 a) 返回 ((2 3) (5 6) (8 9) (11 12))

然后这个递归过程就容易想见了。

(reduce-n + 0 a)
返回：'(22 26 30)

(reduce-n * 1 a)
返回：'(280 880 1944)
注意到我们实际上可以利用reduce-n进行所谓的transpose操作：
只需要令：

op = (lambda (x y) (cons x y))

(reduce-n (lambda (x y) (cons x y)) nil test2)
返回：'((1 4 7 10) (2 5 8 11) (3 6 9 12))
其操作本质就是依次取出第k列组成一个新的list。

4. 矩阵计算

我们不难将上述reduce-n的技巧扩展到map上去。如果map接受两个或者以上数目的list， 我们可以将它们看作level-2 list并且每次取出第k列出来进行计算。

(define (map-n op null . lists) (reduce-n op null lists))

这里的.代表可以输入多个参数，procedure把会他们组合成一个list记作lists

例如：

(define a (list 1 2 3 4))

(define b (list 5 6 7 8))


(map-n * 1 a b)
返回：'(5 12 21 32)
这就完成了一次两列元素之间的对应乘积。

显然我们可以据此进行矢量点乘（这些乘积的和）：

(define (dot-vector a b) (reduce + 0 (map-n * 1 a b)))

(dot-vector a b)
返回：70
接下来我们用三个函数可以巧妙的进行言简意赅的矩阵运算（矩阵＊矩阵，矩阵＊矢量，矩阵转置）

矩阵转置我们已经在上面说过了，只需要令op = (lambda (x y) (cons x y))。因此

(define (transpose m) (reduce-n (lambda (x y) (cons x y)) nil m ))

矩阵乘以矢量m*v，本质上就是矢量乘以矢量。只不过你需要每次取出矩阵的行矢量（需要转置m）：

(define (matrix-vector-product m v) (let ((rows (transpose m))) (map (lambda (row) (dot-vector row v )) rows) ))

最后得到长度等于列矢量数的矢量。

矩阵乘以矩阵m*n：

n每一行列量都和矩阵m做乘法，得到的序列就是结果矩阵。相当于用到了matrix-vector-productprocedure


(define (matrix-matrix-product m n) (map (lambda (col) (matrix-vector-product m col) ) n))


(define m1 (list (list 1 2) (list 3 4) (list 5 6)))
(define v1 (list 1 5 3))
(define m2 (list (list 1 2 6) (list 3 4 10)))

(matrix-vector-product m1 v1)
返回：'(31 40)
(matrix-matrix-product m1 m2)
返回：'((37 46) (65 82))
不难验证计算是正确的。


