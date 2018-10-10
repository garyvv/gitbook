# 正则

- 正则判定unicode中文字符段
```
preg_match('/\p{Han}/u', $string)
```