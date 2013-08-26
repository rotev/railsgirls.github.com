---
layout: default
title: הוספת תגובות לאפליקציית Rails Girls
permalink: commenting
---
# תגובות לאפליקציית Rails Girls
*מאת Janika Liiv, [@janikaliiv](https://twitter.com/janikaliiv)*

אנחנו הולכות להוסיף את האפשרות להגיב לרעיונות באפליקציית ה-*railsgirls* שלך.

ניתן למצוא את הוראות ההתקנה ובניית אפליקציית הרעיונות [כאן](/app).

## שלב 1: הוסיפי את ה-foreigner gem

הוסיפי לקובץ ה-Gemfile
{% highlight ruby %}
gem 'foreigner'
{% endhighlight %}

בטרמינל שלך הפסיקי את השרת אם הוא רץ כרגע, והקלידי
{% highlight sh %}
bundle install
{% endhighlight %}

## שלב 2: צרי את שלד התגובה

צרי את שלד התגובה עם שם המגיב, גוף התגובה (התוכן) ועם הפניה (reference) לטבלת הרעיונות (idea_id).
{% highlight sh %}
rails g scaffold comment user_name:string body:text idea_id:integer
{% endhighlight %}

## שלב 3: הוסיפי foreign key
הוסיפי למיגרציה את ה-foreign key. מתיקיית db/migrate פתחי את הקובץ שמסתיים ב-'create_comments.rb'. לאחר
{% highlight ruby %}
	t.timestamps
end
{% endhighlight %}

הוסיפי
{% highlight ruby %}
add_foreign_key :comments, :ideas
{% endhighlight %}

כעת, הריצי את המיגרציה על מנת לעדכן את השינויים ב-database באמצעות הפקודה
{% highlight sh %}
rake db:migrate
{% endhighlight %}

הפעילי את השרת באמצעות:
{% highlight sh %}
rails s
{% endhighlight %}

## שלב 4: הוסיפי relations (קשרים) למודל התגובה

את צריכה לוודא ש-Rails מבין את הקשר בין האובייקטים רעיון ותגובה.
מאחר ורעיון בודד יכול להכיל תגובות רבות, אנחנו צריכים לוודא שמודל הרעיון יודע את זה.
פתחי את app/models/idea.rb ולאחר השורה
{% highlight ruby %}
class Idea < ActiveRecord::Base
{% endhighlight %}
הוסיפי
{% highlight ruby %}
has_many :comments
{% endhighlight %}

גם מודל התגובה צריך לדעת שהוא משוייך לרעיון. פתחי את app/models/comment.rb ולאחר
{% highlight ruby %}
class Comment < ActiveRecord::Base
{% endhighlight %}

הוסיפי את השורה
{% highlight ruby %}
belongs_to :idea
{% endhighlight %}

## שלב 5: הדפיסי למסך את טופס הוספת התגובה ואת רשימת התגובות הקיימות

פתחי את app/views/ideas/show.html ואחרי ה-image_tag
{% highlight erb %}
<%= image_tag(@idea.picture_url, :width => 600) if @idea.picture.present? %>
{% endhighlight %}

הוסיפי
{% highlight erb %}
<h3>Comments</h3>
<% @idea.comments.each do |comment| %>
  <div>
    <strong><%= comment.user_name %></strong>
    <br />
    <p><%= comment.body %></p>
  </div>
<% end %>
<%= render 'comments/form' %>
{% endhighlight %}

בקובץ app/controllers/ideas_controller.rb הוסיפי בפעולת ה-show אחרי השורה
{% highlight ruby %}
@idea = Idea.find(params[:id])
{% endhighlight %}

את
{% highlight ruby %}
@comment = @idea.comments.build
{% endhighlight %}

פתחי את הקובץ app/views/comments/_form.html ולאחר
{% highlight erb %}
  <div class="field">
    <%= f.label :body %><br />
    <%= f.text_area :body %>
  </div>
{% endhighlight %}

הוסיפי את השורה
{% highlight erb %}
<%= f.hidden_field :idea_id %>
{% endhighlight %}

זהו! כעת צפי ברעיון שהוספת לאפליקצייה שלך ושם תוכלי לראות את טופס הוספת התגובה.
