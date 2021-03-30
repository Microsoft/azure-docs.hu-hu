---
title: Értesítések küldése adott eszközökre (Univerzális Windows-platform) | Microsoft Docs
description: A regisztráció címkéi révén az Azure Notification Hubs használatával friss híreket küldhet Univerzális Windows-platformalkalmazásokba.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 2c77eba69fd914e8ecc7d08a1b16f61ceefe101b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92320577"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Oktatóanyag: értesítések küldése Univerzális Windows-platform alkalmazást futtató adott eszközökre

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Áttekintés

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Notification Hubst a legfrissebb híreket használó értesítések küldéséhez. Ez az oktatóanyag a Windows áruházat vagy a Windows Phone-telefon 8,1 (nem Silverlight) alkalmazásokat ismerteti. Ha Windows Phone-telefon 8,1 Silverlight-t célozza meg, tekintse meg [a leküldéses értesítések adott Windows Phone-telefon-eszközökhöz az Azure Notification Hubs használatával](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)című témakört.

Ebből az oktatóanyagból megtudhatja, hogyan használható az Azure Notification Hubs egy Univerzális Windows-platform (UWP) alkalmazást futtató adott Windows-eszközökre való leküldéses értesítések küldéséhez. Az oktatóanyag elvégzése után regisztrálhat a legfrissebb híreket tartalmazó kategóriákra. Leküldéses értesítéseket csak ezekre a kategóriákra vonatkozóan kaphat.

