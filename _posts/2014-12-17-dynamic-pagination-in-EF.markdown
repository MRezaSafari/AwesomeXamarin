---
layout: post
title:  "صفحه بندی پویا در Entity Framework"
date:   2014-12-17 18:25:23
categories:  programming c# orm
---

در اکثر برنامه‌ها ما نیازمند این موضوع هستیم که بتوانیم اطلاعاتی را به کاربر نشان دهیم. در بعضی از موارد این اطلاعات بسیار زیاد هستند و نیاز است در این حالت از صفحه بندی اطلاعات یا Data Paging استفاده کنیم. در ASP.NET برای ارائه اطلاعات به کاربر معمولا از کنترلهای Gridview ، ListView و امثالهم استفاده می‌شود. مشکل اساسی این کنترل‌ها این است که آنها اطلاعات را به صورت کامل از سرور دریافت کرده، سپس اقدام به نمایش صفحه بندی شده آن می‌نمایند که این موضوع باعث استفاده بی مورد از حافظه سرور شده و هزینه زیادی برای برنامه ما خواهد داشت.

صفحه بندی در سطح پایگاه داده بهترین روش برای استفاده بهینه از منابع است. برای رسیدن به این مقصود ما نیاز به یک کوئری خواهیم داشت که فقط همان صفحه مورد نیاز را به کنترلر تحویل دهد.
با استفاده از متد توسعه یافته زیر می‌توان به این مقصود دست یافت:

{% highlight c# %}
/// <summary>
/// صفحه بندی کوئری
/// </summary>
/// <param name="query">کوئری مورد نظر شما</param>
/// <param name="pageNum">شماره صفحه</param>
/// <param name="pageSize">سایز صفحه</param>
/// <param name="orderByProperty">ترتیب خواص</param>
/// <param name="isAscendingOrder">اگر برابر با <c>true</c> باشد صعودی است</param>
/// <param name="rowsCount">تعداد کل ردیف ها</param>
/// <returns></returns>
private static IQueryable<T> PagedResult<T, TResult>(IQueryable<T> query, int pageNum, int pageSize,
                Expression<Func<T, TResult>> orderByProperty, bool isAscendingOrder, out int rowsCount)
{
    if (pageSize <= 0) pageSize = 20;
     
    //مجموع ردیف‌های به دست آمده
    rowsCount = query.Count();
 
// اگر شماره صفحه کوچکتر از 0 بود صفحه اول نشان داده شود
    if (rowsCount <= pageSize || pageNum <= 0) pageNum = 1;
     
// محاسبه ردیف هایی که نسبت به سایز صفحه باید از آنها گذشت
    int excludedRows = (pageNum - 1) * pageSize;
 
    query = isAscendingOrder ? query.OrderBy(orderByProperty) : query.OrderByDescending(orderByProperty);
     
// ردشدن از ردیف‌های اضافی و  دریافت ردیف‌های مورد نظر برای صفحه مربوطه
    return query.Skip(excludedRows).Take(pageSize);
}
{% endhighlight %}
نحوه استفاده : 
فرض کنید که کوئری مورد نظر قرار است تا یکسری از مطالب را از جدول Articles نمایش دهد. برای دریافت ۲۰ ردیف اول جهت استفاده در صفحه اول، از کد زیر استفاده می‌کنیم :
{% highlight c# %}
var articles = (from article in Articles
                where article.Author == "Abc"
                select article);
 
int totalArticles;    
 
var firstPageData =  PagedResult(articles, 1, 20, article => article.PublishedDate, false, out totalArticles);
{% endhighlight %}
یا به صورت ساده‌تر و قابل اجرا به صورت کلی‌تر :
{% highlight c# %}
var context = new AtricleEntityModel(); 
var query = context.ArticlesPagedResult(articles, <pageNumber>, 20, article => article.PublishedDate, false, out totalArticles);
{% endhighlight %}