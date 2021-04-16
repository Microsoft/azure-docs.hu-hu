---
title: Oktatóanyag – Adatok tárolása SQL-modullal a Azure IoT Edge
description: Ez az oktatóanyag bemutatja, hogyan tárolunk helyileg adatokat egy IoT Edge-eszközön egy SQL Server modullal
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 709c7bf352635ffb1c17f6cb24aee59dcae2e033
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479763"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Oktatóanyag: Adatok tárolása a peremhálózaton SQL Server-adatbázisokkal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Üzembe helyezhet egy SQL Server modult, amely linuxos tárolókat futtató Azure IoT Edge tárolja az adatokat.

Az Azure IoT Edge és az SQL Server segítségével adatokat tárolhat és kérdezhet le a peremhálózaton. Azure IoT Edge tárolóeszköz alapszintű tárolási képességekkel rendelkezik az üzenetek gyorsítótárazáshoz, ha egy eszköz offline állapotba kerül, majd továbbítja őket, amikor a kapcsolat újra létre lett hozva. Szüksége lehet azonban ennél fejlettebb tárolási képességekre is, például az adatok helyi lekérdezéséhez. A IoT Edge helyi adatbázisok használatával összetettebb számításokat végezhet anélkül, hogy kapcsolatot kell fenntartania a IoT Hub.

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

Az oktatóanyag megkezdése előtt át kellett volna néznie az előző oktatóanyagot, amely beállít egy fejlesztési környezetet a Linux-tárolók fejlesztéséhez: IoT Edge [modulok fejlesztése Linux-eszközökhöz.](tutorial-develop-for-linux.md) Az oktatóanyag elvégzésével a következő előfeltételeknek kell megfeleltetve lennie:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Linux-tárolóval Azure IoT Edge AMD64-eszköz. A rövid útmutatók segítségével Linux-eszközt vagy [Windows-eszközt](quickstart-linux.md) állíthat [be.](quickstart.md)
  * Az ARM-eszközök, például a Raspberry Piszk, nem SQL Server. Ha ARM-eszközön szeretné használni az SQL-t, használja a [Azure SQL Edge.](../azure-sql-edge/overview.md)
