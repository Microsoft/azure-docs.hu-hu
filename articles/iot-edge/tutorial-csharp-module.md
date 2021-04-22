---
title: Oktatóanyag – C#-modul fejlesztése Linuxra Azure IoT Edge
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge modult C#-kóddal, és hogyan helyezheti üzembe linuxos IoT Edge eszközön.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e24c04152911f976907e6bdc433c33e035ade639
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874710"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Oktatóanyag: C# IoT Edge modul fejlesztése Linux-tárolók használatával

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az Visual Studio Code használatával C#-kódot fejleszthet, és üzembe helyezheti azt egy Azure IoT Edge.

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. A rövid útmutatókban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A Visual Studio Code használatával hozzon létre egy IoT Edge-modult, amely a .NET Core 2.1 SDK-ra épül.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag bemutatja, hogyan fejleszthet modult **C#** környezetben a **Visual Studio Code** használatával, és hogyan helyezheti üzembe egy IoT Edge eszközön. Ha Windows-tárolók használatával fejleszt modulokat, a C# modul fejlesztése IoT Edge Windows-tárolók [használatával.](tutorial-csharp-module-windows.md)

Az alábbi táblázat segítségével értheti meg a C#-modulok Linux-tárolók használatával való fejlesztésére és üzembe helyezésére vonatkozó lehetőségeket:

| C# | Visual Studio Code | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![C#-modulok LinuxAMD64-hez VS Code-ban](./media/tutorial-c-module/green-check.png) | ![C#-modulok LinuxAMD64-hez a Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C#-modulok LinuxARM32-hez a VS Code-ban](./media/tutorial-c-module/green-check.png) | ![C#-modulok LinuxARM64-hez a Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>A Linux ARM64-eszközök támogatása nyilvános előzetes [verzióban érhető el.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) További információ: [ARM64-modulok fejlesztése és IoT Edge a Visual Studio (előzetes verzió) modulban.](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)

