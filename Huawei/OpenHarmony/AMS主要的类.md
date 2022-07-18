AMS(Activity Manager Service)源于android体系的概念

| namespace  | 域名     |
| ---------- | ------ |
| AAFwk      | 元能力    |
| AppExecFwk | 应用程序框架 |

aa: Ability assistant, Ability助手

Want：对象之间传递信息的载体

### AppExecFwk

命名空间：OHOS::AppExecFwk

#### 类 AppMgrService

父类：SystemAbility、AppMgrStub

构造函数`AppMgrService()`、`AppMgrService(const int32_t serviceId, bool runOnCreate = false)`

析构函数`~AppMgrService()`

主要函数

| -                                                                                                                                                  | 功能                         | 返回          |
| -------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- | ----------- |
| `void AttachApplication(const sptr<IRemoteObject> &app)`                                                                                           | 获取Application启动需要的所有信息     |             |
| `void ApplicationForegrounded(const int32_t recordId)`                                                                                             | 将应用设为前台状态                  |             |
| `void ApplictionBackgrouded(const int32_t recordId)`                                                                                               | 将应用设为后台状态                  |             |
| `void ApplicationTerminated(const int32_t recordId)`                                                                                               | 终止应用                       |             |
| `void AbilityCleaned(const sptr<IRemoteObject> &token)`                                                                                            | 清除应用记录                     |             |
| `int32_t ClearUpApplicationData(const std::string &bundleName)`                                                                                    | 根据传入的应用记录的bundleName清除应用数据 | 处理结果，成功Or失败 |
| `int32_t GetAllRunningProcesses(std::vector<RunningProcessInfo> &info)`                                                                            | 传入app名字列表，管理设备上运行的应用程序信息   | 处理结果，成功Or失败 |
| `int32_t GetProcessRunningInfoByUserId(std::vector<RunningProcessInfo> &info, int32_t userId)`                                                     | 根据应用名和用户id管理设备上运行的应用程序信息   | 处理结果，成功Or失败 |
| `int32_t CheckPermission(const int32_t recordId, const std::string &permission)`                                                                   | 检查是否拥有某些应用权限               | 处理结果，成功Or失败 |
| `AppMgrServiceState QueryServiceState()`                                                                                                           | 查询应用服务状态                   | 应用服务状态码     |
| `sptr<IAmsMgr> GetAmsMgr()`                                                                                                                        |                            |             |
| `void GetSystemMemoryAttr(SystemMemoryAttr &memoryInfo, std::string &strConfig)`                                                                   | 获取系统内存信息                   |             |
| `void AddAbilityStageDone(const int32_t recordId)`                                                                                                 | 通知应用版本已更新                  |             |
| `void StartupResidentProcess(const std::vector<AppExecFwk::BundelInfo> &bundleInfos)`                                                              | 运行所有常驻进程                   |             |
| `int StartUserTestProcess(const AAFwk::Want &want, const sptr<IRemoteObject> &observer, const AppExecFwk::BundleInfo &bundleInfo, int32_t userId)` | 运行用户测试进程                   | 处理结果，成功Or失败 |
| `int FinishUserTest(const std::string &msg, const int64_t &resultCode, const std::string &bundleName)`                                             | 完成用户测试                     | 处理结果，成功Or失败 |
| `void ScheduleAcceptWantDone(const int32_t recordId, const AAFwk::Want &want, const std::string &flag)`                                            |                            |             |
| `int GetAbilityRecordsByProcessID(const int pid, std::vector<sptr<IRemoteObject>> &tokens)`                                                        |                            |             |
| `int StartRenderProcess(const std::string &renderParam, int32_t ipcFd,     int32_t sharedFd, pid_t &renderPid)`                                    |                            |             |
| `void AttachRenderProcess(const sptr<IRemoteObject> &shceduler)`                                                                                   |                            |             |
| `int GetRenderProcessTerminationStatus(pid_t renderPid, int &status)`                                                                              |                            |             |
| `int32_t GetConfiguration(Configuration& config)`                                                                                                  |                            |             |
| `int32_t UpdateConfiguration(const Configuration &config)`                                                                                         |                            |             |
| `int32_t RegisterConfigurationObserver(const sptr<IConfigurationObserver> &observer)`                                                              |                            |             |
| `int32_t UnregisterConfigurationObserver(const sptr<IConfigurationObserver> &observer)`                                                            |                            |             |
|                                                                                                                                                    |                            |             |

private：

| -                                                                                             | -              | -           |
| --------------------------------------------------------------------------------------------- | -------------- | ----------- |
| `ErrCode Init()`                                                                              | c初始化应用管理服务     | 处理结果，成功Or失败 |
| `void OnStart()`                                                                              | 运行应用服务         |             |
| `void OnStop()`                                                                               | 停止应用服务         |             |
| `bool IsReady()`                                                                              | 用于判断应用服务是否准备完毕 |             |
| `void AddAppDeathRecipient(const pid_t pid)`                                                  | ？              |             |
| `void SetInnerService(const std::shared_ptr<AppMgrServiceInner> &innerService)`               | 设置应用服务内部实例     |             |
| `int32_t RegisterApplicationStateObserver(const sptr<IApplicationStateObserver> &observer)`   | 注册应用或进程监视器     | 处理结果，成功Or失败 |
| `int32_t UnregisterApplicationStateObserver(const sptr<IApplicationStateObserver> &observer)` | 放弃注册应用或进程监视器   | 处理结果，成功Or失败 |
| `int32_t GetForegroundApplications(std::vector<AppStateData> &list)`                          | 获取前台应用         | 处理结果，成功Or失败 |

内部属性private：

```
std::shared_ptr<AppMgrServiceInner> appMgrServiceInner_;
AppMgrServiceState appMgrServiceState_;
std::shared_ptr<EventRunner> runner_;
std::shared_ptr<AMSEventHandler> handler_;
sptr<ISystemAbilityManager> systemAbilityMgr_;
sptr<IAmsMgr> amsMgrScheduler_;

DISALLOW_COPY_AND_MOVE(AppMgrService);
```

​    
