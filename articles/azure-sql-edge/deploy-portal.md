---
title: Az Azure SQL Edge üzembe helyezése a Azure Portal használatával
description: Ismerje meg, hogyan helyezheti üzembe az Azure SQL Edge-t a Azure Portal használatával
keywords: SQL Edge üzembe helyezése
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 5d7d232ada814d5d3c30e7b012c6289f847d641f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395087"
---
# <a name="deploy-azure-sql-edge"></a>Az Azure SQL Edge üzembe helyezése 

Az Azure SQL Edge egy IoT és Azure IoT Edge üzemelő példányokhoz optimalizált, a kapcsolatot kezelő adatbázismotor. Lehetőséget biztosít nagy teljesítményű adattárolási és feldolgozási réteg létrehozására a IoT alkalmazások és megoldások számára. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Azure SQL Edge-modult Azure IoT Edge segítségével a Azure Portal használatával.

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
* Hozzon létre egy [Azure-IoT hub](../iot-hub/iot-hub-create-through-portal.md).
* Hozzon létre egy [Azure IoT Edge eszközt](../iot-edge/how-to-install-iot-edge.md).

> [!NOTE]   
> Ha IoT Edge eszközként szeretne üzembe helyezni egy Azure-beli linuxos virtuális gépet, tekintse meg ezt a rövid [útmutatót](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Az SQL Edge-modul üzembe helyezése az Azure Marketplace-en

Az Azure Marketplace egy online alkalmazások és szolgáltatások piaca, ahol az Azure-on futó Nagyvállalati alkalmazások és megoldások széles körét böngészheti, beleértve a [IoT Edge modulokat](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)is. Az Azure SQL Edge üzembe helyezhető egy peremhálózati eszközön a piactéren.

1. Keresse meg az Azure SQL Edge-modult az Azure piactéren.<br><br>

   ![SQL Edge a piactéren](media/deploy-portal/find-offer-marketplace.png)

2. Válassza ki az igényeinek leginkább megfelelő szoftvercsomagot, és kattintson a **Létrehozás** gombra. <br><br>

   ![Válassza ki a megfelelő szoftvercsomagot](media/deploy-portal/pick-correct-plan.png)

3. A IoT Edge modul megcélzott eszközei lapon adja meg a következő adatokat, majd kattintson a **Létrehozás** gombra.

   |**Mező**  |**Leírás**  |
   |---------|---------|
   |Előfizetés  |  Az Azure-előfizetés, amely alatt a IoT Hub létre lett hozva |
   |IoT Hub   |  Azon IoT Hub neve, ahol a IoT Edge eszköz regisztrálva van, majd válassza az "üzembe helyezés eszközre" lehetőséget.|
   |IoT Edge eszköz neve  |  Annak a IoT Edge eszköznek a neve, amelyen az SQL Edge üzembe lett helyezve |

4. A **modulok beállítása az eszközön:** lapon kattintson az Azure SQL Edge-modulra **IoT Edge modulok** területen. Az alapértelmezett modul neve *AzureSQLEdge* értékre van állítva. 

5. A **IoT Edge-modul frissítése** panelen a *modul beállításai* szakaszban adja meg a *IoT Edge modul nevét*, az *Újraindítási szabályzatot* és a *kívánt állapotot*. 

   > [!IMPORTANT]    
   > Ne módosítsa vagy frissítse a **RENDSZERKÉP URI** -beállításait a modulon.

6. A **IoT Edge-modul frissítése** panel *környezeti változók* szakaszában válassza ki a környezeti változók kívánt értékeit. Az Azure SQL Edge környezeti változók teljes listáját a [Konfigurálás környezeti változók használatával](configure.md#configure-by-using-environment-variables)című témakörben találja. A modulhoz a következő alapértelmezett környezeti változók vannak meghatározva. 

   |**Paraméter**  |**Leírás**|
   |---------|---------|
   | MSSQL_SA_PASSWORD  | Módosítsa az alapértelmezett értéket úgy, hogy erős jelszót határozzon meg az SQL Edge rendszergazdai fiókjához. |
   | MSSQL_LCID   | Módosítsa az alapértelmezett értéket az SQL Edge-hez használni kívánt nyelvi azonosító megadásához. Például 1036 a francia. |
   | MSSQL_COLLATION | Módosítsa az alapértelmezett értéket az SQL Edge alapértelmezett rendezésének beállításához. Ez a beállítás felülbírálja a nyelvi azonosító (LCID) alapértelmezett leképezését a rendezéshez. |

   > [!IMPORTANT]    
   > Ne módosítsa vagy frissítse a **ACCEPT_EULA** környezeti változót a modulhoz.

7. A **IoT Edge-modul frissítése** panelen a *tároló létrehozási beállítások* szakaszában frissítse a következő beállításokat, igény szerint. 
   - **Gazda portja:** A megadott gazdagép portjának leképezése a tárolóban lévő 1433-as portra (alapértelmezett SQL-port).
   - **Kötések** és **csatlakoztatások:** ha egynél több SQL Edge-modult kell központilag telepítenie, győződjön meg arról, hogy a csatlakoztatások lehetőségre kattintva új forrás & cél párt hoz létre az állandó kötethez. A csatlakoztatásokról és a kötetekről a [kötetek használata](https://docs.docker.com/storage/volumes/) a Docker-dokumentációban című témakörben olvashat bővebben. 

   ```json
   {
    "HostConfig": {
        "CapAdd": [
            "SYS_PTRACE"
        ],
        "Binds": [
            "sqlvolume:/sqlvolume"
        ],
        "PortBindings": {
            "1433/tcp": [
                {
                    "HostPort": "1433"
                }
            ]
        },
        "Mounts": [
            {
                "Type": "volume",
                "Source": "sqlvolume",
                "Target": "/var/opt/mssql"
            }
        ]
    },
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "PlanId=asde-developer-on-iot-edge"
    ]
   }
   ```
   > [!IMPORTANT]    
   > Ne módosítsa a `PlanId` konfiguráció létrehozása beállításban definiált környezeti változót. Ha ez az érték módosul, az Azure SQL Edge-tároló nem fog elindulni. 
   
8. A **frissítés IoT Edge modul** ablaktáblán kattintson a **frissítés** elemre.
9. Az **eszközön lévő modulok beállítása** lapon kattintson a **Tovább gombra: útvonalak >** , ha útvonalakat kell meghatároznia az üzemelő példányhoz. Ellenkező esetben kattintson a **felülvizsgálat + létrehozás** gombra. Az útvonalak konfigurálásával kapcsolatos további információkért lásd: [modulok telepítése és útvonalak létrehozása IoT Edgeban](../iot-edge/module-composition.md).
11. Az **eszközön található modulok beállítása** lapon kattintson a **Létrehozás** gombra.

## <a name="connect-to-azure-sql-edge"></a>Kapcsolódás az Azure SQL Edge-hez

A következő lépések az Azure SQL Edge parancssori eszközt, az **Sqlcmd**-t használják a tárolón belül az Azure SQL Edge-hez való kapcsolódáshoz.

> [!NOTE]      
> Az SQL parancssori eszközök (Sqlcmd-EK) nem érhetők el az Azure SQL Edge-tárolók ARM64-verzióján belül.

1. A `docker exec -it` paranccsal interaktív bash-rendszerhéj indítható el a futó tárolóban. A következő példában a `azuresqledge` `Name` IoT Edge modul paraméterében megadott név szerepel.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. A tárolón belül a helyi kapcsolat Sqlcmd. A Sqlcmd alapértelmezés szerint nem található az elérési úton, ezért meg kell adnia a teljes elérési utat.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]    
   > A parancssorból kihagyhatja a jelszót, hogy a rendszer megkérje a jelszó megadását.

3. Ha a művelet sikeres, egy **Sqlcmd** parancssorba kell beolvasnia: `1>` .

## <a name="create-and-query-data"></a>Adatlekérdezés létrehozása és lekérdezése

A következő szakasz végigvezeti a **Sqlcmd** és a Transact-SQL használatával egy új adatbázis létrehozásához, az adathozzáadáshoz és a lekérdezés futtatásához.

### <a name="create-a-new-database"></a>Új adatbázis létrehozása

A következő lépések egy nevű új adatbázist hoznak létre `TestDB` .

1. A **Sqlcmd** parancssorba illessze be a következő Transact-SQL parancsot egy teszt adatbázis létrehozásához:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. A következő sorban írjon egy lekérdezést a kiszolgálón található összes adatbázis nevének visszaküldéséhez:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Adat beszúrása

Ezután hozzon létre egy új táblát, `Inventory` és szúrjon be két új sort.

1. A **Sqlcmd** -parancssorban váltson a kontextusra az új `TestDB` adatbázisra:

   ```sql
   USE TestDB
   ```

2. Új nevű tábla létrehozása `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Az új táblába szúrja be az adatbevitelt:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. `GO`Az előző parancsok végrehajtásához írja be a következőt:

   ```sql
   GO
   ```

### <a name="select-data"></a>Adatok kiválasztása

Most futtasson egy lekérdezést a tábla adatainak visszaküldéséhez `Inventory` .

1. A **Sqlcmd** parancssorában adjon meg egy lekérdezést, amely a tábla azon sorait adja vissza, amelyekben a mennyiség meghaladja a 152-et `Inventory` :

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Hajtsa végre a parancsot:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Kilépés a Sqlcmd-parancssorból

1. A **Sqlcmd** -munkamenet befejezéséhez írja be a következőt `QUIT` :

   ```sql
   QUIT
   ```

2. A tárolóban lévő interaktív parancssorból való kilépéshez írja be a következőt: `exit` . Az interaktív bash-rendszerhéjból való kilépés után a tároló továbbra is fut.

## <a name="connect-from-outside-the-container"></a>A tárolón kívülről való kapcsolat

SQL-lekérdezéseket kapcsolódhat és futtathat az Azure SQL Edge-példányon minden olyan külső Linux-, Windows-vagy macOS-eszközön, amely támogatja az SQL-kapcsolatokat. Az SQL Edge-tárolón kívülről való csatlakozásról a [csatlakozás és az Azure SQL Edge lekérdezése](./connect.md)című témakörben olvashat bővebben.

Ebben a rövid útmutatóban egy SQL Edge-modult telepített egy IoT Edge eszközön. 

## <a name="next-steps"></a>Következő lépések

- [Machine Learning és mesterséges intelligencia az SQL Edge ONNX](onnx-overview.md)
- [Végpontok közötti IoT-megoldás létrehozása az SQL Edge használatával IoT Edge](tutorial-deploy-azure-resources.md)
- [Adatfolyamok az Azure SQL Edge-ben](stream-data.md)
- [Üzembehelyezési hibák elhárítása](troubleshoot.md)