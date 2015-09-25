---
layout: post
title: Backbone.js’de ModelView Yardımcı Sınıfı Oluşturmak
abstract: Backbone.js ile uygulama geliştirirken her modelin viewi için bir render methodu tanımlamak durumunda kalıyorduk. Bu duruma bir çözüm üretelim istedim.
---

Backbone.js ile uygulama geliştirirken her modelin viewi için bir `render` methodu tanımlamak durumunda kalıyorduk. Ve bu method hep aynı işlemi yapıyordu. Bu yüzden bu kod fazlalığını azaltmak için çok basitçe bir yöntemle `ModelView` üst sınıfı yaratayım dedim.

{% highlight js %}
Backbone.ModelView = Backbone.View.extend({
    render: function() {
        this.setElement(this.template(this.model.toJSON()));
        return this;
    }
});
{% endhighlight %}

Böylece Backbone içerisinde bir sınıf yaratmış olduk. Artık aşağıdaki şekilde bir modelin view’ini tanımlayabilir olacağız:

{% highlight js %}
var productView = Backbone.ModelView.extend({
    template: _.template('<li><%= name %></li>')
});

var product = Backbone.Model.extend();

var view = new productView({model: new product({name: 'test'})});

view.render().el //render edilmiş halini dönecek!
{% endhighlight %}

Bu basit örnekte görüldüğü gibi, her seferinde yeni bir `render` methodu tanımlamak zorunda kalmıyoruz.
