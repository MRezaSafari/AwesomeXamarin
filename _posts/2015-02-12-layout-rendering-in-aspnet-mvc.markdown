---
layout: post
title:  "راه‌های متفاوت رندر لایه‌ها در ASP.NET MVC"
date:   2014-12-17 18:25:23
categories:  programming dotnet aspnet mvc
---
در MVC لایه‌ها (Layouts) مانند Master Page‌ها در وب فرم عمل می‌کنند. این به ما کمک می‌کند تا بتوانیم از تکرار کدها پرهیز کنیم و سریعتر صفحات خودمان را گسترش دهیم. مثل Master Page‌ها، این صفحات هم (Layouts) می‌تواند شامل قالب‌های CSS مختلف، کدهای Javascript مختلف و قالب بندی‌های مختلفی باشند.

در میان View‌های یک برنامه MVC فایلی را به عنوان _ViewStart  داریم که وظیفه‌ی آن نگهداری قالب اصلی برنامه‌ی ما است.
در این مقاله سعی شده است تا راه‌های موجود برای استفاده از این قالب‌ها را در یک برنامه MVC، بررسی کنیم.
فرض ما بر این است که می‌خواهیم لایه‌ای را که در تصویر زیر می‌بینید، مورد استفاده قرار دهیم:

![layout](http://i61.tinypic.com/2vc8b5t.png)

**روش شماره ۱ : استفاده از  _ViewStart موجود در ریشه‌ی پوشه Views**

با استفاده از کد زیر می‌توانیم فایل پیش فرضی را که قرار است رندر شود، تغییر دهیم:
{% highlight c# %}
@{
 var controller = HttpContext.Current.Request.RequestContext.RouteData.Values["Controller"].ToString();
  
 string layout = "";
 if (controller == "Admin")
 {
 layout = "~/Views/Shared/_AdminLayout.cshtml";
 }
 else
 {
 layout = "~/Views/Shared/_Layout.cshtml";
 }
  
 Layout = layout;
}
{% endhighlight %}

**روش شماره ۲ : مشخص کردن لایه در Action** 

همچنین می‌توانیم فایل مورد نظر را در اکشن خودمان، بازنویسی (override) کنیم:
{% highlight c# %}
public ActionResult Index()
{
 RegisterModel model = new RegisterModel();
 //TO DO:
 return View("Index", "_AdminLayout", model);
}
{% endhighlight %}
**روش شماره ۳ : مشخص کردن لایه به ازای هر View** 

می‌توانیم در هر View هم لایه مربوط به آن را مشخص کنیم:
{% highlight c# %}
@{
 Layout = "~/Views/Shared/_AdminLayout.cshtml";
}
{% endhighlight %}

**روش شماره ۴ : اضافه کردن فایل _ViewStart به ازای هر کنترلر** 

همانطور که در تصویر زیر می‌بینید آخرین روش این هست که می‌توانید فایل‌های _ViewStart مختلفی را به ازای هر کنترلر، داخل پوشه View مربوطه قرار بدید تا سیستم از آن استفاده کند.

![layout1](http://i61.tinypic.com/2ahfceh.png)