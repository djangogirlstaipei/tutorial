# Forms

```
# trips/forms.py

from django import forms


class CommentForm(forms.Form):
    username = forms.CharField(label='暱稱')
    content = forms.CharField(widget=forms.Textarea(),
                              label='留言內容')
```

post.html
```
<form action="" method="post">
{% csrf_token %}
{{ form.as_p }}
<input type="submit" value="送出">
</form>
```


```
# trips/views.py


def post_detail(request, id):
    post = Post.objects.get(id=id)
    form = CommentForm(request.POST or None)
    if form.is_valid():
        print(form.cleaned_data)
        username = form.cleaned_data['username']
        content = form.cleaned_data['content']
        Comment.objects.create(post=post,
                               username=username,
                               content=content)

    return render(request,
                  'post.html',
                  {'post': post, 'form': form})
```
