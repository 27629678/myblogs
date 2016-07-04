### 0x01 User Notifications Framework

#### 1.1 iOS

Full support to schedule and manage notifications

#### 1.2 watchOS

Existing support for forwarded notifications

Local Notifications on the watch**`NEW`**

#### 1.2 tvOS

Suport to badge app icons

Example:

- 3 unwatched episodes
- Pending user turn in game

### 0x02 Notification Delivery

#### 2.1 Registration

##### 2.1.1 User Authorization

- Banners
- Sound alerts
- Badging

> Needed for local and remote notifications

```
// listing 1
// Set notification configration
UNUserNotificationCenter.current().requestAuthorization([.sound, .alert, .badge]) { (success, error) in ... }
```

##### 2.1.2 Notification settings

```
// listing 2
// Access to user-defined settings(Configurable in Settings per app)

UNUserNotificationCenter.current().getNotificationSettings { (settings) in ... }
```

##### 2.1.3 Token Registration(Remote Notifications)

![figure 1](./resources/707_RemoteNotificationTokenRegistration.png)

figure 1, remote notification token registeration


```
// listing 3
// Existing API

UIApplication.shard().registerForRemoteNotifications()
```

##### 2.1.4 Notifications Delivery

Local Notification

```
// listing 4

let content = UNMutableNotificationContent()
content.title = "Introduction to Notifications"
content.subtitle = "Session 707"
content.body = "Woah! These new notifications look amazing! Don't you agree?"
content.badge = 1
```

Remote Notification

```
// listing 5

{
	"aps" : {
		"alert" : {
			"title":"Introduction to Notifications",
			"subtitle":"Session 707",
			"body":"Woah! These new notifications look amazing! Don't you agree?"
		},
		"badge":1
	},
}
```