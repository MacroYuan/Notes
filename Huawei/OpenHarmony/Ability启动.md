API9的新方法，Ability的启动方法AbilityContext::StartAbility位于/frameworks/native/ability/ability_context.cpp中

无启动参数版：

```cpp
ErrCode AbilityContext::StartAbility(const AAFwk::Want &want, int requestCode)
{
    HILOG_INFO("AbilityContext::StartAbility called, requestCode = %{public}d", requestCode);

    AppExecFwk::AbilityType type = GetAbilityInfoType();
    if (type != AppExecFwk::AbilityType::PAGE && type != AppExecFwk::AbilityType::SERVICE) {
        HILOG_ERROR("AbilityContext::StartAbility AbilityType = %{public}d", type);
        return ERR_INVALID_VALUE;
    }

    HILOG_INFO("%{public}s. Start calling ams->StartAbility.", __func__);
    ErrCode err = AAFwk::AbilityManagerClient::GetInstance()->StartAbility(want, token_, requestCode);
    HILOG_INFO("%{public}s. End calling ams->StartAbility. ret=%{public}d", __func__, err);
    if (err != ERR_OK) {
        HILOG_ERROR("AbilityContext::StartAbility is failed %{public}d", err);
    }
    return err;
}
```

首先由GetAbilityInfoType()方法获取到Ability的类型，如果Ability不是PAGE和SERVICE两种类型，直接报错。正常启动时，通过AbMC(AAFwk::AbilityManagerClient)单例的Client再去启动StartAbility，token_为ability在AbMS中的唯一标识，用于识别调用者身份。

有启动参数版：

```cpp
ErrCode AbilityContext::StartAbility(const Want &want, int requestCode, const AbilityStartSetting &abilityStartSetting)
{
    AppExecFwk::AbilityType type = GetAbilityInfoType();
    if (AppExecFwk::AbilityType::PAGE != type && AppExecFwk::AbilityType::SERVICE != type) {
        HILOG_ERROR("AbilityContext::StartAbility AbilityType = %{public}d", type);
        return ERR_INVALID_VALUE;
    }

    HILOG_INFO("%{public}s. Start calling ams->StartAbility.", __func__);
    ErrCode err =
        AAFwk::AbilityManagerClient::GetInstance()->StartAbility(want, abilityStartSetting, token_, requestCode);
    HILOG_INFO("%{public}s. End calling ams->StartAbility. ret=%{public}d", __func__, err);
    if (err != ERR_OK) {
        HILOG_ERROR("AbilityContext::StartAbility is failed %{public}d", err);
    }

    return err;
}
```

相比与无启动参数版，只是在传递给AbMC参数时多传递一个启动参数AbilityStartSetting

AbilityContext的StartAbility()操作相当于前端像后端发起请求的过程。



再看下在AbMC中怎么继续启动ablility的

AbMC也是将请求传递给核心的AbMS(AbilityManagerService)上

```cpp
ErrCode AbilityManagerClient::StartAbility(const Want &want, int requestCode, int32_t userId)
{
    HITRACE_METER_NAME(HITRACE_TAG_ABILITY_MANAGER, __PRETTY_FUNCTION__);
    auto abms = GetAbilityManager();
    CHECK_POINTER_RETURN_NOT_CONNECTED(abms);
    HandleDlpApp(const_cast<Want &>(want));
    return abms->StartAbility(want, userId, requestCode);
}
```

到AbMS的步骤需要通过AbMC本身的一个proxy_去获取到一个IAbilityManager的接口，基本上AbMC中的函数都是先获取到该ambs的接口，再执行操作。



接下来到AbMS中，AbMs中的StartAbility函数如下：

```cpp
int AbilityManagerService::StartAbility(const Want &want, int32_t userId, int requestCode)
{
    HITRACE_METER_NAME(HITRACE_TAG_ABILITY_MANAGER, __PRETTY_FUNCTION__);
    HILOG_INFO("%{public}s coldStart:%{public}d", __func__, want.GetBoolParam("coldStart", false));
    AAFWK::EventInfo eventInfo;
    eventInfo.userId = userId;
    eventInfo.bundleName = want.GetElement().GetBundleName();
    eventInfo.moduleName = want.GetElement().GetModuleName();
    eventInfo.abilityName = want.GetElement().GetAbilityName();
    AAFWK::EventReport::SendAbilityEvent(AAFWK::START_ABILITY,
        HiSysEventType::BEHAVIOR, eventInfo);
    int32_t ret = StartAbilityInner(want, nullptr, requestCode, -1, userId);
    if (ret != ERR_OK) {
        eventInfo.errCode = ret;
        AAFWK::EventReport::SendAbilityEvent(AAFWK::START_ABILITY_ERROR,
            HiSysEventType::FAULT, eventInfo);
    }
    return ret;
}
```

首先创建一个AAFWK::EventInfo对象记录本次的启动ablility事件，通过AAFWK::EventReport::SendAbilityEvent()将事件发送出去（还不知道发送到哪？），通过内部的StartAbilityInner()方法执行启动ability。

在AbilityManagerService::StartAbilityInner()中，会先对用户权限验证和token验证

启动过程中主要步骤：

```cpp
GenerateAbilityRequest(want, requestCode, abilityRequest, callerToken, validUserId);
auto abilityInfo = abilityRequest.abilityInfo;
validUserId = abilityInfo.applicationInfo.singleton ? U0_USER_ID : validUserId;
CheckStaticCfgPermission(abilityInfo);
GrantUriPermission(want, validUserId);
AbilityUtil::JudgeAbilityVisibleControl(abilityInfo, callerUid);
auto type = abilityInfo.type;
UpdateCallerInfo(abilityRequest.want);
auto missionListManager = GetListManagerByUserId(oriValidUserId);

return missionListManager->StartAbility(abilityRequest);
```

通过以上的检验权限、授权等步骤生成一个AbilityRequest对象，并通过missionListManager去处理该请求，missionListManager是通过GetListManagerByUserId()生成的MissionListManager，该方法会根据missionListManager_的map去判断是否包含该userId并返回



再到MissionListManager中，MissionListManager是一个队列处理ability请求，如果当前运行的currentTopAbility是前台应用，则该ability的启动会进入等待。

开始启动ability

1. 选择目标任务列表
2. 获取目标任务
3. 将任务移动到目标列表
4. 将目标列表移动到顶部
5. 调度目标ability
6. ability启动

接下来调用路径为MissionListManager::StartAbility -> MissionListManager::StartAbilityLocked ->AbilityRecord::ProcessForegroundAbility





