---
layout: default
title: הציגו thumbnails לרשימת הרעיונות
permalink: thumbnails
---

# צרי Thumbnails עם Carrierwave

*נוצר ע"י Miha Filej, [@mfilej](https://twitter.com/mfilej)*

__מדריך__: הסבר מה קורה כאשר מגדירים רוחב לתמונה ב-HTML בסוף שלב 4, ומה ההבדל בין זה לבין resize של התמונה על השרת.

## התקנת ImageMagick

* OS X: הריצי את הפקודה `brew install imagemagick`. אם פקודת ה-brew אינה מותקנת אצלך, את יכולה [להתקין HomeBrew כאן][in-homebrew].
* Windows: הורידי והריצי את ה-[ImageMagick installer][im-win] (השתמשי בלינק *download* הראשון).
* Linux: ב-Ubuntu ו-Debian, הריצי `sudo apt-get install imagemagick`. השתמשי ב-package manager המתאים במקום `apt-get` עבור הפצות אחרות.

  [im-win]: http://www.imagemagick.org/script/binary-releases.php?ImageMagick=vkv0r0at8sjl5qo91788rtuvs3#windows
  [in-homebrew]: http://mxcl.github.io/homebrew/

__מדריך__: מה זה ImageMagick ואיך זה שונה מספריות/ג'מים שהשתמשנו בהם קודם?

פתחי את `Gemfile` בפרוייקט והוסיפי לו:

{% highlight ruby %}
gem 'mini_magick', '3.8.0'
{% endhighlight %}

מתחת לשורה:

{% highlight ruby %}
gem 'carrierwave'
{% endhighlight %}

בטרמיניל הריצי:

{% highlight sh %}
bundle
{% endhighlight %}

## אמרי לאפליקציה שלך ליצור thumbnails כשמעלים תמונה

פתחי את `app/uploaders/picture_uploader.rb` ומצאי את השורה שנראית ככה:

{% highlight ruby %}
  # include CarrierWave::MiniMagick
{% endhighlight %}

הסירי את סימן ה `#`.

__מדריך__: הסבר את הקונספט של הערות בקוד.

מתחת לשורה שהרגע שינית, הוסיפי:

{% highlight ruby %}
version :thumb do
  process :resize_to_fill => [50, 50]
end
{% endhighlight %}

התמונות שנעלה מעכשיו אמורות לעבור resize, אבל אלה שכבר העלנו לא השתנו.
אז ערכי את אחד הרעיונות הקיימים והוסיפי מחדש את התמונה שלו.

## הצגת ה-thumbnails

כדי לראות אם התמונה שהועלתה אכן עברה resize פתחי את `app/views/ideas/index.html.erb`. שני את השורה:

{% highlight erb %}
<td><%= idea.picture %></td>
{% endhighlight %}

ל-

{% highlight erb %}
<td><%= image_tag idea.picture_url(:thumb) if idea.picture? %></td>
{% endhighlight %}

הסתכלי ברשימת הרעיונות בדפדפן כדי לראות אם ה-thumbnail באמת נמצא שם.
