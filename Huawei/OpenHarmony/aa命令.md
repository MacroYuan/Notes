# AMS——aa工具

aa是AMS提供的命令行工具，aa最终几乎都是再AbilityManagerService中调用相应的方法实现（除了开发员编写的用于内部测试的命令）

参数命令有

| 命令           | 解释                            | 对应源码                                                                                                   |
| ------------ | ----------------------------- | ------------------------------------------------------------------------------------------------------ |
| help         | 帮助提示信息                        | AbilityManagerShellCommand::RunAsHelpCommand()                                                         |
| screen       | 控制屏幕亮暗                        | AbilityManagerShellCommand::RunAsScreenCommand()<br/>实际无调用                                             |
| start        | 手动启动ability                   | AbilityManagerShellCommand::RunAsStartAbility()<br/>实际调用AbilityManagerService::StartAbility(want)      |
| stop-service | 手动停止ability                   | AbilityManagerShellCommand::RunAsStopService()<br/>实际调用AbilityManagerService::StopServiceAbility(want) |
| dump         | 模拟安卓的dumpsys，执行WHAT操作，见下面详细描述 |                                                                                                        |
| force-stop   | 手动停止服务                        | AbilityManagerShellCommand::RunAsStopService()<br/>实际调用AbilityManagerService::KillProcess()            |
| test         | 运行测试件                         | AbilityManagerShellCommand::RunAsTestCommand()<br/>实际调用AbilityManagerService::StartUserTest()          |

用于测试的参数命令(模拟正常运行中不常见的情况，测试)

通过在ability_runtime/tools/aa下定义单元测试函数，可自定义逻辑，再配置命令行参数的形式能够通过命令行调用单元测试。

| 命令                       | 解释        | 对应源码                                                             |
| ------------------------ | --------- | ---------------------------------------------------------------- |
| force-timeout            | 强制超时      | AbilityManagerShellCommand::RunForceTimeoutForTest()             |
| ApplicationNotResponding | 程序未响应     | AbilityManagerShellCommand::RunAsSendAppNotRespondingProcessID() |
| block-ability            | 阻塞ability | AbilityManagerShellCommand::RunAsBlockAbilityCommand()           |
| block-ams-service        | 阻塞ams服务   | AbilityManagerShellCommand::RunAsBlockAmsServiceCommand()        |
| block-app-service        | 阻塞app服务   | AbilityManagerShellCommand::RunAsBlockAppServiceCommand()        |
| ...                      |           |                                                                  |

## dump命令

dump用于查询ability信息，dump命令的查询主要是从AMS所持有的对象currentMissionListManager_中的dump方法，而data是从dataAbilityManager_中查询，状态state从connectManager_中查询

| 参数                  | 解释            | 对应源码                                         |
| ------------------- | ------------- | -------------------------------------------- |
| --all, -a           | 查询任务列表管理器信息   | AbilityManagerService::DumpInner             |
| --stack-list, -l    | 查询栈列表         | 未实现                                          |
| --stack, -s         | 查询栈           | 未实现                                          |
| --mission, -m       | 查询任务          | AbilityManagerService::DumpMissionInner      |
| --top, -t           | 查询top ability | 未实现                                          |
| --waiting-queue, -w | 查询等待队列        | 未实现                                          |
| --serv, -e          | 查询服务          | AbilityManagerService::DumpStateInner        |
| -- data, -d         | 查询数据          | AbilityManagerService::DataDumpStateInner    |
| -focus, -f          | 查询聚焦ability   | 未实现                                          |
| --win-mode, -z      | 查询窗口模式        | 未实现                                          |
| --mission-list, -L  | 查询任务列表        | AbilityManagerService::DumpMissionListInner  |
| --mission-infos, -S | 查询任务信息        | AbilityManagerService::DumpMissionInfosInner |

## dumpsys命令

dumpsys命令用于查询系统服务、进程、待处理等的信息

| 参数                 | 解释                                          | 对应源码                                           |
| ------------------ | ------------------------------------------- | ---------------------------------------------- |
| --all, -a          | 查询mission-list、service、pending、process的所有信息 | AbilityManagerService::DumpSysInner            |
| --mission-list, -l | 查询任务列表                                      | AbilityManagerService::DumpSysMissionListInner |
| --ability, -i      | 查询ability                                   | AbilityManagerService::DumpSysAbilityInner     |
| --extension, -e    | 查询服务                                        | AbilityManagerService::DumpSysStateInner       |
| --pending, -p      | 查询                                          | AbilityManagerService::DumpSysPendingInner     |
| --process, -r      | 查询进程                                        | AbilityManagerService::DumpSysProcess          |
| --data， -d         | 查询数据                                        | AbilityManagerService::DataDumpSysStateInner   |
