---
title: Értesítések küldése adott felhasználóknak az Azure Notification Hubs használatával | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogy hogyan küldhet értesítéseket adott felhasználóknak az Univerzális Windows-platformon (UWP) alapuló alkalmazások használatával.
documentationcenter: windows
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 97a6a45ab01fc113b79a48ba7fcb246d528684be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019480"
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>Oktatóanyag: Értesítések küldése adott felhasználóknak az Azure Notification Hubs használatával

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére egy adott alkalmazás felhasználója számára egy adott eszközön. Az ASP.NET WebAPI háttérrendszer az ügyfelek hitelesítésére szolgál. Amikor a háttérrendszer hitelesíti az ügyfélalkalmazás felhasználóját, automatikusan hozzáad egy címkét az értesítés regisztrációjához. A háttérrendszer ezt a címkét használja, hogy értesítéseket küldjön az adott felhasználónak.

> [!NOTE]
> Az oktatóanyaghoz tartozó befejezett kód a [githubon](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers)található.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * A WebAPI-projekt létrehozása
> * Ügyfelek hitelesítése a WebAPI háttérrendszeren
> * Regisztráció értesítésekre a WebAPI háttérrendszer használatával
> * Értesítések küldése a WebAPI háttérrendszerről
> * Az új WebAPI háttérrendszer közzététele
> * Az ügyfélprojekt kódjának frissítése
> * Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag az [Oktatóanyag: Értesítések küldése az Univerzális Windows-platformon alapuló alkalmazásoknak az Azure Notification Hubs használatával](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) című oktatóanyagban létrehozott értesítési központra és Visual Studio-projektre épül. Ezért fejezze be azt az oktatóanyagot a jelen oktatóanyag elkezdése előtt.

> [!NOTE]
> Ha az Azure App Service Mobile Apps szolgáltatását használja háttérszolgáltatásként, az oktatóanyag [Mobile Apps-verzióját](/previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push) tekintse meg.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-uwp-client"></a>A UWP-ügyfél kódjának frissítése

Ebben a szakaszban az [Oktatóanyag: Értesítések küldése az Univerzális Windows-platformon alapuló alkalmazásoknak az Azure Notification Hubs használatával](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) című oktatóanyag során létrehozott kódot fogja frissíteni. A projektnek már társítva kell lennie a Windows Áruházhoz. Emellett úgy kell konfigurálni, hogy az értesítési központot használja. Ebben a szakaszban olyan kódot fog hozzáadni, amely meghívja az új WebAPI háttérrendszert, majd az értesítések regisztrálásához és küldéséhez használja azt.

