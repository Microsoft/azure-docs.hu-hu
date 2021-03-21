---
title: Azure Notification Hubs biztonságos leküldés Windows rendszerre
description: Ismerje meg, hogyan küldhet biztonságos leküldéses értesítéseket az Azure-ban. A kódminták C# nyelven íródtak, a .NET API használatával.
author: sethmanheim
manager: femila
editor: thsomasu
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 98e587103e63cd5cc26eab5b00864d00e0b9007f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019420"
---
# <a name="send-secure-push-notifications-from-azure-notification-hubs"></a>Biztonságos leküldéses értesítések küldése az Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Áttekintés

A Microsoft Azure leküldéses értesítések támogatása lehetővé teszi egy könnyen használható, többplatformos, kibővített leküldéses infrastruktúra elérését, amely nagy mértékben leegyszerűsíti a leküldéses értesítések megvalósítását mind a fogyasztói, mind a vállalati alkalmazások számára a mobil platformokhoz.

A szabályozási vagy biztonsági korlátozások miatt előfordulhat, hogy egy alkalmazás az értesítésben olyan dolgot is tartalmaz, amely nem továbbítható a szabványos leküldéses értesítési infrastruktúrán keresztül. Ez az oktatóanyag leírja, hogyan érheti el ugyanezt a élményt úgy, hogy bizalmas adatokat küld biztonságos, hitelesített kapcsolaton keresztül az ügyfél és az alkalmazás háttere között.

Magas szinten a folyamat a következő:

1. Az alkalmazás háttérrendszer:
   * Biztonságos adattartalmat tárol a háttér-adatbázisban.
   * Elküldi az értesítés AZONOSÍTÓját az eszköznek (nincs biztonságos információ elküldve).
2. Az alkalmazás az eszközön az értesítés fogadásakor:
   * Az eszköz kapcsolatba lép a biztonságos adattartalmat kérő háttérrel.
   * Az alkalmazás értesítésként jeleníti meg a hasznos adatokat az eszközön.

Fontos megjegyezni, hogy az előző folyamat során (és ebben az oktatóanyagban) feltételezzük, hogy az eszköz helyi tárolóban tárolja a hitelesítési jogkivonatot, miután a felhasználó bejelentkezik. Ez teljesen zökkenőmentes élményt biztosít, mivel az eszköz a token használatával lekérheti az értesítés biztonságos hasznos adatait. Ha az alkalmazás nem tárolja a hitelesítési jogkivonatokat az eszközön, vagy ha ezek a jogkivonatok elévülnek, akkor az értesítés fogadásakor a felhasználónak az alkalmazás elindítását kérő általános értesítésnek kell megjelennie. Az alkalmazás ezután hitelesíti a felhasználót, és megjeleníti az értesítési adattartalmat.

