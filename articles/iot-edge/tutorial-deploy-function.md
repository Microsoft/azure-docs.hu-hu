---
title: 'Oktatóanyag: Azure Functions üzembe helyezése modulként – Azure IoT Edge'
description: Ebben az oktatóanyagban egy Azure-függvényt fog fejleszteni IoT Edge modulként, majd üzembe helyezheti azt egy peremeszközön.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 30abc4a5a1431800cef2bcbda6f5eeedf9a216a3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874638"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Oktatóanyag: Azure Functions üzembe helyezése IoT Edge modulként

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az Azure Functions használatával olyan kódot helyezhet üzembe, amely közvetlenül az Azure IoT Edge-eszközökön valósítja meg az üzleti logikát. Ez az oktatóanyag végigvezeti egy olyan Azure-függvény létrehozásának és üzembe helyezésének folyamatán, amely szűri az érzékelők adatait a szimulált IoT Edge eszközön. Használja a IoT Edge létrehozott szimulált eszközét. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Az Visual Studio kód használatával hozzon létre egy Azure-függvényt.
> * Docker-rendszerkép létrehozása és közzététele a tárolóregisztrációs adatbázisban a VS Code és a Docker használatával.
> * A modul üzembe helyezése egy tárolóregisztrációs adatbázisból az IoT Edge-eszközön.
> * Szűrt adatok megtekintése.

<center>

![Diagram – Oktatóanyag architektúrája: függvénymodul létrehozása és üzembe helyezése](./media/tutorial-deploy-function/functions-architecture.png)
</center>

