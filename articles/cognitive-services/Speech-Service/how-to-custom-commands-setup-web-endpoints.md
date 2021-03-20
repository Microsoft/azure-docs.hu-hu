---
title: Webes végpontok beállítása (előzetes verzió)
titleSuffix: Azure Cognitive Services
description: webes végpontok beállítása a custom commands-alkalmazáshoz
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 6f2dfdbb5833b34441b4abba7359ad70c4717d1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602162"
---
# <a name="set-up-web-endpoints"></a>Webes végpontok beállítása

Ebből a cikkből megismerheti, hogyan állíthat be webes végpontokat Custom Commands-alkalmazásokban, amelyek lehetővé teszik, hogy HTTP-kéréseket küldjön egy ügyfélalkalmazásból. Az alábbi feladatokat fogja elvégezni:

- Webes végpontok beállítása a Custom Commands-alkalmazásban
- Webes végpontok hívása a Custom Commands-alkalmazásban
- A webes végpontok válaszának fogadása
- Webes végpontok válaszának integrálása egyéni JSON-adatokba, elküldése, illetve vizualizációja egy C# UWP Speech SDK-ügyfélalkalmazásból

## <a name="prerequisites"></a>Előfeltételek
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Egy Azure-előfizetői azonosító a Speech szolgáltatáshoz: [Szerezzen be egyet ingyen](overview.md#try-the-speech-service-for-free), vagy hozza létre az [Azure Portalon](https://portal.azure.com)
> * Egy korábban [létrehozott Custom Commands-alkalmazás](quickstart-custom-commands-application.md)
> * Ügyfélalkalmazás engedélyezett Speech SDK-val: [Útmutató: Az ügyfélalkalmazással folytatott tevékenység befejezése](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Webes végpontok beállítása

1. Nyissa meg a korábban létrehozott Custom Commands-alkalmazást.
1. Nyissa meg a „Webes végpontok” elemet, majd kattintson az „Új webes végpont” lehetőségre.

   > [!div class="mx-imgBorder"]
   > ![Új webes végpont](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Név | UpdateDeviceState | A webes végpont neve. |
   | URL-cím | https://webendpointexample.azurewebsites.net/api/DeviceState | Annak a végpontnak az URL-címe, amellyel azt szeretné, hogy a Custom Commands-alkalmazása kommunikáljon. |
   | Metódus | POST | A végpontján engedélyezett interakciók (például: GET, POST).|
   | Fejlécek | Kulcs: alkalmazás, Érték: az applicationId első 8 számjegye | A kérelemfejlécben megadandó fejlécparaméterek.|

    > [!NOTE]
    > - Az [Azure Function](../../azure-functions/index.yml) használatával létrehozott webes végpontpélda, amely ahhoz az adatbázishoz csatlakozik, amely menti a televízió és a ventilátor eszközállapotát
    > - A javasolt fejléc csak a példavégponthoz szükséges
    > - Annak érdekében, hogy a fejléc értéke egyedi legyen a példában szereplő végpontban, használja az applicationId első 8 számjegyét
    > - Valós helyzetben a webes végpont az eszközeit kezelő [IoT Hub](../../iot-hub/about-iot-hub.md) végpontja lehet.

1. Kattintson a **Mentés** gombra.

## <a name="call-web-endpoints"></a>Webes végpontok hívása

1. Lépjen a **TurnOnOff** parancsra, válassza a **ConfirmationResponse** befejezési szabályt, majd válassza a **Művelet hozzáadása** lehetőséget.
1. Az **Új művelet – Típus** elemnél válassza a **Webes végpont hívása** lehetőséget
1. A **Művelet szerkesztése – Végpontok** elemnél válassza az **UpdateDeviceState** lehetőséget, ami a létrehozott webes végpont.  
1. A **Konfigurálás** elemnél adja meg a következő értékeket:
   > [!div class="mx-imgBorder"]
   > ![Webes végpontok hívásának műveletparaméterei](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Végpontok | UpdateDeviceState | A műveletben hívni kívánt webes végpont. |
   | Lekérdezési paraméterek | item={SubjectDevice}&&value={OnOff} | A webes végpont URL-címéhez hozzáfűzendő lekérdezési paraméterek.  |
   | Törzs tartalma | N/A | A kéréstörzs tartalma. |

    > [!NOTE]
    > - A javasolt lekérdezési paraméterek csak a példavégponthoz szükségesek

1. A **Siker esetén – Végrehajtandó művelet** elemnél válassza a **Szóbeli válasz küldése** lehetőséget.

    Az **Egyszerű szerkesztőben** adja meg a következőt: `{SubjectDevice} is {OnOff}`.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép, amely megjeleníti a végrehajtáskor végrehajtandó sikeres művelet képernyőjét.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Végrehajtandó művelet | Szóbeli válasz küldése | A webes végpontra irányuló kérés sikeressége esetén végrehajtandó művelet |

   > [!NOTE]
   > - A HTTP-válasz mezőit közvetlenül is elérheti a `{YourWebEndpointName.FieldName}` használatával. Például: `{UpdateDeviceState.TV}`

1. A **Hiba esetén – Végrehajtandó művelet** elemnél válassza a **Szóbeli válasz küldése** lehetőséget.

    Az **Egyszerű szerkesztőben** adja meg a következőt: `Sorry, {WebEndpointErrorMessage}`.

   > [!div class="mx-imgBorder"]
   > ![Webes végpontok hívása művelet hiba esetén](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Végrehajtandó művelet | Szóbeli válasz küldése | A webes végpontra irányuló kérés meghiúsulása esetén végrehajtandó művelet |

   > [!NOTE]
   > - A(z) `{WebEndpointErrorMessage}` nem kötelező. Eltávolíthatja, ha nem kíván hibaüzeneteket megjeleníteni.
   > - A példában szereplő végpontban a HTTP-választ részletes hibaüzenetekkel küldjük vissza, amelyek olyan gyakori hibákról értesítenek, mint például a hiányzó fejlécparaméterek.

### <a name="try-it-out-in-test-portal"></a>Próbálja ki a tesztportálon
- Válasz Siker esetén\
Mentés, betanítás és tesztelés
   > [!div class="mx-imgBorder"]
   > ![Képernyőkép, amely megjeleníti a sikerre adott választ.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Válasz Hiba esetén\
Egy lekérdezési paraméter eltávolítása, mentés, betanítás és tesztelés
   > [!div class="mx-imgBorder"]
   > ![Webes végpontok hívása művelet siker esetén](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrálás ügyfélalkalmazással

Az [Útmutató: Tevékenység küldése az ügyfélalkalmazásnak (előzetes verzió)](./how-to-custom-commands-send-activity-to-client.md) során hozzáadott egy **Tevékenység küldése az ügyfélnek** műveletet. A rendszer attól függetlenül elküldi a tevékenységet az ügyfélalkalmazásnak, hogy a **Webes végpont hívása** művelet sikeres volt-e.
A legtöbb esetben azonban csak akkor szeretné elküldeni a tevékenységet az ügyfélalkalmazásnak, amikor a webes végpont hívása sikeres. Ebben a példában ez akkor történik, amikor az eszköz állapota sikeresen frissül.

1. Törölje a korábban hozzáadott **Tevékenység küldése az ügyfélnek** műveletet.
1. Webes végpont hívásának szerkesztése:
    1. A **Konfigurálás** lapon győződjön meg róla, hogy a **Lekérdezési paraméterek** értéke `item={SubjectDevice}&&value={OnOff}`
    1. A **Siker esetén** elemnél módosítsa a **Végrehajtandó művelet** beállítást **Tevékenység küldése az ügyfélnek** lehetőségre
    1. Másolja az alábbi JSON-t a **Tevékenység tartalma** mezőbe
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
Mostantól a tevékenységet csak akkor küldi el az ügyfélnek, ha a webes végpontra irányuló kérés sikeres.

### <a name="create-visuals-for-syncing-device-state"></a>Vizualizációk létrehozása az eszközállapotok szinkronizálásához
Adja hozzá a következő XML-t a `MainPage.xaml` fájlhoz az `"EnableMicrophoneButton"` blokk felett.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Eszközállapot szinkronizálása

A `MainPage.xaml.cs` alatt vegye fel az `using Windows.Web.Http;` hivatkozást. Adja hozzá az alábbi kódot a `MainPage` osztályhoz. Ez a metódus egy GET-kérést küld a példavégpontnak, és kinyeri az aktuális eszközállapotot az alkalmazása számára. Ne feledje megváltoztatni a `<your_app_name>` kifejezést arra az értékre, amelyet a Custom Command webes végpontjának **fejlécében** használt

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request.
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Próba

1. Az alkalmazás elindítása
1. Kattintson az Eszközállapot szinkronizálása elemre.\
Ha az előző szakaszban az alkalmazást a `turn on tv` használatával tesztelte, a televízió „on” (bekapcsolva) állapotban jelenik meg.
    > [!div class="mx-imgBorder"]
    > ![Eszközállapot szinkronizálása](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Válassza a Mikrofon engedélyezése lehetőséget
1. Válassza a Beszéd gombot
1. Mondja a következőt: `turn on the fan`
1. A ventilátor vizuális állapotának „on” (bekapcsolva) állapotra kell váltania
    > [!div class="mx-imgBorder"]
    > ![Ventilátor bekapcsolása](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni parancsok alkalmazás exportálása távoli képességként](./how-to-custom-commands-integrate-remote-skills.md)

