# # 200811

* Django_Pagination 

  > 글을 계속 추가할수록 글은 아래로 쌓이게 됩니다.
  >
  > 하지만 대부분의 웹사이트는 일정 수준 이상 쌓이면 다음 페이지로 넘어갈 수 있도록 구현 되어 있습니다.
  >
  > 이를 pagination 이라고 합니다.



```django
# Blog 클래스의 모든 객체들을 blog_list에 담아두게 됩니다.
blog_list = Blog.objects.all()

# 2개의 인자를 받는데 첫 번째로 페이지로 분할될 객체, 두 번째로 한 페이지에 담길 객체의 수를 받는다.
paginator = Paginator(blog_list, 3)
```



* 참고 [URL](https://github.com/nickhealthy/TIL/blob/master/2020_08_11/pagination.html)

```django
{% if page.has_previous %} Previous {% endif %}
Page {{page.number}} of {{page.paginator.num_pages}}
{% if page.has_next %} Next {% endif %}
```

