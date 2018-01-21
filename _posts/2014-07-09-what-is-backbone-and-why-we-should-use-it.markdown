---
layout: post
title:  "Backbone.js چیست و چرا باید از آن استفاده کنیم ؟"
date:   2014-07-09 18:25:23
categories: js framework
---
Backbone معروفترین کتابخانه جاوااسکریپت است که از الگوی MV* پشتیبانی می کند و به شما اجازه می دهد برنامه هایی با الگوی MVC (Model-View-Controller ) مانند App ها یا SPA ها (Single Page Application) ایجاد کنید .

کامپوننت های اصلی Backbone شامل Model , View , Collection , Router و Event Class Object می باشد .

در Backbone قسمت Model وظیفه نگهداری اطلاعاتی که به وسیله RESTful JSON از سرور دریافت می شوند را دارد که این بخش با View به صورت مستقیم در ارتباط است .

View وظیفه تولید و ارسال کدهای HTML را دارد که می تواند از یک فریم ورک یا قابلیت Javascript Templating استفاده کند .

Router وظیفه ای مانند Controller را دارد و درخواست های داده شده را بررسی می کند و تشخیص می دهد که در حال حاظر کدام URL باید ارسال شود .

Event یک ماژول است که می تواند به هر آبجکتی متصل شده و یکسری Event ها را اجرا نماید .

**نکات کلیدی Backbone**

- وابستگی بسیار زیادی به Underscore.js دارد و به وسیله این وابستگی می تواند قابلیت های خود را گسترش داده و کارا تر شود .
- وابستگی تقریبا زیادی به jQuery دارد .
- Backbone این قابلیت را دارد ، زمانی که Model تغییر می کند به صورت اتوماتیک HTML را نیز تغییر دهد .
- قابلیت کار با فریم ورک ها برای ایجاد کدهای HTML را دارا می باشد تا شما مجبور نباشید کدهای HTML را داخل کدهای JavaScript بنویسید .
- یک راه حل بسیار ساده و روان برای کارکردن با آبجکت های HTML ارائه می کند .

**چرا Backbone**

Backbone به شما این امکان را خواهد داد تا بتوانید یک برنامه را به وسیله javascript و بدون درگیری بیش از حد با لایه های view و model ایجاد کنید .
Backbone بهترین نمایش را در ایجاد برنامه های MVC مثل Web Application ها ،Single Page Application ها ، یا برنامه های javascript کاملا مدیریت شده را دارد .

**شروع کار با Backbone**

برای شروع به کار با Backbone شما به ۳ کتابخانه نیاز دارید : jQuery, Backbone, Underscore

این کتابخانه ها را داخل پوشه دلخواه خود در مسیر برنامه خود قرار دهید و در فایل index خود به آنها لینک دهید
{% highlight html %}
<!DOCTYPE html>
<html>
<head>
 <meta http-equiv="Content-type" content="text/html; charset=utf-8">
 <title>Backbone.js</title>
 <!--CSS for you application-->
 <link rel="stylesheet" href="css/site.css" type="text/css" media="screen" />
 
 <!--Required libraries to setup Backbone-->
 <script type="text/javascript" src="js/lib/jquery.js"></script>
 <script type="text/javascript" src="js/lib/underscore.js"></script>
 <script type="text/javascript" src="js/lib/backbone.js"></script>
 
 <!--Initilizing Backbone components-->
 <script type="text/javascript" src="js/init.js"></script>
 
 <!-- Backbone components-->
 <script type="text/javascript" src="js/models.js"></script>
 <script type="text/javascript" src="js/collections.js"></script>
 <script type="text/javascript" src="js/views.js"></script>
 <script type="text/javascript" src="js/routers.js"></script>
 
 <!--Application related js-->
 <script type="text/javascript" src="js/application.js"></script>
</head>
<body>
 <h1>Getting Started with Backbone.js</h1>
</body>
</html>
{% endhighlight %}