###2.2.2 告警查询

&#160; &#160; &#160; &#160;当在告警查询视图中选择告警源或者告警等级时，就会触发告警查询操作：

```java
private void getAlarms(final AlarmQueryCondition condition) {
    ...
    QueryResult<Alarm> alarms = ClientContext.getFmFacade().getAlarms(condition);
    alarmview.initData(alarms,null);
}
```

&#160; &#160; &#160; &#160;`getAlarms(final AlarmQueryCondition condition)`方法通过RMI调用Server端FmFacadeImpl中的`getAlarms(AlarmQueryCondition condition)`，此方法中调用FmService中的`getAlarms(AlarmQueryCondition condition)`，进而调用AlarmCache中的`QueryAlarms(AlarmQueryCondition condition)`方法：

AlarmCache.java
```java
public QueryResult<Alarm> queryAlarms(AlarmQueryCondition condition){
    List<Alarm> list = new ArrayList<Alarm>();
    ...
    List<String> sources = condition.getSources();
    
    Set<String> set = new HashSet<String>();
    if(sources != null)
        set.addAll(sources);
    
    out: for(Alarm alarm : data.values()){
        ...
        if(ArrayUtils.isNotEmpty(set)){
            if(set.contains(alarm.getSource().toString())){
                list.add(alarm);
                continue out;
            }        
        }else{
            list.add(alarm);
        }
    }
    
    QueryResult<Alarm> result = new QuerResult<Alarm>();
    result.setBoList(list);
    return result;
    
}
```
&#160; &#160; &#160; &#160;`data.values()`是缓存中的当前告警，遍历缓存中的告警，如果告警查询条件中的告警来源包含缓存中告警的告警来源，则将缓存中的该条告警添加到`QueryResult`中，最后将范围的结果添加到当前告警列表的table中。