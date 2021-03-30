---
title: Leküldéses értesítések küldése adott Windows Phone-telefonokra az Azure Notification Hubs használatával | Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére adott (vagyis nem az összes), az alkalmazás háttérrendszerében regisztrált, Windows Phone 8 és Windows Phone 8.1 rendszert futtató eszközökre.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 67700ec643a27f8ae5c581fe9de8e3295a1925e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998237"
---
# <a name="tutorial-send-push-notifications-to-specific-windows-phones-using-azure-notification-hubs"></a>Oktatóanyag: leküldéses értesítések küldése adott Windows Phone-telefonokra az Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére adott, Windows Phone 8 és Windows Phone 8.1 rendszert futtató eszközökre. A Windows Phone 8.1 (nem Silverlight) rendszerre vonatkozó útmutatásért tekintse meg az oktatóanyag [Univerzális Windows-platformmal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) kapcsolatos verzióját.

Ez a forgatókönyv akkor lehetséges, ha egy vagy több *címkét* használ egy regisztráció létrehozásakor az értesítési központban. Ha az értesítések küldése egy címkére történik, az adott címkére regisztrált eszközök mindegyike megkapja az értesítést. A címkékkel kapcsolatban [a regisztrációkban használt címkékkel](notification-hubs-tags-segment-push-message.md) foglalkozó témakörben tekinthet meg további információt.

> [!NOTE]
> A Notification Hubs Windows Phone SDK nem támogatja a Windows leküldéses értesítéseket kezelő szolgáltatásának (WNS) használatát Windows Phone 8.1 Silverlight-alkalmazások esetén. Ha a WNS-t szeretné használni (az MPNS helyett) Windows Phone 8.1 Silverlight-alkalmazásokkal kövesse a [Notification Hubs – Windows Phone Silverlight-oktatóanyagot], amely REST API-kat használ.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Választott kategória hozzáadása a mobilalkalmazáshoz
> * Regisztráció értesítésekre címkék használatával
> * Címkézett értesítések küldése
> * Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek

Végezze el az [Oktatóanyag: Leküldéses értesítések küldése Windows Phone-alkalmazásokba az Azure Notification Hubs használatával](notification-hubs-windows-mobile-push-notifications-mpns.md) című oktatóanyagot. Ebben az oktatóanyagban frissíteni fogja a mobilalkalmazást, hogy regisztrálhasson az Önt érdeklő frisshír-kategóriákra, és csak az ezekre a kategóriákra vonatkozó leküldéses értesítéseket kapja meg.

## <a name="add-category-selection-to-the-mobile-app"></a>Választott kategória hozzáadása a mobilalkalmazáshoz

Az első lépésben hozzá kell adni a felhasználói felületi elemeket a meglévő főoldalhoz, amely lehetővé teszi a felhasználó számára a regisztrálni kívánt kategóriák kiválasztását. A felhasználó által kiválasztott kategóriákat az eszköz tárolja. Az alkalmazás indításakor egy eszközregisztráció jön létre az értesítési központban, amely címkeként tartalmazza a választott kategóriákat.

1. Nyissa meg a `MainPage.xaml` fájlt, majd cserélje le a `Grid` named (elemek) nevű elemet a `TitlePanel` `ContentPanel` következő kódra:

    ```xml
    <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
        <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
        <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
    </StackPanel>

    <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
        <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
        <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
        <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
        <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
        <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. Adjon hozzá egy nevű osztályt `Notifications` a projekthez. Adja hozzá a `public` módosítót az osztály definícióhoz. Ezután adja hozzá a következő `using` utasításokat az új fájlhoz:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    using System.IO.IsolatedStorage;
    using System.Windows;
    ```
