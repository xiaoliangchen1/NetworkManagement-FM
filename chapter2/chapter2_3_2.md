###2.3.2 历史告警查询

&#160; &#160; &#160; &#160;在告警查询视图中选好告警查询条件后，点击确定，进行历史告警查询操作。此操作的流程与当前告警查询类似，不同的是，当前告警查询是从`AlarmCache`告警缓存中的`data`集合中得到当前告警的，而历史告警查询是先将`AlarmCache`告警缓存中的`historyAlarms`中的数据保存或更新到数据库中，把`historyAlarms`中数据清空，再从数据库中取出符合查询条件的历史告警。
```java
AlarmCache.java
public Collection<Alarm> getAndClearHistoryAlarms(){
    List<Alarm> list = new ArrayList<Alarm>(historyAlarms);
    historyAlarms.clear();
    return list;
}

FmService.java
public QueryResult<Alarm> getAlarms(AlarmQueryCondition condition){
    if(condition.getCurrentAlarm()){
        return cache.queryAlarms(condition);
    }
    
    fmDao.saveOrUpdateAllEntites(cache.getAndClearHistoryAlarms());
    return fmDao.getAlarms(condition);
}
```
&#160; &#160; &#160; &#160;最后，根据历史告警的查询结果，将数据添加到历史告警列表中，并且将这些数据进行图形化的统计（根据告警可能原因和告警等级，生成饼状图）。
```java
HistoryAlarmView.java
public void getHistoryAlarms(AlarmQueryCondition condition){
    ...
    QueryResult<Alarm> result = ClientContext.getFmFacade().getAlarms(condition);
    setInitQueryPara(condition,result);
    initData(result,null);
    getAlarmStatview().initData(reult,null);
    
}
```