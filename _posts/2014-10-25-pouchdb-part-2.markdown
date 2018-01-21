---
layout: post
title:  "آموزش PouchDB : قسمت دوم (شروع به کار)"
date:   2014-10-25 17:35:23
categories: database nosql
---

در این آموزش هدف ما ایجاد برنامه ای بر اساس TodoMVC است که می تواند خودش را با یک دیتابیس آنلاین همگام سازی کند .

مطمئن باشید بیشتر از ۱۰ دقیقه وقت شمارا نخواهد گرفت !

**نصب PouchDB**

فایل index.html  را باز کنید و فایل های PouchDB را به آن اضافه کنید :
{% highlight js %}
<script src="//cdn.jsdelivr.net/pouchdb/2.2.0/pouchdb.min.js"></script>
<script src="js/base.js"></script>
<script src="js/app.js"></script>
{% endhighlight %}
حالا PouchDB نصب شده و آماده به کار است . ( در نسخه نهایی بهتر است از فایل های local استفاده کنید )

**ایجاد بانک اطلاعاتی**

بقیه کارها باید در فایل app.js انجام شود . برای شروع باید بانک اطلاعاتی جدیدی را برای درج اطلاعات خودمان ایجاد کنیم . برای ایجاد بانک اطلاعاتی خیلی ساده یک instance جدید از آبجکت PouchDB می سازیم .
{% highlight js %}
var db = new PouchDB('todos');
var remoteCouch = false;
{% endhighlight %}
نیازی نیست که برای بانک خود نما (Schema) ایجاد کنید ! بعد از اینکه اسم را مشخص کنید بانک آماده به کار است .

**ثبت اطلاعات در بانک اطلاعاتی**

اولین کاری که باید انجام بدیم این هست که یک متد ایجاد کنیم که توسط اون اطلاعات خودمون رو در بانک اطلاعاتی ذخیره کنیم . نام متد رو addTodo انتخاب می کنیم و کارش این هست که وقتی کاربر کلید Enter رو فشار داد اطلاعات رو داخل بانک اطلاعاتی ذخیره کنه . متد مورد نظر به این صورت هست :
{% highlight js %}
function addTodo(text) {
  var todo = {
    _id: new Date().toISOString(),
    title: text,
    completed: false
  };
  db.put(todo, function callback(err, result) {
    if (!err) {
      console.log('Successfully posted a todo!');
    }
  });
}
{% endhighlight %}
در PouchDB هر پرونده نیاز داره که یک فیلد unique با نام _id داشته باشه . اگر داده ای بخواهد در بانک اطلاعاتی ثبت شود و دارای _id مشابه باشد با اون به صورت یک update رفتار خواهد شد . در اینجا ما از تاریخ با عنوان id استفاده کردیم که در این مورد خاص می باشد . شما می تواند از db.post() استفاده کنید اگر یک id به صورت random می خواهید . تنها چیزی که اجباری هست در هنگام ساختن یک پرونده همین _id هست و بقیه چیزها کاملا اختیاری هستند .

**نمایش اطلاعات**

در اینجا ما از یک function کمکی به نام redrawTodosUI استفاده خواهیم کرد که وظیفش این هست که یک array رو دریافت کنه و اون رو هر طور که مشخص کردید نمایش بده که البته اون رو به سلیقه خودتون می تونید آماده کنید .

تنها کاری که باید انجام دهیم این است که اطلاعات رو از بانک اطلاعاتی استخراج کنیم و به function مورد نظر پاس بدیم .

در اینجا می تونیم خیلی راحت از db.allDocs برای خواندن اطلاعات از بانک اطلاعاتی استفاده کنیم .

خاصیت include_docs به PouchDB این دستور رو می ده که ما درخواست دریافت همه اطلاعات داخل پرونده ها رو داریم و descending هم نحوه مرتب سازی رو که بر اساس _id هست رو مشخص می کنه که بتونم جدیدترین اطلاعات رو اول دریافت کنیم .
{% highlight js %}
function showTodos() {
  db.allDocs({include_docs: true, descending: true}, function(err, doc) {
    redrawTodosUI(doc.rows);
  });
}
{% endhighlight %}
**به روزرسانی خودکار**

هر بار که ما اطلاعات جدیدی رو در بانک اطلاعاتی وارد می کنیم نیازمند این هستیم که اطلاعات جدید رو به صورت خودکار دریافت کنیم و نمایش بدهیم . برای این منظور می توانیم به روش زیر عمل کنیم :
{% highlight js %}
var remoteCouch = false;