3. Másolja az alábbi kódot az új `Notifications` osztályba:

    ```csharp
    private NotificationHub hub;

    // Registration task to complete registration in the ChannelUriUpdated event handler
    private TaskCompletionSource<Registration> registrationTask;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
        return categories != null ? categories.Split(',') : new string[0];
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        var categoriesAsString = string.Join(",", categories);
        var settings = IsolatedStorageSettings.ApplicationSettings;
        if (!settings.Contains("categories"))
        {
            settings.Add("categories", categoriesAsString);
        }
        else
        {
            settings["categories"] = categoriesAsString;
        }
        settings.Save();

        return await SubscribeToCategories();
    }

    public async Task<Registration> SubscribeToCategories()
    {
        registrationTask = new TaskCompletionSource<Registration>();

        var channel = HttpNotificationChannel.Find("MyPushChannel");

        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
            channel.ChannelUriUpdated += channel_ChannelUriUpdated;

            // This is optional, used to receive notifications while the app is running.
            channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
        }

        // If channel.ChannelUri is not null, complete the registrationTask here.  
        // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
        if (channel.ChannelUri != null)
        {
            await RegisterTemplate(channel.ChannelUri);
        }

        return await registrationTask.Task;
    }

    async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
    {
        await RegisterTemplate(e.ChannelUri);
    }

    async Task<Registration> RegisterTemplate(Uri channelUri)
    {
        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                            "<wp:Toast>" +
                                                "<wp:Text1>$(messageParam)</wp:Text1>" +
                                            "</wp:Toast>" +
                                        "</wp:Notification>";

        // The stored categories tags are passed with the template registration.

        registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(),
            templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

        return await registrationTask.Task;
    }

    // This is optional. It is used to receive notifications while the app is running.
    void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
    {
        StringBuilder message = new StringBuilder();
        string relativeUri = string.Empty;

        message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

        // Parse out the information that was part of the message.
        foreach (string key in e.Collection.Keys)
        {
            message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

            if (string.Compare(
                key,
                "wp:Param",
                System.Globalization.CultureInfo.InvariantCulture,
                System.Globalization.CompareOptions.IgnoreCase) == 0)
            {
                relativeUri = e.Collection[key];
            }
        }

        // Display a dialog of all the fields in the toast.
        System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
        {
            MessageBox.Show(message.ToString());
        });
    }
    ```

    Ez az osztály az elszigetelt tárolót használja azon hírkategóriák tárolására, amelyeket az eszköznek meg kell kapnia. Ezenkívül tartalmaz a szóban forgó kategóriákra való regisztrációra szolgáló metódusokat is, amelyek [sablonalapú](notification-hubs-templates-cross-platform-push-messages.md) értesítésregisztrációt használnak.
4. A `App.xaml.cs` Project fájlban adja hozzá a következő tulajdonságot a `App` osztályhoz. A `<hub name>` és a `<connection string with listen access>` helyőrzőt cserélje le az értesítési központ nevére és a *DefaultListenSharedAccessSignature* korábban beszerzett kapcsolati sztringjére.

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

   > [!NOTE]
   > Mivel az ügyfélalkalmazással terjesztett hitelesítő adatok általában nem biztonságosak, csak a figyelési hozzáférés kulcsát terjessze az ügyfélalkalmazással. A figyelési hozzáférés lehetővé teszi, hogy az alkalmazás regisztráljon értesítésekre, a meglévő regisztrációkat azonban nem lehet módosítani, és értesítéseket sem lehet küldeni. A teljes körű hozzáférési kulcsot egy biztonságos háttérszolgáltatásban használja a rendszer értesítések kiküldésére és a meglévő regisztrációk módosítására.

5. A alkalmazásban `MainPage.xaml.cs` adja hozzá a következő sort:

    ```csharp
    using Windows.UI.Popups;
    ```
