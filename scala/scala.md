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
  * 被lazy修饰后, 只有当该变量被用到时,该变量才产生计算
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
  * 函数里面的参数顺序可以被打乱传入,只要指定对应的参数名称就可以了
    ```scala
    object Test01 {

      def main(args: Array[String]): Unit = {
        println(speed(100,20)) // 5.0
        println(speed(time = 20,distance = 100)) // 5.0
      }

      def speed (distance : Float, time : Float) : Float = {
        distance/time
      }
    }
    ```

* 可变参数
  * 参数的个数是可变的,类似于java的可变参数
    ```scala
    object Test01 {

      def main(args: Array[String]): Unit = {
        println(sum(1,3,5,1)) // 10
      }

      def sum(number : Int*) : Int = {
        var count : Int= 0
        for (elem <- number) {
          count += elem
        }
        count
      }
    }
    ```

* 条件表达式
  ```scala
  object Test01 {

    def main(args: Array[String]): Unit = {
      println(if (1 > 0) true else false)
    }
  }
  ```

* 循环
  ```scala
  object Test01 {

    def main(args: Array[String]): Unit = {
      val w: Inclusive = 1.to(10) // 产生1到10的数 类型 Inclusive类型
      val w2: Inclusive = 1.to(10,2) // 第二个参数是循环的间隔
      w.foreach(elem => println(elem))
      println("-----------------------------")
      w2.foreach(elem => println(elem))
    }
  }

  /*
  1
  2
  3
  4
  5
  6
  7
  8
  9
  10
  -----------------------------
  1
  3
  5
  7
  9
  */
  ```

### Scala对象

* 类的定义与使用
  ```scala
  object Test01 {

    def main(args: Array[String]): Unit = {
      val people = new Test01()
      people.setAge(23)
      people.setName("张三")

      println(people.getAge())
      println(people.getName())
      println(people.eat())
      people.watchFoolball()
    }
  }

  class Test01 {

    private [this] var name : String = _ // _ 表示一个占位符 private [this] 表示只能在这个class里面调用
    private [this] var age : Int = _

    def setName(name :String) = {
      this.name = name
    }

    def getName() : String = {
      this.name
    }

    def setAge(age : Int) = {
      this.age = age
    }

    def getAge() : Int = {
      this.age
    }

    def eat() : String = {
      name + " 正在吃饭"
    }

    def watchFoolball() = {
      println(name + " 正在看足球比赛")
    }
  }
  ```

* 主构造器与附属构造器
  ```scala
  object Test02 {

    def main(args: Array[String]): Unit = {
      var people : Test02 = new Test02("李四",18) // 调用主构造器
      print(people.name + " : "+people.age + " : " + people.School )
      println(" ------------ ")
      var people2 : Test02 = new Test02("王五",23, "家里蹲大学") // 调用附属构造器
      print(people2.name + " : "+people2.age + " : " + people2.School )
    }
  }

  class Test02(val name : String, val age : Int) { // class后面的是主构造器 里面的参数表示该类里面已经有该变量无需额外声明

    def this(name : String, age: Int, school : String){ // def this 表示附属构造器,里面必须调用主构造器,在对该类里面其它变量进行赋值
      this(name,age)
      this.School = school
    }
    var School : String = "牢里蹲大学"
  }
  /*
  李四 : 18 : 牢里蹲大学
   ------------
  王五 : 23 : 家里蹲大学
  */
  ```

* scala的继承

  ```scala
  object Test02 {

    def main(args: Array[String]): Unit = {

      var studen : Child4Test02 = new Child4Test02("王麻子",24,"写作业")
      println(studen.School)
      println(studen.age)
      println(studen.name)
      println(studen.task)
    }
  }

  class Test02(val name : String, val age : Int) { // class后面的是主构造器 里面的参数表示该类里面已经有该变量无需额外声明

    def this(name : String, age: Int, school : String){ // def this 表示附属构造器,里面必须调用主构造器,在对该类里面其它变量进行赋值
      this(name,age)
      this.School = school
    }

    var School : String = "牢里蹲大学"

  }

  // Child4Test02构造器因父类没有task属性,所以构造器里面必须用var或val声明 要不然外界访问不到
  class Child4Test02(name : String, age : Int, var task : String) extends Test02(name, age, "清华大学") {
  }
  /*
  清华大学
  24
  王麻子
  写作业
  */
  ```

* scala重写
  ```scala
  class Test02(val name : String, var age : Int) { // class后面的是主构造器 里面的参数表示该类里面已经有该变量无需额外声明

    def this(name : String, age: Int, school : String){ // def this 表示附属构造器,里面必须调用主构造器,在对该类里面其它变量进行赋值
      this(name,age)
      this.School = school
    }

    var School : String = "牢里蹲大学"

    def pring = {
      print("未重写")
    }

  }

  class Child4Test02(name : String, age : Int, var task : String) extends Test02(name, age, "清华大学") { // Child4Test02构造器因父类没有task属性,所以构造器里面必须用var或val声明 要不然外界访问不到
    override def pring: Unit = {
      print("已重写")
    }
  }
  ```

