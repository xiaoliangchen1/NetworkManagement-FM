##2.1 告警产生
&#160; &#160; &#160; &#160;在本系统中，告警是通过Snmp Trap来产生的。

server/src/java/com/cyt/sbi/trap/TrapReceiver.java
```java
@Component
public class TrapReceiver implements TrapListener {
    
    private SnmpTrapReceiver receiver = null;
        
    @Autowired
    private AlarmReceiver alarmReceiver;
        
    @PostConstruct
    public void init(){
        TaskManager.submit(new Runnable(){
            @Override
            public void run(){
                receiver = new SnmpTrapReceiver();
                receiver.setSnmpVersion(SnmpTarget.VERSION1);
                receiver.setCommunity("public");
                receiver.seTrapAuthEnable(false);
                    
                try{
                    receiver.setPortWithExceptionMsg(162);
                    receiver.addTrapListener(TrapReceiver.this);
                    LogMan.log("服务器启动完毕！！------------------");
                }catch(SnmpException e){
                    LogMan.log("162端口被占用！！！");
                    int option = JOptionPane.showConfirmDialog(null,
                        "系统检测到162端口已被占用，导致网管系统无法收取Trap信息并\n
                        实时反应设备状态，是否继续？","提示",
                        JOptionPane.ERRPR_MESSAGE);
                    if(option != JOptionPane.YES_OPTION){
                        System.exit(0);
                    }
                }
            }
        });
    }
        
    @SuppressWarnings("unchecked")
    @Override
    public void receivedTrap(TrapEvent trap){
        SnmpPDU pdu = trap.getTrapPDU();
            
        ...
            
        Alarm alarm = makeAlarm(ne,binds);
            
        alarmReceiver.onMessage(alarm);
    }
       
    /**
    *    解析生成当前告警
    */
    private Alarm makeAlarm(NetworkElement ne, SnmpVarBind[] Binds){
        ...
    }
        
   /**
   *    处理EOC告警
   */
   
    private Alarm makeAlarm(NetworkElement ne, SnmpVarBind[] Binds){
        ...
    }
        
    ...
}
```

    
&#160; &#160; &#160; &#160;`init()`方法设置了`SnmpTrapReceiver`的一些参数，使其进行监听162端口的trap信息。当有TRAP事件发生时，`receivedTrap(TrapEvent trap)`方法会把trap上来的数据进行解析，生成相应的告警信息Alarm，然后执行`alarmReceiver.onMessage(alarm)`方法对alarm进行处理。

server/src/java/com/cyt/framework/fm/listener/AlarmReceiver.java
    
```java
@Component
public class AlarmReceiver{
    
    @AutoWired 
    private AlarmDBHandler alarmDBHandler;
    ...
        
    public void onMessage(Alarm alarm){
        ...
        trapExecutor.submit(new TrapTask(alarm));
    }
        
    class TrapTask extends BasicTask{
        ...
        @Override
        protected void execute(){
            ...
            alarmDBHandler.process(alarm);
        }
    }
}
```

&#160; &#160; &#160; &#160;`AlarmReceiver`类用于接收SBI端发送到Server的AlarmEventMessage。`onMessage(alarm)`方法通过alarm创建一个TrapTask,并将之放入`trapExecutor`线程池中。TrapTask中的`alarmDBHandler.process(alarm)`对新生的告警进行保存，新生的告警是保存在AlarmCache中的；如果TRAP上来的告警为恢复告警，process方法根据恢复告警查找告警缓存中的当前告警，并将之变为历史告警；如果TRAP上来的告警在缓存中存在，则对之前的告警进行更新操作。