Az oktatóanyag megkezdése előtt el kellett kezdenie az előző oktatóanyagot a fejlesztési környezet beállításához: IoT Edge modul fejlesztése [Linux-tárolók használatával.](tutorial-develop-for-linux.md) Az oktatóanyag elvégzése után már a következő előfeltételeknek kell megfelelnünk:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Linux-tárolókat Azure IoT Edge futtató eszköz. A rövid útmutatók segítségével Linux-eszközt vagy [Windows-eszközt](quickstart-linux.md) állíthat [be.](quickstart.md)
* Egy tároló-beállításjegyzék, például [a Azure Container Registry.](../container-registry/index.yml)
* [Visual Studio a következővel](https://code.visualstudio.com/) konfigurált [Azure IoT Tools.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
* [Linux-tárolók](https://docs.docker.com/install/) futtatására konfigurált Docker CE.

Az oktatóanyagok befejezéséhez készítse elő a következő további előfeltételeket a fejlesztői gépen:

* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).

## <a name="create-a-module-project"></a>Modulprojekt létrehozása

Az alábbi lépések egy IoT Edge modulprojektet hoznak létre a C#-hoz a Visual Studio Code és a Azure IoT Tools használatával. Miután létrehozott egy projektsablont, adjon hozzá egy új kódot, hogy a modul kiszűrje az üzeneteket a jelentett tulajdonságaik alapján.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy C#-megoldást, amelyet a saját kódjával testreszabhat.

1. Az Visual Studio Code-ban válassza a **View** Command Palette  >  **(Parancskatapaletta** megtekintése) lehetőséget a VS Code parancskatapaletta megnyitásához.

2. A parancskatalógusban írja be és futtassa az **Azure: Sign in** parancsot, és az utasításokat követve jelentkezzen be az Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Mappa kiválasztása | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon egy leíró nevet a megoldásnak, vagy fogadja el az **alapértelmezett EdgeSolution nevet.** |
   | Select module template (Modulsablon kiválasztása) | Válassza a **C# modult.** |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CSharpModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A rendszer előre kitölti a tároló rendszerképét az előző lépésben megadott név alapján. Cserélje **le a localhost:5000** értéket az Azure Container Registry bejelentkezési kiszolgálójának értékére.  A bejelentkezési kiszolgálót a tároló-beállításjegyzék Áttekintés lapján olvashatja be a Azure Portal. <br><br>A végső képtár így néz ki: \<registry name\> .azurecr.io/csharpmodule. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. Használja az Azure Container **Registry Hozzáférési** kulcsok szakaszában található hitelesítő adatokat.

A IoT Edge a bővítmény megpróbálja lekért hitelesítő adatokat lekért tárolójegyzékből az Azure-ból, és feltölti őket a környezeti fájlba. Ellenőrizze, hogy szerepel-e már a hitelesítő adatai között. Ha nem, adja hozzá őket most:

1. A VS Code Explorerben nyissa meg az **.env** fájlt.
2. Frissítse a mezőket az Azure Container Registryben található felhasználónév- és  **jelszóértékekkel.**
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>A célarchitektúra kiválasztása

A Visual Studio Code jelenleg C#-modulokat fejleszthet Linux AMD64- és Linux ARM32v7-eszközökhöz. Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldások, mert a tároló minden architektúratípushoz másként van felépítve és futtatva. Az alapértelmezett beállítás a Linux AMD64.

1. Nyissa meg a parancskataccsal, és keressen a **Azure IoT Edge: Set Default Target Platform for Edge Solution**(Alapértelmezett célplatform beállítása az Edge-megoldáshoz) kifejezésre, vagy válassza az ablak alján található oldalsáv parancsikonját.

2. A parancskataában válassza ki a célarchitektúrát a lehetőségek listájából. Ebben az oktatóanyagban ubuntus virtuális gépet használunk IoT Edge eszközként, ezért megtartjuk az **alapértelmezett amd64 -et.**

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code Explorerben nyissa **meg** a  >  **CSharpModule**  >  **Program.cs modult.**

2. A **CSharpModule** névtér tetején adjon hozzá három **using** utasítást a későbbiekben használt típusokhoz:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Adja hozzá a **temperatureThreshold** változót a **Program** osztályhoz. Ez a változó azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Adja hozzá a **MessageBody**, a **Machine** és az **Ambient** osztályokat a **Program** osztályhoz. Ezek az osztályok határozzák meg a bejövő üzenetek törzsének várt sémáját.

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

5. Keresse meg az **Init** függvényt. Ez a függvény létrehoz és konfigurál egy **ModuleClient** objektumot, amely lehetővé teszi, hogy a modul csatlakozzon a helyi Azure IoT Edge modulhoz üzenetek küld és fogadása érdekében. A **ModuleClient** objektum létrehozása után a kód beolvassa a **temperatureThreshold** értéket a modul ikerdokumentumának kívánt tulajdonságaiból. A kód visszahívást regisztrál egy IoT Edge-központból érkező üzenetek fogadásához az **input1** végponton keresztül. Cserélje le a **SetInputMessageHandlerAsync** metódust egy másikra, és adjon hozzá egy **SetDesiredPropertyUpdateCallbackAsync** metódust a kívánt tulajdonságok frissítéséhez. A módosítás végrehajtásához cserélje le az **Init** metódus utolsó sorát az alábbi kódra:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Adja hozzá az **onDesiredPropertiesUpdate** metódust a **Program** osztályhoz. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

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

7. Cserélje le a **PipeMessage** metódust a **FilterMessages** metódusra. Ezt a metódust akkor hívja meg a rendszer, amikor a modul üzenetet kap az IoT Edge-központtól. Kiszűri a modul ikerdokumentumán keresztül beállított hőmérsékleti határérték alatti hőmérsékleteket jelentő üzeneteket. Ezenkívül a **MessageType** tulajdonságot is hozzáadja az üzenethez, amelynek értéke **Alert** (Figyelmeztetés).

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
                using (var filteredMessage = new Message(messageBytes))
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

8. Mentse a Program.cs fájlt.

9. A VS Code Explorerben nyissa meg adeployment.template.js **a** fájlban a IoT Edge munkaterületén.

10. Adja hozzá a **CSharpModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **modulesContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Ikermodul hozzáadása az üzembehely helyezési sablonhoz](./media/tutorial-csharp-module/module-twin.png)

11. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-module"></a>A modul összeállítása és leküldése

Az előző szakaszban létrehozott egy új IoT Edge, és hozzáadott egy kódot a CSharpModule modulhoz. Az új kód kiszűri az olyan üzeneteket, ahol a jelentett géphőmérséklet az elfogadható korlátokon belül van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. Nyissa meg a VS Code integrált terminálját a View Terminal **(Terminál**  >  **megtekintése) lehetőség kiválasztásával.**

1. Jelentkezzen be a Dockerbe az alábbi parancs terminálon való megadásával. Jelentkezzen be az Azure Container Registryből felhasználónévvel, jelszóval és bejelentkezési kiszolgálóval. Ezeket az értékeket  a regisztrációs adatbázis Hozzáférési kulcsok szakaszában lehet lekérni a Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy egy biztonsági figyelmeztetés jelenik meg, amely a használatát `--password-stdin` javasolja. Bár ez az ajánlott eljárás éles környezetben is ajánlott, ez az oktatóanyag nem terjed ki rá. További információkért tekintse meg a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

