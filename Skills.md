# Skills

## 1. 在Python脚本中调用Django环境

    import os
    
    if __name__ == "__main__":
        os.environ.setdefault("DJANGO_SETTINGS_MODULE", "django_server.settings")
        # django_server.settings 对应settings.py文件的路径
        import django
        django.setup()

        from app import models
        # ORM操作...

## 2. Django终端打印SQL语句

在Django项目的settings.py文件中，在最后复制粘贴如下代码：

    LOGGING = {
        'version': 1,
        'disable_existing_loggers': False,
        'handlers': {
            'console':{
                'level':'DEBUG',
                'class':'logging.StreamHandler',
            },
        },
        'loggers': {
            'django.db.backends': {
                'handlers': ['console'],
                'propagate': True,
                'level':'DEBUG',
            },
        }
    }
