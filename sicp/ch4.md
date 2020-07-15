# 元语言抽象

## 元循环求值器

用一种语言实现其自身的求值器，称为**元循环**；

求值的状态模型：

* 求值组合式时，先求值组合式的各个子表达式，而后把运算符子表达式的值作用于运算对象子表达式的值
* 把复合过程应用于实参，是在一个新环境里求值过程体

实现求值器的两个关键过程：eval和apply；

* eval负责表达式求值，apply负责过程应用
* eval和apply相互递归调用，eval还有自递归

eval以一个表达式exp和一个环境env为参数，根据exp分情况求值：

* 基本表达式
  * 各种自求表达式：直接将其返回作为结果
  * 变量：从环境中找出它的当前值
* 特殊形式
  * 引号表达式：返回被引的表达式
  * 变量赋值或定义：修改环境，建立或修改相应的约束
  * if表达式：求值条件部分，而后根据情况求值相应的子表达式
  * lambda表达式：建立过程对象，包装过程的参数表、体和环境
  * begin表达式：按顺序求值其中的各个表达式
  * cond表达式：将其变换为一系列if而后求值

组合式：递归地求值组合式的各子表达式，然后把得到的过程和所有实参送给apply，要求执行过程应用。

```scheme
(define (eval exp env)
  (cond ((self-evaluating? exp) exp)
        ((variable? exp) (lookup-variable-value exp env))
        ((quoted? exp) (text-of-quotation exp))
        ((definition? exp) (eval-definition exp env))
        ((if? exp) (eval-if exp env))
        ((lambda? exp)
         (make-procedure (lambda-parameters exp)
                        (lambda-body exp)
                        env))
        ((begin? exp)
         (eval-sequence (begin-actions exp) env))
        ((cond? exp) (eval (cond->if exp) env))
        ((application? exp)
         (apply (eval (operator exp) env)
                (list-of-values (operands exp) env)))
        (else
         (error "Unknown experssion type -- EVAL" exp))))
```

apply以一个过程和实参表为参数，实现过程应用。

* 基本过程：用apply-primitive-produce直接处理
* 复合过程：建立新环境，而后在新环境里顺序求值过程体里的表达式

```scheme
(define (apply procedure arguments)
  (cond ((primitive-procedure? procedure)
         (apply-primitive-procedure procedure arguments))
        ((compound-procedure? procedure)
         (eval-sequence
          (procedure-body procedure)
          (extend-environment
           (procedure-parameters procedure)
           arguments
           (procedure-environment procedure))))
        (else
         (error "Unknown procedure type -- APPLY" procedure))))
```

对于过程调用，eval先求出各实参表达式的值，得到实参表：

```scheme
(define (list-of-values exps env)
  (if (no-operands? exps)
      '()
      (cons (eval (first-operand exps) env)
            (list-of-values (rest-operands exps) env))))
```

eval-if求值条件表达式：

* 首先求值条件部分
* 而后根据条件部分的值选择被求值的子表达式

```scheme
(define (eval-if exp env)
  (if (true? (eval (if-predicate exp) env))
      (eval (if-consequent exp) env)
      (eval (if-alternative exp) env)))
```

赋值和定义的实现方法类似，把修改环境的工作留给下层过程：

```scheme
(define (eval-assignment exp env)
  (set-variable-value! (assignment-variable exp)
                       (eval (assignment-value exp) env)
                       env)
  'ok)
(define (eval-definition exp env)
  (define-variable! (definition-variable exp)
                    (eval (definition-value exp) env)
                    env)
  'ok)
```

求值表达式序列，就是要在同一个环境里逐个求值序列里的表达式：

```scheme
(define (eval-sequence exps env)
  (cond ((last-exp? exps) (eval (first-exp exps) env))
        (else (eval (first-exp exps) env)
              (eval-sequence (rest-exps exps) env))))
```

子求值表达式。只需要定义一个谓词，数和字符串属于此类：

```scheme
(define (self-evaluating? exp)
  (cond ((number? exp) true)
        ((string? exp) true)
        (else false)))
```

变量：直接用符号表示，只需一个谓词

```scheme
(define (variable? exp) (symbol? exp))
```

其他类型通过判断类型标志加以区分，定义判断标志的通用过程

```scheme
(define (tagged-list? exp tag)
  (if (pair? exp)
      (eq? (car exp) tag)
      false))
```

引号表达式：

```scheme
(define (quoted? exp) (tagged-list? exp 'quote))
(define (text-of- quotation exp) (cadr exp))
```

赋值表达式：

```scheme
(define (assignment? exp) (tagged-list? exp 'set!))
(define (assignment-variable exp) (cadr exp))
(define (assignment-value exp) (caddr exp))
```

定义表达式有两种形式：