1. A VS Code Explorerben kattintson a jobb gombbal a fájlban **deployment.template.js** elemre, és válassza a Build and Push IoT Edge Solution (Megoldás létrehozása **és leküldése) lehetőséget.**

   A build és a push parancs három műveletet kezd el. Először létrehoz egy **config** nevű új mappát a megoldásban, amely tartalmazza a teljes üzembe helyezési jegyzékfájlt, amely a telepítési sablonban és más megoldásfájlokban lévő információkból épül fel. Másodszor a futtatáskor a rendszer a célarchitektúra megfelelő dockerfile-ja alapján építi fel `docker build` a tárolórendszerképet. Ezután a rendszer `docker push` lekulpeli a rendszerkép-adattárat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percig is eltarthat, de a parancsok következő futtatásakor gyorsabb lesz.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A Visual Studio Code Explorer és a Azure IoT Tools bővítmény használatával üzembe helyezheti a modulprojektet a IoT Edge eszközén. Már rendelkezik a forgatókönyvhöz előkészített üzembe helyezési jegyzékfájllal, amelydeployment.amd64.js **konfigurációs** mappában található fájlban található. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Ellenőrizze, hogy a IoT Edge eszköz működik-e.

1. A Visual Studio Code Explorerben a Azure IoT Hub **szakaszban** **bontsa** ki az Eszközök listában az IoT-eszközök listáját.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot.

3. Válassza ki **deployment.amd64.jsfájlban található** fájlt a **config mappában,** majd kattintson a Select Edge Deployment Manifest (Edge üzembe **helyezési jegyzék kiválasztása) elemre.** Ne használja a deployment.template.json fájlt.

4. Az eszköz alatt **bontsa** ki a Modulok listában az üzembe helyezett és futó modulok listáját. Kattintson a frissítés gombra. Látnia kell, hogy az új **CSharpModule** fut a **SimulatedTemperatureSensor** modullal és a $edgeAgent **és** **$edgeHub.**

    A modulok elindulás eltarthat néhány percig. A IoT Edge-modulnak meg kell kapnia az új üzembe helyezési jegyzékfájlt, le kell húznia a modul rendszerképét a tároló-modulból, majd el kell kezdenie minden új modult.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal a IoT Edge eszköz nevére, és válassza a **Start Monitoring Built-in Event Endpoint**(Beépített eseményvégpont figyelése) lehetőséget.

2. Tekintse meg a saját számítógéphez érkező IoT Hub. Az üzenetek megérkezhetnek, mert az IoT Edge eszköznek meg kell kapnia az új üzemelő példányát, és el kell kezdenie az összes modult. Ezután a CModule kódon végzett módosítások az üzenetek küldése előtt megvárják, amíg a gép hőmérséklete eléri a 25 fokot. Emellett hozzáadja a **Riasztás üzenettípust** minden olyan üzenethez, amely eléri a hőmérsékleti küszöbértéket.

   ![A IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Az ikermodul szerkesztése

Az üzembe helyezési jegyzékben a CSharpModule modul ikermodulját használtuk a hőmérsékleti küszöbérték 25 fokra való beállítására. A modulikre használatával anélkül módosíthatja a funkciókat, hogy frissítenie kell a modul kódját.

1. A Visual Studio Code-ban bontsa ki a részleteket a IoT Edge alatt a futó modulokhoz.

2. Kattintson a jobb gombbal a **CSharpModule elemre,** és válassza a **Moduliker szerkesztése lehetőséget.**

3. Keresse **meg a TemperatureThreshold et** a kívánt tulajdonságok között. Módosítsa az értékét 5 fokkal magasabbra, mint a legutóbbi jelentett hőmérséklet.

4. Mentse a modul ikerfájlját.

5. Kattintson a jobb gombbal bárhová a modulikre szerkesztési panelen, és válassza **az Update module twin (Modulikre frissítése) lehetőséget.**

6. Figyelje az eszközről a felhőbe bejövő üzeneteket. Az üzeneteknek le kell állniuk, amíg el nem érik az új hőmérsékleti küszöbértéket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a cikkben használt helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat.

A következő oktatóanyagokból megtudhatja, hogyan segíthet Azure IoT Edge azure-felhőszolgáltatások üzembe helyezésében az adatok feldolgozásához és elemzéséhez a peremhálózaton.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Szolgáltatás](tutorial-deploy-custom-vision.md)