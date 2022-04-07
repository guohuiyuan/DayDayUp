1. 最近的用的标签

```
<p>格式化数字 (3): <fmt:formatNumber type="number" 
            maxFractionDigits="3" value="${balance}" /></p>
```

ps：

后端不应该处理该死的单位，什么数字应该直接返回前端，才能保证接口响应的数字，不需要经过转化，就能再次使用