Ebből az oktatóanyagból megtudhatja, hogyan küldhet biztonságos leküldéses értesítéseket. Az oktatóanyag a [felhasználók értesítése](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) oktatóanyagra épül, ezért először végre kell hajtania az oktatóanyag lépéseit.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy létrehozta és konfigurálta az értesítési központot az [értesítések küldése univerzális Windows-platform alkalmazásokba](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)című témakörben leírtak szerint.
> Azt is vegye figyelembe, hogy Windows Phone-telefon 8,1 Windows (nem Windows Phone-telefon) hitelesítő adatokat igényel, és a háttérben futó feladatok nem működnek Windows Phone-telefon 8,0 vagy Silverlight 8,1 rendszeren. A Windows áruházbeli alkalmazásokhoz csak akkor fogadhat értesítéseket egy háttérbeli feladaton keresztül, ha az alkalmazás engedélyezve van a zárolási képernyőn (kattintson a jelölőnégyzetre a Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>A Windows Phone-telefon projekt módosítása

1. A **NotifyUserWindowsPhone** projektben adja hozzá a következő kódot az app. XAML. cs számára a leküldéses háttér-feladat regisztrálásához. Szúrja be az alábbi kódsort az `OnLaunched()` metódus végén:

    ```csharp
    RegisterBackgroundTask();
    ```

2. Még mindig az app. XAML. cs-ban adja hozzá a következő kódot közvetlenül a `OnLaunched()` metódus után:

    ```csharp
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```

3. Adja hozzá a következő `using` utasításokat az app. XAML. cs fájl elejéhez:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```

4. A Visual Studio **File** (Fájl) menüjében kattintson az **Save All** (Összes mentése) parancsra.

## <a name="create-the-push-background-component"></a>A leküldéses háttér összetevő létrehozása

A következő lépés a leküldéses háttér-összetevő létrehozása.

1. Megoldáskezelő kattintson a jobb gombbal a megoldás legfelső szintű csomópontjára (ebben az esetben a **megoldás SecurePush** ), majd kattintson a **Hozzáadás**, majd az **új projekt** elemre.
2. Bontsa ki az **áruházbeli alkalmazások** csomópontot, majd kattintson az **alkalmazások Windows Phone-telefon**, majd a **Windows-futtatókörnyezet összetevő (Windows Phone-telefon)** elemre. Nevezze el a projekt **PushBackgroundComponent**, majd kattintson az **OK** gombra a projekt létrehozásához.

    ![Képernyőfelvétel az új projekt hozzáadása párbeszédpanelről a Windows-futtatókörnyezet Component (Windows Phone-telefon) Visual C# kapcsoló kiemelve.][12]
3. Megoldáskezelő kattintson a jobb gombbal a **PushBackgroundComponent (Windows Phone-telefon 8,1)** projektre, majd kattintson a **Hozzáadás**, majd az **osztály** elemre. Nevezze el az új osztályt `PushBackgroundTask.cs` . Az osztály létrehozásához kattintson a **Hozzáadás** gombra.
4. Cserélje le a névtér teljes tartalmát a `PushBackgroundComponent` következő kódra, és helyettesítse be a helyőrzőt `{back-end endpoint}` a háttérrendszer üzembe helyezése során kapott háttér-végponttal:

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```

5. Megoldáskezelő kattintson a jobb gombbal a **PushBackgroundComponent (Windows Phone-telefon 8,1)** projektre, majd kattintson a **NuGet-csomagok kezelése** elemre.
6. A képernyő bal oldalán kattintson az **Online** lehetőségre.
7. A **Search** (Keresés) mezőbe írja be a **Http Client** (HTTP-ügyfél) kifejezést.
8. Az eredmények listájában kattintson a **Microsoft http-ügyféloldali kódtárak** elemre, majd kattintson a **telepítés** gombra. Fejezze be a telepítést.
9. A NuGet **Search** (Keresés) mezőjébe írja be a **Json.net** kifejezést. Telepítse a **JSON.net** csomagot, majd lépjen be a NuGet csomagkezelő ablakba.
10. Adja hozzá a következő `using` utasításokat a fájl elejéhez `PushBackgroundTask.cs` :

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```

11. Megoldáskezelő a **NotifyUserWindowsPhone (Windows Phone-telefon 8,1)** projektben kattintson a jobb gombbal a **hivatkozások** elemre, majd kattintson a **hivatkozás hozzáadása..**. elemre. A Reference Manager párbeszédpanelen jelölje be a **PushBackgroundComponent** melletti jelölőnégyzetet, majd kattintson az **OK** gombra.
12. A Megoldáskezelő kattintson duplán a **Package. appxmanifest** elemre a **NotifyUserWindowsPhone (Windows Phone-telefon 8,1)** projektben. Az **értesítések** területen állítsa a **pirítóst** **Igen** értékre.

    ![Képernyőkép az Megoldáskezelő ablakról a csomagra összpontosítva. a appxmanifest a Toast-kompatibilis beállítás értéke Igen, a piros színnel van kiválasztva.][3]
13. Továbbra is a **Package. appxmanifest**, kattintson a felül található **deklarációk** menüre. Az **elérhető deklarációk** legördülő menüben kattintson a **háttér-feladatok** elemre, majd a **Hozzáadás** gombra.
14. A **Package. appxmanifest** területen a **Tulajdonságok** alatt keresse le a **leküldéses értesítést**.
15. A **Package. Appxmanifest** **alkalmazásban az Alkalmazásbeállítások** területen írja be a **PushBackgroundComponent. PushBackgroundTask** értéket a **belépési pont** mezőbe.

    ![A csomagra összpontosító Megoldáskezelő ablak képernyőképe. a appxmanifest a rendelkezésre álló deklarációkkal, a támogatott deklarációkkal, a leküldéses értesítésekkel és a beléptetési pontokkal kapcsolatos, piros színnel megadott beállításokkal.][13]
16. Kattintson a **File** (Fájl) menü **Save All** (Összes mentése) elemére.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához tegye a következőket:

1. A Visual Studióban futtassa a **AppBackend** web API alkalmazást. Megjelenik egy ASP.NET weblap.
2. A Visual Studióban futtassa a **NotifyUserWindowsPhone (Windows Phone-telefon 8,1)** Windows Phone-telefon alkalmazást. Az Windows Phone-telefon-emulátor futtatja és automatikusan betölti az alkalmazást.
3. A **NotifyUserWindowsPhone** alkalmazás felhasználói felületén adja meg a felhasználónevet és a jelszót. Ezek bármilyen sztringek lehetnek, de az értékeknek is szerepelniük kell.
4. A **NotifyUserWindowsPhone** alkalmazás felhasználói felületén kattintson a **Bejelentkezés és regisztráció** elemre. Ezután kattintson a **leküldés küldése** gombra.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
