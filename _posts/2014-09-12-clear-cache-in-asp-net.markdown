---
layout: post
title:  "پاکسازی کش در ASP.NET"
date:   2014-09-12 13:32:20
categories: programming dotnet aspnet
---

سیستم Caching در asp.net بسیار عالی و کارآمد ساخته شده و باعث کاهش بار اضافی بر روی سرور و بانک اطلاعاتی شما می شود

با این وجود بعضی مواقع نیاز است تا Cache را به صورت دستی پاک کنیم و اطلاعات جدید را از بانک اطلاعاتی دریافت کنیم . با این که ساده به نظر می رسد اما اکثر اوقات اینکار به یک کابوس تبدیل می شود !

کد زیر به شما اجازه می دهد تا این کار را به سادگی انجام دهید . کافی است که یک generic handler در پروژه خودایجاد کنید که به راحتی قابل دسترس باشد ، مثلا : www.yoursite.com/clearcache.ashx

{% highlight c# %}
using System;
using System.Web;
using System.Collections;
 
public class ClearCache : IHttpHandler
{
    public bool IsReusable
    {
        get { return true; }
    }
 
    public void ProcessRequest(HttpContext context)
    {
        foreach (DictionaryEntry item in context.Cache)
            context.Cache.Remove(item.Key as string);
 
        context.Response.ContentType = "text/html";
        context.Response.Write("Cache cleared.");
    }
}
{% endhighlight %}