* $(define <var> <value>)$
* $(define (<var> <parameter_1> ... <parameter_n>) <body>)$实际等同于$(define <var>(lambda(<parameter_1>...<parameter_n>) <body>))$

```scheme
(define (definition? exp) (tagged-list? exp 'define))
(define (definition-variable exp)
  (if (symbol? (cadr exp))
      (cadr exp)
      (caadr exp)))
(define (definition-value exp)
  (if (symbol? (cadr exp))
      (caddr exp)
      (make-lambda (cdadr exp)
                   (caddr exp))))
```

lambda表达式在形式上是一个表，以lambda为第一个元素

```scheme
(define (lambda? exp) (tagged-list? exp 'lambda))
(define (lambda-parameters exp) (cadr exp))
(define (lambda-body exp) (cddr exp))
(define (make-lambda parameters body)
  (cons 'lambda (cons parameters body)))
```

if表达式：

```scheme
(define (if? exp) (tagged-list? exp 'if))
(define (if-predicate exp) (cadr exp))
(define (if-consequent exp) (caddr exp))
(define (if-alternative exp)
  (if (not (null? (cdddr exp)))
      (cadddr exp)
      'false))
(define (make-if predicate consequent alternative)
  (list 'if predicate consequent alternative))
```

begin 包装一系列表达式，要求对它们顺序求值：

```scheme
(define (begin? exp) (tagged-list? exp 'begin))
(define (begin-actions exp) (cdr exp))
(define (last-exp? seq) (null? (cdr seq)))
(define (first-exp seq) (car seq))
(define (rest-exps seq) (cdr seq))
(define (sequence->exp seq)
  (cond ((null? seq) seq)
        ((last-exp? seq) (first-exp seq))
        (else (make-begin seq))))
(define (make-begin seq) (cons 'begin seq))
```

另一些语法过程：

```scheme
(define (application? exp) (pair? exp))
(define (operator exp) (car exp))
(define (operands exp) (cdr exp))
(define (no-operands? ops) (null? ops))
(define (first-operand ops) (car ops))
(define (rest-operands ops) (cdr ops))
```

至此基本表达式的语法过程全部定义完成;

派生表达式：cond，cond 总可以用嵌套的 if 表达式实现。把对 cond 的求值变换为 if：

```scheme
(define (cond? exp) (tagged-list? exp 'cond))
(define (cond-clauses exp) (cdr exp))
(define (cond-else-clause? clause) (eq? (cond-predicate clause) 'else))
(define (cond-predicate clause) (car clause))
(define (cond-actions clause) (cdr clause))
(define (cond->if exp) (expand-clauses (cond-clauses exp)))
(define (expand-clauses clauses)
  (if (null? clauses)
      'false; no else clause
      (let ((first (car clauses))
            (rest (cdr clauses)))
        (if (cond-else-clause? first)
            (if (null? rest)
                (sequence->exp (cond-actions first))
                (error "ELSE clause isn't last -- COND->IF"
                       clauses))
            (make-if (cond-predicate first)
                     (sequence->exp (cond-actions first))
                     (expand-clauses rest))))))
```

为了实现对各种表达式的处理，还需要定义好过程和环境的表示形式，逻辑值等的表示形式，这些都属于求值器的内部数据结构。

谓词检测。把所有非 false 对象都当作逻辑真：

```scheme
(define (true? x) (not (eq? x false)))
(define (false? x) (eq? x false))
```

设$(apply-primitive-procedure <proc> <args>)$处理基本过程应用，$(primitive-procedure? <proc>)$检查基本过程。复合过程的处理：

```scheme
(define (make-procedure parameters body env)
  (list 'procedure parameters body env))
(define (compound-procedure? p) (tagged-list? p 'procedure))
(define (procedure-parameters p) (cadr p))
(define (procedure-body p) (caddr p))
(define (procedure-environment p) (cadddr p))
```

解释器求值时需要有一个环境，现在考虑环境的实现

环境是框架的序列，每个框架是一个表格，其中的项就是变量与值的约束。把环境定义为数据抽象，提供下面操作：

* $(lookup-variable-value <var> <env>)$
* $(extend-environment <variables> <values> <base-env>)$
* $(define-variable! <var> <value> <env>)$
* $(set-variable-value! <var> <value> <env>)$

环境用框架的表表示，其 cdr 是外围环境：

```scheme
(define (first-frame env) (car env))
(define (enclosing-environment env) (cdr env))
(define the-empty-environment '())
(define (make-frame variables values) (cons variablesd values))
(define (frame-variables frame) (car frame))
(define (frame-values frame) (cdr frame))
(define (add-binding-to-frame! var val frame)
  (set-car! frame (cons var (car frame)))
  (set-cdr! frame (cons val (cdr frame))))
```

