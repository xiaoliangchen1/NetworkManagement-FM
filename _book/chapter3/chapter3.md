#3 发现问题
&#160; &#160; &#160; &#160;在直接或间接进行确认并清除操作后，当前告警列表中所选中的告警消失，但是历史告警列表中并未出现相应的该条历史告警，查询数据库中的告警表，发现其中有当前告警的记录（只有历史告警才被保存到数据库中）。后来发现问题所在：

```java
AlarmCache.java
public void toHistory(Alarm alarm){
    if(alarm == null)
        reyurn;
    Alarm removed = this.data.remove(alarm.getId());
    if(removed != null)
        historyAlarms.add(removed);
}
```

这个方法首先是将确认并清除后的告警（也就是告警状态为`AlarmState.AckedAndClrred`）添加到告警缓存中的`historyAlarms`（历史告警集合）中。首先根据更改状态后的告警查询缓存中与之对应的当前告警`removed`，将之从当前告警集合`data`中移除，再把更改状态后的告警`alarm`添加到历史告警集合`historyAlarms`中，上面代码中，`add(removed)`应改为`add(alarm)`。