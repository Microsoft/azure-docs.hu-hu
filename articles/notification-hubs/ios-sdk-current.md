---
title: Leküldéses értesítések küldése iOS-re az Azure Notification Hubs és az iOS SDK Version 3.0.0 Preview 1 használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan használható az Azure Notification Hubs és az Apple push Notification szolgáltatás leküldéses értesítések küldésére iOS-eszközökre (3.0.0-Preview1).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: f905bfa830bfc78caa6ebb02ae49d4839168367b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100627816"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-sdk-for-apple"></a>Oktatóanyag: leküldéses értesítések küldése iOS-alkalmazásokba az Apple Azure Notification Hubs SDK használatával

Ez az oktatóanyag bemutatja, hogyan küldhet leküldéses értesítéseket iOS-alkalmazásokba az Azure Notification Hubs használatával az Apple Azure Notification Hubs SDK-val.

Ez az oktatóanyag a következő lépéseken vezet végig:

- Hozzon létre egy minta iOS-alkalmazást.
- Az iOS-alkalmazás csatlakoztatható az Azure Notification Hubshoz.
- Teszt leküldéses értesítések küldése.
- Ellenőrizze, hogy az alkalmazás fogad-e értesítéseket.

Az oktatóanyag teljes kódját letöltheti a [githubról](https://github.com/Azure/azure-notificationhubs-ios/tree/main/SampleNHAppObjC).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő előfeltételekre lesz szüksége:

- Egy [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)-t futtató Mac, valamint egy érvényes, a kulcstartóba telepített fejlesztői tanúsítvány.
- Egy iPhone vagy iPad, amely az iOS 10-es vagy újabb verzióját futtatja.
- A fizikai eszköz regisztrálva van az [Apple Portalon](https://developer.apple.com/), és a tanúsítványhoz van társítva.

Mielőtt továbblépne, mindenképpen tekintse át az [Azure Notification Hubs iOS-alkalmazásokkal](ios-sdk-get-started.md)való használatáról szóló oktatóanyagot, amellyel beállíthatja és konfigurálhatja a leküldéses hitelesítő adatokat az értesítési központban. Ha még nem rendelkezik az iOS-fejlesztéssel kapcsolatos korábbi tapasztalatokkal, kövesse az alábbi lépéseket.

> [!NOTE]
> A leküldéses értesítések konfigurációs követelményei miatt a leküldéses értesítéseket az iOS-emulátor helyett fizikai iOS-eszközön (iPhone vagy iPad) kell üzembe helyeznie és tesztelni.

## <a name="connect-your-ios-app-to-notification-hubs"></a>iOS-alkalmazás összekapcsolása a Notification Hubs szogáltatással

1. Az Xcode-ban hozzon létre egy új iOS-projektet, és válassza az **Single View Application** (Egynézetes alkalmazás) sablont.

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Sablon kiválasztása":::

2. Amikor megadja az új projekt beállításait, győződjön meg arról, hogy a **Terméknév** és a **Szervezetazonosító** ugyanaz, mint amit az Apple fejlesztői portálján a csomagazonosító beállításakor használt.

3. A Project Navigator alatt válassza ki a projekt nevét a **célok** területen, majd válassza az **aláírási & képességek** lapot. Győződjön meg arról, hogy a megfelelő **csapatot** választotta Apple Developer-fiókjához. Az Xcode a csomagazonosító alapján automatikusan lekéri a korábban létrehozott kiépítési profilt.

   Ha nem jelenik meg az Xcode-ban létrehozott új létesítési profil, frissítse az aláíró identitása profiljait. A menüsoron kattintson az **Xcode** elemre, majd a **Preferences** (Beállítások) lehetőségre, az **Account** (Fiók) lapra és a **View Details** (Részletek megtekintése) gombra. Ezután kattintson az aláírási identitására, majd kattintson a frissítési gombra a jobb alsó sarokban.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Részletek megtekintése":::

4. Az **aláírási & képességek** lapon válassza a **+ képesség** lehetőséget. Az engedélyezéséhez kattintson duplán a **leküldéses értesítések** elemre.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Képesség":::

5. Adja hozzá az Azure Notification Hubs SDK-modulokat.

   Az Azure Notification Hubs SDK-t integrálhatja az alkalmazásba a [Cocoapods](https://cocoapods.org/) használatával, vagy manuálisan is hozzáadhatja a bináris fájlokat a projekthez.

   - Integráció a Cocoapods-on keresztül: adja hozzá a következő függőségeket a cocoapods, hogy tartalmazza az Azure Notification Hubs SDK-t az alkalmazásban:

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Futtassa a pod installt az újonnan definiált Pod telepítéséhez, és nyissa meg a. xcworkspace.

         Ha olyan hibaüzenetet lát, amely **nem találja a AzureNotificationHubs-iOS-hez készült specifikációt** a pod telepítés futtatása közben, futtassa a parancsot a `pod repo update` Cocoapods-tárház legújabb hüvelyének beszerzéséhez, majd futtassa a pod install parancsot.

   - Integráció a Carthage használatával: adja hozzá a következő függőségeket a Cartfile, hogy tartalmazza az Azure Notification Hubs SDK-t az alkalmazásban:

      ```ruby
      github "Azure/azure-notificationhubs-ios"
      ```

      - Következő lépésként frissítse a Build függőségeit:

      ```shell
      $ carthage update
      ```

      A Carthage használatával kapcsolatos további információkért tekintse meg a [Carthage GitHub-tárházat](https://github.com/Carthage/Carthage).

   - Integráció a bináris fájlok a projektbe való másolásával:

      A bináris fájlok a következő módon történő másolásával integrálható a projektbe:

        - Töltse le a zip-fájlként megadott [Azure Notification HUBS SDK](https://github.com/Azure/azure-notificationhubs-iOS/releases/) -keretrendszert, és csomagolja ki.

        - Az Xcode-ban kattintson a jobb gombbal a projektjére, majd kattintson az **Add Files to** (Fájlok hozzáadása a következőhöz:) lehetőségre a **WindowsAzureMessaging.framework** mappa az Xcode-projektjéhez adásához. Válassza a **Beállítások** lehetőséget, és győződjön meg arról, hogy az **Elemek másolása, ha szükséges** elem be van jelölve, majd kattintson a **Hozzáadás** elemre.

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Keretrendszer hozzáadása":::

6. Adjon hozzá vagy szerkesszen egy **DevSettings. plist** nevű fájlt, amely két tulajdonságot tartalmaz, az `CONNECTION_STRING` Azure Notification hub kapcsolódási karakterláncához és az `HUB_NAME` Azure Notification hub neveként.

7. Adja meg az adatokat az Azure Notification Hubshoz való csatlakozáshoz a megfelelő `<string></string>` szakaszban.  Cserélje le a szöveges literál helyőrzőket `--HUB-NAME--` és `--CONNECTION-STRING--` a hub nevét, valamint a **DefaultListenSharedAccessSignature** a portálról korábban beszerzett módon:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>HUB_NAME</key>
        <string>--HUB-NAME--</string>
        <key>CONNECTION_STRING</key>
        <string>--CONNECTION-STRING--</string>
    </dict>
    </plist>
    ```

8. Ugyanebben a **AppDelegate. m** fájlban cserélje le az összes kódot a `didFinishLaunchingWithOptions` következő kód után:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>

    // Extend the AppDelegate to listen for messages using MSNotificationHubDelegate and User Notification Center
    @interface AppDelegate () <MSNotificationHubDelegate>

    @end

    @implementation AppDelegate
    
    @synthesize notificationPresentationCompletionHandler;
    @synthesize notificationResponseCompletionHandler;

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

        NSString *path = [[NSBundle mainBundle] pathForResource:@"DevSettings" ofType:@"plist"];
        NSDictionary *configValues = [NSDictionary dictionaryWithContentsOfFile:path];
        
        NSString *connectionString = [configValues objectForKey:@"CONNECTION_STRING"];
        NSString *hubName = [configValues objectForKey:@"HUB_NAME"];

        if([connectionString length] != 0 && [hubName length] != 0) {
            [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
            [MSNotificationHub setDelegate:self];
            [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];
    
            return YES;
        }

        NSLog(@"Please setup CONNECTION_STRING and HUB_NAME in DevSettings.plist and restart application");

        exit(-1);
    }

    - (void)notificationHub:(MSNotificationHub *)notificationHub didReceivePushNotification:(MSNotificationHubMessage *)message {
        // Send message using NSNotificationCenter with the message
        NSDictionary *userInfo = [NSDictionary dictionaryWithObject:message forKey:@"message"];
        [[NSNotificationCenter defaultCenter] postNotificationName:@"MessageReceived" object:nil userInfo:userInfo];
    }

    @end
    ```

    Ez a kód az **DevSettings. plist** fájlban megadott kapcsolati adatokkal csatlakozik az értesítési központhoz. Ezután megadja az eszköz jogkivonatát az értesítési központnak, így a hub értesítéseket küldhet.

### <a name="create-notificationdetailviewcontroller-header-file"></a>NotificationDetailViewController-fejléc fájljának létrehozása

1. Az előző utasításokhoz hasonlóan adjon hozzá egy másik, **SetupViewController. h** nevű fejlécet. Cserélje le az új header fájl tartalmát a következő kódra:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface SetupViewController : UIViewController

   @end

   NS_ASSUME_NONNULL_END
   ```

2. Adja hozzá a **SetupViewController. m** implementációs fájlt. Cserélje le a fájl tartalmát a következő kódra, amely megvalósítja a UIViewController metódusokat:

   ```objc
   #import "SetupViewController.h"

    static NSString *const kNHMessageReceived = @"MessageReceived";
    
    @interface SetupViewController ()
    
    @end

    @implementation SetupViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        
        // Listen for messages using NSNotificationCenter
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceivePushNotification:) name:kNHMessageReceived object:nil];
    }

    - (void)dealloc {
        // Clean up subscription to NSNotificationCenter
        [[NSNotificationCenter defaultCenter] removeObserver:self name:kNHMessageReceived object:nil];
    }

    - (void)didReceivePushNotification:(NSNotification *)notification {
        MSNotificationHubMessage *message = [notification.userInfo objectForKey:@"message"];

        // Create UI Alert controller with message title and body
        UIAlertController *alertController = [UIAlertController alertControllerWithTitle:message.title
                             message:message.body
                      preferredStyle:UIAlertControllerStyleAlert];
        [alertController addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleCancel handler:nil]];
        [self presentViewController:alertController animated:YES completion:nil];
        
        // Dismiss after 2 seconds
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            [alertController dismissViewControllerAnimated:YES completion: nil];
        });

    }

    @end
   ```

3. Hozza létre és futtassa az alkalmazást az eszközön az esetleges hibák kereséséhez.

## <a name="send-test-push-notifications"></a>Teszt leküldéses értesítések küldése

Az [Azure Portal](https://portal.azure.com/)**Tesztküldés** lehetőségével tesztelheti az alkalmazásban az értesítések fogadását. A parancs egy leküldéses tesztértesítést küld az eszközre.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Küldési teszt":::

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis könyvtár használatával, mint a Mobile Apps vagy az ASP.NET. Ha egy könyvtár nem érhető el a háttérrendszer számára, akkor a REST API közvetlenül is használhatja az értesítési üzenetek küldéséhez.

Az alábbiakban felsorolunk néhány olyan oktatóanyagot, amelyet érdemes lehet áttekinteni az értesítések küldéséhez:

- Azure Mobile Apps: a [leküldéses értesítések hozzáadása iOS-alkalmazáshoz](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)című témakörből megtudhatja, hogyan küldhet értesítéseket az Notification Hubs-vel integrált Mobile apps háttérből.
- ASP.NET: [a Notification Hubs használatával leküldéses értesítéseket küldhet a felhasználóknak](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- Azure Notification Hubs Java SDK: [How to use Notification Hubs from Java](notification-hubs-java-push-notification-tutorial.md) (A Notification Hubs használata Javából) oktatóprogram bemutatja, hogyan küldhetők értesítések a Javával. Ez az Eclipse-ben lett tesztelve Android-fejlesztéshez.
- PHP: [How to use Notification Hubs from PHP](notification-hubs-php-push-notification-tutorial.md) (A Notification Hubs használata PHP-val).

## <a name="verify-that-your-app-receives-push-notifications"></a>Annak ellenőrzése, hogy az alkalmazás fogad-e leküldéses értesítéseket

A leküldéses értesítések iOS rendszeren történő teszteléséhez üzembe kell helyezni az alkalmazást egy fizikai iOS-eszközön. Az iOS-szimulátor használatával nem lehet Apple leküldéses értesítéseket küldeni.

1. Futtassa az alkalmazást és ellenőrizze, hogy a regisztráció sikeres-e, majd nyomja meg az **OK** gombot.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Regisztrálja":::

2. Következő lépésként küldjön egy leküldéses értesítést a [Azure Portalról](https://portal.azure.com/)az előző szakaszban leírtak szerint.

3. A rendszer elküldi a leküldéses értesítést minden olyan eszköznek, amely regisztrálva van az értesítéseknek a megadott értesítési központból való fogadásához.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Teszt küldése":::

## <a name="next-steps"></a>Következő lépések

Ebben az egyszerű példában leküldéses értesítéseket küld a regisztrált iOS-eszközökre. Ha szeretné megtudni, hogyan küldhet leküldéses értesítéseket adott iOS-eszközökre, folytassa a következő oktatóanyaggal:

[Oktatóanyag: leküldéses értesítések adott eszközökre](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

További információért tekintse át a következő cikkeket:

- [Az Azure Notification Hubs áttekintése](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API-k](/rest/api/notificationhubs/)
- [Notification Hubs SDK a háttérbeli műveletekhez](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK a GitHubon](https://github.com/Azure/azure-notificationhubs)
- [Regisztrálás az alkalmazás háttérben](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md)
- [Címkék használata](notification-hubs-tags-segment-push-message.md)
- [Egyéni sablonok használata](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus hozzáférés-vezérlés közös hozzáférési aláírásokkal](../service-bus-messaging/service-bus-sas.md)
- [SAS-tokenek programozott módon történő előállítása](/rest/api/eventhub/generate-sas-token)
- [Apple Security: általános kriptográfia](https://developer.apple.com/security/)
- [UNIX-kor időpontja](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)