环境扩充：

* 建立一个框架
* 将其放在给定的环境的前面
* 返回扩充后的环境

变量表元素于值表长度不同时报错

```scheme
(define (extend-environment vars vals base-env)
  (if (= (length vars) (length vals))
      (cons (make-frame vars vals) base-env)
      (if (< (length vars) (length vals))
          (error "Too many arguments supplied" vars vals)
          (error "Too few arguments supplied" vars vals))))
```

变量取值：顺序检查环境中的各框架

* 找到变量的第一个出现时，返回值表中与变量对应的元素
* 遇到空环境时报错

```scheme
(define (lookup-variable-value var env0)
  (define (env-loop env)
    (define (scan vars vals)
      (cond ((null? vars)
             (env-loop (enclosing-environment env)))
            ((eq? var (car vars)) (car vals))
            (else (scan (cdr vars) (cdr vals)))))
    (if (eq? env the-empty-environment)
        (error "Unbound variable" var)
        (let ((frame (first-frame env)))
          (scan (frame-variables frame) (frame-values frame)))))
  (env-loop env0))
```

变量赋值

* 修改环境里变量的第一个出现的关联值
* 找不到变量时报错

```scheme
(define (set-variable-value! var val env0)
  (define (env-loop env)
    (define (scan vars vals)
      (cond ((null? vars)
             (env-loop (enclosing-environment env)))
            ((eq? var (car vars)) (set-car! vals val))
            (else (scan (cdr vars) (cdr vals)))))
    (if (eq? env the-empty-environment)
        (error "Unbound variable -- SET!" var)
        (let ((frame (first-frame env)))
          (scan (frame-variables frame) (frame-values frame)))))
  (env-loop env0))
```

 定义变量：在第一个框架里加入该变量与值的关联。如果存在该变量的约束时修改与之关联的值。

```scheme
(define (define-variable! var val env)
  (let ((frame (first-frame env)))
    (define (scan vars vals)
      (cond ((null? vars)
             (add-binding-to-frame! var val frame))
            ((eq? var (car vars)) (set-car! vals val))
            (else (scan (cdr vars) (cdr vals)))))
    (scan (frame-variables frame) (frame-values frame))))
```

**求值器的运行**

创建初始环境：

```scheme
(define (setup-environment)
  (let ((initial-env
         (extend-environment (primitive-procedure-names)
                             (primitive-procedure-objects)
                             the-empty-environment)))
    (define-variable! 'true true initial-env)
    (define-variable! 'false false initial-env)
    initial-env))
(define the-global-environment (setup-environment))
```

基本过程对象以符号 primitive 开头

```scheme
(define (primitive-procedure? proc)
  (tagged-list? proc 'primitive))
(define (primitive-implementation proc) (cadr proc))
(define primitive-procedures
  (list (list 'car car)
        (list 'cdr cdr)
        (list 'cons cons)
        (list 'null? null?)
        <more primitives>
        ))
(define (primitive-procedure-names)
  (map car primitive-procedures))
(define (primitive-procedure-objects)
  (map (lambda (proc) (list 'primitive (cadr proc)))
       primitive-procedures))
```

需要应用基本过程时，直接通过基础 Scheme 系统将它们应用于参数

```scheme
(define (apply-primitive-procedure proc args)
  (apply-in-underlying-scheme
   (primitive-implementation proc) args))
```

为了使用方便，可仿照 Scheme 系统，给元循环求值器定义一个基本循环，输出提示符后读入-求值-打印。输出前加一个特殊标志：

```scheme
(define input-prompt ";;; M-Eval input:")
(define output-prompt ";;; M-Eval value:")
(define (driver-loop)
  (prompt-for-input input-prompt)
  (let ((input (read)))
    (let ((output (eval input the-global-environment)))
      (announce-output output-prompt)
      (user-print output)))
  (driver-loop))
(define (prompt-for-input string)
  (newline) (newline) (display string) (newline))
(define (announce-output string)
  (newline) (display string) (newline))
```

定义 user-print 过程是为了避免打印复合过程的环境，这种环境可能有复杂的结构，而且可能包含循环结构，打印环境的内容可能出问题

```scheme
(define (user-print object)
  (if (compound-procedure? object)
      (display (list 'compound-procedure
                     (procedure-parameters object)
                     (procedure-body object)
                     '<procedure-env>))
      (display object)))
```

核心过程eval：