6. A MainPage.xaml.cs projektfájlban adja hozzá a következő metódust:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldCheckBox.IsChecked == true) categories.Add("World");
        if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
        if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
        if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
        if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
        if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
            result.RegistrationId);
    }
    ```

    Ez a módszer kategóriákat hoz létre, és a `Notifications` osztály használatával tárolja a listát a helyi tárolóban, és regisztrálja a megfelelő címkéket az értesítési központban. A kategóriák módosításakor a rendszer újra létrehozza a regisztrációt az új kategóriákkal.

Az alkalmazás most már képes egy kategóriakészlet tárolására az eszköz helyi tárterületén, és az értesítési központban is regisztrálhat, ha a felhasználó módosítja a kiválasztott kategóriákat.

## <a name="register-for-notifications"></a>Regisztráció értesítésekre

Ezek a lépések végzik az értesítési központban való regisztrációt az indításkor, a helyi tárterületen tárolt kategóriák használatával.

> [!NOTE]
> Mivel a Microsoft Push Notification Service (MPNS) által hozzárendelt csatorna URI bármikor megváltozhat, gyakran regisztráljon az értesítésekre, hogy elkerülhesse az értesítési hibákat. Ebben a példában a rendszer az alkalmazás minden egyes indításakor regisztrál az értesítésekre. A gyakran, naponta egynél többször futtatott alkalmazások esetén a sávszélesség megőrzése érdekében akár ki is hagyhatja a regisztrációt, ha kevesebb mint egy nap telt el az előző regisztráció óta.

1. Nyissa meg az app. XAML. cs fájlt, és adja hozzá a `async` módosítót a `Application_Launching` metódushoz, és cserélje le az első [lépések Notification Hubs] a következő kóddal hozzáadott Notification Hubs regisztrációs kódot:

    ```csharp
    private async void Application_Launching(object sender, LaunchingEventArgs e)
    {
        var result = await notifications.SubscribeToCategories();

        if (result != null)
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
    }
    ```

    Ez a kód biztosítja, hogy az alkalmazás minden egyes indításkor lekéri a kategóriákat a helyi tárterületből, és regisztrációt kérelmez ezekre a kategóriákra vonatkozóan.
2. A Főoldal. XAML. cs projektfájl adja hozzá a következő kódot, amely megvalósítja a `OnNavigatedTo` metódust:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
        if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
        if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
        if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
        if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
        if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
    }
    ```

    Ez a kód a korábban mentett kategóriák állapota alapján frissíti a főoldalt.

Az alkalmazás ezzel elkészült, és képes egy kategóriakészlet tárolására az eszköz helyi tárterületén, illetve az értesítési központban való regisztrálásra, ha a felhasználó módosítja a választott kategóriákat. A következő lépésben határozzon meg egy háttérrendszert, amely kategóriaértesítéseket küldhet ennek az alkalmazásnak.

## <a name="send-tagged-notifications"></a>Címkézett értesítések küldése

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Az alkalmazás tesztelése

1. Az alkalmazás lefordításához és indításához nyomja le az F5 billentyűt a Visual Studióban.

    ![Mobilalkalmazás kategóriákkal][1]

    Az alkalmazás felhasználói felületén váltógombok segítségével választhatja ki, hogy mely kategóriákra szeretne feliratkozni.
2. Engedélyezzen egy vagy több kategóriát, majd kattintson a **Subscribe** (Feliratkozás) gombra.

    Az alkalmazás címkékké alakítja át a kiválasztott kategóriákat, és új eszközregisztrációt kezdeményez az értesítési központban a kiválasztott címkékre vonatkozóan. A rendszer visszaadja, és egy párbeszédablakban jeleníti meg a regisztrált kategóriákat.

    ![Feliratkozást megerősítő üzenet][2]
3. Ha megkapta a kategóriákra való sikeres feliratkozást megerősítő üzenetet, futtassa a konzolalkalmazást, amellyel értesítéseket küldhet az egyes kategóriákra vonatkozóan. Ellenőrizze, hogy csak azon kategóriákhoz kapcsolódóan kap-e értesítést, amelyekre feliratkozott.

    ![Értesítési üzenet][3]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban elsajátította, hogy hogyan küldhet leküldéses értesítéseket olyan adott eszközökre, amelyek a regisztrációjukhoz társított címkékkel rendelkeznek. Ha szeretné megtudni, hogy hogyan küldhet értesítéseket adott felhasználóknak, akik egyszerre akár több eszközt is használhatnak, lépjen tovább a következő oktatóanyagra: 

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott felhasználóknak](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png

<!-- URLs.-->
[Ismerkedés a Notification Hubs szolgáltatással]: notification-hubs-windows-mobile-push-notifications-mpns.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Phone]: ??