* 伴生类与伴生对象
  ```scala
  // 伴生类
  class Test01 {

  }

  // 伴生对象
  object Test01 {

  }
  ```
* apply方法
  ```scala
  object Run {
    def main(args: Array[String]): Unit = {
      var bean : Test02 = Test02() // 直接类名加括号 是调用的伴生对象的apply函数
      var bean2 : Test02 = new Test02() // new一个对象并没有调用class里面的apply函数
      bean2() // 实例对象加括号才会默认调用class里面的apply函数
    }
  }

  // 这是一个伴生类
  class Test02 {
    // class里面的apply
    def apply() = {
      println("class 里面的apply")
    }
  }

  // 这是一个伴生对象
  object Test02 {
    // 对象里面的apply方法
    def apply() : Test02 = {
      println("object 里面的apply")
      new Test02 // 一般在实践里面的做法就是在伴生对象里面new一个对象并返回
    }
  }
  /*
  object 里面的apply
  class 里面的apply
  */
  ```

* -case class
  ```scala
  object ggg {
    def main(args: Array[String]): Unit = {
      println(Test03("王麻子").name)
    }
  }

  // case 不需要new就可以直接用 一般用到模式匹配里面
  case class Test03(name : String) {
  }
  ```

* -Trait
  * `Trait xxx extends ATrait with BTrait with CTrait`
  * 类似于抽象类
    ```scala
    class SparkConf() extends ATrait with BTrait with CTrait ...
    ```

### Scala集合

* 定长数组
  ```scala
  object CollectionTest {
    def main(args: Array[String]): Unit = {
      // 不可变数组
      var arr02 = Array("111","222","333") // 声明一个数组
      arr02.foreach(elem => print(elem + " "))
      var arr03 = Array(10,20,36,1,74,33)
      println(arr03.sum)  // 数组元素的求和
      println(arr03.min)  // 数组求最小值
      println(arr03.max)  // 数组求最大值
      println(arr03.mkString("[",",","]")) // 转成String 类型 , 用逗号进行分割, 并在两边加上[]

      println("------------------------------------")

      // 可变数组
      var d = scala.collection.mutable.ArrayBuffer[Int]()
      // 添加元素
      d += 1
      d += 2
      // 成批添加元素
      d += (3,4,5)
      // 把不可变数组添加进去
      d ++= Array(6,7,8)
      println(d)
      d.insert(0,0) // 在下标为0的位置加上元素0
      println(d)
      d.remove(1,2) // 从下边为1的元素向后删除两个
      println(d)
      d.trimEnd(2) // 从尾巴开始删除两个元素
      println(d)
      var arr04 = d.toArray // 把可变数组转成不可变数组
      println(arr04.max)
    }
  }
  /*
  111 222 333 174
  1
  74
  [10,20,36,1,74,33]
  ------------------------------------
  ArrayBuffer(1, 2, 3, 4, 5, 6, 7, 8)
  ArrayBuffer(0, 1, 2, 3, 4, 5, 6, 7, 8)
  ArrayBuffer(0, 3, 4, 5, 6, 7, 8)
  ArrayBuffer(0, 3, 4, 5, 6)
  6
  */
  ```
* List
  ```scala
  object CollectionTest {
    def main(args: Array[String]): Unit = {
      // 集合
      var list1 : List[Int] = List(1,2,3,4,5,6) // List(不可变)声明对象的第一种方法
      var list2 = 1 :: 2 :: 3 :: 4 :: 5 :: 6 :: Nil // List(不可变)声明对象的第二种方法
      var list3 = ListBuffer[Int]() // 可变List
      list3 += 1 // 向集合中添加 1 这个元素
      println(list3.mkString("[", ",", "]"))
      list3 += (2,3,4,5,6,10) // 向集合添加多个元素
      println(list3.mkString("[", ",", "]"))
      list3 -= 10 // 减去10这个元素
      println(list3.mkString("[", ",", "]"))
      list3 --= List(2,3,4) // 减去对应List中包含的元素
      println(list3.mkString("[", ",", "]"))
    }
  }
  /*
  [1]
  [1,2,3,4,5,6,10]
  [1,2,3,4,5,6]
  [1,5,6]
  */
  ```

* Set
  ```scala
  object CollectionTest {
    def main(args: Array[String]): Unit = {
      var set = Set(1,1,3,5,2,4,6,5,5)
      set += 10
      println(set.mkString("[", ",", "]"))
    }
  }
  /*
  [5,10,1,6,2,3,4]
  */
  ```

* Map
  ```scala

  ```
