# ViewSet与Router

## 1. 使用ViewSet

    from django.http import HttpResponse, JsonResponse

    from rest_framework.renderers import JSONRenderer
    from rest_framework.parsers import JSONParser
    from rest_framework.permissions import IsAuthenticated, AllowAny

    from rest_framework import viewsets, status
    from rest_framework import serializers
    from rest_framework.decorators import detail_route

    from .models import Snippet
    from .serializers import SnippetSerializer
    from . import filters

    class SnippetViewSet(viewsets.ModelViewSet):
        """
        This viewset automatically provides `list`, `create`, `retrieve`,
        `update` and `destroy` actions.
        """
        queryset = Snippet.objects.all()    # 设置查询集
        serializer_class = SnippetSerializer    # 设置序列
        filter_class = filters.SnippetFilter    # 设置筛选类
        permission_classes = (AllowAny,)    # 设置访问权限

        #  获取所有snippets
        def list(self, request, *args, **kwargs):

            queryset = self.filter_queryset(self.get_queryset())
            serializer = SnippetSerializer(queryset, many=True)
            return JsonResponse(serializer.data, safe=False)

        def create(self, request, *args, **kwargs):
            # drf的request对django的request进行了封装，不需要使用request.POST
            # 1. JSON -> dict
            # 2. 序列化(form功能)
            data = JSONParser().parse(request)
            serializer = SnippetSerializer(data=data)

            # 1. 如果验证成功，就存储数据，并返回数据+201(创建成功)
            # 2. 验证失败，返回错误信息+400(语义有误)
            serializer.is_valid(raise_exception=True)
            serializer.save()
            return JsonResponse(serializer.data, status=201)

        def update(self, request, *args, **kwargs):
            instance = Snippet.objects.get(id=kwargs['pk'])
            data = JSONParser().parse(request)
            serializer = SnippetSerializer(data=data)
            serializer.is_valid(raise_exception=True)
            serializer.save(instance, request.user)

            return JsonResponse({'message': '修改成功!'})

        def partial_update(self, request, *args, **kwargs):
            return JsonResponse({'message': '非法操作!'}, status=status.HTTP_400_BAD_REQUEST)

1. ModelViewSet提供了所有的ACTION（5种）

## 2. 使用Router

创建 routers.py 文件

    from rest_framework import routers
    from . import views

    router = routers.DefaultRouter()
    router.register(r'snippet', views.SnippetViewSet, base_name='snippet')

注册views中的类

## 3. 更强的url

    from django.conf.urls import url,include
    from django.contrib import admin


    from helloworld.apps.first_app.routers import router as user_router

    urlpatterns = [
        url(r'^admin/', admin.site.urls),
        url(r'^api/', include(user_router.urls)),   # 指向routers文件
    ]

> /api/snippet/ >>>>>> SnippetViewSet()

## 4. 自定义方法

est_framework 中对于自定义的 viewset 方法提供了两种装饰器：

 * list_route

 * detail_route

区别就是 list_route 的参数不包含 pk（对应 list），而 detail_route 包含pk（对应 retrieve）

    @list_route(methods=['post', 'delete'])
    def custom_handler(self, request):
        pass


    @detail_route(methods=['get'])
    def custom_handler(self, request, pk=None):
        pass
