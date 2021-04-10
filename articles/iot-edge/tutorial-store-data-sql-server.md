---
title: Oktatóanyag – az adattárolás az SQL-modullal Azure IoT Edge használatával
description: Ez az oktatóanyag bemutatja, hogyan tárolhatja az adattárolást helyileg IoT Edge eszközön SQL Server modullal
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e5aeda35ed4110807f17c11e93b31d0b3998846e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461113"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Oktatóanyag: Adatok tárolása a peremhálózaton SQL Server-adatbázisokkal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

SQL Server modul üzembe helyezése Azure IoT Edge-t futtató linuxos eszközön tárolt adattároláshoz.

Az Azure IoT Edge és az SQL Server segítségével adatokat tárolhat és kérdezhet le a peremhálózaton. Azure IoT Edge rendelkezik az üzenetek gyorsítótárazásához szükséges alapszintű tárolási képességekkel, ha egy eszköz offline állapotba kerül, majd továbbítja őket a kapcsolat újbóli létrehozásakor. Szüksége lehet azonban ennél fejlettebb tárolási képességekre is, például az adatok helyi lekérdezéséhez. A IoT Edge-eszközök a helyi adatbázisokkal összetettebb számítási feladatokat hajthatnak végre anélkül, hogy fenn kellene tartaniuk a IoT Hubhoz való kapcsolódást.

A jelen cikk az SQL Server-adatbázisok IoT Edge-eszközön történő üzembe helyezésének utasításait tartalmazza. Az IoT Edge-eszközön futó Azure Functions-függvények elvégzik a bejövő adatok rendszerezését, majd elküldik azokat az adatbázisnak. A cikkben szereplő lépések a tárolókban üzemeltetett egyéb adatbázisokra (például MySQL vagy PostgreSQL) is alkalmazhatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Azure-függvény létrehozása a Visual Studio Code használatával
> * SQL-adatbázis üzembe helyezése az IoT Edge-eszközön
> * A Visual Studio Code használatával modulokat hozhat létre és helyezhet üzembe az IoT Edge-eszközön
> * A létrejött adatok megtekintése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt el kellett volna végeznie az előző oktatóanyagot a fejlesztői környezet létrehozásához a Linux-tárolók fejlesztéséhez: [IoT Edge modulok létrehozása Linux-eszközökhöz](tutorial-develop-for-linux.md). Az oktatóanyag elvégzésével a következő előfeltételek szükségesek:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Azure IoT Edge rendszert futtató AMD64-eszköz. A rövid útmutatók segítségével [Linux-eszközt](quickstart-linux.md) vagy [Windows-eszközt](quickstart.md)állíthat be.
  * Az ARM-eszközök (például a málna PiS) nem futhatnak SQL Server. Ha az SQL-t egy ARM-eszközön szeretné használni, regisztráljon az [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/) előzetes verziójának kipróbálásához.
