##2.2 当前告警查询 

&#160; &#160; &#160; &#160;查询当前告警的入口为`FmQueryAction`中的`actionPerformed(ActionEvent e)`方法，点击后，出现告警视图`FmPane`。
```java
public class FmPane extends JPanel{
    ...
    public AlarmViewTree tree = new AlarmViewTree();
    private DomainFmTree domainTree = new DomainFmTree();
    private AlarmView alarmView = new AlarmView();
    
}
```
&#160; &#160; &#160; &#160;其中，`tree`是告警等级视图，`domainTree`是告警源视图，`alarmView`是查询出的告警显示视图。