1. A Visual Studióban nyissa meg az [Oktatóanyag: Értesítések küldése az Univerzális Windows-platformon alapuló alkalmazásoknak az Azure Notification Hubs használatával](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) című oktatóanyaghoz létrehozott megoldást.
2. Megoldáskezelő kattintson a jobb gombbal a Univerzális Windows-platform (UWP) projektre, majd kattintson a **NuGet-csomagok kezelése** elemre.
3. A bal oldali oldalon válassza a **Tallózás** lehetőséget.
4. A **Search** (Keresés) mezőbe írja be a **Http Client** (HTTP-ügyfél) kifejezést.
5. A találatok listájában kattintson a **System.Net.Http** elemre, majd az **Install** (Telepítés) parancsra. Fejezze be a telepítést.
6. A NuGet **Search** (Keresés) mezőjébe írja be a **Json.net** kifejezést. Telepítse a **Newtonsoft.json** csomagot, majd zárja be a NuGet-csomagkezelő ablakát.
7. A Megoldáskezelőben megnyitott **WindowsApp** projektben kattintson duplán a **MainPage.xaml** fájlra a Visual Studio-szerkesztőben való megnyitásához.
8. A `MainPage.xaml` fájlban cserélje le a `<Grid>` szakaszt a következő kódra: Ez a kód felvesz egy felhasználónevet és egy jelszót tartalmazó szövegmezőt, amelyet a felhasználó hitelesít. Emellett szövegmezőket is felvesz az értesítési üzenethez, valamint a felhasználónevet jelölő címkét, amely a következő értesítést kapja:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

        <StackPanel Grid.Row="1" VerticalAlignment="Center">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                            Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                <ToggleButton Name="toggleFCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="FCM" />
                <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
            </Grid>
        </StackPanel>
    </Grid>
    ```

9. A Megoldáskezelő nyissa meg a `MainPage.xaml.cs` **(Windows 8,1)** és a **(Windows Phone-telefon 8,1)** projekteket tartalmazó fájlt. Adja hozzá a következő `using` utasításokat mindkét fájl elejéhez:

    ```csharp
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```

10. A `MainPage.xaml.cs` **WindowsApp** projektben adja hozzá a következő tagot a `MainPage` osztályhoz. Ne felejtse el az `<Enter Your Backend Endpoint>` karakterláncot a saját háttérrendszere korábban beszerzett végpontjára cserélni. Például: `http://mybackend.azurewebsites.net`.

    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```

11. Adja hozzá az alábbi kódot a `MainPage.xaml.cs` **(Windows 8,1)** és **(Windows Phone-telefon 8,1)** projektekhez tartozó Főoldal-osztályhoz.

    A `PushClick` metódus a **Send Push** (Leküldéses értesítés küldése) gomb kattintáskezelője. A háttérrendszer meghívásával aktiválja egy értesítés küldését az összes olyan eszközre, amely a `to_tag` paraméterrel egyező felhasználónév-címkével rendelkezik. Az értesítési üzenet küldése a kérés törzsében található JSON-tartalomként történik.

    A `LoginAndRegisterClick` metódus a **Login and register** (Bejelentkezés és regisztráció) gomb kattintáskezelője. Az alapszintű hitelesítési jogkivonatot (a hitelesítési séma által használt bármely jogkivonatot képviseli) tárolja a helyi tárterületen, majd a `RegisterClient` használatával regisztrál értesítésekre a háttérrendszer használatával.

    ```csharp
    private async void PushClick(object sender, RoutedEventArgs e)
    {
        if (toggleWNS.IsChecked.Value)
        {
            await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleFCM.IsChecked.Value)
        {
            await sendPush("fcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleAPNS.IsChecked.Value)
        {
            await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

        }
    }

    private async Task sendPush(string pns, string userTag, string message)
    {
        var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
            pns + "&to_tag=" + userTag;

        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                alert.ShowAsync();
            }
        }
    }

    private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
    {
        SetAuthenticationTokenInLocalStorage();

        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // The "username:<user name>" tag gets automatically added by the message handler in the backend.
        // The tag passed here can be whatever other tags you may want to use.
        try
        {
            // The device handle used is different depending on the device and PNS.
            // Windows devices use the channel uri as the PNS handle.
            await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            SendPushButton.IsEnabled = true;
        }
        catch (Exception ex)
        {
            MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
            alert.ShowAsync();
        }
    }

    private void SetAuthenticationTokenInLocalStorage()
    {
        string username = UsernameTextBox.Text;
        string password = PasswordTextBox.Password;

        var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
        ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
    }
    ```

12. Nyissa meg az `App.xaml.cs` eseménykezelőt, és keresse meg a hívást `InitNotificationsAsync()` `OnLaunched()` . Tegye megjegyzésbe vagy törölje az `InitNotificationsAsync()` meghívását. A gomb kezelője inicializálja az értesítési regisztrációkat:

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```

