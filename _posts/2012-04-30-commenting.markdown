---
layout: default
title: הוספת תגובות לאפליקציית Rails Girls
permalink: commenting
---
# תגובות לאפליקציית Rails Girls
*מאת Janika Liiv, [@janikaliiv](https://twitter.com/janikaliiv)*

אנחנו הולכות להוסיף את האפשרות להגיב לרעיונות באפליקציית ה-*railsgirls* שלך.

ניתן למצוא את הוראות ההתקנה ובניית אפליקציית הרעיונות [כאן](/app).

## *1.*הוסיפי תגובות של Scaffold

הוסיפי תגובות של Scaffold עם שם המגיב, התגובה עצמה והפניה לטבלת הרעיונות ( `idea_id`).
{% highlight sh %}
rails g scaffold comment user_name:string body:text idea_id:integer
{% endhighlight %}

זה יצור קובץ מיגרציה אשר מעדכן את מסד הנתונים על הטבלה החדשה של התגובות. הריצי את המיגרציה כך:
{% highlight sh %}
rake db:migrate
{% endhighlight %}

## *2.*הוסיפי קשרים בין המודלים

צריך לגרום לRails לדעת על הקשר בין האובייקטים השונים (רעיונות ותגובות).
לרעיון אחד יכולים להיות הרבה תגובות, ואנחנו צריכים לגרום למודל לדעת את זה.
פתחי את app/models/idea.rb ואחרי השורה: 
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

## *3.* הדפיסי למסך את טופס הוספת התגובה ואת רשימת התגובות הקיימות


 פתחי את `app/views/ideas/show.html.erb` ואחרי image_tag
{% highlight erb %}
<%= image_tag(@idea.picture_url, :width => 600) if @idea.picture.present? %>
{% endhighlight %}

הוסיפי
{% highlight erb %}
<h3>Comments</h3>
<% @comments.each do |comment| %>
  <div>
    <strong><%= comment.user_name %></strong>
    <br>
    <p><%= comment.body %></p>
    <p><%= link_to 'Delete', comment_path(comment), method: :delete, data: { confirm: 'Are you sure?' } %></p>
  </div>
<% end %>
<%= render 'comments/form' %>
{% endhighlight %}

ב`app/controllers/ideas_controller.rb` הוסיפי לאחר השורה 
{% highlight ruby %}
@idea = Idea.find(params[:id])
{% endhighlight %}

את
{% highlight ruby %}
@comments = @idea.comments.all
@comment = @idea.comments.build
{% endhighlight %}

פתחי את `app/views/comments/_form.html.erb` ולאחר  
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

לבסוף, הורידי את 
{% highlight erb %}
<div class="field">
  <%= f.label :idea_id %><br>
  <%= f.number_field :idea_id %>
</div>
{% endhighlight %}

זהו! כעת צפי ברעיון שהוספת לאפליקצייה שלך ושם תוכלי לראות את טופס הוספת התגובה.
