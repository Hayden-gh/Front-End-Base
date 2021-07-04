# this指针总结

### 导语
JavaScript的继承方式和其他面对对象语言不同，不是通过class，而是通过原型对象“prototype”实现，虽然es6加入了class但只是语法糖而已，在本质上仍是基于原型对象实现的。

JavaScript的继承方式主要分为六种，分别是：
````
原型链继承，原型式继承，借用构造函数继承，组合继承，寄生继承，寄生式组合继承
````
### 原型链继承
````
function Person(grade) {
    this.grade = grade;
    this.printGrade = function() {
        console.log(this.grade);
    }
}
function Student(name) {
    this.name = name;
}
Student.prototype = new Person("初一");
var LiMing = new Student("LiMing");
LiMing.printGrade();//初一
````
原型链继承是将父类创建的实例赋值给子类的prototype得以实现继承。

优点：写法简单。

缺点：
1.  父类型的所有属性会被子类型共享，当父类型中的引用类型属性被子类型修改会影响                 到其他子类型实例。

2.  创建子类型实例时不能向父类型的构造函数传递参数。

### 原型式继承

````
function object(o){
    function F(){};
    F.prototype = o;
    return new F();
}
Person {
    grade: "初一",
    printGrade: function() {
        console.log(this.grade);
    }
}
var LiMing = object(Person);
LiMing.printGrade();//初一
````
原型式继承是通过在一个函数中创建一个空函数，再将函数原型设置为父类，并创建返回一个实例实现继承。

优点：可以实现基于一个对象的简单继承，不必创建构造函数。

缺点：
1. 父类型的所有属性会被子类型共享，当父类型中的引用类型属性被子类型修改会影响                到其他子类型实例。

2. 创建子类型实例时不能向父类型的构造函数传递参数。

### 借用构造函数继承
````
function Person(grade) {
    this.grade = grade;
    this.printGrade = function() {
        console.log(this.grade);
    }
}
function Student(name, grade) {
    this.name = name;
    Person.call(this, grade);
}
var LiMing = new Student("LiMing", "初一");
LiMing.printGrade();//初一
````
借用构造函数继承是通过在子类的构造函数中用call或apply使父类的this指针指向子类实现继承。

优点：创建子类型实例时可以向父类型的构造函数传递参数。

缺点：
1. 所有方法属性都在构造函数中定义，无法进行复用。
2. 父类型的原型的方法对子类型不可见。

### 组合继承
````
function Person(grade) {
    this.grade = grade;
    this.printGrade = function() {
        console.log(this.grade);
    }
}
function Student(name, grade) {
    this.name = name;
    Person.call(this, grade);
}
Student.prototype = new Person();
Student.prototype.constructor = Student;
Student.prototype.printName = function() {
    console.log(this.name);
}
var LiMing = new Student("LiMing", "初一");
LiMing.printGrade();//初一
LiMing.printName();//LiMing
````
组合继承通过在子类构造函数中将父类this指针指向子类，并在函数外将父类创建的实例赋值给子类的prototype属性，同时将constructor修改为子类。

优点：避免了原型链继承和借用构造函数继承的缺点，并融合了二者的优点。
创建子类型实例时可以向父类型的构造函数传递参数。
保持了原型链的完整。

缺点：调用了两次超类的构造函数，导致子类的原型对象中增添了不必要的超类的实例对象中的所有属性。

### 寄生式继承
````
Person = {
    grade: "初一",
    printGrade: function() {
        console.log(this.grade);
    }
}
function createStudent(parent, name) {
    var student = Object.create(parent);
    student.name = name;
    student.printName = function() {
        console.log(this.name);
    }
    return student;
}
var LiMing = createStudent(Person, "LiMing");
LiMing.printGrade();//初一
LiMing.printName();//LiMing
````
通过在一个函数中创建一个父类的新实例，并在函数中增强它后返回。

优点： 在主要考虑对象而不是自定义类型和构造函数的情况下，实现简单的继承。

缺点：使用该继承方式，在为对象添加函数的时候，没有办法做到函数的复用。

### 寄生式组合继承
````
function Person(grade) {
    this.grade = grade,
    this.printGrade = function() {
        console.log(this.grade);
    }
}
function Student(name, grade) {
    this.name = name;
    Person.call(this, grade);
}
function inherit(child, parent) {
    var prototype = Object.create(parent.prototype);
    child.prototype = prototype;
    child.prototype.constructor = child;
}
var LiMing = new Student("LiMing", "初一");
inherit(Student, Person);
LiMing.printGrade();//初一
````
结合了寄生继承和组合继承，在子类的构造函数中将父类this指针指向子类，并在继承函数中将父类的prototype属性创建的实例赋给子类的prototype并修改其constructor为子类构造函数。

优点：
1. 效率高，避免了在 SubType.prototype 上创建不必要的属性。
2. 保持原型链不变，目前最好的继承范式。

