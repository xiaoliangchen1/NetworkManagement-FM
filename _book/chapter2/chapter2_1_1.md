###2.2.1 告警查询条件

&#160; &#160; &#160; &#160;在查询当前告警时，要根据条件进行查找。在告警视图`FmPane`左侧部分，是告警查询条件的视图，分为两个方面的查询：根据告警源查询和根据告警视图（等级）查询。实质上，无论选择哪个方面的查询，都是将这些条件封装成告警查询条件`AlarmQueryCondition`中的属性。告警源对应其中的`sources`属性，告警视图对应其中的`alarmSeverities`属性。除此之外，由于查询的是当前告警，要设置·currentAlarm = true·和`alarmStates = Arrays.asList(AlarmState.Active,AlarmState.AckedAndNotCleared,AlarmState.ClearedAndNotAcked)`。

com/cyt/framework/bo/fm/AlarmQueryCondition.java

```java
public class AlarmQueryCondition extends QueryCondition {
    ...
    private String name;
    private Boolean currentAlarm = true;
    private List<String> sources;
    private List<ProbableCause> probableCauses;
    private List<AlarmSeverity> alarmSeverities;
    private List<AlarmStates> alarmStates = Arrays.asList(AlarmState.Active,
            AlarmState.AckedAndNotCleared,AlarmState.ClearedAndNotAcked);
    private String address;
    private String ackWho;
    private String clearWho;
    private long createStartTime;
    private long createEndTime;
    private long ackStartTime;
    private long ackEndTime;
    private long clearStartTime;
    private long clearEndTime;
    ...
}
```