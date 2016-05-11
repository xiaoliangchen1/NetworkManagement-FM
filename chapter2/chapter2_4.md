&#160; &#160; &#160; &#160;告警报表统计实质上就是根据特定条件查询当前告警，与查询当前告警不同的是，告警查询报表统计将查询条件封装成`AlarmStatisQueryCondition`对象，将查询结果封装成`Alarmstatis`对象。
```java
AlarmStatisQueryCondition.java
public class AlarmStatisQueryCondition extends QueryCondition{
    ...
    private int fmReportTempType;
    private long fromTime;
    Private long toTime;
    private List<String> alarmSources;
    private List<TopoDomain> domains;
    ...
}

AlarmStatis.java
public class AlarmStatis implements Serializable,Comparator<AlarmStatis>{
    ...
    private String domain;
    private AlarmSeverity severity;
    private int ackandClear;
    private ackAndUnClear;
    private unConfirmAndClear;
    private unConfirmAndUnClear;
    private int count;
    ...
    
 
}
```
&#160; &#160; &#160; &#160;在告警报表统计面板中，根据需求选择告警报表统计查询条件，点击查询，进行查询操作：
```java
FmReportView.java
public void onQuery(){
    getData();
    ...
    List<AlarmStatis> staResult = ClientContext.getFmFacade()
        .getAlarmCountStatic(condition);
    ...
    table.clearAllRowDatas();
    table.addRowDatas(staResult);
    getChartPane(staResult);
}
```

&#160; &#160; &#160; &#160;根据查询的结果，将数据添加到告警报表统计列表`table`中，并生成相应的柱状图。

&#160; &#160; &#160; &#160;查询操作最终执行的是`FmService`中的`getAlarmCountStatic`方法：
```java
FmService.java
Public List<AlarmStatis> getAlarmCountStatic(AlarmStatisQueryCondition condition){
    if(FmReportType.Area.getValue() == condition.getFmReportTempType())
        return getAlarmStatisByDoamin(condition);
    else
        return getAlarmStatisBySeverity(condition);
}
```
&#160; &#160; &#160; &#160;根据查询条件中告警报表类型（按设备区域报表统计和按告警等级报表统计）的不同，分别执行不同的查询方法。