layout: '[layout]'
title: Yii2 笔记整理-概念-组件
date: 2018-12-26 16:54:30
tags: [Yii2,概念,组件]
categories: [hexo,github]
---

# [组件（Component）](https://www.yiichina.com/doc/guide/2.0/concept-components)


组件是yii的基础，是yii\base\Component类和其子类的实力，主要功能如下
- [属性（Property）](https://www.yiichina.com/doc/guide/2.0/concept-components)
- [事件（Event）](https://www.yiichina.com/doc/guide/2.0/concept-events)
- [行为（Behavior）](https://www.yiichina.com/doc/guide/2.0/concept-events)

或单独使用，或彼此配合，这些功能的应用让 Yii 的类变得更加灵活和易用

组件比常规的对象（Object）稍微重量级一点，因为他们要使用额外的内存和 CPU 时间来处理 事件 和 行为 

当继承 yii\base\Component 或 yii\base\Object 时， 推荐你使用如下的编码风格：

    - 若你需要重写构造方法（Constructor），传入 $config 作为构造器方法最后一个参数， 然后把它传递给父类的构造方法。
    - 永远在你重写的构造方法结尾处调用一下父类的构造方法。
    - 如果你重写了 yii\base\BaseObject::init() 方法，请确保你在 init 方法的开头处调用了父类的 init 方法。

案例
```php
<?php
    namespace yii\components\MyClass;
    use yii\base\BaseObject;
    class MyClass extends BaseObject
    {
        public $prop1;
        public $prop2;
        public function __construct($param1, $param2, $config = [])
        {
            // ... 在应用配置之前初始化
            parent::__construct($config);
        }
        public function init()
        {
            parent::init();
            // ... 应用配置后进行初始化
        }
    }
    
    $component = new MyClass(1, 2, ['prop1' => 3, 'prop2' => 4]);
    // 方法二：
    $component = \Yii::createObject([
        'class' => MyClass::className(),
        'prop1' => 3,
        'prop2' => 4,
    ], [1, 2]);
```
信息： 尽管调用 Yii::createObject() 的方法看起来更加复杂，但这主要因为它更加灵活强大， 它是基于依赖注入容器实现的。

yii\base\BaseObject 类执行时的生命周期如下：

    1 构造方法内的预初始化过程。你可以在这儿给各属性设置缺省值。
    2 通过 $config 配置对象。配置的过程可能会覆盖掉先前在构造方法内设置的默认值。
    3 在 init() 方法内进行初始化后的收尾工作。你可以通过重写此方法，进行一些良品检验，属性的初始化之类的工作。
    对象方法调用。

    前三步都是在对象的构造方法内发生的。这意味着一旦你获得了一个对象实例（即一个对象）， 那么它就已经初始化就绪可供使用。
    
    
    
### 属性

##### 在 PHP 中，类的成员变量也被称为属性。它们是类定义的一部分， 用来表现一个实例的状态（也就是区分类的不同实例）。 在具体实践中，常常会想用一个稍微特殊些的方法实现属性的读写。 例如，如果有需求每次都要对 label 属性执行 trim 操作， 就可以用以下代码实现：

    $object->label = trim($label); //label 就是一个属性
    
### 事件

##### 事件可以将自定义代码“注入”到现有代码中的特定执行点。 附加自定义代码到某个事件，当这个事件被触发时，这些代码就会自动执行。 例如，邮件程序对象成功发出消息时可触发 messageSent 事件。 如想追踪成功发送的消息，可以附加相应追踪代码到 messageSent 事件。

    事件处理器（Event Handlers）

    事件处理器是一个PHP 回调函数， 当它所附加到的事件被触发时它就会执行。可以使用以下回调函数之一：
    
    事件处理器顺序（Event Handler Order）
    
    可以附加一个或多个处理器到一个事件。当事件被触发，已附加的处理器将按附加次序依次调用。 如果某个处理器需要停止其后的处理器调用，可以设置 $event 参数的 yii\base\Event::$handled 属性为真，
    
    默认新附加的事件处理器排在已存在处理器队列的最后。
     因此，这个处理器将在事件被触发时最后一个调用。 在处理器队列最前面插入新处理器将使该处理器最先调用，可以传递第四个参数 $append 为假并调用 yii\base\Component::on() 方法实现：
     
     触发事件（Triggering Events）
     事件通过调用 yii\base\Component::trigger() 方法触发，此方法须传递事件名， 还可以传递一个事件对象，用来传递参数到事件处理器。如：