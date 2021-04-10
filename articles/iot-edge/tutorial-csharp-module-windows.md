---
title: Oktatóanyag – C#-modulok fejlesztése a Windowshoz Azure IoT Edge használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre IoT Edge-modulokat C#-kóddal, és hogyan telepítheti azokat Windows-eszközökre, amelyek IoT Edge futtatnak.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 4e01b1ca9a3858ff31ad9b5da1d1159209c44330
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464061"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Oktatóanyag: C# IoT Edge-modulok fejlesztése Windows-tárolók használatával

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Ez a cikk bemutatja, hogyan hozhat létre C#-kódot a Visual Studióval, és hogyan telepítheti azt egy Azure IoT Edge rendszert futtató Windows-eszközre.

>[!NOTE]
>IoT Edge 1,1 LTS az utolsó kiadási csatorna, amely támogatni fogja a Windows-tárolókat. A 1,2-es verziótól kezdődően a Windows-tárolók nem támogatottak. A Windows rendszerű eszközökön [IoT Edge Linux rendszeren](iot-edge-for-linux-on-windows.md) való használatának vagy áthelyezésének megfontolnia, hogy Windows-eszközökön fusson IoT Edge.

Azure IoT Edge-modulok használatával olyan programkódot helyezhet üzembe, amely közvetlenül a IoT Edge-eszközökön valósítja meg az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A Visual Studio használatával hozzon létre egy IoT Edge modult, amely a C# SDK-ra épül.
> * A Visual Studio és a Docker használatával hozzon létre egy Docker-rendszerképet, és tegye közzé azt a beállításjegyzékben.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. A modul csak akkor küldi az üzeneteket, ha a hőmérséklet meghaladja a megadott küszöbértéket. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag bemutatja, hogyan fejleszthet egy modult a C#-ban a Visual Studio 2019 használatával, majd hogyan telepítheti azt egy Windows-eszközre. Ha Linux-tárolókat használó modulokat fejleszt, ugorjon a [C# IoT Edge-modulok fejlesztése Linux-tárolók használatával](tutorial-csharp-module.md) című lépésre.

A C#-modulok Windows-tárolókkal való fejlesztésének és telepítésének lehetőségeiről a következő táblázatban talál további információt:

