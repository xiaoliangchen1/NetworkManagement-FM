###2.2.3 告警操作

&#160; &#160; &#160; &#160;当选中当前告警列表中的告警时，可以对选中告警进行操作：确认、清除、确认并清除告警。

&#160; &#160; &#160; &#160;确认告警。如果选中告警的状态为`AlarmState.Active`，点击确认告警后，最终执行的是`Fmservice`中的`ackAlarms`方法：把选中告警的确认时间设置为当前时间，设置告警状态为`AlarmState.AckedAndNotCleared`，设置确认者，并将告警缓存中和当前告警列表中的该条告警分别进行更新；如果选中告警的状态为`AlarmState.ClearedAndNotAcked`,点击确认告警后，执行的是`Fmservice`中`ackAndClearAlarms`确认并清除告警方法。

&#160; &#160; &#160; &#160;清除告警。如果选中告警的状态为`AlarmState.Active`，点击清除告警后，最终执行的是`Fmservice`中的`clearAlarms`方法：把选中告警的清除时间设置为当前时间，设置告警状态为`AlarmState.ClearedAndNotAcked`，设置清除者，并将告警缓存中和当前告警列表中的该条告警分别进行更新；如果选中告警的状态为`AlarmState.AckedAndNotCleared`,点击清除告警后，执行的是`Fmservice`中`ackAndClearAlarms`确认并清除告警方法。

&#160; &#160; &#160; &#160;确认并清除告警。当点击确认并清除或者以上所述两种情况触发确认并清除告警时，最终执行的是`Fmservice`中`ackAndClearAlarms`方法：设置确认时间，清除时间，确认者，清除者，设置告警状态为`AlarmState.AckedAndCleared`，将告警缓存中和当前告警列表中的该条告警分别进行更新。其中，对当前告警列表的的更新就是将这条告警从列表中清除（因为其已成为历史告警）；告警缓存中的更新就是把缓存中找到当前告警集合中该条告警，并从中移除，同时在缓存中历史告警集合中添加这条告警。