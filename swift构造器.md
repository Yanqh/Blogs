---
title: Swift的构造器的知识点
date: 2016-11-28
tags: 
- Swift
- 构造器
category: Swift
keywords:
- swift
- 构造器

fields:
    - title
    - date
    - permalink
    - thumbnailImageUrl
    - tags
    - categories
    - excerpt
    - excerpt:strip
comments: true
---

熟悉swift的构造器用法是必须的，这样才能写出方便、快捷、高效、强大的类。

<!-- more -->
<!-- toc -->

# 三种构造器的含义

- 默认构造器：当类中的属性都有默认值，而且没有指定构造器，那么系统会隐式的创建默认构造器init()。
- 指定构造器：指定构造器的格式为init(prama) {}，它是类构造中重要的组成部分，负责向上沿着继承链初始化，并完成所有非可选无默认属性的初始化。
- 便利构造器：便利构造器格式为convenience init(prama) {}，它是构造器中次要的，辅助的。便利构造器中必须要调用指定构造器来完成初始化。

{%alert warning no-icon %}
默认构造器不需要显示的调用。
{% endalert %}
{% alert warning no-icon %}
指定构造器必须调用直接父类的指定构造器（或者默认构造器）。
{% endalert %}
{% alert warning no-icon %}
便利构造器必须调用或者导向本类中的指定构造器。
{% endalert %}
{% alert warning no-icon %}
指定构造器向上代理，便利构造器横向代理。
{% endalert %}
{% alert warning no-icon %}
便利构造器不需要写override，但如果重写指定构造函数为便利构造，也需要添加override。
{% endalert %}


# 当父类只有默认构造器
子类在重写默认构造器时，不需要显式的调用默认构造器即可完成构造。

{% tabbed_codeblock %}
<!-- tab swift -->
	class Car {
    var wheels = 4
}

class SportsCar: Car {
    var price: Double
    override init() {
        price = 1000000
    }
}
<!-- endtab -->
{% endtabbed_codeblock %}

如果需要重写父类的属性，必须在调用super.init()之后调用，任何属性在调用super.init()之后都可以得到重写，包括父类属性和子类属性。

{% tabbed_codeblock %}
<!-- tab swift -->
	class Car {
    var wheels = 4
}

class SportsCar: Car {
    var price: Double
    override init() {
        price = 1000000
        super.init()
        price = 123123
        wheels = 3
    }
}
<!-- endtab -->
{% endtabbed_codeblock %}

{% alert warning no-icon %}
子类无默认值属性必须在调用父类的构造器之前调用，如果是隐式调用父类构造器也必须要完成没有默认值属性的构造。至于之后的重写就没有必要了，因为在调用父类的构造器之前已经完成了构造值，没必要再覆盖一次。
{% endalert %}

# 当父类有指定构造器

## 子类无指定构造器

当父类中有无默认值，也不是可选属性时，那么类在实例化对象的时候就必须初始化该属性。这时候就需要用到指定构造器，比如这样的init(name: Sting)，那么子类在重写的时候就不能省略父类的构造器了，而且必须要调用父类的指定构造器，调用顺序和上面默认构造器一样。

{% tabbed_codeblock %}
<!-- tab swift -->
class Car {
    var wheels: Int
    init(wheels: Int) {
        self.wheels = wheels
    }
}

class SportsCar: Car {
    var price: Double?
    override init(wheels: Int) {
        price = 10000
        super.init(wheels: wheels)
        self.wheels = 3
    }
}
<!-- endtab -->
{% endtabbed_codeblock %}

## 子类有指定构造器

如果子类有指定构造器，那么子类的指定构造器中必须要调用父类的指定构造器。

{% tabbed_codeblock %}
<!-- tab swift -->
class Car {
    var wheels: Int
    init(wheels: Int) {
        self.wheels = wheels
    }
}

class SportsCar: Car {
    var price: Double
    
    init(wheels: Int,price: Double) {
        self.price = price
        super.init(wheels: wheels)
    }
    
    override init(wheels: Int) {
        price = 10000
        super.init(wheels: wheels)
        self.wheels = 3
    }
}
<!-- endtab -->
{% endtabbed_codeblock %}

