---
title: elastic踩坑记录
date: 2019-12-06 13:00:08
tags: -elastic

---

### 启动es默认使用1G内存

后台压力大才发现的 只用了1G内存!

启动参数加 `--Xms4g —Xmx4g`

### logstash-jdbc 增量同步mysql

因为原记录会被不断更新,一开始选择使用updateTime字段作为更新依据

在小批量数据下的测试和使用都没有发现问题

后台有次遇到大量数据需要被更新的情况,发现有几批数据迟迟没有被更新,之后的数据都被更新过了

<!-- more -->

经过思考发现:

sql_last_value使用时间戳时,多条数据时间戳一致,会导致更新时的数据遗漏

比如当前时间戳的数据有101条 此次更新limit 100 进入下次更新时sql_last_value也被更新 就会有1条数据遗漏

解决办法是使用自己实现的id生成器代替时间戳 保证生成数据在各个地方使用不重复,顺序递增并且生成效率高

这里使用的雪花算法

### elastic聚合查询结果数量限制

使用聚合查询时发现的,聚合结果最多就只有10条数据

```
/**
 * Sets the size - indicating how many term buckets should be returned (defaults to 10)
 */
public TermsBuilder size(int size) {
    bucketCountThresholds.setRequiredSize(size);
    return this;
}
```

源码中显示聚合查询默认返回十条,需要更多的话要自己指定size

试了`size:0` 和 `size:1` 试图返回全部 结果报错 只能根据自己的大致范围来设置了

```
"aggs": {
	"province": {
		"terms": {
			"field": "province",
			"size": 50
		},
		"aggs": {
			"city": {
				"terms": {
					"field": "city",
					"size": 50
				}
			}
		}
	}
}
```