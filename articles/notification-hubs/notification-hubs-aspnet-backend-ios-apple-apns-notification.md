---
title: Leküldéses értesítések küldése adott felhasználóknak az Azure Notification Hubs használatával | Microsoft Docs
description: Megtudhatja, hogyan küldhet leküldéses értesítéseket adott iOS-felhasználóknak az Azure Notification Hubs használatával.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 167c666c536ee33531fd069dbd1edb530331a9f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016945"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Oktatóanyag: leküldéses értesítések küldése adott felhasználóknak az Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Az oktatóanyag bemutatja, hogy hogyan küldhetők leküldéses értesítések adott alkalmazásfelhasználónak, adott eszközre az Azure Notification Hubs használatával. A ASP.NET-WebAPI háttérrendszer az ügyfelek hitelesítésére és értesítések létrehozására szolgál, ahogyan az az [alkalmazás-háttérbeli regisztrációban](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)szereplő útmutató témakörben látható.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * A WebAPI-projekt létrehozása
> * Ügyfelek hitelesítése a WebAPI háttérrendszeren
> * Regisztráció értesítésekre a WebAPI háttérrendszer használatával
> * Értesítések küldése a WebAPI háttérrendszerről
> * Az új WebAPI háttérrendszer közzététele
> * IOS-alkalmazás módosítása
> * Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy létrehozta és konfigurálta az értesítési központot a [leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs használatával](ios-sdk-get-started.md)című cikkben leírtak szerint. Ez az oktatóanyag a [biztonságos leküldéses (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) oktatóanyag előfeltétele is.
Ha Mobile Apps háttér-szolgáltatásként szeretné használni, tekintse meg a [Mobile apps Ismerkedés a leküldéssel](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)című témakört.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>IOS-alkalmazás módosítása

1. Nyissa meg a [leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs oktatóanyag használatával](ios-sdk-get-started.md) létrehozott Egyoldalas nézet alkalmazást.

   > [!NOTE]
   > Ez a szakasz azt feltételezi, hogy a projekt üres nevű szervezettel van konfigurálva. Ha nem, adja meg a szervezet nevét az összes osztály nevéhez.

2. A `Main.storyboard` fájlban adja hozzá az objektum könyvtárából a képernyőképen látható összetevőket.

    ![Storyboard szerkesztése a Xcode Interface Builder-ben][1]

   * **Username**: helyőrző szöveggel rendelkező UITextField, *írja be a felhasználónevet*, közvetlenül a küldési eredmények címkéje alatt, és a bal és a jobb oldali margóra, valamint az eredmények küldése címke alá.
   * **Password (jelszó**): helyőrző szöveggel rendelkező UITextField, *írja be a jelszót*, közvetlenül a Felhasználónév szövegmező alá, és korlátozza a bal és a jobb oldali margókat, valamint a Felhasználónév szövegmező alatt. Tekintse meg az attribútum-ellenőr **biztonságos szövegbeviteli** beállítását a *visszatérési kulcs* területen.
   * **Bejelentkezés**: közvetlenül a jelszó szövegmezője alatt megjelenő UIButton, és **törölje a** jelölést az attribútumok ellenőrben a *vezérlés – tartalom* területen.
   * **WNS**: címke és kapcsoló, amely lehetővé teszi az értesítési Windows értesítési szolgáltatás küldését, ha az a központban lett beállítva. Tekintse meg a [Windows első lépések](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) oktatóanyagot.
   * **GCM**: címke és kapcsoló, amely lehetővé teszi az értesítés küldését Google Cloud Messaging, ha be lett állítva a központban. Lásd: [Android első lépések](notification-hubs-android-push-notification-google-gcm-get-started.md) oktatóanyag.
   * **APNS**: címke és kapcsoló, amely lehetővé teszi az értesítés küldését az Apple platform Notification Service-be.
   * **Címzett felhasználóneve:** helyőrző szöveggel rendelkező UITextField, *címzett felhasználónevet jelölő címke*, közvetlenül a GCM címke alatt, a bal és a jobb oldali margók, valamint a GCM-címke alatt van korlátozva.

     Néhány összetevőt hozzáadtak a [leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs oktatóanyag használatával](ios-sdk-get-started.md) .

3. A **CTRL billentyűt lenyomva** tartva adja hozzá a kívánt összetevőket az `ViewController.h` alábbi új lehetőségek eléréséhez:

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. A alkalmazásban `ViewController.h` adja hozzá a következőt az `#define` importálási utasítások után. Helyettesítse be a `<Your backend endpoint>` helyőrzőt az alkalmazás-backend az előző szakaszban való üzembe helyezéséhez használt célként megadott URL-címmel. Például `http://your_backend.azurewebsites.net` :

    ```objc
    #define BACKEND_ENDPOINT @"<Your backend endpoint>"
    ```

5. A projektben hozzon létre egy nevű új kakaó Touch osztályt a `RegisterClient` ASP.net felülettel, amelyet Ön hozott létre. Hozza létre az osztály öröklését `NSObject` . Ezután adja hozzá a következő kódot a alkalmazásban `RegisterClient.h` :

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. A alkalmazásban `RegisterClient.m` frissítse a `@interface` szakaszt:

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. Cserélje le az `@implementation` RegisterClient. m szakaszban található szakaszt a következő kódra:

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    Ez a kód a NSURLSession használatával az alkalmazás [-háttérből való regisztráláskor](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) az útmutatóban ismertetett logikát valósítja meg, és az NSUserDefaults az értesítési központ által visszaadott regisztrációban helyileg tárolja.

    Ehhez az osztályhoz a tulajdonságot `authorizationHeader` be kell állítani ahhoz, hogy megfelelően működjön. Ezt a tulajdonságot a `ViewController` Bejelentkezés után az osztály állítja be.

8. A alkalmazásban `ViewController.h` adjon hozzá egy `#import` utasítást a következőhöz: `RegisterClient.h` . Ezután adjon hozzá egy deklarációt az eszköz jogkivonatához, és hivatkozzon a `RegisterClient` szakasz egyik példányára `@interface` :

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. A ViewController. m-ben adjon hozzá egy privát metódus deklarációt a `@interface` szakaszban:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > A következő kódrészlet nem biztonságos hitelesítési séma, a-    `createAndSetAuthenticationHeaderWithUsername:AndPassword:` t az adott hitelesítési mechanizmussal kell helyettesítenie, amely létrehoz egy hitelesítési jogkivonatot, amelyet a regisztrációs ügyfél osztály használ fel, például OAuth, Active Directory.

10. Ezután `@implementation` `ViewController.m` adja hozzá a következő kódot a szakaszához, amely hozzáadja az eszköz-jogkivonat és a hitelesítési fejléc beállításának megvalósítását.

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    Figyelje **meg,** hogy az eszköz jogkivonatának beállítása lehetővé teszi a bejelentkezés gombot. Ez azért van, mert a bejelentkezési művelet részeként a nézet vezérlő regisztrálja a leküldéses értesítéseket az alkalmazás-háttérrel. Nem szeretné, hogy a **bejelentkezési** művelet csak akkor legyen elérhető, ha az eszköz jogkivonatát megfelelően beállította. A leküldéses regisztrációból leválaszthatja a bejelentkezést, amennyiben az előbbi az utóbbi előtt történik.

11. A ViewController. m-ben használja a következő kódrészleteket a **bejelentkezési** gombhoz tartozó műveleti módszer megvalósításához, valamint egy metódust az értesítési üzenet elküldéséhez a ASP.net háttér használatával.

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. Frissítse az **Értesítés küldése** gombra vonatkozó műveletet a ASP.net háttérrendszer használatához, és küldjön egy kapcsoló által engedélyezett PNS.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. A `ViewDidLoad` függvényben adja hozzá a következőt a példány létrehozásához, `RegisterClient` és állítsa be a szöveges mezők delegált értékét.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Ezután `AppDelegate.m` távolítsa el a metódus összes tartalmát, `application:didRegisterForPushNotificationWithDeviceToken:` és cserélje le a következőre (annak ellenőrzéséhez, hogy a vezérlő tartalmazza-e a APNs által lekért legújabb eszköz tokent):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Végül a-ben `AppDelegate.m` ellenőrizze, hogy rendelkezik-e a következő módszerrel:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

1. A XCode-ben futtassa az alkalmazást egy fizikai iOS-eszközön (a leküldéses értesítések nem működnek a szimulátorban).
2. Az iOS-alkalmazás felhasználói felületén adja meg ugyanazt az értéket a Felhasználónév és a jelszó mezőben. Ezután kattintson **a bejelentkezés** elemre.

    ![iOS-teszt alkalmazás][2]

3. Ekkor megjelenik egy előugró ablak, amely tájékoztatja a regisztráció sikerességéről. Kattintson az **OK** gombra.

    ![iOS-teszt értesítés megjelenítve][3]

4. A **címzett felhasználóneve címke* szövegmezőbe írja be a regisztrációhoz használt felhasználónevet jelölő címkét egy másik eszközről.
5. Adjon meg egy értesítési üzenetet, és kattintson az **Értesítés küldése** gombra. Csak azok az eszközök kapják meg az értesítési üzenetet, amelyek regisztrálva vannak a címzett felhasználóneve címkével. Csak ezekre a felhasználókra lesz küldve.

    ![iOS-teszt címkézett értesítése][4]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban elsajátította, hogy hogyan küldhet leküldéses értesítéseket olyan adott felhasználóknak, akik a regisztrációjukhoz társított címkével rendelkeznek. Ha szeretné megtudni, hogy hogyan küldhet helyalapú értesítéseket, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Helyalapú leküldéses értesítések küldése](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
