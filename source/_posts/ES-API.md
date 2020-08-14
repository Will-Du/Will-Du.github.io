---
title: ES API
date: 2020-08-10 22:19:26
tags: ElasticSearch
---
<b>1.maven引入ElasticSearch</b>
```XML
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.8.0</version>
</dependency>
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-client</artifactId>
    <version>7.8.0</version>
</dependency>
<dependency>
    <groupId>org.elasticsearch</groupId>
    <artifactId>elasticsearch</artifactId>
    <version>7.8.0</version>
</dependency>
```
<!-- more -->
<b>2.设置ES config</b>
```java
@Configuration
public class ElasticSearchConfig {
    @Bean
    public RestHighLevelClient restHighLevelClient() {
        HttpHost[] httpHosts = null;
        List<String> hostStrList = getHosts();// getHosts???????????
        if (hostStrList.size > 0) {
            httpHosts = new HttpHost[hostStrList.size()];
            for (int i = 0; i < hostStrList.size(); i++) {
                String[] s = hostStrList.get(i).split(":");
                String ip = s[0].trim();
                int port = Integer.valueOf(s[1].trim());
                httpHosts[i] = new HttpHost(ip, port, "http");								
            } 						
        }
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(httpHosts));
        return client;				
    } 		
}
```
<b>3.查询</b>
```java
// 设置查询的索引库
SearchRequest searchRequest = new SearchRequest("search_index");

// 设置查询器
SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
BoolQueryBuilder boolBuilder = QueryBuilders.boolQuery();

// 拼接查询条件, filter近似于must，但效率比must高，拼接条件=and
// should近似于or操作
// mustnot为!=
// termQuery为精确查找
boolBuilder.filter(QueryBuilders.termQuery("id",queryVo.getId()));
// matchQuery为模糊查询，会匹配带有相关的结果
boolBuilder.filter(QueryBuilders.matchQuery("name", queryVo.getName()));
// termsQuery近似于in，后面放数组即可
boolBuilder.filter(QueryBuilders.termsQuery("status",queryVo.getStatus().split(",") ));
// 分页，从第10笔资料开始，取20笔
searchSourceBuilder.from(10).size(20);
// 排序
searchSourceBuilder.sort("create_time", SortOrder.DESC);

searchRequest.source(searchSourceBuilder);
// 查询返回的结果集
SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);

SearchHits hits = searchResponse.getHits();
// 查询的具体结果
SearchHit[] searchHits = hits.getHits();
int total = searchHits.length;
```