db.info(function(err, info) {
  db.changes({
    since: info.update_seq,
    live: true
  }).on('change', showTodos);
});
{% endhighlight %}
حالا هر بار که اطلاعات جدیدی در بانک اطلاعات ثبت بشه به صورت خودکار نمایش داده میشه . خاصیت live مشخص می کنه که این کار می تونه بی نهایت بار انجام بشه .

**ویرایش اطلاعات**

وقتی که کاربر یک Todo را انجام شده (Complete) می کند یک چک باکس را علامت می زند و اعلام می کند که این کار انجام شده است .
{% highlight js %}
function checkboxChanged(todo, event) {
  todo.completed = event.target.checked;
  db.put(todo);
}
{% endhighlight %}
این بخش شبیه به قسمت ثبت اطلاعات است با این تفاوت که باید شامل یک فیلد _rev ( اضافه بر _id ) نیز باشد در غیر اینصورت تغییرات ثبت نخواهد شد . این کار برای این است که اشتباهی اطلاعاتی در بانک ثبت نشود .

**حذف اطلاعات**

برای حذف اطلاعات باید از متد db.remove به همراه آبجکت مورد نظر استفاده کنید .
{% highlight js %}
function deleteButtonPressed(todo) {
  db.remove(todo);
}
{% endhighlight %}
مانند بخش ویرایش نیز باید در اینجا هم _id و هم _rev مشخص شود .

باید توجه داشته باشید در اینجا همان آبجکتی که از بانک فراخوانی کرده ایم را به این متد پاس می دهیم .

البته شما می تونید آبجکت خودتون رو ایجاد کنید {_id: todo._id, _rev: todo._rev} اما خوب همان روش قبلی عاقلانه تر است و احتمال خطای کمتری دارد .

**نصب CouchDB**

حالا می خواهیم همگام سازی را اجرا کنیم و برای این کار نیاز هست یا CouchDB را به صورت Local نصب کنیم یا از سرویس های آنلاین مثل IrisCouch استفاده کنید .

**فعال سازی CORS**

برای اینکه به صورت مستقیم با CouchDB در ارتباط باشید باید مطمئن بشید که CORS فعال هست .
در اینجا فقط نام کاربری و رمز ورود رو مشخص کنید . به صورت پیش فرض CouchDB به صورت Admin Party نصب میشه و نیازی به User و password نداره مگر اینکه براش مشخص کنید .

شما باید myname.iriscouch.com رو با سرور خودتون جایگزین کنید که اگر به صورت local کار می کنید ۱۲۷.۰.۰.۱:۵۹۸۴ است .
{% highlight js %}
$ export HOST=http://username:password@myname.iriscouch.com
$ curl -X PUT $HOST/_config/httpd/enable_cors -d '"true"'
$ curl -X PUT $HOST/_config/cors/origins -d '"*"'
$ curl -X PUT $HOST/_config/cors/credentials -d '"true"'
$ curl -X PUT $HOST/_config/cors/methods -d '"GET, PUT, POST, HEAD, DELETE"'
$ curl -X PUT $HOST/_config/cors/headers -d 
  '"accept, authorization, content-type, origin"'
{% endhighlight %}
**راه اندازی ارتباط ساده دو طرفه**

به فایل app.js برگردید . در اینجا باید آدرس بانک اطلاعاتی آنلاین را مشخص کنیم .
{% highlight js %}
var db = new PouchDB('todos');
var remoteCouch = 'http://user:pass@mname.iriscouch.com/todos';
{% endhighlight %}
فراموش نکنید که نام کاربری و رمز ورود را بسته به نیاز خود تغییر دهید .
حالا می تونیم function که وظیفه همگام سازی اطلاعات را به عهده دارد بنویسیم :
{% highlight js %}
function sync() {
  syncDom.setAttribute('data-sync-state', 'syncing');
  var opts = {live: true};
  db.replicate.to(remoteCouch, opts, syncError);
  db.replicate.from(remoteCouch, opts, syncError);
}
{% endhighlight %}
db.replicate() به PouchDB می گوید که که همه اطلاعات را “به” یا “از” remoteCouch انتقال دهد .

ما دوبار این درخواست را دادیم . در بار اول اطلاعات به داخل سرور ریموت منتقل می شود و در بار دوم اطلاعات local جایگزین می شوند .

یک callback هم وقتی که این کار به پایان برسد اجرا خواهد شد .

می توانید برنامه خود را در دو مرورگر مختلف اجرا کنید تا نتیجه کار را ببینید .

تبریک می گوییم !

شما اولین برنامه خود را توسط PouchDB ایجاد کنید . البته این یک برنامه ساده بود و در دنیای واقعی نیاز هست تا خیلی کارهای بیشتری را انجام دهید . اما باز هم اصول اولیه کار را یاد گرفتید و ادامه راه را می توانید تنهایی ادامه دهید .