* Egy tároló-beállításjegyzék, például [Azure Container Registry](../container-registry/index.yml).
* A [Visual Studio Code](https://code.visualstudio.com/) az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)van konfigurálva.
* A [Docker CE](https://docs.docker.com/install/) Linux-tárolók futtatására van konfigurálva.

Ez az oktatóanyag egy Azure Functions modult használ az adatSQL Serverba való adatküldéshez. IoT Edge modul Azure Functions használatával történő fejlesztéséhez telepítse a következő további előfeltételeket a fejlesztői gépre:

* [C# for Visual Studio Code (OmniSharp)-bővítmény a Visual Studio Code-](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)hoz.
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

Ahhoz, hogy adatokat küldhessen egy adatbázisba, egy olyan modulra van szüksége, amely képes megfelelően rendszerezni, majd egy táblába elmenteni az adatokat.

### <a name="create-a-new-project"></a>Új projekt létrehozása

A következő lépések bemutatják, hogyan hozhat létre IoT Edge függvényt a Visual Studio Code és az Azure IoT Tools használatával.

1. Nyissa meg a Visual Studio Code-ot.

2. Nyissa meg a vs Code parancs- palettát a  >  **parancs-paletta** megtekintése lehetőség kiválasztásával.

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Mappa kiválasztása | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, például **SqlSolution**, vagy fogadja el az alapértelmezett értéket. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Azure functions – C#** elemet. |
   | Provide a module name (Modulnév megadása) | A modulnak adja az **sqlFunction** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost: 5000** értéket a **bejelentkezési kiszolgáló** értékére az Azure Container registryben. A bejelentkezési kiszolgálót a tároló beállításjegyzékének áttekintés lapjáról kérheti le a Azure Portal. <br><br>A végső karakterlánc a következőhöz hasonlít: \<registry name\> . azurecr.IO/sqlfunction. |

   A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét.

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

A IoT Edge bővítmény megpróbálja lekérni a tároló beállításjegyzékbeli hitelesítő adatait az Azure-ból, és feltölti azokat a környezeti fájlban. Ellenőrizze, hogy a hitelesítő adatok már szerepelnek-e. Ha nem, adja hozzá őket most:

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a cél architektúrát

Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldásokkal, mivel a tárolót az egyes architektúrák típusainál eltérően építették és futtatják. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancssort, és keressen rá **Azure IoT Edge: állítsa be az alapértelmezett cél platformot az Edge megoldáshoz**, vagy válassza a parancsikon ikont az ablak alján található oldalsó sávban.

2. A parancs palettáján válassza ki a cél architektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu rendszerű virtuális gépet használunk IoT Edge eszközként, így megtarthatja az alapértelmezett **amd64**-t.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A vs Code Explorerben nyissa meg a **modulok**  >  **sqlFunction**  >  **sqlFunction. csproj**.

2. Keresse meg a csomagok hivatkozásainak csoportját, és vegyen fel egy újat, hogy tartalmazza a SqlClient.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

3. Mentse a **sqlFunction. csproj** fájlt.

4. Nyissa meg a **sqlFunction. cs** fájlt.

5. Cserélje le a fájl teljes tartalmát a következő kódra:

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
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
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

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");";
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
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

6. Az 35. sorban cserélje le a karakterláncot **\<sql connection string\>** a következő sztringre. Az **adatforrás** tulajdonság a SQL Server tárolóra hivatkozik, amely még nem létezik. A következő szakaszban létre kell hoznia az **SQL** néven.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Mentse a **sqlFunction. cs** fájlt.

## <a name="add-the-sql-server-container"></a>Az SQL Server tároló hozzáadása

Az IoT Edge-futtatókörnyezet által az IoT Edge-eszközön telepítendő modulokat az [üzembehelyezési jegyzékfájl](module-composition.md) határozza meg. Az előző szakaszban létrehozott egy testreszabott Function modult, de a SQL Server modul már fel van építve és elérhető az Azure piactéren. Utasítsa az IoT Edge-futtatókörnyezetet ennek belefoglalására, majd végezze el a konfigurálást az eszközön.

1. A Visual Studio Code-ban nyissa meg a parancssort a   >  **parancs paletta** megtekintése lehetőség kiválasztásával.

2. Írja be a parancsot a parancssorba, és futtassa a következő parancsot **Azure IoT Edge: IoT Edge modul hozzáadása**. Az új modul hozzáadásához a parancssorban adja meg a következő információkat:

   | Mező | Érték |
   | ----- | ----- |
   | Select deployment template file (Üzembehelyezési sablonfájl kiválasztása) | A Command paletta kiemeli az aktuális megoldás mappájában található fájl **deployment.template.js** . Válassza ki a fájlt.  |
   | Select module template (Modulsablon kiválasztása) | Válassza ki **a modult az Azure Marketplace-** en. |

3. A Azure IoT Edge modul Marketplace-en keresse meg és válassza ki **SQL Server modult**.

4. Módosítsa a modul nevét **SQL**-re, az összes kisbetűs értékre. Ez a név megegyezik a sqlFunction. cs fájlban a kapcsolatok karakterláncában deklarált tároló nevével.

5. Válassza az **Importálás** lehetőséget a modul megoldáshoz való hozzáadásához.

6. A megoldás mappájába nyissa meg a **deployment.template.js** fájlt.

7. Keresse meg a **modulok** szakaszt. Három modulnak kell megjelennie. Az új megoldásokban alapértelmezés szerint a modul *SimulatedTemperatureSensor* szerepel, és a többi modullal használható tesztelési célú adatkészleteket biztosít. A modul *sqlFunction* az a modul, amelyet eredetileg új kóddal hozott létre és frissített. Végül az *SQL* -modul az Azure Marketplace-ről lett importálva.

   >[!Tip]
   >A SQL Server modulhoz alapértelmezett jelszó van beállítva a telepítési jegyzékfájl környezeti változói között. Ha éles környezetben hoz létre SQL Server-tárolót, minden esetben [módosítsa az alapértelmezett rendszergazdai jelszót](/sql/linux/quickstart-install-connect-docker).

8. A fájl **deployment.template.jsának** lezárása.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszokban egyetlen modullal hozott létre megoldást, majd hozzáadott egy másik modult az üzembehelyezési jegyzéksablonfájlhoz. A SQL Server modult nyilvánosan a Microsoft üzemelteti, de a functions modulban kell tárolóba helyezése a kódot. Ebben a szakaszban felépíti a megoldást, létrehozza a sqlFunction modulhoz tartozó tároló lemezképeket, és leküldi a lemezképet a tároló-beállításjegyzékbe.

1. A Visual Studio Code-ban nyissa meg az integrált terminált a terminál **megtekintése** lehetőség kiválasztásával  >  .  

1. Jelentkezzen be a tárolóregisztrációs adatbázisba a Visual Studio Code felületén, hogy le tudja küldeni a rendszerképeket a regisztrációs adatbázisba. Használja ugyanazt a Azure Container Registry (ACR) hitelesítő adatokat, amelyeket a. env fájlhoz adott hozzá. Az integrált terminálon írja be a következő parancsot:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Előfordulhat, hogy megjelenik egy biztonsági figyelmeztetés, amely a--Password-stdin paraméter használatát javasolja. Bár a paraméter használatát a cikk nem tárgyalja, javasoljuk, kövesse ezt az ajánlott eljárást. További információkért lásd a [Docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) parancs referenciáját.

1. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

   A build és a push parancs három műveletet indít el. Először létrehoz egy új mappát a **konfigurációban** , amely tartalmazza a teljes telepítési jegyzékfájlt, amely a központi telepítési sablonban és más megoldási fájlokban található információkból épül fel. Másodszor, futtatja `docker build` a tároló rendszerképét a célként megadott architektúra megfelelő Docker alapján. Ezután futtatja, `docker push` hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percet is igénybe vehet, de gyorsabb a parancsok következő futtatásakor.

   Ellenőrizheti, hogy a sqlFunction modul sikeresen leküldve a tároló-beállításjegyzékbe. A Azure Portal navigáljon a tároló-beállításjegyzékhez. Válassza ki a **Tárházak** lehetőséget, és keresse meg a **sqlFunction**. A másik két modul, a SimulatedTemperatureSensor és az SQL nem lesz leküldve a tároló-beállításjegyzékbe, mert a Tárházak már a Microsoft-jegyzékekben vannak.

## <a name="deploy-the-solution-to-a-device"></a>A megoldás üzembe helyezése egy eszközön

Az IoT Hub felületén keresztül modulokat állíthat be egy eszközön, de az IoT Hubhoz és az eszközökhöz a Visual Studio Code felületén keresztül is hozzáférhet. Ebben a szakaszban az IoT Hubhoz való hozzáférést fogja beállítani, majd a VS Code használatával üzembe fogja helyezni a megoldást az IoT Edge-eszközön.

1. A Visual Studio Code Explorer **Azure IoT hub** szakasza alatt bontsa ki az **eszközök** elemet a IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal arra az eszközre, amelyet a központi telepítéssel kíván megcélozni, és válassza a **központi telepítés létrehozása egyetlen eszközhöz** lehetőséget.

3. Válassza ki a **konfigurációs** mappában található fájl **deployment.amd64.jsét** , majd kattintson az **Edge központi telepítési jegyzék kiválasztása** elemre. Ne használja a deployment.template.json fájlt.

4. Az eszköz alatt bontsa ki a **modulok** elemet a telepített és futó modulok listájának megtekintéséhez. Kattintson a frissítés gombra. Meg kell jelennie az új **SQL** -és **sqlFunction** -moduloknak, valamint a **SimulatedTemperatureSensor** modulnak, valamint a **$edgeAgent** és **$edgeHubnak**.

    Azt is ellenőrizheti, hogy üzemel-e az összes modul az eszközön. Futtassa az alábbi parancsot az IoT Edge-eszközön a modulok állapotának megtekintéséhez.

   ```cmd/sh
   iotedge list
   ```

    A modulok elindításához néhány percet is igénybe vehet. Az IoT Edge futtatókörnyezetnek meg kell kapnia az új üzembe helyezési jegyzékfájlt, le kell kérnie a modul lemezképeit a tároló futtatókörnyezetből, majd el kell indítania az új modulokat.

## <a name="create-the-sql-database"></a>Az SQL-adatbázis létrehozása

Ha alkalmazza az üzembehelyezési jegyzékfájlt az eszközön, akkor három futó modulja lesz. A SimulatedTemperatureSensor modul szimulált környezeti adatgyűjtést hoz létre. Az sqlFunction modul az adatbázis számára megfelelő formátumba konvertálja az adatokat. Ez a szakasz az SQL-adatbázis beállítását mutatja be a hőmérsékletadatok mentéséhez.

Futtassa az alábbi parancsokat a IoT Edge eszközön. Ezek a parancsok csatlakoznak az eszközön futó **SQL** -modulhoz, és létrehoznak egy adatbázist és egy táblázatot, amely az elküldött hőmérsékleti adatait fogja tárolni.

1. A IoT Edge eszköz egyik parancssori eszközén kapcsolódjon az adatbázishoz.

      ```bash
      sudo docker exec -it sql bash
      ```

2. Nyissa meg az SQL-parancssori eszközt.

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Adatbázis létrehozása:

      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Végezze el a tábla definiálását.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Az SQL Server Docker-fájljának testreszabásával megadhatja, hogy a rendszer automatikusan elvégezze az SQL Server üzembe helyezését több IoT Edge-eszközön. További információkért lásd a [Microsoft SQL Server-tároló demóprojektjét](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="view-the-local-data"></a>A helyi adatok megtekintése

Ha a tábla létrejött, az sqlFunction modul elkezdi menteni az adatokat az IoT Edge-eszköz helyi SQL Server 2017-adatbázisába.

Futtassa a következő parancsot az SQL-parancssori eszközből a formázott tábla adatainak megtekintéséhez:

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![A helyi adatbázis tartalmának megtekintése](./media/tutorial-store-data-sql-server/view-data.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

Ebben az oktatóanyagban olyan kódot tartalmazó Azure Functions-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha saját modulokat kíván létrehozni, a témáról további információt [az Azure Functions a Visual Studio Code és az Azure IoT Edge segítségével történő fejlesztését](./how-to-vs-code-develop-module.md) ismertető cikkben talál.

## <a name="next-steps"></a>Következő lépések

Ha a peremhálózat egy másik tárolási módszerét szeretné kipróbálni, olvassa el, hogyan használható az Azure Blob Storage a IoT Edge.

> [!div class="nextstepaction"]
> [Adatok tárolása a peremhálózaton az Azure Blob Storage az IoT Edge-ben segítségével](how-to-store-data-blob.md)