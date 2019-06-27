---
title: Backbone学习笔记(2)
date: 2017-07-02 20:10:03
tags: Backbone
---

## TodoList的实现

### （1）创建一个Todo的Model

##### 用到的API：

**Extend (API-1)**  

```
 Backbone.Model.extend(properties, [classProperties]) 

```

这是个很有意思的函数，Extend可以建立一条原型链，用它创建的子类可以继续被extend。

**model.defaults (API-2)** 

```
model.defaults or model.defaults() 
```
defaults可以用来制定Model的默认值， 当一个Model的实例被创建时，所有未定义的属性都可以用defaults中设定的属性值来填充。

##### 创建Todo Model：
一个Todolist中，包含了许多个todo的Model，我们可以通过extend来创建一个todo的Model。

```
  var Todo = Backbone.Model.extend({
  
    defaults: function() {
      return {
        title: "empty todo...",
        order: Todos.nextOrder(),
        done: false
      };
    },

    toggle: function() {
      this.save({done: !this.get("done")});
    }

  });
```
其中，toggle函数用于反转每个todo实例的状态。

### （2）创建一个Todo的Collection
所有要做的事项可以看作一个集合，这个集合中包含了所有Todo的Model，这就是一个TodoList。

用到的API：

**collection.model([attrs], [options])** 

用于指定集合中的模型，一种用法是直接传递原始属性的对象，也就是以下的用法；另一种用法是包含多态的模型，具体可见官方文档：[Collection-model](http://backbonejs.org/#Collection-model)

**collection.comparator** 

用于保持collection中models的顺序，每当加入一个model时，都会以特定的顺序加入进来。


```
  var TodoList = Backbone.Collection.extend({

    // 包含的Model是Todo
    model: Todo,

    // 木有后台，这里保存所有todo实例用到的localStorage
    localStorage: new Backbone.LocalStorage("todos-backbone"),

    // 过滤出已经完成的事项
    done: function() {
      return this.where({done: true});
    },

    // 过滤出待完成的事项
    remaining: function() {
      return this.where({done: false});
    },

    // 正常每次生成todo实例，需要保存数据库，这时要生成一个order值
    nextOrder: function() {
      if (!this.length) return 1;
      return this.last().get('order') + 1;
    },

    // Todos are sorted by their original insertion order.
    comparator: 'order'

  });
```

写完记得new一下：

```
  var Todos = new TodoList;
```

### （3）创建View视图

用到的API：

**view.el**

Backbone中的视图都会有一个DOM元素，所以views任何时候都可以被渲染并且一次性插入DOM中。这样的方式将reflows和repaint降低到最少，从而保持高性能的UI渲染。

this.el可以从DOM选择器中解析出来，不然它会通过view的tagName、className、id和属性创建出来。 以下的TodoView就设置了tagName，从而创建了el。 如果这些都没设置的话，el就变成了空的div元素。 一个el和元素的关联通过view的构造器来传递。

el和元素的关系稍后补充。

**view.render()**

view的render是默认不用操作的。当然你可以重写这个函数，可以把model的数据渲染到模版上，之后view的el就会被新的模版数据的HTML更新。最好的方式就是在渲染的最后一步来return这个函数来启用链式的函数调用。

在backbone中，可以使用组合的HTML字符串，也可以用document.createElement来生产DOM树去渲染。Mustache.js，Haml-js和Eco是很好的选择，具体使用没有在bacbone里做尝试。但bachbone严重依赖underscore.js,如果是倾向用使用简单的插入javascript的模版的话，还是用underscore.js吧。

**view.events or view.events()**

events的哈希表指定了一系列的DOM事件，这些事件通过事件委托绑定了View的方法。在**调用initialize之前**，Backbone会自动在实例化的时候添加这些事件监听。


**view.initialize**
    
  TodoView监听model的变化，然后重新渲染。 Todo 和 TodoView是一对一的关系，所以这里设置了直接的引用。
 
 

**object.listenTo(other, event, callback)** 

其实正常我们的比较熟悉的用法是other.on(event, callback, object)，这里直接用object来监听是为了方便的一次性清除所有在other上的监听事件。

首先Todo要创建一个view视图：

```
  var TodoView = Backbone.View.extend({

    tagName:  "li",

    // 对单个的todo的模版做缓存
    template: _.template($('#item-template').html()),

    // events事件的hash
    events: {
      "click .toggle"   : "toggleDone",
      "dblclick .view"  : "edit",
      "click a.destroy" : "clear",
      "keypress .edit"  : "updateOnEnter",
      "blur .edit"      : "close"
    },


    initialize: function() {
      this.listenTo(this.model, 'change', this.render);
      this.listenTo(this.model, 'destroy', this.remove);
    },

    // 对todo的item做渲染
    render: function() {
      this.$el.html(this.template(this.model.toJSON()));
      this.$el.toggleClass('done', this.model.get('done'));
      this.input = this.$('.edit');
      return this;
    },

    // 反转 “done” 的状态
    toggleDone: function() {
      this.model.toggle();
    },

    // 通过"editing"` 状态的改变控制显示input框.
    edit: function() {
      this.$el.addClass("editing");
      this.input.focus();
    },

    // 关闭“editing”状态，保存新的todo的item
    close: function() {
      var value = this.input.val();
      if (!value) {
        this.clear();
      } else {
        this.model.save({title: value});
        this.$el.removeClass("editing");
      }
    },

    // 13是enter键的keynode，enter之后更新
    updateOnEnter: function(e) {
      if (e.keyCode == 13) this.close();
    },

    // 删除todo的item
    clear: function() {
      this.model.destroy();
    }

  });
```

APP也要创建一个view视图：

```
  var AppView = Backbone.View.extend({
  
  	// APPView的el是$("#todoapp")
    el: $("#todoapp"),

    statsTemplate: _.template($('#stats-template').html()),

    events: {
      "keypress #new-todo": "createOnEnter"
    },

    initialize: function() {

      this.input = this.$("#new-todo");
      this.allCheckbox = this.$("#toggle-all")[0];

      this.listenTo(Todos, 'add', this.addOne);
      this.listenTo(Todos, 'reset', this.addAll);
      this.listenTo(Todos, 'all', this.render);

      this.footer = this.$('footer');
      this.main = $('#main');

      Todos.fetch();
    },

    render: function() {
      var done = Todos.done().length;
      var remaining = Todos.remaining().length;

      if (Todos.length) {
        this.main.show();
        this.footer.show();
        this.footer.html(this.statsTemplate({done: done, remaining: remaining}));
      } else {
        this.main.hide();
        this.footer.hide();
      }

      this.allCheckbox.checked = !remaining;
    },

    createOnEnter: function(e) {
      if(e.keyCode != 13) return;
      if(!this.input.val()) return;

      Todos.create({title: this.input.val()});
      this.input.val('');
    }
  });
```

总体上看，demo可以运行了。下一遍开始总结一下backbone的设计模式。