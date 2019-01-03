不用重复写dict
```python
props = {'comment_id': 'comment_id'}
r_props = dict(props, target_id='target_id')
```


不用`\`使用括号
```python
if (self.type in (AM.NOTE_MENTION_NOTIFICAIONT) and 
    ver >= Version('6.4')):
    pass
```


github中跨项目的pr/issue自动链接
```markdown
subject/article-common#30
```


有需要计算的，可以使用异步任务在缓存数据内容过时的时候refresh缓存任务


list初始化dict
```python
res = dict((k, {}) for k in keys)
```


itertools.groupby如果是没有sorted过的可能k会重复
```python
In [35]: for k, v in groupby('abcddddaaaabbbbcccc'):
    ...:     print k, list(v)
    ...:
a ['a']
b ['b']
c ['c']
d ['d', 'd', 'd', 'd']
a ['a', 'a', 'a', 'a']
b ['b', 'b', 'b', 'b']
c ['c', 'c', 'c', 'c'http://doubandev3.intra.douban.com:8090/pages/diffpagesbyversion.action?pageId=65855&selectedPageVersions=3&selectedPageVersions=4
```


`0x`和`'\x'`和`'\'`的区别
```python
a, b, c = 0b11, 0o17, 0xff   # 数字 3, 15, 255
a = '\x21'                   # 十六进制21在ascii码中对应感叹号 (最大f) (better) (表示范围只有U+0000~U+00FF)
a = '\41'                    # 转义符 8进制 -> 100 001 -> -> 十六进制的 0010 0001 -> ascii码中的感叹号 (最大7)
# 所以表示不可打印字符的时候 '\x21' 和 '\41' 都可以
```


注意map的时候会遍历所有，但是all和any是遇到成功的就不继续。所以
```python
if not all(map(lambda s: isinstance(s, cls), streams))  # 不好
if not all(isinstance(s, cls) for s in streams)         # 比较好
```


filter缺少一种如下
```python
[convert(x) for x in xs if test(x)] # 这样需要filter和map
```


使用not来将非boolean类型的转换
```python
public_only = not bool(req.user and req.user == self.user) # 有了not可以不要bool
public_only = not (req.user and req.user == self.user)
```


命名的时候如果确定是一个动作最好带有动词，如`def total_count` -> `def get_total_count`


变量命名的时候改掉`_name`，尽量通过namespace和取名来区分，如果一定需要可以用`name_`


Mixin的作用

