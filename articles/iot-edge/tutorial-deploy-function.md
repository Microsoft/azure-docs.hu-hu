---
title: 'Oktatóanyag: Azure Functions üzembe helyezése modulként – Azure IoT Edge'
description: Ebben az oktatóanyagban egy Azure-függvényt fejleszt IoT Edge modulként, majd üzembe helyezi egy peremhálózati eszközön.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 85bc54402237ff8e9c82c43ab9cdbe2dc6658af1
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464105"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Oktatóanyag: Azure Functions üzembe helyezése IoT Edge modulként

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az Azure Functions használatával olyan kódot helyezhet üzembe, amely közvetlenül az Azure IoT Edge-eszközökön valósítja meg az üzleti logikát. Ez az oktatóanyag végigvezeti egy olyan Azure-függvény létrehozásának és üzembe helyezésének lépésein, amely az érzékelőket a szimulált IoT Edge eszközön szűri. A rövid útmutatókban létrehozott szimulált IoT Edge eszközt használja. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Hozzon létre egy Azure-függvényt a Visual Studio Code használatával.
> * Docker-rendszerkép létrehozása és közzététele a tárolóregisztrációs adatbázisban a VS Code és a Docker használatával.
> * A modul üzembe helyezése egy tárolóregisztrációs adatbázisból az IoT Edge-eszközön.
> * Szűrt adatok megtekintése.

<center>

![Diagram – oktatóanyag architektúrája: szakasz és üzembe helyezés Function modul](./media/tutorial-deploy-function/functions-architecture.png)
</center>

