# Advanced Templates


base.html, index.html, post.html

{% extends %}

{% block %}{% endblock %}

```
# trips/views.py

from django.shortcuts import render
from trips.models import Post


def post_detail(request, id):
    # get post by id
    post = Post.objects.get(id=id)

    return render(request, 'post.html', {'post': post})
```

```
# mysite/urls.py

urlpatterns = patterns('',
    ...
    url(r'^post/(?P<id>\d+)$', 'trips.views.post_detail'),
)

```

post screenshot