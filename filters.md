# Filters

rest-framwork 提供了 filters 来根据 url参数 来对数据进行一些排序或过滤的操作。

## 1.  settings 里指定应用到全局的 filter

    REST_FRAMEWORK = {
        'DEFAULT_FILTER_BACKENDS': ('rest_framework.filters.DjangoFilterBackend',)
    }

## 2. 自定义筛选类

创建 filters.py 文件

    import django_filters
    from . import models

    class SnippetFilter(django_filters.FilterSet):
        code = django_filters.CharFilter(lookup_expr='contains')
        id = django_filters.CharFilter(method='filter_id')
        
        class Meta:
            model = models.Snippet
            fields = ('code', 'id')

        def filter_id(self, queryset, name, value):
            return queryset.filter(id=value)
