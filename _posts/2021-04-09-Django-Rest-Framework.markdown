---
layout: post
title:  "Django Rest Framework"
date:   2020-04-09 21:11:11
categories: Django Python DRF
---

# Pagination
This way, we passed `page` or `page_size` as parameter, then the `StandardResultsSetPagination` will work.
`/api/v1/something/?page=5` or

`/api/v1/something/?page_size=20`

```python
class StandardResultsSetPagination(PageNumberPagination):
    page_size_query_param = 'page_size'
    max_page_size = 5

class YourView(generics.ListAPIView):
    pagination_class = StandardResultsSetPagination
```

So at most, you can get 5 records.

But if we have no `page` or `page_size` as parameter, like `/api/v1/something/`, the the `StandardResultsSetPagination` will not work.
So you could get all the records with no Pagination.

