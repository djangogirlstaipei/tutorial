# Advanced Models

foreign key 的概念

```
# trips/models.py


class Comment(models.Model):
    post = models.ForeignKey(Post)
    username = models.CharField(max_length=50)
    content = models.TextField()

    def __str__(self):
        return str(self.id)
```

## 同步資料庫

```
(VENV) ~/djangogirls/mysite$ python manage.py migrate

```

```
<hr>
<h2>留言</h2>
{% if comment %}
    {% for comment in post.comment_set.all %}
        <div>{{ comment.username }}</div>
        <div>{{ comment.content }}</div>
        <hr>
    {% endfor %}
{% else %}
    來搶頭香吧
    <hr>
{% endif %}
```