13. Kattintson a jobb gombbal a **WindowsApp** projektre, kattintson az **Add** (Hozzáadás) lehetőségre, majd a **Class** (Osztály) elemre. Nevezze el az osztályt `RegisterClient.cs` , majd kattintson az **OK** gombra az osztály létrehozásához.

    Ez az osztály burkolja azon REST-hívásokat, amelyek az alkalmazás háttérrendszeréhez való kapcsolódáshoz szükségesek a leküldéses értesítésekre való regisztrálás érdekében. Emellett helyben tárolja az értesítési központ által a [Regisztráció az alkalmazás háttérrendszeréből](/previous-versions/azure/azure-services/dn743807(v=azure.100)) című szakaszban leírtak szerint létrehozott *registrationIds* fájlt. A helyi tárterületen tárolt hitelesítési jogkivonatot használ, amikor a **Login and register** (Bejelentkezés és regisztráció) gombra kattint.
14. Adja hozzá a következő `using` utasításokat a RegisterClient.cs fájl elejéhez:

    ```csharp
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```

15. Adja hozzá a következő kódot az `RegisterClient` osztály definíciójában:

    ```csharp
    private string POST_URL;

    private class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    public RegisterClient(string backendEndpoint)
    {
        POST_URL = backendEndpoint + "/api/register";
    }

    public async Task RegisterAsync(string handle, IEnumerable<string> tags)
    {
        var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

        var deviceRegistration = new DeviceRegistration
        {
            Platform = "wns",
            Handle = handle,
            Tags = tags.ToArray<string>()
        };

        var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

        if (statusCode == HttpStatusCode.Gone)
        {
            // regId is expired, deleting from local storage & recreating
            var settings = ApplicationData.Current.LocalSettings.Values;
            settings.Remove("__NHRegistrationId");
            regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
            statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
        }

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
        {
            // log or throw
            throw new System.Net.WebException(statusCode.ToString());
        }
    }

    private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
    {
        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

            var putUri = POST_URL + "/" + regId;

            string json = JsonConvert.SerializeObject(deviceRegistration);
                            var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
    {
        var settings = ApplicationData.Current.LocalSettings.Values;
        if (!settings.ContainsKey("__NHRegistrationId"))
        {
            using (var httpClient = new HttpClient())
            {
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                if (response.IsSuccessStatusCode)
                {
                    string regId = await response.Content.ReadAsStringAsync();
                    regId = regId.Substring(1, regId.Length - 2);
                    settings.Add("__NHRegistrationId", regId);
                }
                else
                {
                    throw new System.Net.WebException(response.StatusCode.ToString());
                }
            }
        }
        return (string)settings["__NHRegistrationId"];

    }
    ```

16. Mentse az összes módosítást.

## <a name="test-the-application"></a>Az alkalmazás tesztelése

1. Indítsa el az alkalmazást mindkét Windows-rendszeren.
2. Adjon meg egy **felhasználónevet** és **jelszót** az alábbi képen látható módon. A Windows Phone rendszeren megadott felhasználónévtől és jelszótól eltérőnek kell lenniük.
3. Kattintson a **Login and register** (Bejelentkezés és regisztráció) gombra, és ellenőrizze, hogy a párbeszédpanel megjeleníti-e a sikeres bejelentkezés tényét. Ez a kód engedélyezi a **Send Push** (Leküldéses értesítés küldése) gombot is.

    ![Képernyőkép a Notification Hubs alkalmazásról, amely a Felhasználónév és a jelszó kitöltését mutatja.][14]
4. Ezt követően adja meg a regisztrált felhasználónevet a **Recipient Username Tag** (Címzett felhasználónév-címke) mezőben. Adjon meg egy értesítési üzenetet, és kattintson a **Send Push** (Leküldéses értesítés küldése) gombra.
5. Csak az egyező felhasználónév-címkével regisztrált eszközök kapják meg az értesítési üzenetet.

    ![Képernyőkép a Notification Hubs-alkalmazásról, amely a leküldett üzenetet mutatja.][15]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban elsajátította, hogy hogyan küldhet leküldéses értesítéseket olyan adott felhasználóknak, akik a regisztrációjukhoz társított címkével rendelkeznek. Ha szeretné megtudni, hogy hogyan küldhet helyalapú értesítéseket, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Helyalapú leküldéses értesítések küldése](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png

<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