```scheme
(define (eval exp env)
  (cond ((self-evaluating? exp) exp)
        ((variable? exp) (lookup-variable-value exp env))
        ((quoted? exp) (text-of-quotation exp))
        ((assignment? exp) (eval-assignment exp env))
        ((definition? exp) (eval-definition exp env))
        ((if? exp) (eval-if exp env))
        ((lambda? exp)
         (make-procedure (lambda-parameters exp)
                         (lambda-body exp)
                         env))
        ((begin? exp)
         (eval-sequence (begin-actions exp) env))
        ((cond? exp) (eval (cond->if exp) env))
        ((application? exp)
         (apply (eval (operator exp) env)
                (list-of-values (operands exp) env)))
        (else
         (error "Unknown expression type -- EVAL" exp))))
```

apply 以一个过程和一个实参表为参数，实现过程应用

```scheme
(define (apply procedure arguments)
  (cond ((primitive-procedure? procedure)
         (apply-primitive-procedure procedure arguments))
        ((compound-procedure? procedure)
         (eval-sequence
          (procedure-body procedure)
          (extend-environment
           (procedure-parameters procedure)
           arguments
           (procedure-environment procedure))))
        (else
         (error "Unknown procedure type -- APPLY" procedure))))
```

元循环求值器已经完成实现，但效率低；

* 表达式的语法分析和执行交织在一起
* 如果在程序中一个表达式需要求值很多次，就需要对它做很多次语法分析

因此要将语法分析和执行分离；

所以，可以把eval的工作分为两部分：

* 分析被处理的程序，生成与之对应的可执行程序
* 直接执行生成的程序

因此定义一个过程analyze：

* 专门做对于被求值表达式的语法分析
* 对每个被分析的表达式，analyze返回一个过程，称为执行过程
* 把表达式分析的结果封装在这个执行过程里

```scheme
(define (eval exp env) ((analyze exp) env))
```

analyze只是根据表达式类型完成分配工作：

```scheme
(define (analyze exp)
  (cond ((self-evaluating? exp) (analyze-self-evaluating exp))
        ((quoted? exp) (analyze-quoted exp))
        ((variable? exp) (analyze-variable exp))
        ((assignment? exp) (analyze-assignment exp))
        ((definition? exp) (analyze-definition exp))
        ((if? exp) (analyze-if exp))
        ((lambda? exp) (analyze-lambda exp))
        ((begin? exp) (analyze-sequence (begin-actions exp)))
        ((cond? exp) (analyze (cond->if exp)))
        ((application? exp) (analyze-application exp))
        (else (error "Unknown expression type -- ANALYZE" exp))))
```

**实现各种表达式的分析**

```scheme
(define (analyze-self-evaluating exp)
        (lambda (env) exp))

(define (analyze-quoted exp)
  (let ((qval (text-of-quotation exp)))
    (lambda (env) qval)))

(define (analyze-variable exp)
  (lambda (env)
    (look-up-variable exp env)))

(define (analyze-assignment exp)
  (let ((var (assignment-variable exp))
        (vproc (analyze (assignment-value exp))))
    (lambda (env)
      (set-variable-value! var (vproc env) env)
      'ok)))

(define (analyze-definition exp)
  (let ((var (definition-variable epx))
        (vproc (analyze (definition-value exp))))
    (lambda (env)
      (define-variable! var (vproc env) env)
      'ok)))

(define (analyze-if exp)
  (let ((pproc (analyze (if-predicate exp)))
        (cproc (analyze (if-consequent exp)))
        (aproc (analyze (if-alternative exp))))
    (lambda (env)
      (if (true? (pproc env))
          (cproc env)
          (aproc env)))))

(define (analyze-lambda exp)
  (let ((vars (lambda-parameters exp))
        (bproc (analyze-sequence (lambda-body exp))))
    (lambda (env) (make-procedure vars bproc env))))

(define (analyze-sequence exps)
  (define (sequentially proc1 proc2)
    (lambda (env) (proc1 env) (proc2 env)))
  (define (loop first-proc rest-procs)
    (if (null? rest-procs)
        first-proc
        (loop (sequentially first-proc (car rest-procs))
              (cdr rest-procs))))
  (let ((procs (map analyze exps)))
    (if (null? procs) (error "Empty sequence -- ANALYZE"))
    (loop (car procs) (cdr procs))))
```

过程应用的分析

```scheme
(define (analyze-application exp)
  (let ((fproc (analyze (operator exp))) # 分析运算符表达式
        (aprocs (map analyze (operands exp))))
    (lambda (env)
      (execute-application (fproc env)
                           (map (lambda (aproc) (aproc env)) aprocs)))))
(define (execute-application proc args)
  (cond ((primitive-procedure? proc)
         (apply-primitive-procedure proc args))
        ((compound-procedure? proc)
         ((procedure-body proc)
          (extend-environment (procedure-parameters proc)
                              args
                              (procedure-environment proc))))
        (else
         (error "Unknown proc type -- EXEC-APPLICATION" proc))))
```

至此分析求值器的构造完成。