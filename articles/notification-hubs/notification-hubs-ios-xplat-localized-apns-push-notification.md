---
title: Honosított leküldéses értesítések küldése iOS-re az Azure Notification Hubs használatával | Microsoft Docs
description: Megtudhatja, hogyan használhat leküldéses honosított értesítéseket iOS-eszközökre az Azure Notification Hubs használatával.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a78d3a76e2b13a120e9e744e181c95bfcb330f27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92313909"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Oktatóanyag: honosított leküldéses értesítések küldése iOS-re az Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Áruház C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Notification Hubs [sablonok](notification-hubs-templates-cross-platform-push-messages.md) szolgáltatását a nyelv és az eszköz által honosított feltörési Hírek küldéséhez. Ebben az oktatóanyagban először a use Notification Hubs használatával létrehozott iOS-alkalmazást kell elindítania a [legfrissebb hírek elküldéséhez]. Ha elkészült, regisztrálhatja a kívánt kategóriákat, megadhatja, hogy milyen nyelven kapja meg az értesítéseket, és csak leküldéses értesítéseket kapjon a kiválasztott kategóriákhoz az adott nyelven.

A forgatókönyv két részből áll:

* az iOS-alkalmazás lehetővé teszi, hogy az ügyféleszközök megadják a nyelvet, és előfizessenek a különböző Breaking News-kategóriákra;
* A háttér az Azure Notification Hubs **címke** -és **sablon** -funkcióit használva közvetíti az értesítéseket.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Az alkalmazás felhasználói felületének frissítése
> * Az iOS-alkalmazás létrehozása
> * Honosított sablon értesítéseinek küldése a .NET-konzol alkalmazásból
> * Honosított sablon értesítéseinek küldése az eszközről

## <a name="overview"></a>Áttekintés

[Használat Notification Hubs a legfrissebb hírek elküldéséhez]létrehozott egy alkalmazást, amely **címkéket** használt a különböző híreket tartalmazó értesítésekre való előfizetéshez. Számos alkalmazás azonban több piac megcélzása és honosítás megkövetelése. Ez azt jelenti, hogy az értesítések tartalmának honosítva kell lenniük, és a megfelelő eszközökre kell továbbítani őket. Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Notification Hubs **sablon** funkcióját a honosított Breaking News-értesítések egyszerű kézbesítéséhez.

> [!NOTE]
> A honosított értesítések küldésének egyik módja az, hogy az egyes címkék több verzióját hozza létre. Például az angol, a francia és a mandarin támogatásához három különböző címkére van szükség a világ híreihez: "world_en", "world_fr" és "world_ch". Ezután el kell küldenie a világ Hírek honosított verzióját az egyes címkékre. Ebben a témakörben a sablonok segítségével elkerülheti a címkék elterjedését és a több üzenet küldésének követelményét.

A sablonok segítségével megtudhatja, hogy egy adott eszköz hogyan kapjon értesítést. A sablon meghatározza a hasznos adatok pontos formátumát a háttéralkalmazás által küldött üzenet részét képező tulajdonságokra hivatkozva. Ebben az esetben az összes támogatott nyelvet tartalmazó locale-agnosztikus üzenetet küld:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Ezután gondoskodjon arról, hogy az eszközök a megfelelő tulajdonságra hivatkozó sablonnal regisztráljanak. Például egy iOS-alkalmazás, amely a francia Hírek regisztrálásához szeretne regisztrálni, a következő szintaxissal:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

A sablonokkal kapcsolatos további információkért lásd: [sablonok](notification-hubs-templates-cross-platform-push-messages.md) című cikk.

## <a name="prerequisites"></a>Előfeltételek

* Töltse le a [leküldéses értesítéseket adott iOS-eszközök](notification-hubs-ios-xplat-segmented-apns-push-notification.md) oktatóanyagára, és álljon rendelkezésre a kód, mert ez az oktatóanyag közvetlenül erre a kódra épül.
* A Visual Studio 2019 nem kötelező.

## <a name="update-the-app-user-interface"></a>Az alkalmazás felhasználói felületének frissítése

Ebben a szakaszban a következő témakörben létrehozott Breaking News-alkalmazást módosítja, amely a honosított híreket a sablonok használatával küldi el a honosított Hírek küldéséhez [Notification Hubs használatával] .

A `MainStoryboard_iPhone.storyboard` -ben adjon hozzá egy szegmentált vezérlőelemet a három nyelvhez: angol, francia és mandarin.

![Az iOS felhasználói felület létrehozása – történet][13]

