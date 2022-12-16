# 杀进程

在系统运行过程中，我们经常会遇到主动杀进程而不是等进程自动运行完结束的场景。例如，程序超时未响应ANR处理。

也许在代码层面上我们仅需一个简单的`kill`命令就能杀掉对应pid的进程。而在系统中需要考虑对象调用，最后才到kill进程。

从`KillApplicationByUid`到`KillProcessByPid`，或从`KillApplication` 到`KillProcessByPid`，最后到代码`kill(pid, SIGNAL_KILL)`完成进程的杀死。调用流程可以用序列图表示：

1. AbilityManagerService::UninstallApp->
   AppScheduler::KillApplicationByUid->
   AppMgrClient::KillApplicationByUid->
   AmsMgrScheduler::KillApplicationByUid->
   AppMgrServiceInner::KillProcessesByUid->
   AppMgrServiceInner::KillProcessByPid->
   kill(pid, SIGNAL_KILL)

2. Ability::TerminateAbility->
   AbilityContext::TerminateAbility->
   AbilityManagerClient::TerminateAbility->
   AbilityManagerService::TerminateAbility->
   AbilityManagerService::TerminateAbilityWithFlag->
   MissionListManager::TerminateAbility->
   MissionListManager::TerminateAbilityLock->
   AbilityRecord::Terminate(task)

3. AmsMgrScheduler::TerminateAbility->
   AppMgrServiceInner::TerminateAbility->
   AppRunningManager::TerminateAbility->
   AppMgrServiceInner::KillProcessByPid->
   kill(pid, SIGNAL_KILL)
