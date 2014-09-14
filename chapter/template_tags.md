# Template tags


```
# trips\views.py

from django.shortcuts import render
from trips.models import Post


def home(request):
    # get all the posts
    post_list = Post.objects.all()

    return render(request,
                  'home.html',
                  {'post_list': post_list})
```
`home.html`

```
HTML 用 posts
```

`{{ posts }}`

posts screenshot

```
HTML 用 for
```

## Template tags
### for loop
```
{% for post in posts %}
    {{ post }}
{% endif %}
```
### if...else

```
{% if %}
{% else %}
{% endif %}
```

想範例!!!


## Template filter
###date