Az oktatóanyagban létrehozott Azure-függvény az eszköz által generált hőmérsékleti adatait szűri. A függvény csak az Azure IoT Hub felé irányuló üzeneteket küldi, ha a hőmérséklet egy megadott küszöbérték felett van.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt el kellett volna végeznie az előző oktatóanyagot a fejlesztői környezet létrehozásához a Linux-tárolók fejlesztéséhez: [IoT Edge modulok létrehozása Linux-tárolók használatával](tutorial-develop-for-linux.md). Az oktatóanyag elvégzésével a következő előfeltételek szükségesek:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Azure IoT Edge rendszert futtató eszköz. A rövid útmutatók segítségével [Linux-eszközt](quickstart-linux.md) vagy [Windows-eszközt](quickstart.md)állíthat be.
* Egy tároló-beállításjegyzék, például [Azure Container Registry](../container-registry/index.yml).
* A [Visual Studio Code](https://code.visualstudio.com/) az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)van konfigurálva.
* A [Docker CE](https://docs.docker.com/install/) Linux-tárolók futtatására van konfigurálva.

Ha IoT Edge-modult szeretne létrehozni a Azure Functionsban, telepítse a következő további előfeltételeket a fejlesztői gépre:

* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

Az előfeltételekben telepített Visual Studio Code-hoz készült Azure IoT Tools felügyeleti képességeket és egyes kódrészleteket biztosít. Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy IoT Edge-megoldást, amely egy Azure-függvényt tartalmaz.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Hozzon létre egy C#-függvény megoldási sablont, amelyet testreszabhat saját kódjával.

1. Nyissa meg a Visual Studio Code-ot a fejlesztői gépen.

2. Nyissa meg a vs Code parancs- palettát a  >  **parancs-paletta** megtekintése lehetőség kiválasztásával.

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Mappa kiválasztása | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, például **FunctionSolution**, vagy fogadja el az alapértelmezett értéket. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Azure functions – C#** elemet. |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CSharpFunction** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost: 5000** értéket a **bejelentkezési kiszolgáló** értékére az Azure Container registryben. A bejelentkezési kiszolgálót a tároló beállításjegyzékének áttekintés lapjáról kérheti le a Azure Portal. A végső karakterlánc a következőhöz hasonlít: \<registry name\> . azurecr.IO/CSharpFunction. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

A IoT Edge bővítmény megpróbálja lekérni a tároló beállításjegyzékbeli hitelesítő adatait az Azure-ból, és feltölti azokat a környezeti fájlban. Ellenőrizze, hogy a hitelesítő adatok már szerepelnek-e. Ha nem, adja hozzá őket most:

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a cél architektúrát

A Visual Studio Code jelenleg Linux AMD64 és Linux rendszerű ARM32v7-eszközökhöz is fejleszt C modulokat. Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldásokkal, mivel a tárolót az egyes architektúrák típusainál eltérően építették és futtatják. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancssort, és keressen rá **Azure IoT Edge: állítsa be az alapértelmezett cél platformot az Edge megoldáshoz**, vagy válassza a parancsikon ikont az ablak alján található oldalsó sávban.

2. A parancs palettáján válassza ki a cél architektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu rendszerű virtuális gépet használunk IoT Edge eszközként, így megtarthatja az alapértelmezett **amd64**-t.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Vegyünk fel néhány további kódot, hogy a modul feldolgozza az üzeneteket a peremen, mielőtt továbbítaná őket a IoT Hubba.

1. A Visual Studio Code-ban nyissa meg a **modulok**  >  **CSharpFunction**  >  **CSharpFunction.cs**.

1. Cserélje le a **CSharpFunction.cs** fájl tartalmát a következő kódra. Ez a kód telemetria kap a környezeti és a gépi hőmérsékletről, és csak akkor továbbítja az üzenetet IoT Hub, ha a gép hőmérséklete meghatározott küszöbérték felett van.

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

## <a name="build-and-push-your-iot-edge-solution"></a>IoT Edge-megoldás létrehozása és leküldése

Az előző szakaszban létrehozott egy IoT Edge megoldást, és módosította a **CSharpFunction** , hogy kiszűrje az üzeneteket a jelentett gépi hőmérséklettel az elfogadható küszöbérték alatt. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. Nyissa meg a vs Code integrált terminált a terminál **megtekintése** lehetőség kiválasztásával  >  .

2. Jelentkezzen be a Docker-be a következő parancs beírásával a terminálon. Jelentkezzen be a felhasználónévvel, a jelszóval és a bejelentkezési kiszolgálóval az Azure Container registryből. Ezeket az értékeket a beállításjegyzék **hozzáférési kulcsok** részéből kérheti le a Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Biztonsági figyelmeztetés jelenhet meg, amely a használatát javasolja `--password-stdin` . Habár az ajánlott eljárás az éles környezetekben javasolt, az oktatóanyag hatókörén kívül esik. További információkért lásd a [Docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

3. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.js** fájlra, és válassza a **IoT Edge megoldás létrehozása és leküldése** lehetőséget.

   A build és a push parancs három műveletet indít el. Először létrehoz egy új mappát a **konfigurációban** , amely tartalmazza a teljes telepítési jegyzékfájlt, amely a központi telepítési sablonban és más megoldási fájlokban található információkból épül fel. Másodszor, futtatja `docker build` a tároló rendszerképét a célként megadott architektúra megfelelő Docker alapján. Ezután futtatja, `docker push` hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percet is igénybe vehet, de gyorsabb a parancsok következő futtatásakor.

## <a name="view-your-container-image"></a>A tárolórendszerkép megtekintése

A Visual Studio Code sikert jelző üzenetet küld, miután leküldte a tárolórendszerképet a tárolóregisztrációs adatbázisba. Ha személyesen is ellenőrizni szeretné a műveletet, keresse meg a rendszerképet az adatbázisban.

1. Az Azure Portalon tallózzon az Azure Container Registry-adatbázisra.
2. Válassza az **Adattárak** elemet.
3. Ekkor a **csharpfunction** adattár jelenik meg a listában. Válassza ki az adattárat a további részletek megtekintéséhez.
4. A **Címkék** szakaszban megjelenik a **0.0.1-amd64** címke. Ez a címke jelzi a létrehozott rendszerkép verziószámát és platformját. Ezek az értékek a CSharpFunction mappa module.json fájljában találhatók meg.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A Azure Portal használatával üzembe helyezheti a függvény modulját egy olyan IoT Edge eszközön, mint amit a gyors útmutatókban adott meg. A modulokat emellett a Visual Studio Code használatával is üzembe helyezheti és monitorozhatja. A következő részekben a VS Code-hoz készült Azure IoT Tools (az előfeltételek részben felsorolt) használható. Ha még nem tette meg, telepítse most a bővítményt.

1. A Visual Studio Code Explorer **Azure IoT hub** szakasza alatt bontsa ki az **eszközök** elemet a IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal a IoT Edge eszköz nevére, majd válassza a **központi telepítés létrehozása egyetlen eszközhöz** lehetőséget.

3. Navigáljon a **CSharpFunction** függvényt tartalmazó megoldásmappához. Nyissa meg a konfigurációs mappát, válassza ki a **deployment.amd64.js** fájlt, majd válassza az **Edge központi telepítési jegyzék kiválasztása** lehetőséget.

4. Az eszköz alatt bontsa ki a **modulok** elemet a telepített és futó modulok listájának megtekintéséhez. Kattintson a frissítés gombra. Meg kell jelennie az új **CSharpFunction** , amelyen a **SimulatedTemperatureSensor** modul, valamint a **$edgeAgent** és a **$edgeHub** fut.

    Néhány percet is igénybe vehet, amíg az új modulok megjelennek. A IoT Edge eszközének le kell kérnie az új központi telepítési adatokat a IoT Hubról, el kell indítania az új tárolókat, majd vissza kell jelentenie az állapotot a IoT Hubnak.

   ![Üzembe helyezett modulok megtekintése a VS Code-ban](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

Az Azure IoT Hub futtatásával megtekintheti az IoT hub-ra érkező összes üzenetet: a parancs palettáján **megkezdheti a beépített esemény-végpont figyelését** .

A nézetet szűrheti is, hogy csak a megjelölt eszközről az IoT Hub-központra érkező üzenetek jelenjenek meg. Kattintson a jobb gombbal az eszközre az **Azure IoT hub-eszközök** szakaszban, majd válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget.

Az üzenetek figyelésének leállításához futtassa az Azure IoT Hub parancsot: a parancssorban **állítsa le a beépített esemény végpontjának figyelését** .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Function modult hozott létre kóddal a IoT Edge-eszköz által generált nyers adat szűréséhez.

Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Átlagértékek keresése lebegő Azure Stream Analytics-ablakban](tutorial-deploy-stream-analytics.md)