A szórási forgatókönyvek engedélyezéséhez vegyen fel egy vagy több *címkét* , ha regisztrációt hoz létre az értesítési központban. Ha az értesítéseket egy címkére küldi, a címkéhez regisztrált összes eszköz megkapja az értesítést. További információ a címkékkel kapcsolatban: [útválasztási és címkézési kifejezések](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> A Windows áruház és a Windows Phone-telefon projekt 8,1-es és korábbi verziói nem támogatottak a Visual Studio 2019-ben. További információ: [Visual Studio 2019 platform Targeting and Compatibility](/visualstudio/releases/2019/compatibility).

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> * Választott kategória hozzáadása a mobilalkalmazáshoz
> * Regisztráció értesítésekre
> * Címkézett értesítések küldése
> * Az alkalmazás futtatása és értesítések létrehozása

## <a name="prerequisites"></a>Előfeltételek

Ezen oktatóanyag megkezdése előtt végezze el az [Oktatóanyag: Értesítések küldése az Univerzális Windows-platformon alapuló alkalmazásoknak az Azure Notification Hubs használatával][get-started] című oktatóanyagot.  

## <a name="add-category-selection-to-the-app"></a>Kategóriaválasztó hozzáadása az alkalmazáshoz

Az első lépésben hozzá kell adni a felhasználói felületi elemeket a meglévő főoldalhoz, amely lehetővé teszi a felhasználó számára a regisztrálni kívánt kategóriák kiválasztását. A kiválasztott kategóriákat az eszköz tárolja. Az alkalmazás indításakor létrehoz egy eszköz-regisztrációt az értesítési központban, és a kiválasztott kategóriák címkével rendelkeznek.

1. Nyissa meg a *Főoldal. XAML* projektfájlt, majd másolja a következő kódot a `Grid` elembe:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

1. **Megoldáskezelő** kattintson a jobb gombbal a projektre, majd válassza az  >  **osztály** hozzáadása elemet. Az **új elem hozzáadása** lapon adja meg az osztály *értesítéseinek* nevét, majd válassza a **Hozzáadás** lehetőséget. Ha szükséges, adja hozzá a `public` módosítót az osztály definícióhoz.

1. Adja hozzá a következő `using` utasításokat az új fájlhoz:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Másolja az alábbi kódot az új `Notifications` osztályba:

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    Ez az osztály a helyi tárolóban tárolja a hírkategóriákat, amelyeket ennek az eszköznek meg kell kapnia. A metódus meghívása helyett `RegisterNativeAsync` hívja `RegisterTemplateAsync` meg a regisztrációt a kategóriákhoz a sablon regisztrációjának használatával.

    Ha egynél több sablont szeretne regisztrálni, adja meg a sablon nevét (például *simpleWNSTemplateExample*). A sablonok elnevezése lehetővé teszi a frissítésüket vagy törlésüket. Előfordulhat, hogy több sablont is regisztrálnia kell, hogy legyen egy a pirítóssal kapcsolatos értesítések és egy a csempék számára.

    >[!NOTE]
    > A Notification Hubs segítségével egy eszköz több sablont is regisztrálhat ugyanazzal a címkével. Ebben az esetben a címkére irányuló bejövő üzenet több értesítést küld az eszköznek, egy pedig minden sablonhoz. Ez a folyamat lehetővé teszi, hogy ugyanazt az üzenetet több vizualizációs értesítésben jelenítse meg, például jelvényként, valamint egy Windows áruházbeli alkalmazásban bejelentési értesítésként.

    További információért lásd a [Sablonok](notification-hubs-templates-cross-platform-push-messages.md) szakaszt.

1. Az *app. XAML. cs* projektfájl mezőben adja hozzá a következő tulajdonságot a `App` osztályhoz:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Ezt a tulajdonságot használja egy példány létrehozásához és eléréséhez `Notifications` .

    A `<hub name>` és a `<connection string with listen access>` helyőrzőt cserélje le a kódban az értesítési központ nevére és a **DefaultListenSharedAccessSignature** korábban beszerzett kapcsolati sztringjére.

   > [!NOTE]
   > Mivel az ügyfélalkalmazással terjesztett hitelesítő adatok általában nem biztonságosak, csak a *figyelési* hozzáférés kulcsát terjessze az ügyfélalkalmazással. A figyelési hozzáférés lehetővé teszi, hogy az alkalmazás regisztráljon értesítésekre, a meglévő regisztrációkat azonban nem lehet módosítani, és értesítéseket sem lehet küldeni. A teljes körű hozzáférési kulcsot egy biztonságos háttérszolgáltatásban használja a rendszer értesítések kiküldésére és a meglévő regisztrációk módosítására.

1. A *Főoldal. XAML. cs* fájlban adja hozzá a következő sort:

    ```csharp
    using Windows.UI.Popups;
    ```

1. A *Főoldal. XAML. cs* fájlban adja hozzá a következő metódust:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    Ez a módszer kategóriákat hoz létre, és a `Notifications` osztály használatával tárolja a listát a helyi tárolóban. Ezenkívül a megfelelő címkéket is regisztrálja az értesítési központban. A kategóriák változásakor a rendszer újból létrehozza a regisztrációt az új kategóriákkal.

Az alkalmazás most már képes egy kategóriakészlet tárolására az eszköz helyi tárterületén. Az alkalmazás regisztrálja az értesítési központban, ha egy felhasználó módosítja a kiválasztott kategóriáit.

## <a name="register-for-notifications"></a>Regisztráció értesítésekre

Ebben a szakaszban elvégzi az értesítési központban való regisztrációt az indításkor, a helyi tárterületen tárolt kategóriák használatával.

> [!NOTE]
> Mivel a Windows Notification Service (WNS) által hozzárendelt csatorna URI bármikor megváltozhat, gyakran regisztráljon az értesítésekre, hogy elkerülhesse az értesítési hibákat. Ebben a példában a rendszer az alkalmazás minden egyes indításakor regisztrál az értesítésekre. A gyakran használt alkalmazások esetében például naponta többször is kihagyhatja a regisztrációt a sávszélesség megőrzése érdekében, ha a korábbi regisztráció óta kevesebb mint egy nap telt el.

1. Ha az `notifications` osztályt a kategóriák alapján szeretné használni, nyissa meg az *app. XAML. cs* fájlt, majd frissítse a `InitNotificationsAsync` metódust.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Ez a folyamat biztosítja, hogy az alkalmazás indításakor beolvassa a kategóriákat a helyi tárolóból. Ezután megkéri a kategóriák regisztrálását. A `InitNotificationsAsync` metódust a küldési értesítések részeként hozta létre [univerzális Windows-platform alkalmazások számára az Azure Notification Hubs oktatóanyag használatával][get-started] .
2. A *Főoldal. XAML. cs* projektfájlt adja hozzá a következő kódot a `OnNavigatedTo` metódushoz:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    Ez a kód frissíti a főoldalt, a korábban mentett kategóriák állapota alapján.

Az alkalmazás ezzel elkészült. Az eszköz helyi tárolójában a kategóriák készlete is tárolható. Ha a felhasználók megváltoztatja a kategória kiválasztását, a rendszer a mentett kategóriákat használja az értesítési központban való regisztráláshoz. A következő szakaszban meghatároz egy háttérrendszert, amely kategóriaértesítéseket küldhet ennek az alkalmazásnak.

## <a name="run-the-uwp-app"></a>A UWP alkalmazás futtatása

1. Az alkalmazás lefordításához és indításához nyomja le az F5 billentyűt a Visual Studióban. Az alkalmazás felhasználói felületén váltógombok segítségével választhatja ki, hogy mely kategóriákra szeretne feliratkozni.

   ![Legfrissebb hírek alkalmazás](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Engedélyezzen egy vagy több kategóriát, majd válassza az **előfizetés** lehetőséget.

   Az alkalmazás címkékké alakítja át a kiválasztott kategóriákat, és új eszközregisztrációt kezdeményez az értesítési központban a kiválasztott címkékre vonatkozóan. Az alkalmazás megjeleníti a regisztrált kategóriákat egy párbeszédpanelen.

    ![Kategória-váltógombok és Előfizetés gomb](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Konzolos alkalmazás létrehozása címkézett értesítések küldéséhez

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>A konzol alkalmazás futtatása címkézett értesítések küldéséhez

Futtassa az előző szakaszban létrehozott alkalmazást. A kijelölt kategóriák értesítései bejelentési értesítésként jelennek meg.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megismerhette a friss hírek kategóriák szerinti küldését. A háttérbeli alkalmazás leküldi a címkézett értesítéseket azokon az eszközökön, amelyek regisztrálva vannak a címkére vonatkozó értesítések fogadásához. Ha meg szeretné tudni, hogyan leküldéses értesítéseket küldhet az egyes felhasználóknak az általuk használt eszköztől függetlenül, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Honosított értesítések leküldése a Windows-alkalmazásoknak az Azure Notification Hubs használatával](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)