* Egy tároló-beállításjegyzék, például [a Azure Container Registry.](../container-registry/index.yml)
* [Visual Studio a következővel](https://code.visualstudio.com/) konfigurált [Azure IoT Tools.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
* [Linux-tárolók](https://docs.docker.com/install/) futtatására konfigurált Docker CE.

Ez az oktatóanyag egy Azure Functions modul segítségével küld adatokat a SQL Server. Ha egy IoT Edge modult Azure Functions, telepítse a következő további előfeltételeket a fejlesztői gépen:

* [C# a Visual Studio Code -hez (az OmniSharp által működtetett) Visual Studio kódhoz.](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

Ahhoz, hogy adatokat küldhessen egy adatbázisba, egy olyan modulra van szüksége, amely képes megfelelően rendszerezni, majd egy táblába elmenteni az adatokat.

### <a name="create-a-new-project"></a>Új projekt létrehozása

A következő lépések azt mutatják be, hogyan hozhat létre IoT Edge függvényt a Visual Studio Code és a Azure IoT Tools.

1. Nyissa meg a Visual Studio Code-ot.

2. Nyissa meg a VS Code parancskatapadját a **Parancskatapaletta**  >  **megtekintése lehetőség kiválasztásával.**

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Mappa kiválasztása | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon egy leíró nevet a megoldásnak, például **SqlSolution,** vagy fogadja el az alapértelmezettet. |
   | Select module template (Modulsablon kiválasztása) | Válassza **a Azure Functions - C# lehetőséget.** |
   | Provide a module name (Modulnév megadása) | A modulnak adja az **sqlFunction** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje **le a localhost:5000** értéket az Azure Container Registry bejelentkezési kiszolgálójának értékére.  A bejelentkezési kiszolgálót a tároló-beállításjegyzék Áttekintés lapján olvashatja be a Azure Portal. <br><br>Az utolsó sztring a \<registry name\> következő: .azurecr.io/sqlfunction. |

   A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét.

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

A IoT Edge a bővítmény megpróbálja lekért hitelesítő adatokat lekért tárolójegyzékből az Azure-ból, és feltölti őket a környezeti fájlba. Ellenőrizze, hogy szerepel-e már a hitelesítő adatai között. Ha nem, adja hozzá őket most:

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>A célarchitektúra kiválasztása

Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldások, mivel a tároló minden architektúratípushoz másképpen van felépítve és futtatva. Az alapértelmezett beállítás a Linux AMD64.

1. Nyissa meg a parancskataccsal, és keressen rá a **Azure IoT Edge: Set Default Target Platform for Edge Solution**(Alapértelmezett célplatform beállítása az Edge-megoldáshoz) kifejezésre, vagy válassza az ablak alján található oldalsáv parancsikonját.

2. A parancskatatúrában válassza ki a célarchitektúrát a lehetőségek listájából. Ebben az oktatóanyagban ubuntus virtuális gépet fogunk használni IoT Edge eszközként, ezért megtartjuk az alapértelmezett **amd64-et.**

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code Explorerben nyissa meg **az**  >  **sqlFunction**  >  **sqlFunction.csproj modulokat.**

2. Keresse meg a csomaghivatkozások csoportját, és adjon hozzá egy újat az SqlClient hozzáadásához.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

3. Mentse az **sqlFunction.csproj** fájlt.

4. Nyissa meg **az sqlFunction.cs** fájlt.

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

6. A 35. sorban cserélje le a **\<sql connection string\>** sztringet a következő sztringre. Az **Adatforrás tulajdonság** a SQL Server hivatkozik, amely még nem létezik. A következő szakaszban **SQL** néven fogja létrehozni.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Mentse az **sqlFunction.cs** fájlt.

## <a name="add-the-sql-server-container"></a>A SQL Server hozzáadása

Az IoT Edge-futtatókörnyezet által az IoT Edge-eszközön telepítendő modulokat az [üzembehelyezési jegyzékfájl](module-composition.md) határozza meg. Az előző szakaszban egy testreszabott függvénymodul kódot adott meg, de az SQL Server modul már létre van hozva és elérhető a Azure Marketplace. Utasítsa az IoT Edge-futtatókörnyezetet ennek belefoglalására, majd végezze el a konfigurálást az eszközön.

1. A Visual Studio Code-ban nyissa meg a parancskatapaletta nézetét a **Parancskatapaletta**  >  **megtekintése lehetőség kiválasztásával.**

2. A parancskata ban írja be és futtassa a **következő parancsot: Azure IoT Edge: Add IoT Edge module**. A parancskataában adja meg a következő információkat egy új modul hozzáadásához:

   | Mező | Érték |
   | ----- | ----- |
   | Select deployment template file (Üzembehelyezési sablonfájl kiválasztása) | A parancskatapaletta kiemeli **deployment.template.jsaktuális** megoldásmappában található fájlban található fájlt. Válassza ki a fájlt.  |
   | Select module template (Modulsablon kiválasztása) | Válassza **a Modul lehetőséget Azure Marketplace.** |

3. Az Azure IoT Edge modul piacterén keresse meg és válassza ki **a SQL Server modult.**

4. Módosítsa a modul nevét **sql-névre,** csak kisbetűket. Ez a név megegyezik az sqlFunction.cs fájlban található kapcsolati sztringben deklarált tárolónévvel.

5. Válassza **az Importálás** lehetőséget, hogy hozzáadja a modult a megoldáshoz.

6. A megoldás mappájában nyissa meg adeployment.template.js **fájlban.**

7. Keresse meg **a modulok szakaszt.** Három modult kell látnia. A *SimulatedTemperatureSensor* modult alapértelmezés szerint az új megoldások tartalmazzák, és a többi modulhoz használható tesztadatokat biztosít. Az *sqlFunction* modul az a modul, amely eredetileg létre lett hozva és frissítve lett az új kóddal. Végül az *sql modult* importáltuk a Azure Marketplace.

   >[!Tip]
   >A SQL Server modul alapértelmezett jelszóval van beállítva az üzembe helyezési jegyzék környezeti változóiban. Ha éles környezetben hoz létre SQL Server-tárolót, minden esetben [módosítsa az alapértelmezett rendszergazdai jelszót](/sql/linux/quickstart-install-connect-docker).

8. Zárja be **deployment.template.jsfájlt.**

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszokban egyetlen modullal hozott létre megoldást, majd hozzáadott egy másik modult az üzembehelyezési jegyzéksablonfájlhoz. Az SQL Server modult a Microsoft üzemelteti nyilvánosan, de a kódot tárolóba kell helyeznie a Functions modulban. Ebben a szakaszban létre fog hozni egy megoldást, tároló rendszerképeket hoz létre az sqlFunction modulhoz, majd lekulpeli a rendszerképet a tároló-beállításjegyzékbe.

1. A Visual Studio Code-ban nyissa meg az integrált terminált a **View** Terminal (Terminál  >  **megtekintése) lehetőség kiválasztásával.**  

1. Jelentkezzen be a tárolóregisztrációs adatbázisba a Visual Studio Code felületén, hogy le tudja küldeni a rendszerképeket a regisztrációs adatbázisba. Használja ugyanazt a Azure Container Registry (ACR) hitelesítő adatokat, mint az .env fájlhoz. Az integrált terminálon írja be a következő parancsot:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Előfordulhat, hogy egy biztonsági figyelmeztetés jelenik meg, amely a --password-stdin paraméter használatát javasolja. Bár a paraméter használatát a cikk nem tárgyalja, javasoljuk, kövesse ezt az ajánlott eljárást. További információért tekintse meg a [docker login parancs](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

1. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

   A build és a push parancs három műveletet kezd el. Először létrehoz egy **config** nevű új mappát a megoldásban, amely tartalmazza a teljes üzembe helyezési jegyzékfájlt, amely a telepítési sablonban és más megoldásfájlokban található információkból épül fel. Másodszor fut, hogy a tárolórendszerképet a célarchitektúra megfelelő `docker build` dockerfile-ja alapján készítse el. Ezután lekulpeli `docker push` a rendszerkép-adattárat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percig is eltarthat, de a parancsok következő futtatásakor gyorsabb lesz.

   Ellenőrizheti, hogy az sqlFunction modul sikeresen le lett-e küldve a tároló-beállításjegyzékbe. A Azure Portal lépjen a tároló-beállításjegyzékhez. Válassza **ki az adattárakat,** és keressen rá az **sqlFunction kifejezésre.** A másik két modul, a SimulatedTemperatureSensor és az sql nem lesz leküldve a tárolóregisztr adatbázisban, mert az adattárak már a Microsoft regisztrációs adatbázisában vannak.

## <a name="deploy-the-solution-to-a-device"></a>A megoldás üzembe helyezése egy eszközön

Az IoT Hub felületén keresztül modulokat állíthat be egy eszközön, de az IoT Hubhoz és az eszközökhöz a Visual Studio Code felületén keresztül is hozzáférhet. Ebben a szakaszban az IoT Hubhoz való hozzáférést fogja beállítani, majd a VS Code használatával üzembe fogja helyezni a megoldást az IoT Edge-eszközön.

1. A Visual Studio Code Explorer Azure IoT Hub **szakaszban** bontsa ki  az Eszközök listában az IoT-eszközök listáját.

2. Kattintson a jobb gombbal arra az eszközre, amelybe a telepítést meg szeretné célozni, majd válassza a Create Deployment for Single Device (Üzembe helyezés **létrehozása egyetlen eszközhöz) lehetőséget.**

3. Válassza ki **deployment.amd64.jsfájlt a** **config mappában,** majd kattintson a Select Edge Deployment Manifest (Edge üzembe **helyezési jegyzék kiválasztása) elemre.** Ne használja a deployment.template.json fájlt.

4. Az eszköz alatt **bontsa** ki a Modulok listában az üzembe helyezett és futó modulok listáját. Kattintson a frissítés gombra. Látnia kell, hogy az **új sql** és **sqlFunction** modulok futnak a **SimulatedTemperatureSensor** modullal, valamint a $edgeAgent **és** a **$edgeHub.**

    Azt is ellenőrizheti, hogy üzemel-e az összes modul az eszközön. Futtassa az alábbi parancsot az IoT Edge-eszközön a modulok állapotának megtekintéséhez.

   ```cmd/sh
   iotedge list
   ```

    A modulok elindulás eltarthat néhány percig. A IoT Edge-modulnak meg kell kapnia az új üzembe helyezési jegyzékfájlt, le kell húznia a modul rendszerképét a tároló-modulból, majd el kell kezdenie minden új modult.

## <a name="create-the-sql-database"></a>Az SQL-adatbázis létrehozása

Ha alkalmazza az üzembehelyezési jegyzékfájlt az eszközön, akkor három futó modulja lesz. A SimulatedTemperatureSensor modul szimulált környezeti adatokat hoz létre. Az sqlFunction modul az adatbázis számára megfelelő formátumba konvertálja az adatokat. Ez a szakasz az SQL-adatbázis beállítását mutatja be a hőmérsékletadatok mentéséhez.

Futtassa az alábbi parancsokat a IoT Edge eszközön. Ezek a parancsok  az eszközön futó SQL-modulhoz csatlakoznak, és létrehoznak egy adatbázist és egy táblát, amely tartalmazza az eszközre küldött hőmérsékleti adatokat.

1. Az eszköz parancssori eszközén IoT Edge az adatbázishoz.

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

   ![Helyi adatbázis tartalmának megtekintése](./media/tutorial-store-data-sql-server/view-data.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

Ebben az oktatóanyagban olyan kódot tartalmazó Azure Functions-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha saját modulokat kíván létrehozni, a témáról további információt [az Azure Functions a Visual Studio Code és az Azure IoT Edge segítségével történő fejlesztését](./how-to-vs-code-develop-module.md) ismertető cikkben talál.

## <a name="next-steps"></a>Következő lépések

Ha egy másik tárolási módszert szeretne kipróbálni a peremhálózaton, olvassa el, hogyan használhatja a Azure Blob Storage a IoT Edge.

> [!div class="nextstepaction"]
> [Adatok tárolása a peremhálózaton az Azure Blob Storage az IoT Edge-ben segítségével](how-to-store-data-blob.md)