Az oktatóanyagban létrehozott Azure-függvény szűri az eszköz által létrehozott hőmérsékleti adatokat. A függvény csak akkor küld üzeneteket a Azure IoT Hub, ha a hőmérséklet meghaladja a megadott küszöbértéket.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt át kellett volna néznie az előző oktatóanyagot, amely beállít egy fejlesztési környezetet a Linux-tárolók fejlesztéséhez: IoT Edge modulok fejlesztése [Linux-tárolók használatával.](tutorial-develop-for-linux.md) Az oktatóanyag elvégzésével a következő előfeltételeknek kell megfeleltetve lennie:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Linux-tárolókat Azure IoT Edge futtató eszköz. A rövid útmutatók segítségével Linux-eszközt vagy [Windows-eszközt](quickstart-linux.md) állíthat [be.](quickstart.md)
* Egy tároló-beállításjegyzék, például [a Azure Container Registry.](../container-registry/index.yml)
* [Visual Studio a következővel](https://code.visualstudio.com/) konfigurált [Azure IoT Tools.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
* [Linux-tárolók](https://docs.docker.com/install/) futtatására konfigurált Docker CE.

Ha egy IoT Edge modult a Azure Functions, telepítse a következő további előfeltételeket a fejlesztői gépen:

* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

Az Azure IoT Tools előfeltételek Visual Studio telepített kódhoz való használata felügyeleti képességeket és néhány kódsablont is tartalmaz. Ebben a szakaszban az Visual Studio Code használatával hoz létre egy Azure-IoT Edge tartalmazó virtuális függvényt.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Hozzon létre egy C#-függvénymegoldássablont, amely testre szabható a saját kód használatával.

1. Nyissa meg a Visual Studio Code-ot a fejlesztői gépen.

2. Nyissa meg a VS Code parancskatapadját a View Command Palette **(Parancskatapaletta**  >  **megtekintése) lehetőség kiválasztásával.**

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Mappa kiválasztása | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon egy leíró nevet a megoldásnak, például **FunctionSolution**, vagy fogadja el az alapértelmezettet. |
   | Select module template (Modulsablon kiválasztása) | Válassza **a Azure Functions - C# lehetőséget.** |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CSharpFunction** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje **le a localhost:5000** értéket az Azure Container Registry bejelentkezési kiszolgálójának értékére.  A bejelentkezési kiszolgálót a tároló-beállításjegyzék Áttekintés lapján olvashatja be a Azure Portal. Az utolsó sztring a \<registry name\> következő: .azurecr.io/CSharpFunction. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

A IoT Edge a bővítmény megpróbálja lekért hitelesítő adatokat lekért tárolójegyzékből az Azure-ból, és feltölti őket a környezeti fájlba. Ellenőrizze, hogy szerepel-e már a hitelesítő adatai között. Ha nem, adja hozzá őket most:

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>A célarchitektúra kiválasztása

A Visual Studio Code jelenleg C-modulokat fejleszthet Linux AMD64- és Linux ARM32v7-eszközökhöz. Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldások, mivel a tároló minden architektúratípushoz másképpen van felépítve és futtatva. Az alapértelmezett beállítás a Linux AMD64.

1. Nyissa meg a parancskataccsal, és keressen rá a **Azure IoT Edge: Set Default Target Platform for Edge Solution**(Alapértelmezett célplatform beállítása az Edge-megoldáshoz) kifejezésre, vagy válassza az ablak alján található oldalsáv parancsikonját.

2. A parancskatatúrában válassza ki a célarchitektúrát a lehetőségek listájából. Ebben az oktatóanyagban ubuntus virtuális gépet fogunk használni IoT Edge eszközként, ezért megtartjuk az alapértelmezett **amd64-et.**

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Adjunk hozzá néhány további kódot, hogy a modul feldolgozza az üzeneteket a peremhálózaton, mielőtt továbbítja őket a IoT Hub.

1. A Visual Studio Code-ban nyissa meg a  >  **CSharpFunction**  >  **CSharpFunction.cs modult.**

1. Cserélje le a **CSharpFunction.cs** fájl tartalmát a következő kódra. Ez a kód telemetriát fogad a környezeti és géphőmérsékletről, és csak akkor továbbítja az üzenetet a IoT Hub, ha a gép hőmérséklete egy meghatározott küszöbérték felett van.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. Mentse a fájlt.

## <a name="build-and-push-your-iot-edge-solution"></a>Saját megoldás összeállítása és IoT Edge leküldése

Az előző szakaszban létrehozott egy IoT Edge megoldást, és úgy módosította a **CSharpFunctionet,** hogy kiszűrje az elfogadható küszöbérték alatti jelentett géphőmérsékleteket. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. Nyissa meg a VS Code integrált terminálját a View Terminal **(Terminál**  >  **megtekintése) lehetőség kiválasztásával.**

2. Jelentkezzen be a Dockerbe az alábbi parancs terminálon való megadásával. Jelentkezzen be az Azure Container Registryből felhasználónévvel, jelszóval és bejelentkezési kiszolgálóval. Ezeket az értékeket a regisztrációs adatbázis **Hozzáférési** kulcsok szakaszában lehet lekérni a Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy egy biztonsági figyelmeztetés jelenik meg, amely a használatát `--password-stdin` javasolja. Bár ez az ajánlott eljárás éles környezetben is ajánlott, ez az oktatóanyag nem terjed ki rá. További információkért tekintse meg a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

3. A VS Code Explorerben kattintson a jobb gombbal a fájlban **deployment.template.js** elemre, és válassza a Build and Push IoT Edge Solution (Megoldás létrehozása **és leküldése) lehetőséget.**

   A build és a push parancs három műveletet kezd el. Először létrehoz egy **config** nevű új mappát a megoldásban, amely tartalmazza a teljes üzembe helyezési jegyzékfájlt, amely a telepítési sablonban és más megoldásfájlokban található információkból épül fel. Másodszor fut, hogy a tárolórendszerképet a célarchitektúra megfelelő `docker build` dockerfile-ja alapján készítse el. Ezután a rendszer `docker push` lekulpeli a rendszerkép-adattárat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percig is eltarthat, de a parancsok következő futtatásakor gyorsabb lesz.

## <a name="view-your-container-image"></a>A tárolórendszerkép megtekintése

A Visual Studio Code sikert jelző üzenetet küld, miután leküldte a tárolórendszerképet a tárolóregisztrációs adatbázisba. Ha személyesen is ellenőrizni szeretné a műveletet, keresse meg a rendszerképet az adatbázisban.

1. Az Azure Portalon tallózzon az Azure Container Registry-adatbázisra.
2. Válassza az **Adattárak** elemet.
3. Ekkor a **csharpfunction** adattár jelenik meg a listában. Válassza ki az adattárat a további részletek megtekintéséhez.
4. A **Címkék** szakaszban megjelenik a **0.0.1-amd64** címke. Ez a címke jelzi a létrehozott rendszerkép verziószámát és platformját. Ezek az értékek a CSharpFunction mappa module.json fájljában találhatók meg.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A függvénymodult Azure Portal üzembe helyezheti egy IoT Edge eszközön, ahogy azt a rövid útmutatókban tette. A modulokat emellett a Visual Studio Code használatával is üzembe helyezheti és monitorozhatja. A következő szakaszok az előfeltételekben Azure IoT Tools VS Code-ban található táblázatot használják. Ha még nem tette meg, telepítse most a bővítményt.

1. A Visual Studio Code Explorerben a Azure IoT Hub **szakaszban** **bontsa** ki az Eszközök listában az IoT-eszközök listáját.

2. Kattintson a jobb gombbal a IoT Edge nevére, majd válassza a Create Deployment for Single Device (Üzembe helyezés létrehozása **egyetlen eszközhöz) lehetőséget.**

3. Navigáljon a **CSharpFunction** függvényt tartalmazó megoldásmappához. Nyissa meg a config mappát, válassza kideployment.amd64.js **fájlt,** majd válassza a Select Edge Deployment Manifest (Edge üzembe **helyezési jegyzék kiválasztása) lehetőséget.**

4. Az eszköz alatt **bontsa** ki a Modulok bontsa ki az üzembe helyezett és futó modulok listáját. Kattintson a frissítés gombra. Látnia kell, hogy az új **CSharpFunction** fut a **SimulatedTemperatureSensor** modullal, valamint a $edgeAgent **és** **$edgeHub.**

    Az új modulok néhány pillanatig is eltelhetnek. A IoT Edge eszköznek le kellkérni az új üzembe helyezési információkat a IoT Hub-ból, el kell kezdenie az új tárolókat, majd jelentenie kell az állapotot a IoT Hub.

   ![Üzembe helyezett modulok megtekintése a VS Code-ban](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

Az IoT Hubra érkező összes üzenetet az **Azure IoT Hub: Start Monitoring Built-in Event Endpoint** (Beépített eseményvégpont figyelése) parancskatajánlán való futtatásával láthatja.

A nézetet szűrheti is, hogy csak a megjelölt eszközről az IoT Hub-központra érkező üzenetek jelenjenek meg. Kattintson a jobb gombbal az eszközre a **Azure IoT Hub eszközök szakaszban,** és válassza a Start Monitoring Built-in Event Endpoint (Beépített **eseményvégpont figyelése) lehetőséget.**

Az üzenetek monitorozásának leállításhoz futtassa a **következő parancsot: Azure IoT Hub: Stop Monitoring Built-in Event Endpoint (Beépített eseményvégpont** monitorozásának leállítása) parancsot a parancskataján belül.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy Azure-függvénymodult a saját eszközén létrehozott nyers adatok szűrésére IoT Edge kóddal.

Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Átlagértékek keresése lebegő Azure Stream Analytics-ablakban](tutorial-deploy-stream-analytics.md)