Ezután vegyen fel egy IBOutlet a ViewController. h fájlba az alábbi képen látható módon:

![Kivezetések létrehozása a kapcsolókhoz][14]

## <a name="build-the-ios-app"></a>Az iOS-alkalmazás létrehozása

1. A alkalmazásban `Notification.h` adja hozzá a `retrieveLocale` metódust, és módosítsa az áruház és az előfizetés metódusait az alábbi kódban látható módon:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    A alkalmazásban `Notification.m` módosítsa a `storeCategoriesAndSubscribe` metódust úgy, hogy hozzáadja a `locale` paramétert, és a felhasználói alapértelmezésekben tárolja azt:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Ezután módosítsa az *előfizetés* metódust a területi beállítás belefoglalásához:

    ```objc
    - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

        NSString* localeString;
        switch (locale) {
            case 0:
                localeString = @"English";
                break;
            case 1:
                localeString = @"French";
                break;
            case 2:
                localeString = @"Mandarin";
                break;
        }

        NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    A metódust használja a `registerTemplateWithDeviceToken` helyett `registerNativeWithDeviceToken` . Ha regisztrál egy sablont, meg kell adnia a JSON-sablont és a sablon nevét is (mivel az alkalmazásnak más sablonokat is regisztrálnia kell). Ügyeljen arra, hogy a kategóriákat címkékként regisztrálja, mivel szeretné megkapni az adott híreket érintő értesítéseket.

    Adjon hozzá egy metódust a területi beállítás lekéréséhez a felhasználói alapértelmezett beállítások közül:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Most, hogy módosította az `Notifications` osztályt, meg kell győződnie arról, hogy az `ViewController` új használatot tesz lehetővé `UISegmentControl` . Adja hozzá a következő sort a `viewDidLoad` metódushoz, és győződjön meg arról, hogy az aktuálisan kiválasztott területi beállítás látható:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Ezután a `subscribe` metódusban módosítsa a hívást a `storeCategoriesAndSubscribe` következő kódra:

    ```objc
    [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                    @"Subscribed!" delegate:nil cancelButtonTitle:
                                    @"OK" otherButtonTitles:nil, nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

3. Végül frissítenie kell a `didRegisterForRemoteNotificationsWithDeviceToken` metódust a AppDelegate. m-ben, hogy megfelelően frissítse a regisztrációt az alkalmazás indításakor. Változtassa meg az `subscribe` értesítések metódusának hívását a következő kóddal:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>választható Honosított sablon értesítéseinek küldése a .NET-konzol alkalmazásból

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>választható Honosított sablon értesítéseinek küldése az eszközről

Ha nincs hozzáférése a Visual studióhoz, vagy csak tesztelni szeretné a honosított sablon értesítéseinek küldését közvetlenül az eszközről az alkalmazásból. A honosított sablon paramétereit hozzáadhatja az `SendNotificationRESTAPI` előző oktatóanyagban megadott metódushoz.

```objc
- (void)SendNotificationRESTAPI:(NSString*)categoryTag
{
    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                defaultSessionConfiguration] delegate:nil delegateQueue:nil];

    NSString *json;

    // Construct the messages REST endpoint
    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                        HUBNAME, API_VERSION]];

    // Generated the token to be used in the authorization header.
    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

    //Create the request to add the template notification message to the hub
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    [request setHTTPMethod:@"POST"];

    // Add the category as a tag
    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

    // Template notification
    json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
            \"News_English\":\"Breaking %@ News in English : %@\","
            \"News_French\":\"Breaking %@ News in French : %@\","
            \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
            categoryTag, self.notificationMessage.text,
            categoryTag, self.notificationMessage.text,  // insert English localized news here
            categoryTag, self.notificationMessage.text,  // insert French localized news here
            categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

    // Signify template notification format
    [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

    // JSON Content-Type
    [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

    //Authenticate the notification message POST request with the SaS token
    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

    //Add the notification message body
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

    // Send the REST request
    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
            }
            if (data != NULL)
            {
                //xmlParser = [[NSXMLParser alloc] initWithData:data];
                //[xmlParser setDelegate:self];
                //[xmlParser parse];
            }
        }];

    [dataTask resume];
}
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban honosított értesítéseket küldött iOS-eszközökre. Ha szeretné megtudni, hogyan küldhet le értesítéseket az iOS-alkalmazások adott felhasználói számára, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott felhasználóknak](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: /previous-versions/azure/reference/dn223264(v=azure.100)
[A legfrissebb hírek elküldése a Notification Hubs használatával]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/reference/dn223264(v=azure.100)