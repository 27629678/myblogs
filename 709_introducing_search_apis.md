##· App Search

three apis:

- NSUserActivity(viewed app content)|iOS8
- CoreSpotlight(any app content)|iOS9
- Web Markup(app content on web)|iOS9|some like deep links


####- CoreSpotlight

- for any app content
- methods to add/update and delete items
- used by mail/calendar and so on

>NOTE:Although app search is available for iOS 9 users, the search functionality of NSUserActivity and Core Spotlight is not supported on iPhone 4s, iPad 2, iPad (3rd generation), iPad mini, and iPod touch (5th generation).

####- create item

```
// create attribute_set and populate with metadata
let attribute_set = CSSearchableItemAttributeSet(itemContentType:kUTTypeImage as string)
attribute_set.title = "haleakala sunrise"
attribute_set.contentDescription = "May 12, 2015 Maui, Hawaii"

// create item with unique identifier; domain identifier used to group items
let item = CSSearchableItem(uniqueIdentifier:"1", domainIdentifier:"album-1", attributeSet:attribute_set)

// index item
CSSearchableIndex.defaultSearchableIndex().indexSearchbaleItems([item]) { error in
	if error != nil {
		print(error?.localizedDescription)
	}
	else {
		print("item indexed")
	}
}

// fullfill application delegate called when CoreSpotlight result is taped
func application(UIApplication, continueUserActivity userActivity:NSUserActivity, restorationHandler:[anyObject]?->Void)->Bool {
	if userActivity.activityType == CSSearchableItemActionType {
		let uniqueIdentifier = userActivity.userInfo? [CSSearchableItemActivityIdentifier] as ? String
		// use 'uniqueIdentifier
	}
	
	return true
}

```

![spotlight][spotlight_show]


>NOTE:Although only the title, uniqueIdentifier, and attributeSet properties are required, it’s highly recommended that you also provide content-specific values for the thumbnailData and contentDescription properties.

####- update item

same method as adding item to index

```
func indexSearchableItems(items:[CSSearchableItem], completionHandler:((NSError?)->Void)?)
```

####- delete item

using unique_identifiers

```
// identifiers = ["1", "4", "5"]
func deleteSearchableItemsWithIdentifiers(identifiers:[String], completionHandler:((NSError?)->Void)?)
```

using domain_identifier

```
// domainIdentifiers = ["domain-2"]
func deleteSearchableItemsWithDomainIdentifiers(domainIdentifiers:[String], completionHandler:((NSError?)->Void)?)
```

delete all items

```
func deleteAllSearchableItemsWithCompletionHandler(completionHandler:((NSError?)->Void)?)
```

>NOTE:The items you index using Core Spotlight APIs are not added to Apple’s server-side index or synced between devices. 


<!--links-->

[spotlight_show]:./Resources/709_spotlight_show.png