{% alert danger no-icon %}
如果指定了构造器，那么系统不会再隐式的创建默认构造器，这时候子类再重写父类的默认构造器是不对的。
{% endalert %}

# 便利构造器

便利构造器依赖于指定构造器，也就是说，便利构造器只是在初始化对象后，加上了属性的修改，如果对象还没有初始化就对对象属性进行赋值操作，是不是显得太流氓了？所以这样是不对的，如果要在便利构造器中修改属性的值，必须在调用指定构造器之后调用，这样才是正确的。

{% tabbed_codeblock %}
<!-- tab swift -->
class Car {
    var wheels: Int
    var price: Double
    init(wheels: Int,price: Double) {
        self.wheels = wheels
        self.price = price
    }
    
    convenience init() {
        self.init(wheels: 4,price: 10000)
        self.price = 111111
    }
<!-- endtab -->
{% endtabbed_codeblock %}

指定构造器能被子类重写为便利构造器，但是子类如果有增加的非可选无默认的属性，那必须提供指定的构造器给便利构造器使用，需要加override。便利构造器也能被指定构造器重写为指定构造器使用，不需要加override。

{% tabbed_codeblock %}
<!-- tab swift -->
class Car {
    var wheels: Int
    init(wheels: Int) {
        self.wheels = wheels
    }
    
    convenience init() {
        self.init(wheels: 4)
    }
}

class SuperCar: Car {
    
    var price: Double
    
    init(wheels: Int,price: Double) {
        self.price = price
        super.init(wheels: wheels)
    }
    
    // 指定构造器被重写为便利构造
    override convenience init(wheels: Int) {
        self.init(wheels: wheels,price: 10000)
    }
    
    // 便利构造器被重写为指定构造器
    init() {
        self.price = 1000
        super.init(wheels: 4)
    }
}
<!-- endtab -->
{% endtabbed_codeblock %}

# 构造器的继承

如果子类没有实现任何指定构造器，当然也不可能实现便利构造器，那么子类将继承父类所有的指定构造器和便利构造器。
如果子类实现了所有的父类构造器，不论是继承还是手写实现，那么子类也会获得所有的便利构造器。

# 可失败构造器

可失败构造器的格式：
{% tabbed_codeblock %}
<!-- tab swift -->
class Car {
    var wheels: Int
    init?(wheels: Int) {
        if wheels < 4 {
            return nil
        }
        self.wheels = wheels
    }
}

class SuperCar: Car {
    
}

guard let car = Car(wheels: 4) else {
    return
}

print(car.wheels)
<!-- endtab -->
{% endtabbed_codeblock %}

{% alert warning no-icon %}
可失败构造器可以被重写为不可失败构造器，但不可失败构造器不能被重写为可失败构造器。
{% endalert %}
{% alert warning no-icon %}
可失败构造器参数名和类型都不能与指定构造器的参数名和类型相同。
{% endalert %}

# 必须实现的构造器

当指定构造器被required修饰的时候，子类必须实现该构造方法，但不用再添加override。如果子类中没有任何构造器，那么将自动继承required修饰的父类构造器，否则必须手写实现。
{% tabbed_codeblock %}
<!-- tab swift -->
class Car {
    var wheels: Int
    required init(wheels: Int) {
        self.wheels = wheels
    }
}

class SuperCar: Car {
    var price: Double
    required init(wheels: Int) {
        self.price = 10000
        super.init(wheels: wheels)
    }
}
<!-- endtab -->
{% endtabbed_codeblock %}

# 构造器是可以被重载的

swift的构造器是可以重载的，与Java不同的是，如果参数都一样，返回值不一样，也可以实现重载，但是Java的重载只与参数有关，与返回值无关。

# swift构造器的规则

- 指定构造器必须保证它所在类引入的所有属性都必须初始化完成，之后才能调用父类的构造器完成其他构造任务。
- 指定构造器必须调用父类构造器，然后为继承的属性设置新值。如果没有这样做，那么指定构造器赋予的新值将会被父类中的构造器所覆盖。
- 便利构造器必须向调用本类中的其他指定构造器，然后再为任意属性赋新的值。如果这样的操作，那么遍历构造器赋予的新值将覆盖指定构造器中的值。
- 构造器在初始化构造完成之前，不能调用任何实例方法，也不能读取任何实例属性的值，且self的值不能被引用。




