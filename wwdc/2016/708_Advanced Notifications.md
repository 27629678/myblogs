## Advanced Notifications

### 0x1、Media Attachments

![workflow](./resources/708_media_attachments.png)

```
// 1st, payload

{
	aps:{
		alert:{...},
		mutable-content:1
	}
	my-attachment:"https://example.com/photo.jpg"
}

// 2nd, service extension(remote and local notification)
public class NENotificationService:UNNotificationServiceExtension {
	override public func didReceive(_ request:UNNotificationRequest, withContentHandler contentHandler:(UNNotificationContent)->void {
		let file_url = //...
		
		// notification surppot image, video and audio etc. 
		// downloaded in service extension.
		let attachment = UNNotificationAttachment(identifier:"image" url:file_url options:nil)
		
		// add attachment to notification
		let content = request.content.mutableCopy as! UNMutableNotificationContent
		content.attachments = [attachment]
		contentHandler(content)
	}
}

// 3rd, ...
```

### 0x2、Custom User Interface

1. `Notification` content extension
2. Custom views
3. No interaction
4. Respond to `Notification` actions

#### 1 `Notification` content extension

**step 1**: create a new target:

![NotificationContent](./resources/708_notification_content.png)

```
// minimal content extension

class NENotificationViewController:UIViewController, UNNotificationContentExtension {
	
	@IBOutlet var label:UILabel?
	
	oerride func viewDidLoad() {
		super.viewDidLoad()
	}
	
	func didReceive(_ notification:UNNotification) {
		label?.text = notification.request.content.body
	}
}
```

**step 2**: telling system to find your service extension

![extension_settings](./resources/708_notification_settings.png)

#### 2 Custom views

add ui components by code or in storyboard 

remove the highlighted item in following picture

![info.plist.settings1](./resources/708_notification_settings1.png)

resize the view controller

```
// minimal content extension

class NENotificationViewController:UIViewController, UNNotificationContentExtension {
	
	@IBOutlet var label:UILabel?
	
	oerride func viewDidLoad() {
		super.viewDidLoad()
		
		// or other autolayout methods and so on.
		let size = view.bounds.size
		preferredContentSize = CGSize(width:size.width, height:size.width/2)
	}
	
	func didReceive(_ notification:UNNotification) {
		// ...
	}
}
```

![content_size_ratio](./resources/708_content_size_ratio.png)

#### 3 interaction with notification

```
// interacting notification action response

class NENotificationViewController:UIViewController, UNNotificationContentExtension {
	func didReceive(_ response:UNNotificationResponse, completionHandler done:(UNNotificationContentExtensionResponseOption)->void) {
		server.postEventResponse(response.actionIdentifier) {
			if response.actionIdentifier == "accept" {
				eventResponse.text = "Going!"
				eventResponse.textColor = UIColor.green()
			}
			else if response.actionIdentifier == "decline" {
				eventResponse.text = "Not Going :("
				eventResponse.textColor = UIColor.red()
			}
			
			done(.dismiss)
		}
	}
}
```

custom input accessory view(ignored, to be continued...)