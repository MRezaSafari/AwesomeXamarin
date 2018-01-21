---
layout: post
title:  "بازکردن url در پنجره جدید توسط کنترلر"
date:   2014-09-26 18:23:47
categories: programming dotnet aspnet mvc
---

امروز در سایت برنامه نویس و بخش ASP.NET MVC یک سوالی مطرح شد که خوب فکر می کنم سوال خیلی ها باشه و زیاد براشون پیش بیاد .

لینک اصلی سوال : [بازکردن url در پنجره جدید توسط کنترلر](http://barnamenevis.org/showthread.php?450035-%D8%A8%D8%A7%D8%B2%DA%A9%D8%B1%D8%AF%D9%86-url-%D8%AF%D8%B1-%D9%BE%D9%86%D8%AC%D8%B1%D9%87-%D8%AC%D8%AF%DB%8C%D8%AF-%D8%AA%D9%88%D8%B3%D8%B7-%DA%A9%D9%86%D8%AA%D8%B1%D9%84%D8%B1)

سوال هم این هست که : تو سایتم قسمتی دارم به نام لینک ها
میخوام وقتی کاربر روی اون کلیک میکنه تو پنجره جدید سایت مورد نظر باز بشه و همچنین تعداد کلیک ها رو هم نمایش بده

راهی که ایشون انتخاب کردند این هست که توسط کنترلر و به صورت مستقیم بیان و این پنجره رو نشون بدن .

خوب اولین مسئله این هست که این کار اصلا ربطی به کنترلر نداره . کنترلر در سمت سرور وظایف خودش رو داره و تنها وظیفه ای که اینجا میشه به عهده کنترلر گذاشت ثبت تعداد کلیک ها در جایی مثل بانک اطلاعاتی هست .

بقیه کارها رو باید به View و Ajax سپرد .

خوب حالا یکی از راحت ترین روش هایی که میشه این کار رو خیلی تر و تمیز انجام داد رو براتون شرح می دم .

برای شروع صفحه مورد نظر رو اینجوری درست می کنید :
{% highlight html %}
<input id="txtSiteAddress" type="text" value="http://www.google.com" />
<a class="btn btn-success" id="Showpopup" href="">نمایش اعلان</a>
{% endhighlight %}
خوب از اونجایی که این یک مثال هست ما یک فیلد رو مشخص کردیم که داخلش آدرس اون سایت رو بنویسیم و یک کلید هم که با کلیک کردن روش اون صفحه نمایش داده بشه .

اینجا از جاوااسکریپت کمک می گیریم تا یک درخواست Ajax رو برای کنترلر ارسال کنیم و اگر کنترلر ثبت شدن اطلاعات رو تایید کرد بعد پنجره مورد نظر رو نشون بدیم
{% highlight js %}
$('#Showpopup').click(function () {
	var UserSiteAddress = $('#txtSiteAddress').val();

	$.ajax({
		url: "/Home/ShowMyPopup",
		data: { SiteAddress: UserSiteAddress },
		cache: true,
		type: "POST",
		success: function (data) {
			if (data = "True") {
				window.showModalDialog(UserSiteAddress, "wndPopUp", width = 1000, height = 1000);
			} else {
				alert("خطایی رخ داده است . مجددا امتحان کنید")
			};
		}
	});
});
{% endhighlight %}
توضیح کدهای بالا :

در خط اول از jQuery درخواست کردیم زمانی که روی یک کلید با آی دی Showpopup کلیک کردیم یک function رو اجرا کنه .

در خط دوم محتوای فیلد مورد نظرمون رو داخل یک متغیر با نام UserSiteAddress قرار دادیم .

در خط بعد یک درخواست Ajax به یک function با نام ShowMyPopup در کنترلر Home دادیم .

در خط بعدی نام سایت رو به صورت data به کنترلر ارسال کردیم .

در متد success هم چک کردیم که اگر پاسخ function ما True بود اون وقت پنجره رو نشون بده در غیر این صورت خطایی رو نشون بده .

 
 خوب حالا نوبت به کدهای کنترلر میرسه که به این صورت هست :
 {% highlight c# %}
[AcceptVerbs(HttpVerbs.Post)]
public JsonResult ShowMyPopup(string SiteAddress)
{
	try
	{
		AddSiteVisit(SiteAddress);
		return Json(Boolean.TrueString, JsonRequestBehavior.AllowGet);
	}
	catch (Exception)
	{
		return Json(Boolean.FalseString, JsonRequestBehavior.AllowGet);
	}

}

private void AddSiteVisit(string SiteAddress)
{
	//ذخیره اطلاعات در هر جایی
}
{% endhighlight %}
خوب این کدها نحوه کارکردش کاملا مشخص هست . یک متد اصلی هست که به یک متد دستور میده که اطلاعات رو ثبت کنه و اگر تایید بود یک جواب رو به صورت Json به View برگردونه .
