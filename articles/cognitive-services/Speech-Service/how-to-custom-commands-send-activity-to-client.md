---
title: Egyéni parancsok tevékenység küldése ügyfélalkalmazás számára
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan küldhet tevékenységeket egyéni parancsok alkalmazásából a Speech SDK-t futtató ügyfélalkalmazás számára.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52a4dbc4ff01515af8cd7d2503877184a09f7e64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94566095"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Egyéni parancsok tevékenység küldése ügyfélalkalmazás számára

Ebből a cikkből megtudhatja, hogyan küldhet tevékenységeket egyéni parancsok alkalmazásából a Speech SDK-t futtató ügyfélalkalmazás számára.

A következő feladatokat hajtja végre:

- Egyéni JSON-adattartalom definiálása és küldése az egyéni parancsok alkalmazásából
- Egyéni JSON-adattartalom tartalmának fogadása és megjelenítése C# UWP Speech SDK-ügyfélalkalmazás alapján

## <a name="prerequisites"></a>Előfeltételek
> [!div class = "checklist"]
> * A [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) -es vagy újabb verziója. Ez az útmutató a Visual Studio 2019-et használja
> * Egy Azure-előfizetői azonosító a Speech szolgáltatáshoz: [Szerezzen be egyet ingyen](overview.md#try-the-speech-service-for-free), vagy hozza létre az [Azure Portalon](https://portal.azure.com)
> * Egy korábban [létrehozott Custom Commands-alkalmazás](quickstart-custom-commands-application.md)
> * Egy Speech SDK-kompatibilis ügyfélalkalmazás: [útmutató: integrálás egy ügyfélalkalmazás használatával a SPEECH SDK-val](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>A telepítő tevékenység küldése az ügyfélnek 
1. Nyissa meg a korábban létrehozott egyéni parancsok alkalmazást
1. Válassza a **TurnOnOff** parancsot, válassza a **ConfirmationResponse** lehetőséget a befejezési szabály területen, majd válassza **a művelet hozzáadása** lehetőséget.
1. Az **új művelet típusa** területen válassza a **tevékenység küldése ügyfélnek** lehetőséget.
1. Az alábbi JSON másolása a **tevékenység tartalmába**
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
1. Kattintson a **Save (Mentés** ) gombra egy új szabály létrehozásához egy küldési tevékenység művelettel, a **betanítással** és a módosítás **közzétételével**

   > [!div class="mx-imgBorder"]
   > ![Tevékenység-befejezési szabály küldése](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integrálás ügyfélalkalmazással

[Útmutató: az ügyfélalkalmazás beállítása a SPEECH SDK-val (előzetes verzió)](./how-to-custom-commands-setup-speech-sdk.md), a Speech SDK-val létrehozott UWP-ügyfélalkalmazás olyan parancsokat kezelt, mint például a `turn on the tv` , `turn off the fan` . Néhány vizualizáció hozzáadásával megtekintheti a parancsok eredményét.

Ha **be** -vagy **kikapcsolt** szöveggel rendelkező címkével ellátott mezőket szeretne felvenni, adja hozzá a következő XML-blokkot a StackPanel `MainPage.xaml` .

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>Hivatkozási kódtárak hozzáadása

Mivel létrehozott egy JSON-adattartalmat, a deszerializálás kezeléséhez hozzá kell adnia egy hivatkozást a [JSON.net](https://www.newtonsoft.com/json) -könyvtárhoz.

1. A jobb ügyfél a megoldás.
1. Válassza **a megoldás NuGet-csomagok kezelése** lehetőséget, majd kattintson a **Tallózás gombra** . 
1. Ha már telepített **Newtonsoft.jsa-on**, győződjön meg arról, hogy a verziószáma legalább 12.0.3. Ha nem, lépjen a **NuGet-csomagok kezelése a megoldáshoz-frissítésekhez**, és keressen rá **Newtonsoft.jsa** frissítéshez. Ez az útmutató a 12.0.3 verzióját használja.

    > [!div class="mx-imgBorder"]
    > ![Tevékenység-adattartalom küldése](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Győződjön meg arról is, hogy a **Microsoft. NETCore. UniversalWindowsPlatform** NuGet-csomag legalább 6.2.10. Ez az útmutató a 6.2.10 verzióját használja.

A "Főoldal. XAML. cs" elemnél adja hozzá a következőt:

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>A fogadott tartalom kezelése

A alkalmazásban `InitializeDialogServiceConnector` cserélje le az `ActivityReceived` eseménykezelőt a következő kódra. A módosított `ActivityReceived` eseménykezelő Kinyeri a hasznos adatokat a tevékenységből, és megváltoztatja a televízió vagy a ventilátor vizualizációs állapotát.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Próba

1. Az alkalmazás elindítása
1. Válassza a Mikrofon engedélyezése lehetőséget
1. Válassza a Beszéd gombot
1. Mondja a következőt: `turn on the tv`
1. A TV vizualizációs állapotának "on" értékre kell váltania
   > [!div class="mx-imgBorder"]
   > ![Képernyőkép, amely azt mutatja, hogy a T V vizualizációs állapota most már be van kapcsolva.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató: webes végpontok beállítása (előzetes verzió)](./how-to-custom-commands-setup-web-endpoints.md)
