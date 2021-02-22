# RESTFramework

[RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)

[DjangoRESTFramework官网](https://www.django-rest-framework.org/)

## 序列化







```python
# 新增外键ForeignKey关系字段之后 迁移报错

# 原因 外键关系对应的表中 没有记录 迁移选择默认值的时候 没有可以设置的关联字段的id 报错

django.db.utils.IntegrityError: (1452, 'Cannot add or update a child row: a foreign ke
y constraint fails (`snippets`.`#sql-6309_84b`, CONSTRAINT `snippets_snippet_owner_id_
20604299_fk_auth_user_id` FOREIGN KEY (`owner_id`) REFERENCES `auth_user` (`id`))')
```

