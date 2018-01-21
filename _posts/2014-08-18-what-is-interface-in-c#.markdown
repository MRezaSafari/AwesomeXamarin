---
layout: post
title:  "هدف از Interface در CSharp چیست ؟"
date:   2014-08-18 13:32:20
categories: programming dotnet c#
---

خیلی وقت های برای کسانی که به صورت حرفه ای برنامه نویسی می کنند این نوع سوالات پیش خواهد آمد ! هدف از این و یا آن چیست ؟ اصلا چرا باید اینطور باشد و آن طور نباشد ؟ هدف از استفاده از این نوع برنامه نویسی چیست یا اصلا چرا باید اینگونه کد نوشت .یکی از این نوع سوالات همین است ! هدف از Interface در C# چیست و اصلا چرا باید از آن استفاده کرد ؟

معمولا این سوال با جواب های بسیار پیچیده و گاها اشتباه پاسخ داده شده که فقط باعث گمراهی سوال کننده همراه میشه .

بیاید کار رو با یک مثال پیش ببریم :

فرض کنید میخواید برنامه ای رو برای کنترل پخت پیتزا در یک پیتزا فروشی بنویسید .

هدف هم این هست که چند نوع پیتزا رو به سیستم بدیم و اون پیتزا رو بپزه و بعد تحویل ما بده . دقت کنید که هر پیتزا باید پخت بشه (Prepeare) و البته هر کدوم هم به روش مختلفی .

به طور مثال وقتی پیتزای مخلوط قرار هست که آماده بشه سیستم باید چک کنه که مواد اولیه اون موجود باشه بعد شروع به پخت اون کنه .

اگر در حالت معمولی بخواید کد این قسمت رو بنویسید احتما به روش زیر عمل خواهید کرد :
{% highlight c# %}
public class Pizza()
{
    public void Prepare(PizzaType tp)
    {
        switch (tp)
        {
            case PizzaType.StuffedCrust:
                // prepare stuffed crust ingredients in system
                break;

            case PizzaType.DeepDish:
                // prepare deep dish ingredients in system
                break;

            //.... etc.
        }
    }
}
{% endhighlight %}
اما خوب شما مگه فقط همین ۲ مدل پیتزا رو در سیستم دارید ؟ پس اگر قرار باشه که برای هر کدوم یک case جدا تعریف کنید یک کد بسیار طولانی خواهید داشت که بعدا هم خودتون حوصله ندارید که رفع اشکالش کنید و به سختی هم قابل بازبینی هست .  مثلا فرض کنید یک پیتزای جدید باید به این لیست اضافه کنید . حالا فکر کنید که چه کار طاقت فرسایی خواهد بود و چه مقدار کد تکراری وجود خواهد داشت .

راه حل ساده این مشکل استفاده از Interface هست .اینترفیس همه نوع پخت رو تعریف می کنه و هر پیتزا می تونه به صورت مختلفی پخت بشه .

پس اگر این کد رو با استفاده از اینترفیس بنویسیم :
{% highlight c# %}
public interface IPizza
{
    public void Prepare();
}

public class StuffedCrustPizza : IPizza
{
    public void Prepare()
    {
        // Set settings in system for stuffed crust preparations
    }
}

public class DeepDishPizza : IPizza
{
    public void Prepare()
    {
        // Set settings in system for deep dish preparations
    }
}
{% endhighlight %}
حالا قسمت سفارشات دیگه لازم نیست که بدونه هر پیتزا باید چطور پخته بشه تا بتونه مواد اولیه رو بررسی کنه :
{% highlight c# %}
public PreparePizzas(IList<IPizza> pizzas)
{
    foreach (IPizza pizza in pizzas)
        pizza.Prepare();
}
{% endhighlight %}
البته در کدی که ما نوشتیم اصلا مهم نیست که هر پیتزا چطور باید پخته باشه فقط مهم این هست که باید پخته بشه حتی اگر چند نوع پیتزا همزمان سفارش داده بشه .
