---
title: Tevékenység egyéni parancsok ügyfélalkalmazásnak
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan küldhet tevékenységet egy egyéni parancsok a Speech SDK-t futtató ügyfélalkalmazásnak.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52e0b750f02044afafe233a76e4f43755d9ed303
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725097"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Tevékenység egyéni parancsok ügyfélalkalmazásnak

Ebből a cikkből megtudhatja, hogyan küldhet tevékenységet egy egyéni parancsok a Speech SDK-t futtató ügyfélalkalmazásnak.

A következő feladatokat kell elvégeznie:

- Egyéni JSON-adat definiálás és küldése a egyéni parancsok alkalmazásból
- Egyéni JSON hasznos adatok tartalmának fogadása és vizualizációja egy C# UWP Speech SDK-ügyfélalkalmazásból

## <a name="prerequisites"></a>Előfeltételek
> [!div class = "checklist"]
> * [Visual Studio 2019-es vagy újabb](https://visualstudio.microsoft.com/downloads/) verzió. Ez az útmutató a Visual Studio 2019-et használja
> * Egy Azure-előfizetői azonosító a Speech szolgáltatáshoz: [Szerezzen be egyet ingyen](overview.md#try-the-speech-service-for-free), vagy hozza létre az [Azure Portalon](https://portal.azure.com)
> * Egy korábban [létrehozott Custom Commands-alkalmazás](quickstart-custom-commands-application.md)
> * Speech SDK-kompatibilis ügyfélalkalmazás: [How-to: Integrate with a client application using Speech SDK](./how-to-custom-commands-setup-speech-sdk.md) (Beszéd SDK-kompatibilis ügyfélalkalmazással való integráció a Speech SDK használatával)

## <a name="setup-send-activity-to-client"></a>Tevékenység küldése az ügyfélnek beállítás 
1. Nyissa meg egyéni parancsok korábban létrehozott alkalmazásalkalmazást
1. Válassza **a TurnOnOff parancsot,** válassza **a ConfirmationResponse** lehetőséget a befejezési szabály alatt, majd válassza **a Művelet hozzáadása lehetőséget**
1. Az **Új művelet-típus alatt válassza** a Tevékenység küldése az **ügyfélnek lehetőséget.**
1. Másolja az alábbi JSON-t a **Tevékenység tartalmaiba**
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
1. Kattintson **a Mentés gombra** egy új szabály létrehozásához egy Tevékenység küldése művelet, a **módosítás** betanítás és **közzététel** műveletével

   > [!div class="mx-imgBorder"]
   > ![Tevékenység befejezési szabályának küldése](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integrálás ügyfélalkalmazással

Az [How-to: Setup client application with Speech SDK (Preview) (Ügyfélalkalmazás](./how-to-custom-commands-setup-speech-sdk.md)beállítása a Speech SDK előzetes verziójával) dokumentumban létrehozott egy UWP-ügyfélalkalmazást a Speech SDK-val, amely olyan parancsokat kezelt, mint a `turn on the tv` , `turn off the fan` . Néhány vizualizáció hozzáadása után láthatja ezeknek a parancsoknak az eredményét.

Ha be- vagy kikapcsolt  szöveget tartalmazó címkével jelölt mezőket szeretne **hozzáadni,** adja hozzá a stackpanel alábbi XML-blokkját a `MainPage.xaml` elemhez.

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

### <a name="add-reference-libraries"></a>Referenciakódtárak hozzáadása

Mivel létrehozott egy hasznos JSON-referenciát, hozzá kell [](https://www.newtonsoft.com/json) adni egy hivatkozást a JSON.NET kódtárhoz a deserializálás kezeléshez.

1. Jobb ügyfélprogram a megoldáshoz.
1. Válassza **a Manage NuGet Packages for Solution (NuGet-csomagok kezelése a megoldáshoz) lehetőséget,** majd a Browse (Tallózás) **gombra.** 
1. Ha már telepítette **aNewtonsoft.js,** győződjön meg arról, hogy a verziószáma legalább 12.0.3. Ha nem, a **Manage NuGet Packages for Solution - Updates (NuGet-csomagok** kezelése megoldáshoz – frissítésekhez) oldalon keressen rá a **Newtonsoft.jsa** frissítéséhez. Ez az útmutató a 12.0.3-as verziót használja.

    > [!div class="mx-imgBorder"]
    > ![Tevékenység hasznos ának küldése](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Arról is győződjön meg, hogy a **Microsoft.NETCore.UniversalWindowsPlatform** NuGet-csomag legalább 6.2.10-es. Ez az útmutató a 6.2.10-es verziót használja.

A MainPage.xaml.cs fájlban adja hozzá a következőt:

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>A fogadott hasznos tartalom kezelés

`InitializeDialogServiceConnector`A-ban cserélje le az `ActivityReceived` eseménykezelőt a következő kódra. A módosított eseménykezelő kinyeri a hasznos okat a tevékenységből, és módosítja a tv vagy a ventilátor vizuális `ActivityReceived` állapotát.

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
1. A tv vizuális állapotának "on" (be) állapotra kell változnia
   > [!div class="mx-imgBorder"]
   > ![Képernyőkép, amely azt mutatja, hogy a TV vizualizációs állapota be van stb.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes végpontok beállítása](./how-to-custom-commands-setup-web-endpoints.md)
