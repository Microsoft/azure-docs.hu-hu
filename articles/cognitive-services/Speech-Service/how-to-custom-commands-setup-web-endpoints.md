---
title: Webes végpontok beállítása
titleSuffix: Azure Cognitive Services
description: webes végpontok beállítása egyéni parancsok
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5f1d5318140dd14c5024e8dd3ad0def0afc7f378
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725907"
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
> * Egy egyéni parancsok alkalmazás [(lásd: Hangsegéd létrehozása a egyéni parancsok](quickstart-custom-commands-application.md)használatával)
> * Speech SDK-kompatibilis ügyfélalkalmazás (lásd: [Integrálás ügyfélalkalmazással a Speech SDK használatával)](how-to-custom-commands-setup-speech-sdk.md)

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Külső webes végpont üzembe helyezése az Azure-függvényalkalmazással

Ebben az oktatóanyagban egy OLYAN HTTP-végpontra lesz szüksége, amely az összes olyan eszközre fenntartja az államokat, amelyek az alkalmazás **TurnOnOff** egyéni parancsok vannak beállítva.

Ha már rendelkezik hívni kívánt webes végponttal, ugorjon a [következő szakaszra.](#setup-web-endpoints-in-custom-commands) A következő szakasz egy alapértelmezett üzemeltetett webes végpont részleteit is tartalmazza, amely akkor használható, ha ki szeretné hagyni ezt a szakaszt.

### <a name="input-format-of-azure-function"></a>Az Azure-függvény bemeneti formátuma

A következő lépés egy végpont üzembe helyezése a [következő Azure Functions.](../../azure-functions/index.yml)
Az alábbiakban az Azure-függvénynek egyéni parancsok esemény formátuma van meg. Ezt az információt az Azure-függvényalkalmazás írásakor használhatja.

```json
{
  "conversationId": "string",
  "currentCommand": {
    "name": "string",
    "parameters": {
      "SomeParameterName": "string",
      "SomeOtherParameterName": "string"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "string",
      "SomeOtherGlobalParameterName": "string"
  }
}
```

    
A következő táblázat ennek a bemenetnek a fő attribútumát ismerteti:
        
| Attribútum | Magyarázat |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId (beszélgetésazonosító)** | A beszélgetés egyedi azonosítója. Vegye figyelembe, hogy ezt az azonosítót az ügyfélalkalmazás generálhatja. |
| **currentCommand** | A beszélgetésben jelenleg aktív parancs. |
| **név** | A parancs neve. Az `parameters` attribútum egy térkép, amely a paraméterek aktuális értékeit tartalmaz. |
| **currentGlobalParameters (aktuális globálisparaméterek)** | Egy olyan térkép, `parameters` mint a , de globális paraméterekhez használatos. |


A **DeviceState** Azure-függvényhez egy példa egyéni parancsok az alábbihoz hasonló lesz. Ez a függvényalkalmazás **bemeneteként** fog működni.
    
```json
{
  "conversationId": "someConversationId",
  "currentCommand": {
    "name": "TurnOnOff",
    "parameters": {
      "item": "tv",
      "value": "on"
    }
  }
}
```

### <a name="azure-function-output-for-a-custom-command-app"></a>Azure-függvény kimenete egyéni parancsalkalmazáshoz

Ha az Azure-függvény kimenetét egy egyéni parancsok használja, a következő formátumban kell megjelennie. Részletekért [lásd: Parancs frissítése webes végpontról.](./how-to-custom-commands-update-command-from-web-endpoint.md)

```json
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

### <a name="azure-function-output-for-a-client-application"></a>Azure-függvény kimenete ügyfélalkalmazáshoz

Ha az Azure-függvény kimenetét egy ügyfélalkalmazás használja, a kimenet az ügyfélalkalmazás által megkövetelt formát is használhatja.

A **DeviceState** végpont esetén az Azure-függvény kimenetét egy ügyfélalkalmazás használja a egyéni parancsok helyett. Az Azure-függvény példakimenetének az alábbihoz hasonlónak kell lennie:
    
```json
{
  "TV": "on",
  "Fan": "off"
}
``` 

Ezt a kimenetet egy külső tárolóba kell írni, hogy megtartsa az eszközök állapotát. A külső tárolási állapotot az alábbi Integrálás [ügyfélalkalmazással](#integrate-with-client-application) szakaszban használjuk.


### <a name="deploy-azure-function"></a>Az Azure-függvény üzembe helyezése

Biztosítunk egy mintaalkalmazásként konfigurálható és üzembe Azure Functions alkalmazást. Ha tárfiókot hoz létre a mintához, kövesse az alábbi lépéseket.
 
1. Táblatároló létrehozása az eszköz állapotának mentéséhez. A Azure Portal hozzon létre egy tárfiók típusú új erőforrást **devicestate néven.** 
1. Másolja ki **a Kapcsolati sztring** értékét **a devicestate -> hozzáférési kulcsokból.** Ezt a sztringkódot hozzá kell adni a letöltött függvényalkalmazás-kódhoz.
1. Töltse le a [minta függvényalkalmazás kódját.](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/custom-commands/quick-start)
1. Nyissa meg a letöltött megoldást a 2019 Visual Studio ban. A **Connections.jsa helyére** STORAGE_ACCOUNT_SECRET_CONNECTION_STRING 2. lépésben lekért titkos helyére. 
1.  Töltse le **a DeviceStateAzureFunction** kódot.

A mintaalkalmazás üzembe helyezéséhez kövesse Azure Functions lépéseket.

1. [A](../../azure-functions/index.yml) Azure Functions üzembe helyezése.
1. Várja meg, amíg az üzembe helyezés sikeres lesz, és az üzembe helyezett erőforrást a Azure Portal. 
1. A **bal oldali panelen** válassza a Függvények, majd a **DeviceState lehetőséget.**
1.  Az új ablakban válassza a **Kód + teszt,** majd a **Függvény URL-címének bejőt.**
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Webes végpontok beállítása a egyéni parancsok

Most pedig az Azure-függvényt a meglévő alkalmazással egyéni parancsok össze.
Ebben a szakaszban egy meglévő alapértelmezett **DeviceState végpontot fog** használni. Ha az Azure-függvény használatával vagy egyéb módon hozta létre a saját webes végpontját, használja azt az alapértelmezett `https://webendpointexample.azurewebsites.net/api/DeviceState` helyett.

1. Nyissa meg a korábban létrehozott Custom Commands-alkalmazást.
1. A Webes **végpontok menüben** kattintson a **New web endpoint (Új webes végpont) elemre.**

   > [!div class="mx-imgBorder"]
   > ![Új webes végpont](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Név | UpdateDeviceState | A webes végpont neve. |
   | URL-cím | https://webendpointexample.azurewebsites.net/api/DeviceState | Annak a végpontnak az URL-címe, amellyel azt szeretné, hogy a Custom Commands-alkalmazása kommunikáljon. |
   | Metódus | POST | A végpontján engedélyezett interakciók (például: GET, POST).|
   | Fejlécek | Kulcs: alkalmazás, Érték: az applicationId első 8 számjegye | A kérelemfejlécben megadandó fejlécparaméterek.|

    > [!NOTE]
    > - A használatával létrehozott példa webes végpont [Azure Functions,](../../azure-functions/index.yml)amely a TV és a ventilátor eszközállapotát mentő adatbázishoz van kötve.
    > - A javasolt fejléc csak a példavégponthoz szükséges.
    > - Annak érdekében, hogy a fejléc értéke egyedi legyen a példavégpontban, használja az applicationId első 8 **számjegyét.**
    > - A való életben a webes végpont lehet az eszközöket felügyelő [IOT Hub](../../iot-hub/about-iot-hub.md) végpontja.

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
   > ![A Sikeres művelet – Végrehajtáskor képernyőt bemutató képernyőkép.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

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
- A sikeres válaszra mentse, betanítsa és tesztelje.
   > [!div class="mx-imgBorder"]
   > ![A Sikeres válasz képernyőképe.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- A Sikertelen válaszban távolítsa el az egyik lekérdezési paramétert, majd mentse, tesztelje újra és tesztelje.
   > [!div class="mx-imgBorder"]
   > ![Webes végpontok hívása művelet siker esetén](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrálás ügyfélalkalmazással

A [Tevékenység egyéni parancsok ügyfélalkalmazásnak](./how-to-custom-commands-send-activity-to-client.md)lépésnél hozzáadott egy **Tevékenység küldése az ügyfélnek** műveletet. A rendszer attól függetlenül elküldi a tevékenységet az ügyfélalkalmazásnak, hogy a **Webes végpont hívása** művelet sikeres volt-e.
Általában azonban csak akkor szeretne tevékenységet küldeni az ügyfélalkalmazásnak, ha a webes végpont hívása sikeres volt. Ebben a példában ez akkor történik, amikor az eszköz állapota sikeresen frissül.

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
Most csak akkor küld tevékenységet az ügyfélnek, ha a webes végpontra küldött kérés sikeres volt.

### <a name="create-visuals-for-syncing-device-state"></a>Vizualizációk létrehozása az eszközállapotok szinkronizálásához

Adja hozzá a következő XML-t `MainPage.xaml` az **EnableMicrophoneButton blokk fölé.**

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Eszközállapot szinkronizálása

A `MainPage.xaml.cs` alatt vegye fel az `using Windows.Web.Http;` hivatkozást. Adja hozzá az alábbi kódot a `MainPage` osztályhoz. Ez a metódus egy GET-kérést küld a példavégpontnak, és kinyeri az aktuális eszközállapotot az alkalmazása számára. Mindenképpen módosítsa a Custom Command webes végpont fejlécében `<your_app_name>` használtra. 

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

## <a name="try-it-out"></a>Próbálja ki

1. Indítsa el az alkalmazást.
1. Kattintson az Eszközállapot szinkronizálása elemre.\
Ha az előző szakaszban tesztelte az alkalmazást, a TV-sorozat a `turn on tv` következőként jelenik **meg:**.
    > [!div class="mx-imgBorder"]
    > ![Eszközállapot szinkronizálása](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Válassza a **Mikrofon engedélyezése lehetőséget.**
1. Válassza a **Beszéd gombot.**
1. Például: `turn on the fan` . A ventilátor vizuális állapota a következőre változik: **.**
    > [!div class="mx-imgBorder"]
    > ![Ventilátor bekapcsolása](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazás egyéni parancsok exportálása távoli képességként](./how-to-custom-commands-integrate-remote-skills.md)
