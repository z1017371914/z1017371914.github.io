## Order By



```sql
CREATE TABLE `t` (
  `id` int(11) NOT NULL,
  `city` varchar(16) NOT NULL,
  `name` varchar(16) NOT NULL,
  `age` int(11) NOT NULL,
  `addr` varchar(128) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `city` (`city`)
) ENGINE=InnoDB;
```

```sql
select city,name,age from t where city='杭州' order by name limit 1000  
```

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gtfg1x7oabj60mb0f2ack02.jpg)

1.`sort_buffer`：一块内存，用于在其中进行排序

2.如果需要查询上面的sql，InnoDB是怎么实现的呢?

### 全字段排序

1.上述的查询，一般是会采用以下方法获取结果，也就是所谓的`全字段排序`

- 初始化sort_buffer，放入name，city，age三个字段(全字段排序是放入结果需要的所有字段)
- 从索引city中找到对应条件的主键id，也就是ID_X
- 通过主键ID索引取出改行的记录，取name，city，age放入到sort_buffer中
- 重复第二步和第三步的操作
- 对sort_buffer中的数据按name进行快排
- 取前1000返回

![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gtfg84fuzoj60mm0h1n1r02.jpg)

2.`问题`：由于全字段排序需要加载所有结果集的字段到sort_buffer中，如果字段的长度过长，那么就只能一次加载较少的行先排序，最后再进行归并排序。也就是外排序。

因此有了rowid排序

### rowid排序

rowid排序不会将结果集的所有字段都加载到sort_buffer中，而是只加载需要排序的字段和主键。

- 初始化sort_buffer
- 通过city索引定位到对应的主键ID
- 通过主键回表取出对应的行，选择主键和name，放入到sort_buffer中
- 重复第二第三步操作
- 对sort_buffer进行排序
- 取排序后的前1000的主键ID，回表取出name，city，age放入到结果集合，然后返回

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gtfgci18k2j60mc0gjaf502.jpg)

---

会优先使用全字段排序，因为rowId排序需要回表访问磁盘的次数实在是过多。

