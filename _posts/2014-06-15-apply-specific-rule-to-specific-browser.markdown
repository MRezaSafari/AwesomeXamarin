---
layout: post
title:  "اعمال یک خاصیت css در مرورگری خاص"
date:   2014-06-15 18:25:23
categories: css
---
برای یک طراح رویه یا همان Frontend developer بعضی مواقع رخ خواهد داد که می بایست یک خاصیت خاص مثلا margin-top یا overflow را فقط در بعضی از مرورگر ها فعال کند .

به طور مثال در پلاگین Masonry در بعضی از حالت ها رخ خواهد داد که میزان Top یک Div در مرورگر کروم به اشتباه محاسبه می شود و div به بالای container می چسبد اما در فایرفاکس و دیگر مرورگر ها مشکلی نیست .

در این حالت بهترین ، راحت ترین و سریع ترین کار استفاده از یک margin است . اما اگر margin را به طور کلی اعمال کنیم اینبار در فایرفاکس به مشکل بر خواهیم خورد .

راه حل ، استفاده از یک خاصیت css است که فقط در کروم اعمال شود . استفاده از کد زیر بهترین راه حل است :
{% highlight css %}
@media screen and (-webkit-min-device-pixel-ratio:0) { 
 div{top:0;} 
}
{% endhighlight %}
البته می توانید با javascript هم اینکار را انجام دهید :
{% highlight css %}
if (navigator.appVersion.indexOf("Chrome/") != -1) {
// modify button 
}
{% endhighlight %}
در بعضی مواقع هم شاید بخواهید فقط یک خاصیت را برای فایرفاکس اعمال کنید :
{% highlight css %}
@-moz-document url-prefix() { 
  .selector {
     color:lime;
  }
}
{% endhighlight %}
البته شاید هم ، فقط برای استاد! internet explorer
{% highlight css %}
.selector
 { 
color: #fff\9; 
}
{% endhighlight %}
قراردادن \۹ باعث می شود تا این خاصیت فقط برای ie اعمال گردد .