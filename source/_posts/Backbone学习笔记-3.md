---
title: Backbone学习笔记(3)
date: 2017-07-11 21:58:21
tags: Backbone
---

本篇主要讲一讲Backbone的设计模式。
### MVC？ MVP？

现有JavaScript框架很少会按照经典的MVC或者MCP模式来实现，但通常我们会认为Backbone是MVC风格的库。然鹅我们在Backbone中很难界定controller的部分，因为Backbone.View和Backbone.Router共同分担了这部分的责任，所以也有很多开发者认为Backbone更符合MVP的风格。

这么说主要是由于我们可以发现，当存在很多view被分离到它们的组件中的时候，最后需要把这些分离的View组装到一起。 一般可以通过Backbone.Router或者获取数据之后的回调函数。如果说是MVP风格的话，那Backbone中不同模块需要这样划分：

* Underscore或HandleBars这种模板看作纯视图层
* Backbone.View则作为P，表示器这一层。
* Model还是Model

回到上一篇的代码中的View层来具体分析：这里的APPView除了作为视图之外，还承包了很多其他的活儿：

* 监听Todos的变化，一有变化就重新渲染，这就是观察者模式；
* render部分的模板也是它处理的哦

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

所以强行给Backbone归类不是特别科学，毕竟只是在理论上来匹配，实际当中更多是在经典的模式上有自己的实现。



