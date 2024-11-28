1. 类继承
    在原模型上进行修改，不新建模型
    `缺点`:会影响原模型
```python
_inherit = 'res.partner'
```

2.原型继承(用的少)
  在原模型的基础上新建一个模型，并扩展字段,它需要同时定义_name
  `缺点`：会造成多个字段的重复
```python
 _inherit = 'res.partner'
 _name = 'res partner copy'
  
```
3.委托继承(用的较多)
   `缺点`：不能继承方法
   但是是最灵活的
   ```python
   _inherits='res.partner'_
```

## api.model
The @api.model decorator is similar, but it’s used on methods for which only the model is important, not the contents of the recordset, which is not acted upon by the method. The concept is similar to Python’s @classmethod decorator.
 
``@api.model 装饰器是类似的，但它用于仅模型重要的方法，而不是记录集的内容，记录集的内容不受方法的影响。这个概念类似于 Python 的 @classmethod 装饰器。``

