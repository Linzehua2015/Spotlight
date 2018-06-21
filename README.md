# Spotlight
iOS9 Spotlight 简单使用Demo
# 为什么要加这个功能
根据产品的性质、用户习惯，提高整个产品的体验与曝光率。
- 用户习惯：就以我为例子，我的手机内装有上百来个app，每次要找应用我都通过spotlight直接输入应用名，这个时候输入关键字，相关的内容会出现在里边，试想，如果你能猜到用户的需求，在你的应用内加入相关的关键字，这里就会出现你的产品！增加了曝光度，刚好内容又吸引了用户的注意，用户点了，流量来了！
- 产品：如果你的产品是社交类的app，那么在spotlight加上好友的相关信息，聊天记录作为关键字，如果用户这个时候想联系到这个人，在spotlight中输入好友姓名即可显示，这点可以参考下QQ的做法。
# 实现效果
![实现效果](https://upload-images.jianshu.io/upload_images/2083012-5d1402032e7c4f04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/480)

# 功能说明
调用iOS9提供的接口将数据的存入系统中，之后在spotlight中搜索就会出现你录入的数据。

```
    CSSearchableItemAttributeSet *attributeSet = [[CSSearchableItemAttributeSet alloc] initWithItemContentType:@"Package"];
    attributeSet.title = @"test-title";
    //显示的描述
    attributeSet.contentDescription = @"test-description";
    //搜索关键字
//    attributeSet.keywords = @[title, @"CX"];
    //显示的图标
    UIImage *icon = [UIImage imageNamed:@"icon"];
    attributeSet.thumbnailData = UIImageJPEGRepresentation(icon, 1);

    CSSearchableItem *item = [[CSSearchableItem alloc] initWithUniqueIdentifier:@"demo_id"
                                                               domainIdentifier:@"domainIdentifier"
                                                                   attributeSet:attributeSet];
    NSArray *items = @[item];
    
    CSSearchableIndex *searchableIndex = [CSSearchableIndex defaultSearchableIndex];
    [searchableIndex indexSearchableItems:items completionHandler:^(NSError * _Nullable error) {
        if (error != nil) {//添加索引失败
            NSLog(@"%@",[error localizedDescription]);
        }else{//成功
            NSLog(@"indexing successful");
        }
    }];
```

用户在spotlight中点击item后，调用AppDelegate如下方法
```
- (BOOL)application:(UIApplication *)application
continueUserActivity:(NSUserActivity *)userActivity
 restorationHandler:(void (^)(NSArray * _Nullable))restorationHandler
{
    NSString *activityType = userActivity.activityType; // com.apple.corespotlightitem
    if ([activityType isEqualToString:@"com.apple.corespotlightitem"]) {
        NSString *uniqueId = userActivity.userInfo[CSSearchableItemActivityIdentifier];
        NSLog(@"uniqueId: %@", uniqueId);
    }
    return YES;
}
```
# 注意

- CSSearchableItem 可以设置过期时间
```
// 默认是一个月，如果有需求，可以设置一下。
@property (copy, null_resettable) NSDate * expirationDate;
```
- 如果你的应用支持iOS9以下的系统，在使用以上接口的时候，注意添加iOS系统版本号的判断，避免iOS9以下的设备闪退。 

