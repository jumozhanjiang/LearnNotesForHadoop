### SCALA简介

> [官网地址](https://www.scala-lang.org/)

> scalable language, 可伸缩语言。Scala是一门综合了面向对象和函数式编程概念的静态类型的编程语言。

> 运行在JVM虚拟机上,可以无缝调用java的方法库

> 函数式编程（函数的地位是和整数、字符串等是相同的）

> 高阶面向对象（每个值都是对象，每个操作都是方法调用）

> 类型推断

### 为什么要学SCALA

> spark和基于spark生态圈的相关组件或FLINK,kafka框架都是由SCALA语言书写的, 虽然它们都可以由java来调用api或写任务,但java代码可读性没有scala高 运行效率也没SCALA高, 用java代码书写 只是调用别人的API, 如果遇到什么问题 需要读源码, 那么只会java的话根本读不懂源码,对大数据的开发也只能停留在API调用的阶段,并不能进一步的获得大数据技术的增长

### SCALA入门

* `hello world`按例

  ```scala
  object HelloScala {
    def main(args: Array[String]) {
      println("Hello World")
    }
  }
  ```

* val跟var的区别
  * `val`是一个值(不能改变) 与java的`final`和 ES6的`const`差不多
  * `var`是个变量(可以改变) 与java的变量和ES6的`let`差不多


* SCALA的基本数据类型
  * `Byte`
  * `Char`
  * `Short`
  * `Int`
  * `Long`
  * `Float`
  * `Double`
  * `Boolean`
* lazy的操作
  * 被lazy修饰后, 只有当该变量发生改变时,该变量才产生计算
    ```scala
    object HelloScala {
      def main(args: Array[String]) {
        lazy val a : Int = 1;
        println(a)
      }
    }
    ```

### SCALA函数

* 函数的定义与使用
  * 函数的定义
    ```scala
    object TestFuncation01 {

      def main(args: Array[String]): Unit = {
        val result : Int = test01(5,6)
        println(result)
      }

      def test01(pam1 : Int,pam2 : Int) : Int = {
        if (pam1 > pam2)
          pam1
        else
          pam2
      }
    }
    ```
    * `def` : 声明这是一个函数
    * `test01` : 函数名称
    * `pam1 : Int,pam2 : Int` : 参数的名称与参数的类型
    * ` : Int` : 返回值的类型
    * **注意 scala是把最后一条代码的结果作为返回值的, 不需要特定的写返回值**
    * **当函数没有入参的时候,调用函数时括号是可以省略的**


* 默认参数的使用(函数定义时指定函数参数的默认值)
  ```scala
  object TestFuncation01 {

    def main(args: Array[String]): Unit = {
      val result : Int = test01() // 有默认值后不用传参数
      val result2 : Int = test01(100,200) // 可以不用传后面有默认值的参数
      println(result)
      println(result2)
    }

    /**
      * 有默认值的函数
      * @param pam1
      *             参数1 有默认值为 1
      * @param pam2
      *             参数2 有默认值 2
      * @param pam3
      *             参数3 有默认值3
      * @return
      */
    def test01(pam1 : Int = 1,pam2 : Int = 2,pam3 : Int = 3) : Int = {
      if (pam1 > pam2)
        pam1
      else
        pam2
    }
  }
  ```

* 命名参数
  *
