---
title: Azure Notification Hubs Rich push
description: Ismerje meg, hogyan küldhet részletes leküldéses értesítéseket egy iOS-alkalmazásba az Azure-ból. A Objective-C és C# nyelven írt példák.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 33626b7aee615d07ef88dd9fbca46e6512e2cafc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90090363"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure Notification Hubs Rich push

## <a name="overview"></a>Áttekintés

Ha szeretné, hogy a felhasználók Instant Rich Content szolgáltatással legyenek elérhetők, előfordulhat, hogy egy alkalmazás az egyszerű szöveg fölé szeretné leküldeni. Ezek az értesítések népszerűsítik a felhasználói interakciókat, és olyan tartalmakat mutatnak be, mint az URL-címek, hangok, képek/kuponok stb. Ez az oktatóanyag a [felhasználók értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyagra épül, és bemutatja, hogyan küldhet leküldéses értesítéseket a hasznos adatokkal (például képekkel).

Ez az oktatóanyag kompatibilis az iOS 7-es és 8-as verzióval.

  ![Három képernyőkép: egy alkalmazás képernyőjén egy leküldéses gomb, egy eszközön lévő kezdőképernyő és egy vissza gombbal rendelkező Windows-embléma.][IOS1]

Magas szinten:

1. Az alkalmazás háttere:
   * A részletes adattartalom (ebben az esetben a rendszerkép) tárolása a háttér-adatbázis/helyi tárolóban.
   * A gazdag értesítés AZONOSÍTÓjának küldése az eszközre.
2. Alkalmazás az eszközön:
   * A gazdag adattartalomot kérő háttérrel kapcsolatba lép a kapott AZONOSÍTÓval.
   * A felhasználók értesítéseit küldi az eszközön, amikor az adatok beolvasása befejeződött, és azonnal megjeleníti a hasznos adatokat, amikor a felhasználók további információra koppintanak.

## <a name="webapi-project"></a>WebAPI projekt

1. A Visual Studióban nyissa meg a [felhasználók értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyagban létrehozott **AppBackend** projektet.
2. Szerezze be azt a rendszerképet, amelyet a felhasználók értesítésére szeretne küldeni, majd a projekt könyvtára egy **IMG** mappájába helyezi.
3. Kattintson a Megoldáskezelő **összes fájl megjelenítése** elemre, majd kattintson a jobb gombbal a **projektbe felvenni** kívánt mappára.
4. Ha a rendszerkép be van jelölve, a **Tulajdonságok** ablakban módosítsa a **kiépítési műveletet** a **beágyazott erőforrás** elemre.

    ![Képernyőkép a Megoldáskezelőról. A képfájl ki van választva, és a tulajdonságok ablaktáblájában a beágyazott erőforrás a Build műveletként jelenik meg.][IOS2]
5. A alkalmazásban `Notifications.cs` adja hozzá a következő `using` utasítást:

    ```csharp
    using System.Reflection;
    ```

6. Cserélje le az `Notifications` osztályt a következő kódra. Ügyeljen arra, hogy a helyőrzőket cserélje le az értesítési központ hitelesítő adataival és a képfájl nevével:

    ```csharp
    public class Notification {
        public int Id { get; set; }
        // Initial notification message to display to users
        public string Message { get; set; }
        // Type of rich payload (developer-defined)
        public string RichType { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
        }

        public Notification CreateNotification(string message, string richType, string payload) {
            var notification = new Notification() {
                Id = notifications.Count,
                Message = message,
                RichType = richType,
                Payload = payload,
                Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Stream ReadImage(int id) {
            var assembly = Assembly.GetExecutingAssembly();
            // Placeholder: image file name (for example, logo.png).
            return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
        }
    }
    ```

7. A alkalmazásban `NotificationsController.cs` a következő kóddal újra kell definiálni `NotificationsController` . Ez egy kezdeti csendesen gazdag értesítési azonosítót küld az eszköznek, és lehetővé teszi a rendszerkép ügyféloldali lekérését:

    ```csharp
    // Return http response with image binary
    public HttpResponseMessage Get(int id) {
        var stream = Notifications.Instance.ReadImage(id);

        var result = new HttpResponseMessage(HttpStatusCode.OK);
        result.Content = new StreamContent(stream);
        // Switch in your image extension for "png"
        result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

        return result;
    }

    // Create rich notification and send initial silent notification (containing id) to client
    public async Task<HttpResponseMessage> Post() {
        // Replace the placeholder with image file name
        var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // Silent notification with content available
        var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

        // Send notification to apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

8. Most telepítse újra az alkalmazást egy Azure-webhelyre, hogy az minden eszközről elérhető legyen. Kattintson jobb gombbal az **AppBackend** projektre, és válassza a **Publish** (Közzététel) lehetőséget.
9. A közzétételi célként válassza az **Azure-webhely** lehetőséget. Jelentkezzen be az Azure-fiókjával, és válasszon ki egy meglévő vagy új webhelyet, és jegyezze fel a **cél URL** -tulajdonságot a **kapcsolatok** lapon. Ebben az oktatóanyagban a *háttérbeli végpontként* erre az URL-címre fogunk hivatkozni. Válassza a **Közzététel** lehetőséget.

## <a name="modify-the-ios-project"></a>Az iOS-projekt módosítása

Most, hogy módosította az alkalmazás-hátteret, hogy csak az értesítés *azonosítóját* küldje el, módosítsa az iOS-alkalmazást az azonosító kezelésére, és olvassa be a részletes üzenetet a háttérből:

1. Nyissa meg az iOS-projektet, és engedélyezze a távoli értesítéseket a **célok** szakaszának fő alkalmazási céljával.
2. Válassza a **képességek**, a **háttér mód** engedélyezése lehetőséget, és jelölje be a **távoli értesítések** jelölőnégyzetet.

    ![Képernyőfelvétel a képességek képernyőt bemutató iOS-projektről. A háttérben lévő üzemmód be van kapcsolva, és a távoli értesítések jelölőnégyzet be van jelölve.][IOS3]
3. Nyissa meg `Main.storyboard` , és ellenőrizze, hogy rendelkezik-e a [felhasználó értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) című oktatóanyagban, és győződjön meg róla, hogy rendelkezik-e a megtekintés vezérlővel (ez az oktatóanyag a Kezdőlap nézet
4. Vegyen fel egy **navigációs vezérlőt** a történetbe, és a vezérlő húzásával húzza a Kezdőlap nézet vezérlőjét, hogy a **legfelső szintű nézetet** adja meg a navigáláshoz. Győződjön meg arról, hogy a csak a navigációs vezérlőhöz van kiválasztva az attribútumok ellenőrének **kezdeti nézet vezérlője** .
5. Adjon hozzá egy **nézet vezérlőt** a történethez, és adjon hozzá egy **képnézetet**. Ez az oldal a felhasználók számára jelenik meg, ha úgy dönt, hogy további információkat szeretne megtudni az értesítésre kattintva. A történetnek a következőképpen kell kinéznie:

    ![Képernyőkép egy történetről. Három alkalmazás-képernyő látható: egy navigációs nézet, egy otthoni nézet és egy Képnézet.][IOS4]
6. Kattintson a történet **Kezdőlap nézet vezérlőjére** , és győződjön meg róla, hogy a **homeViewController** az Identity Inspector alatt **Egyéni OSZTÁLYként** és **storyboard-azonosítóként** rendelkezik.
7. Tegye ugyanezt a Képnézet vezérlőre, mint **imageViewController**.
8. Ezután hozzon létre egy új, **imageViewController** nevű View Controller-osztályt az imént létrehozott felhasználói felület kezeléséhez.
9. A **imageViewController. h**-ben adja hozzá a következő kódot a vezérlő Interface deklarációjában. Ügyeljen rá, hogy a két hely összekapcsolásához húzza a storyboard képnézetből a következő tulajdonságokat:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```

10. A-ben `imageViewController.m` adja hozzá a következőt a végén `viewDidload` :

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```

11. `AppDelegate.m`Importálja a létrehozott rendszerkép-vezérlőt:

    ```objc
    #import "imageViewController.h"
    ```

12. Vegyen fel egy Interface szakaszt a következő deklarációval:

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```

13. A ben `AppDelegate` ellenőrizze, hogy az alkalmazás regisztrálja-e a csendes értesítéseket a következőben `application: didFinishLaunchingWithOptions` :

    ```objc
    // Software version
    self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

    // Register for remote notifications for iOS8 and previous versions
    if (self.iOS8) {
        NSLog(@"This device is running with iOS8.");

        // Action
        UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
        richPushAction.identifier = @"richPushMore";
        richPushAction.activationMode = UIUserNotificationActivationModeForeground;
        richPushAction.authenticationRequired = NO;
        richPushAction.title = @"More";

        // Notification category
        UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
        richPushCategory.identifier = @"richPush";
        [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

        // Notification categories
        NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge
                                                                                    categories:richPushCategories];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    }
    else {
        // Previous iOS versions
        NSLog(@"This device is running with iOS7 or earlier versions.");

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    }

    return YES;
    ```

14. Helyettesítse be a következő implementációját `application:didRegisterForRemoteNotificationsWithDeviceToken` , hogy a történet felhasználói felületének változásait vegye figyelembe:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```

15. Ezután adja hozzá a következő metódusokat a `AppDelegate.m` rendszerkép lekéréséhez a végpontból, és küldjön helyi értesítést a beolvasás befejezésekor. Ügyeljen arra, hogy a helyőrzőt a `{backend endpoint}` háttérbeli végponttal helyettesítse:

    ```objc
    NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

    // Helper: retrieve notification content from backend with rich notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
        // Check if authenticated
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                delegate:nil
                                delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200) {
                // From NSData to UIImage
                self.imagePayload = [UIImage imageWithData:data];

                completion(nil);
            }
            else {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(error);
                else {
                    completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    // Handle silent push notifications when id is sent from backend
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
        self.userInfo = userInfo;
        int richId = [[self.userInfo objectForKey:@"richId"] intValue];
        NSString* richType = [self.userInfo objectForKey:@"richType"];

        // Retrieve image data
        if ([richType isEqualToString:@"img"]) {  
            [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                if (!error){
                    // Send local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                    // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                    localNotification.userInfo = self.userInfo;
                    localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];

                    // iOS8 categories
                    if (self.iOS8) {
                        localNotification.category = @"richPush";
                    }

                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    handler(UIBackgroundFetchResultNewData);
                }
                else{
                    handler(UIBackgroundFetchResultFailed);
                }
            }];
        }
        // Add "else if" here to handle more types of rich content such as url, sound files, etc.
    }
    ```

16. A következő módszerekkel kezelheti az előző helyi értesítést úgy, hogy megnyitja a rendszerkép nézet vezérlőjét `AppDelegate.m` :

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
        imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
        // Pass data/image to image view controller
        imgViewController.imagePayload = img;

        // Redirect
        [navigationController pushViewController:imgViewController animated:YES];
    }

    // Handle local notification sent above in didReceiveRemoteNotification
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        if (application.applicationState == UIApplicationStateActive) {
            // Show in-app alert with an extra "more" button
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
            [alert show];
        }
        // App becomes active from user's tap on notification
        else {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
    }

    // Handle buttons in in-app alerts and redirect with data/image
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
        // Handle "more" button
        if (buttonIndex == 1)
        {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        // Add "else if" here to handle more buttons
    }

    // Handle notification setting actions in iOS8
    - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
        // Handle richPush related buttons
        if ([identifier isEqualToString:@"richPushMore"]) {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        completionHandler();
    }
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

1. A XCode-ben futtassa az alkalmazást egy fizikai iOS-eszközön (a leküldéses értesítések nem fognak működni a szimulátorban).
2. Az iOS-alkalmazás felhasználói felületén adja meg a hitelesítéshez tartozó felhasználónevet és jelszót, majd kattintson a **Bejelentkezés** elemre.
3. Kattintson a **leküldéses küldés** lehetőségre, és megjelenik az alkalmazáson belüli riasztás. Ha a **további** gombra kattint, a rendszer az alkalmazás-háttérbe felvenni kívánt képet fogja elhelyezni.
4. Kattintson a **leküldés küldése** gombra, és azonnal nyomja meg az eszköz Home (Kezdőlap) gombját. Néhány pillanat múlva leküldéses értesítést fog kapni. Ha rákoppint, vagy kattintson a továbbiak lehetőségre, a rendszer az alkalmazást és a gazdag képtartalmat fogja megtekinteni.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
