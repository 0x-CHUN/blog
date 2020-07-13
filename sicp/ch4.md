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