| C# | Visual &nbsp; Studio &nbsp; Code | Visual Studio 2017 &nbsp; és &nbsp; 2019 |
| -- | :------------------: | :------------------: |
| Windows AMD64 fejlesztés | ![C#-modulok fejlesztése a WinAMD64 a Visual Studio Code-ban](./media/tutorial-c-module/green-check.png) | ![C#-modulok fejlesztése a WinAMD64 a Visual Studióban](./media/tutorial-c-module/green-check.png) |
| Windows AMD64 hibakeresés |   | ![C#-modulok hibakeresése a WinAMD64 a Visual Studióban](./media/tutorial-c-module/green-check.png) |

Mielőtt elkezdené ezt az oktatóanyagot, állítsa be a fejlesztési környezetet a [IoT Edge modulok fejlesztése a Windows-tárolók használatával](tutorial-develop-for-windows.md) című oktatóanyag utasításait követve. A befejezése után a környezet a következő előfeltételeket fogja tartalmazni:

* Egy ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge rendszert futtató Windows-eszköz](how-to-install-iot-edge-windows-on-windows.md).
* Egy tároló-beállításjegyzék, például [Azure Container Registry](../container-registry/index.yml).
* A [Visual Studio 2019](/visualstudio/install/install-visual-studio), amely a [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) bővítménnyel van konfigurálva.
* A [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), amely Windows-tárolók futtatására van konfigurálva.

> [!TIP]
> Ha a Visual Studio 2017-es verzióját (15,7-es vagy újabb verziót) használja, töltse le és telepítse Azure IoT Edge Tools for Visual Studio 2017 alkalmazást a Visual Studio [Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)-ről.

## <a name="create-a-module-project"></a>Modul-projekt létrehozása

Ebben a szakaszban egy IoT Edge modul-projektet hoz létre a Visual Studio és a Azure IoT Edge Tools bővítmény használatával. A sablon létrehozása után új kódot kell hozzáadnia, hogy a modul kiszűrje az üzeneteket a jelentett tulajdonságaik alapján.

### <a name="create-a-new-project"></a>Új projekt létrehozása

A Azure IoT Edge Tools a Visual Studióban az összes támogatott IoT Edge modul nyelvéhez biztosít Project sablonokat. Ezekben a sablonokban minden olyan fájl és kód szerepel, amelyre a IoT Edge teszteléséhez munkamodult kell telepíteni. Emellett kiindulási pontot is biztosítanak a saját üzleti logikájuk testreszabásához.

1. Nyissa meg a Visual Studio 2019 alkalmazást, majd válassza az **új projekt létrehozása** lehetőséget.

1. A **create a New Project (új projekt létrehozása** ) panelen keresse meg **IoT Edge** , majd az eredmények listájában válassza ki a **Azure IoT Edge (Windows amd64)** projektet.

   ![Képernyőkép a IoT Edge "új projekt létrehozása" panelről.](./media/tutorial-csharp-module-windows/new-project.png)

1. Kattintson a **Tovább** gombra.

    Megnyílik az **új projekt konfigurálása** panel.

   ![Képernyőkép az "új projekt konfigurálása" panelről.](./media/tutorial-csharp-module-windows/configure-project.png)

1. Az **új projekt konfigurálása** panelen nevezze át a projektet és a megoldást valami más leíróra, például **CSharpTutorialApp**. 

1. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

   Megnyílik a **modul hozzáadása** panel.

   ![Képernyőkép a projekt konfigurálásához szükséges "modul hozzáadása" panelről.](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. Az **új projekt konfigurálása** lapon tegye a következőket:

   a. A bal oldali panelen válassza ki a **C# modul** sablonját.  
   b. A **modul neve** mezőbe írja be a **CSharpModule** nevet.  
   c. Az **adattár URL-címe** mezőben cserélje le a **localhost: 5000** értéket az Azure Container Registry **bejelentkezési kiszolgáló** értékére, a következő formátumban: `<registry name>.azurecr.io/csharpmodule`

    > [!NOTE]
    > Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve a modul Project-Name értékével.  A bejelentkezési kiszolgálót a tároló beállításjegyzékének áttekintés lapjáról kérheti le a Azure Portal.

1. A projekt létrehozásához válassza a **Hozzáadás** lehetőséget.

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

Az üzembe helyezési jegyzék megosztja a tároló beállításjegyzékének hitelesítő adatait a IoT Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. Használja az Azure Container Registry **hozzáférési kulcsok** szakaszának hitelesítő adatait.

1. A Visual Studio Megoldáskezelő nyissa *meg adeployment.template.js* fájlt.

1. Az $edgeAgent kívánt tulajdonságok között keresse meg a **registryCredentials** tulajdonságot. A tulajdonsághoz tartozó beállításjegyzékbeli címnek a projekt létrehozásakor megadott információval kell kitöltenie. A Felhasználónév és a jelszó mezőben a változók neveinek kell szerepelniük. Például:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Nyissa meg a környezeti (ENV) fájlt a modul-megoldásban. Alapértelmezés szerint a fájl el van rejtve Megoldáskezelőban, ezért előfordulhat, hogy az **összes fájl megjelenítése** gombra kell kattintania a megjelenítéséhez. Az ENV-fájlnak ugyanazt a Felhasználónév és jelszó változót kell tartalmaznia, amelyet a fájl *deployment.template.jsban* látott.

1. Adja hozzá a **Felhasználónév** és a **jelszó** értékét az Azure Container registryből.

1. Mentse a módosításokat az ENV-fájlba.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Az alapértelmezett modul kódja üzeneteket fogad egy bemeneti várólistában, és egy kimeneti várólistán keresztül továbbítja azokat. Vegyünk fel néhány további kódot, hogy a modul az IoT hub-ba való továbbítás előtt dolgozza fel az üzeneteket. Frissítse a modult úgy, hogy az minden üzenetben elemezze a hőmérsékleti adatokat, és csak akkor küldje el az üzenetet az IoT hub számára, ha a hőmérséklet meghaladja az adott küszöbértéket.

1. A Visual Studióban válassza a **CSharpModule**  >  **program. cs** elemet.

1. A **CSharpModule** névtér tetején adjon hozzá három **using** utasítást a későbbiekben használt típusokhoz:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. Adja hozzá a **temperatureThreshold** változót a **program** osztályhoz a számláló változó után. A temperatureThreshold változó állítja be azt az értéket, amelyet a mért hőmérsékletnek meg kell haladnia az IoT hub számára küldendő adat esetében.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Adja hozzá a **MessageBody**, a **gépet** és a **környezeti** osztályt a **program** osztályhoz a változó deklarációja után. Ezek az osztályok határozzák meg a bejövő üzenetek törzsének várt sémáját.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

1. Keresse meg az **init** metódust. Ez a metódus egy **ModuleClient** objektumot hoz létre és konfigurál, amely lehetővé teszi, hogy a modul a helyi Azure IoT Edge futtatókörnyezethez kapcsolódjon üzenetek küldéséhez és fogadásához. A kód emellett regisztrálja a visszahívást, hogy egy IoT Edge hub üzeneteit a **input1** -végponton keresztül fogadja.

   Cserélje le a teljes init metódust a következő kódra:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Ez a frissített init-módszer továbbra is beállítja a IoT Edge futtatókörnyezethez való kapcsolódást a ModuleClient, de új funkciókat is biztosít. A **temperatureThreshold** értékének lekéréséhez beolvassa a modulhoz tartozó dupla kívánt tulajdonságokat. Ezután létrehoz egy visszahívást, amely figyeli a modulhoz tartozó dupla kívánt tulajdonságok jövőbeli frissítéseit. Ezzel a visszahívással távolról frissítheti a modul hőmérsékleti küszöbértékét, és a módosítások bekerülnek a modulba.

   A frissített init metódus a meglévő **SetInputMessageHandlerAsync** metódust is módosítja. A mintakód a *input1* lévő bejövő üzeneteket a *PipeMessage* függvénnyel dolgozza fel, de módosítani szeretnénk ezt a *FilterMessages* -függvényt, amelyet a következő lépésekben hozunk létre.

1. Adjon hozzá egy új **onDesiredPropertiesUpdate** metódust a **program** osztályhoz. Ez a metódus frissítéseket fogad a kívánt tulajdonságokról a különálló modulból, és a **temperatureThreshold** változót frissíti a megfelelő értékre. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

1. Távolítsa el a minta **PipeMessage** metódust, és cserélje le egy új **FilterMessages** metódusra. Ezt a metódust akkor hívja meg a rendszer, amikor a modul üzenetet kap az IoT Edge-központtól. Kiszűri azokat az üzeneteket, amelyek a megadott hőmérsékleti küszöbérték alatti hőmérsékletet jelentik a különálló modulon keresztül. A metódus a **MessageType** tulajdonságot is hozzáadja az üzenethez a **riasztásra** beállított értékkel.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using(var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

1. Mentse a *Program.cs* fájlt.

1. Nyissa meg a *deployment.template.jst* a IoT Edge-megoldás fájljában. Ez a fájl közli a IoT Edge-ügynökkel, hogy mely modulok telepíthetők, és közli a IoT Edge hub-val, hogyan irányítja az üzeneteket közöttük. Itt a telepítendő modulok a következők: **SimulatedTemperatureSensor** és **CSharpModule**.

1. Adja hozzá a **CSharpModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a szakasz alján a `modulesContent` **$edgeHub** modul Twin:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Képernyőfelvétel: az üzembe helyezési sablonhoz hozzáadandó modul.](./media/tutorial-csharp-module-windows/module-twin.png)

1. Mentse a *deployment.template.json* fájlt.

## <a name="build-and-push-your-module"></a>Modul létrehozása és leküldése

Az előző szakaszban létrehozott egy IoT Edge megoldást, és hozzáadta a kódot a **CSharpModule** , hogy kiszűrje azokat az üzeneteket, amelyekben a jelentett gépi hőmérséklet az elfogadható küszöbérték alá esik. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

### <a name="sign-in-to-docker"></a>Bejelentkezés a Docker-be

1. A következő parancs használatával jelentkezzen be a Docker-be a fejlesztői gépre. Használja a felhasználónevet, a jelszót és a bejelentkezési kiszolgálót az Azure Container registryből. Ezeket az értékeket a beállításjegyzék **hozzáférési kulcsok** részéből kérheti le a Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy olyan biztonsági figyelmeztetést kap, amely a használatát javasolja `--password-stdin` . Bár ezt ajánlott eljárásként javasoljuk a termelési forgatókönyvek esetében, az oktatóanyag hatókörén kívül esik. További információkért lásd a [Docker bejelentkezési referenciáját](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Létrehozás és leküldés

1. A Visual Studio Megoldáskezelőban kattintson a jobb gombbal a létrehozni kívánt projekt nevére. Az alapértelmezett név a **AzureIotEdgeApp1** , és mivel Windows-modult készít, a bővítménynek **Windows. Amd64**-nek kell lennie.

1. Válassza ki **az IoT Edge-modulok kiépítése és leküldése** lehetőséget.

   A build és a push parancs három műveletet indít el:
   * Először létrehoz egy új mappát a *konfiguráció* nevű megoldásban, amely tartalmazza a teljes telepítési jegyzéket. A szolgáltatás a központi telepítési sablonban és más megoldás fájljaiban található információk alapján készült. 
   * Másodszor, futtatja `docker build` a tároló rendszerképének összeállítását a cél architektúrájának megfelelő Docker alapján. 
   * Végül futtatja, `docker push` hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percet is igénybe vehet, de a parancsok legközelebb történő futtatása gyorsabb lesz.

## <a name="deploy-modules-to-the-device"></a>Modulok üzembe helyezése az eszközön

A Visual Studio Cloud Explorer és a Azure IoT Edge Tools bővítmény használatával telepítse a modul-projektet a IoT Edge eszközre. Már előkészített egy üzembe helyezési jegyzéket a forgatókönyvhöz, a *konfigurációs* mappában található fájl *deployment.windows-amd64.js* . Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Ellenőrizze, hogy a IoT Edge eszköz működik-e.

1. A Visual Studio Cloud Explorerben bontsa ki az erőforrásokat a IoT-eszközök listájának megtekintéséhez.

1. Kattintson a jobb gombbal annak a IoT Edge eszköznek a nevére, amelyről szeretné fogadni a központi telepítést.

1. Válassza a **központi telepítés létrehozása** lehetőséget.

1. A Visual Studio fájlkezelőben válassza ki a *deployment.windows-amd64.js* fájlt a megoldás *konfigurációs* mappájába.

1. A Cloud Explorer frissítésével megtekintheti az eszköz alatt felsorolt telepített modulokat.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazza az üzembe helyezési jegyzéket a IoT Edge eszközre, az eszközön futó IoT Edge-futtatókörnyezet gyűjti az új központi telepítési adatokat, és elindítja a futtatását. Az eszközön futó, de az üzembe helyezési jegyzékben nem szereplő modulok leállnak. Az eszközről hiányzó modulok elindulnak.

A IoT Edge Tools bővítmény használatával megtekintheti az IoT hub-ra beérkező üzeneteket.

1. A Visual Studio Cloud Explorerben válassza ki a IoT Edge eszköz nevét.

1. A **műveletek** listában válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget.

1. Megtekintheti az IoT hub-ra érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek, mivel a IoT Edge eszköznek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. A CSharpModule-kódnak kell megvárnia, amíg a gép hőmérséklete 25 fokkal nem éri el az üzenetek küldését. A kód az üzenet típusú **riasztást** is hozzáadja az adott hőmérsékleti küszöbértéket elérő üzenetekhez.

   ![A kimeneti ablak képernyőképe, amely az IoT hub-on érkező üzeneteket jeleníti meg.](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>A modul két különálló szerkesztése

A CSharpModule modult a Twin értékre használta, hogy a hőmérsékleti küszöbértéket 25 fokos értékre állítsa be. A Twin modul használatával módosíthatja a funkciót anélkül, hogy frissítenie kellene a modul kódját.

1. A Visual Studióban nyissa meg a *deployment.windows-amd64.js* fájlt. 

   Ne *nyissa* meg a *Deployment. template* fájlt. Ha nem látja az üzembe helyezési jegyzéket a Megoldáskezelő található *konfigurációs* fájlban, válassza a megoldáskezelő eszköztár **összes fájl megjelenítése** ikonját.

1. Keresse meg a CSharpModule Twin értéket, és módosítsa a **temperatureThreshold** paraméter értékét egy olyan új hőmérsékletre, amely a legutóbbi jelentett hőmérsékletnél 5 – 10 fok.

1. Mentse a *deployment.windows-amd64.js* fájlt.

1. Kövesse a központi telepítési útmutatót, és alkalmazza a frissített üzembe helyezési jegyzéket az eszközre.

1. A bejövő eszközről a felhőbe irányuló üzenetek figyelése. Az új hőmérsékleti küszöb eléréséig az üzenetek le kell állnia.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a következő javasolt cikket, megtarthatja és újra felhasználhatja az oktatóanyagban létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a felmerülő költségek elkerülése érdekében törölheti a helyi konfigurációkat és az itt használt Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban IoT Edge-modult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat.

Annak megismeréséhez, hogy a Azure IoT Edge hogyan segíthet az Azure Cloud Services üzembe helyezésében az adatfeldolgozásban és az adatelemzésben, folytassa a következő oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure functions](tutorial-deploy-function.md) 
>  [Azure stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Azure Machine learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision szolgáltatás](tutorial-deploy-custom-vision.md)
