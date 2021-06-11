---
id: tap-fun-friends
title: 好友
sidebar_label: 好友
---

import MultiLang from '@theme/MultiLang';
import useBaseUrl from '@docusaurus/useBaseUrl';

该功能暂未对外开放

## 好友功能概览

### 通用服务
在开发者中心后台，开启「好友」功能后即可使用SDK

通用功能         | 说明      |
 ----------- | -------- |
[获取互关列表](#3-获取关注列表)    |   获取TapTap相互关注的好友列表  |


:::info
下面为深度功能。需要联系商务获取权限后，在开发者中心进行手动开启。商务联系方式 operation@taptap.com
:::

### 深度服务

深度功能         | 说明      |
 ----------- | -------- |
[添加好友](#4-添加好友) |   添加好友，同步到TapTap   |
[删除好友](#5-删除好友)    |    -  |
[拉黑好友](#6-拉黑好友)    |   -  |
[取消拉黑](#7-取消拉黑) |   -   |
[获取粉丝列表](#8-获取粉丝列表)  |   -  |
[获取黑名单](#9-获取黑名单) |   -   |
[分享好友邀请链接](#10-分享好友邀请链接)    |   自动生成分享链接，同时调用起系统分享组组件行选择分享  |
[获取好友邀请链接](#11-获取好友邀请链接)   |  生成邀请链接  |
[搜索用户](#12-搜索用户)  |   可以主动搜索tds id然后进行添加好友  |


## 1. 应用配置

### 工程导入

<MultiLang>
<>



```json
"com.taptap.tds.friends": "https://github.com/TapTap/TapFriends-Unity.git#2.1.4",
```

</>
<>

```json
TapFriendUI_2.1.4.arr  
TapFriend_2.1.4.arr
```

</>
<>

- TapFriendSDK.framework
- TapFriendUISDK.framework
- TapFriendResource.bundle

</>
</MultiLang>

### 工程配置
Android或者unity开发时，都需要在manifest中加入以下代码

```java
<activity
    android:name="com.tapsdk.friends.TapFriendsRouterPageActivity"
    android:allowTaskReparenting="true"
    android:exported="true"
    android:launchMode="singleTask"
    android:screenOrientation="nosensor"
    android:theme="@android:style/Theme.Translucent.NoTitleBar">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />

        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <data
            android:host="{client_id}"
            android:path="/friends"
            android:scheme="tapsdk" />
    </intent-filter>
</activity>

```

## 2. 注册消息回调

code  | msg |说明
| ------ | ------ |------|
130001 | 用户id (tds id) | 打开他人的分享链接唤起游戏后回调，在回调里处理tds id，如：[关注TA](#4-添加好友)


<MultiLang>

```cs
//实现ITapMessageListener接口
 public void OnMessageWithCode(int code, Dictionary<string, object> extras)
{
    Debug.Log( "code: " + code + " extras: " + Json.Serialize(extras));
}

```

```java
TapFriends.registerMessageCallback(new ComponentMessageCallback() {
    @Override
    public void onMessage(int code, Map<String, String> map) {
                
    }
});
```


```objectivec
- (void)onMessageWithCode:(NSInteger)code extras:(NSDictionary *)extras
{
    NSLog(@"code %@, %@", code,extras);
}
```

</MultiLang>


## 3. 获取关注列表

以下列表形式获取均为分页获取  
int from：起始位置  
bool mutualAttention: true 为互相关注；false 为单向关注其他人  
int limit：结束位置  

返回参数请参考[参数说明](#14-参数说明)

<MultiLang>

```cs
//public static void GetFollowingList (int from, bool mutualAttention, int limit, Action<List<TapUserRelationShip>, TapError> action);

 TapFriends.GetFollowingList(0, true, 100, ( relationShip ,err) => {
    foreach (TapUserRelationShip fi in relationShip)
    {
        Debug.Log("tds id :" + fi.userId);
    }
});
```

```java
TapFriends.getFollowingList(0, true, 100, new ListCallback<TapUserRelationship>() {
    @Override
    public void onSuccess(List<TapUserRelationship> list) {
        for (TapUserRelationship i : list){
            System.out.println(i);
        }
    }

    @Override
    public void onFail(TapFriendError tapFriendError) {
        Log.d("TapTap", "关注好友列表失败： " + tapFriendError.detailMessage);
    }
});
```


```objectivec
[TapFriends getFollowingList:0 mutualAttention:true limit:100 handler:^(NSArray<TapUserRelationShip *> * _Nullable userList, NSError * _Nullable error) {
    if (error!=nil){
        for (int i=0; userList.count; i++) {
            NSLog(@"%@", userList[0]);
        }
    }
}];

```

</MultiLang>




## 4. 添加好友

<MultiLang>

```cs
TapFriends.AddFriend("tds id",(err)=> {
    if (err != null)
    {
        Debug.Log("添加好友成功");
    }
});
```

```java
TapFriends.addFriend("userID", new Callback<Boolean>() {
    @Override
    public void onSuccess(Boolean aBoolean) {
        Log.d("TapTap", "添加好友成功");
    }

    @Override
    public void onFail(TapFriendError tapFriendError) {
        Log.d("TapTap", "添加好友失败" + tapFriendError.detailMessage);
    }
});
```


```objectivec
[TapFriends addFriend:@"tds id" handler:^(NSError * _Nullable error) {
    if (error!=nil){
        NSLog (@"添加成功");
    }
}];
```

</MultiLang>

#### 参数说明
userId : tds id，登录成功后从服务端获取

## 5. 删除好友

<MultiLang>

```cs
TapFriends.DeleteFriend("tds id", (err) => {
    if (err != null)
    {
        Debug.Log("删除好友成功");
    }
});
```
```java
TapFriends.deleteFriend("userID", new Callback<Boolean>() {
    @Override
    public void onSuccess(Boolean aBoolean) {
        Log.d("TapTap", "删除好友成功");
    }

    @Override
    public void onFail(TapFriendError tapFriendError) {
        Log.d("TapTap", "删除好友失败" + tapFriendError.detailMessage);
    }
});
```


```objectivec
[TapFriends deleteFriend:@"tds id" handler:^(NSError * _Nullable error) {
    if (error!=nil){
        NSLog (@"删除成功");
    }
}];
```
</MultiLang>

## 6. 拉黑好友

<MultiLang>

```cs
TapFriends.BlockUser("tds id", (err) => {
    if (err != null)
    {
        Debug.Log("拉黑成功");
    }
});
```

```java
TapFriends.blockUser("userID", new Callback<Boolean>() {
    @Override
    public void onSuccess(Boolean aBoolean) {
        Log.d("TapTap", "拉黑好友成功");
    }

    @Override
    public void onFail(TapFriendError tapFriendError) {
        Log.d("TapTap", "拉黑好友失败" + tapFriendError.detailMessage);
    }
});
```


```objectivec
[TapFriends blockUser:@"tds id" handler:^(NSError * _Nullable error) {
    if (error!=nil){
        NSLog (@"拉黑成功");
    }
}];

```

</MultiLang>



## 7. 取消拉黑

<MultiLang>

```cs
TapFriends.UnblockUser("tds id", (err) => {
    if (err != null) {
        Debug.Log("取消拉黑成功");
    }
});
```

```java
TapFriends.unblockUser("userID", new Callback<Boolean>() {
    @Override
    public void onSuccess(Boolean aBoolean) {
        Log.d("TapTap", "取消拉黑好友成功");
    }

    @Override
    public void onFail(TapFriendError tapFriendError) {
        Log.d("TapTap", "取消拉黑好友失败" + tapFriendError.detailMessage);
    }
});
```


```objectivec
[TapFriends unblockUser:@"tds id" handler:^(NSError * _Nullable error) {
    if (error!=nil){
        NSLog (@"取消拉黑成功");
    }
}];
```

</MultiLang>


## 8. 获取粉丝列表
返回参数请参考[参数说明](#14-参数说明)

<MultiLang>

```cs
TapFriends.GetFollowerList(0, 100, (relationShip, err) => {
    foreach (TapUserRelationShip fi in relationShip)
    {
        Debug.Log("tds id :" + fi.userId);
    }
});
```


```java
TapFriends.getFollowerList(0, 100, new ListCallback<TapUserRelationship>() {
@Override
public void onSuccess(List<TapUserRelationship> list) {
        for (TapUserRelationship f : list){
        System.out.println(f);
        }
        }

@Override
public void onFail(TapFriendError tapFriendError) {
        Log.d("TapTap", "关注粉丝列表失败： " + tapFriendError.detailMessage);
        }
        });
```


```objectivec
[TapFriends getFollowerList:0 limit:100 handler:^(NSArray<TapUserRelationShip *> * _Nullable userList, NSError * _Nullable error) {
    if (error!=nil){
        for (int i=0; userList.count; i++) {
            NSLog(@"%@", userList[0]);
        }
    }
}];
```

</MultiLang>



## 9. 获取黑名单
返回参数请参考[参数说明](#14-参数说明)

<MultiLang>

```cs
TapFriends.GetBlockList(0, 100, (relationShip, err) => {
    foreach (TapUserRelationShip fi in relationShip) {
        Debug.Log("tds id :" + fi.userId);
    }
});
```


```java
TapFriends.getBlockList(0, 100, new ListCallback<TapUserRelationship>() {
@Override
public void onSuccess(List<TapUserRelationship> list) {
        for (TapUserRelationship b : list){
        System.out.println(b);
        }
        }

@Override
public void onFail(TapFriendError tapFriendError) {
        Log.d("TapTap", "获取黑白单列表失败： " + tapFriendError.detailMessage);
        }
        });
```


```objectivec
[TapFriends getBlockList:0 limit:100 handler:^(NSArray<TapUserRelationShip *> * _Nullable userList, NSError * _Nullable error) {
        if (error!=nil){
            for (int i=0; userList.count; i++) {
                NSLog(@"%@", userList[0]);
            }
        }
}];
```

</MultiLang>



## 10. 分享好友邀请链接

1. 生成链接并唤起系统分享控件
2. 选择分享应用，分享链接给对方
3. 对方点击链接，会打开下面图示
4. 如果对方已经安装该游戏，则直接打开游戏并添加关注；如果对方未安装该游戏，则先额跳转到 TapTap 进行安装

<img src={useBaseUrl('/img/friends-follow01.png')} alt="" width="400" />

<MultiLang>

```cs
TapFriends.SendFriendInvitation((isInvitation, error) =>
{
    if (error != null)
    {
        label = $"Error:{error.code} Description:{error.errorDescription}";
    }
    else
    {
        label = "分享好友邀请: ";
        this.label = this.label + (isInvitation ? "成功" : "失败");
    }
});
```


```java
TapFriends.sendFriendInvitation(Activity activity, new Callback<Boolean>() {
@Override
public void onSuccess(Boolean aBoolean) {
        // 分享好友邀请成功
        }

@Override
public void onFail(TapFriendError tapFriendError) {
        // 分享好友邀请失败
        }
        });
```


```objectivec
[TapFriends sendFriendInvitationWithHandler:^(BOOL success, NSError *_Nullable error) {
    if (success) {
        NSLog(@"分享成功");
    } else {
        NSLog(@"分享失败 %@", error);
    }
}];

```

</MultiLang>

##  11. 获取好友邀请链接

获取到上图示「关注好友」的分享链接（「分享好友邀请链接」功能的简略版，省去了分享，只生成相关链接），游戏可以通过自己的方式将该链接分享出去

<MultiLang>

```cs
 TapFriends.GenerateFriendInvitation((invitationString, error) =>
{
    if (error != null)
    {
        label = $"Error:{error.code} Description:{error.errorDescription}";
    }
    else
    {
        label = "获取好友邀请链接成功: ";
        this.label = this.label + invitationString;
    }
});
```


```java
TapFriends.generateFriendInvitation(new Callback<String>() {
@Override
public void onSuccess(String s) {
        // 获取好友邀请链接成功
        }

@Override
public void onFail(TapFriendError tapFriendError) {
        // 获取好友邀请链接失败
        }
        });
```


```objectivec
[TapFriends generateFriendInvitationWithHandler:^(NSString *_Nullable invitationString, NSError *_Nullable error) {
    if (error) {
        NSLog(@"error:%@", error);
    } else {
        NSLog(@"url %@", invitationString);
    }
}];
```

</MultiLang>




## 12. 搜索用户

<MultiLang>

```cs

TapFriends.SearchUser(userId, (relationShip, error) =>
{
    if (error != null)
    {
        label = $"Error:{error.code} Description:{error.errorDescription}";
    }
    else
    {
        label = "搜索用户成功";
        this.label = this.label + "userId：" + relationShip.userId +
                        " name：" + relationShip.name +
                        " avatar：" + relationShip.avatar +
                        " gender：" + relationShip.gender +
                        " mutualAttention：" + relationShip.mutualAttention +
                        " relationship：" + relationShip.relationship + "\n";
    }
});
```


```java
TapFriends.searchUser("userID", new Callback<TapUserRelationship>() {
@Override
public void onSuccess(TapUserRelationship tapUserRelationship) {
        // 搜索好友成功
        tapUserRelationship.toJSON();
        }

@Override
public void onFail(TapFriendError tapFriendError) {
        // 搜索好友失败
        }
        })
```


```objectivec
[TapFriends searchUserWithUserId:@"tds id" value:@"playing" handler:^(TapSimpleHandler *_Nullable user, NSError *_Nullable error) {
    if (error) {
        NSLog(@"error:%@", error);
    } else {
        NSString *str = @"";

        str = [str stringByAppendingString:[self beanToString:user]];
        str = [str stringByAppendingString:@"\n\n"];

        NSLog(@"friend list %@ %@ %@ %@", str, user.isBlocked ? @"yes" : @"no", user.isFollowed ? @"yes" : @"no", user.isFollowing ? @"yes" : @"no");
    }
}];

```

</MultiLang>



## 13. 富信息

#### 使用说明

- 使用富信息需要先按[服务端配置](#服务端配置)格式提供给技术支持进行
- 最多支持配置20个 key，移动端以 key-value 键值对发送信息
- key-不支持空字符串，最短长度2位，最长长度64位；value-不支持空字符串，最短长度2位，最长长度256位

### 移动端 

#### 设置富信息

<MultiLang>

```cs

TapFriends.SetRichPresence("display", "playing", (,error) =>
{
    if (error != null)
    {
        label = $"Error:{error.code} Description:{error.errorDescription}";
    }
    else
    {
        label = "设置富信息成功";
    }
});

TapFriends.SetRichPresence("leadboard", "100", (,error) =>
{
    if (error != null)
    {
        label = $"Error:{error.code} Description:{error.errorDescription}";
    }
    else
    {
        label = "设置富信息成功";
    }
});
```


```java
//TODO
```


```objectivec
[TapFriends setRichPresence:@"display" value:@"playing" handler:^(NSError * _Nullable error) {
        if (error) {
            NSLog(@"error:%@", error);
        } else {
            NSLog(@"设置成功");
        }
}];

```

</MultiLang>

#### 清除富信息

<MultiLang>

```cs

TapFriends.SetRichPresence("display", "playing", (,error) =>
{
    if (error != null)
    {
        label = $"Error:{error.code} Description:{error.errorDescription}";
    }
    else
    {
        label = "设置富信息成功";
    }
});

TapFriends.ClearRichPresence("leadboard", (,error) =>
{
    if (error != null)
    {
        label = $"Error:{error.code} Description:{error.errorDescription}";
    }
    else
    {
        label = "清除富信息成功";
    }
});
```


```java
//TODO
```


```objectivec
   [TapFriends clearRichPresence:@"display" handler:^(NSError * _Nullable error) {
            if (error) {
                NSLog(@"error:%@", error);
            } else {
                NSLog(@"清除成功");
            }
    }];

```

</MultiLang>


### 服务端

请先确认好要配置的key-value，按照下面格式发给技术支持 

#### 服务端配置

服务端支持两种配置，用 type 进行区分 `令牌`和`变量`

```
[
  {
	  "key": "display",
	  "type": "token"
  }, {
	  "key": "leadboard",
	  "type": "token"
  }, {
	  "key": "inviteable",
	  "type": "variable"
  }, {
	  "key": "score",
	  "type": "variable"
  }
]
```

#### 令牌
对于令牌，提供key之后，需要提供与之对应的value  

如上type = token的令牌有`display`和`leadboard`。如需配置多语言也请标识，语言标识没有要求，如 "CN"、"US"、"TW" 能理解即可

```
{
	"CN": {
		"display": {
			"#playing": "游戏中",
			"#idle": "在线",
			"#room": "准备中",
			"#matching": "组队中"
		},
		"leadboard": {
			"#rank": "%rank%名", // 如需要提供占位符，请用%%标识
			"#score": "%score%分"
		}
	},
	"US": {
		"display": {
			"#playing": "Playing",
			"#idle": "Idle",
			"#room": "Room",
			"#matching": "Matching"
		},
		"leadboard": {
			"#rank": "%rank% rank",
			"#score": "%score% score"
		}
	}
}
```

#### 变量
对于变量，value 值游戏可以在移动端自行指定，设置后会传递给好友  
如上，type = variable的变量有两个 `inviteable`和`score`  




## 14. 参数说明
TapUserRelationShip

参数  | 描述
| ------ | ------ |
userid | 用户id (tds id)
name  | 用户nick name
avatar  | 头像地
gender | UNKNOWN = 0;<br/>MALE = 1;<br/> FEMALE = 2;
mutualAttention | 是否互相关注 <br/>false:不是互相关注 <br/>true: 互相关注
relationship | 返回字符串类型【000】<br/>从左到右：是否关注，是否被关注，是否拉黑<br/>如:【010】为粉丝（单向被关注），【110】为好友（双向互关）
online | 是否在线
time | 事件触发时间，关注列表回调的是你关注他的时间，粉丝列表回调表示他关注你的时间，单位s 
richPresence | 回调好友的富信息 [富信息使用说明](#使用说明)
