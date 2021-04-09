---
title: Az Azure SQL Edge összekötése és lekérdezése
description: Ismerje meg, hogyan csatlakozhat az Azure SQL Edge-hez, és hogyan kérdezheti le azokat.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: b56b65261950e9cf534a3755d214229ef7d5bb1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395206"
---
# <a name="connect-and-query-azure-sql-edge"></a>Az Azure SQL Edge összekötése és lekérdezése

Az Azure SQL Edge-ben a tároló üzembe helyezése után a következő helyekről csatlakozhat az adatbázis-motorhoz:

- A tárolón belül
- Ugyanazon a gazdagépen futó másik Docker-tárolóból
- A gazdagépről
- A hálózat bármely más ügyfélszámítógépéről

## <a name="tools-to-connect-to-azure-sql-edge"></a>Eszközök az Azure SQL Edge-hez való kapcsolódáshoz

Bármelyik általános eszközről csatlakozhat az Azure SQL Edge-példány egy példányához:

* [Sqlcmd](/sql/linux/sql-server-linux-setup-tools): a Sqlcmd ügyféleszközök már szerepelnek az Azure SQL Edge tároló-rendszerképében. Ha egy futó tárolóhoz egy interaktív bash-rendszerhéj csatlakozik, az eszközöket helyileg is futtathatja. Az SQL-ügyfél eszközei nem érhetők el a ARM64 platformon, mivel azok nem szerepelnek az SQL Edge-tárolók ARM64-verziójában. 
* [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)

Ha egy hálózati gépről szeretne csatlakozni egy Azure SQL Edge-adatbázis-motorhoz, a következőkre lesz szüksége:

- **A gazdagép IP-címe vagy hálózati neve**: ez az a gazdagép, ahol az Azure SQL Edge-tároló fut.
- **Azure SQL Edge Container Host port leképezése**: Ez a Docker-tároló portjának hozzárendelése a gazdagép egy portjához. A tárolón belül az Azure SQL Edge mindig a 1433-es portra van leképezve. Ezt megteheti, ha szeretné. A portszám módosításához frissítse a **tároló létrehozási beállításait** a Azure IOT Edge Azure SQL Edge-modulhoz. A következő példában a tárolón lévő 1433-es port a gazdagép 1600-es portjára van leképezve.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **Rendszergazdai jelszó az Azure SQL Edge-példányhoz**: Ez a `SA_PASSWORD` környezeti változóhoz megadott érték az Azure SQL Edge telepítése során.

## <a name="connect-to-the-database-engine-from-within-the-container"></a>Kapcsolódás az adatbázis-motorhoz a tárolón belülről

Az [SQL Server parancssori eszközöket](/sql/linux/sql-server-linux-setup-tools) az Azure SQL Edge Container-rendszerképe tartalmazza. Ha egy interaktív parancssorhoz csatolja a tárolót, az eszközöket helyileg is futtathatja. Az SQL-ügyfél eszközei nem érhetők el a ARM64 platformon, mivel azok nem szerepelnek az SQL Edge-tárolók ARM64-verziójában. 

1. A `docker exec -it` paranccsal interaktív bash-rendszerhéj indítható el a futó tárolóban. A következő példában `e69e056c702d` a tároló azonosítója.

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > Nem mindig kell megadnia a teljes tároló AZONOSÍTÓját. Csak annyi karaktert kell megadnia, hogy egyedileg azonosítsa azt. Így ebben a példában `e6` `e69` a teljes azonosító helyett elég lehet a vagy a használata.

2. Ha a tárolón belül van, a Sqlcmd-mel kapcsolódjon a helyi hálózathoz. A Sqlcmd alapértelmezés szerint nem található az elérési úton, ezért meg kell adnia a teljes elérési utat.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Ha végzett a Sqlcmd, írja be a következőt: `exit` .

4. Ha elkészült az interaktív parancssorral, írja be a következőt: `exit` . Az interaktív bash-rendszerhéjból való kilépés után a tároló továbbra is fut.

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>Kapcsolódás az Azure SQL Edge-hez egy másik tárolóból ugyanazon a gazdagépen

Mivel az ugyanazon a gazdagépen futó két tároló ugyanazon a Docker-hálózaton található, egyszerűen elérheti őket a szolgáltatás tárolójának nevével és portszámával. Ha például az Azure SQL Edge-példányhoz csatlakozik egy másik Python-modulból (tárolóból) ugyanazon a gazdagépen, az alábbihoz hasonló kapcsolati karakterláncot használhat. (Ez a példa feltételezi, hogy az Azure SQL Edge az alapértelmezett port figyelésére van konfigurálva.)

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>Kapcsolódás az Azure SQL Edge-hez egy másik hálózati gépről

Előfordulhat, hogy a hálózat egy másik számítógépéről szeretne csatlakozni az Azure SQL Edge-példányához. Ehhez használja a Docker-gazdagép IP-címét és azt a gazda portot, amelyhez az Azure SQL Edge-tároló le van képezve. Ha például a Docker-gazdagép IP-címe *xxx.xxx.xxx.xxx*, és az Azure SQL Edge-tároló a *1600*-es gazdagép-portra van leképezve, akkor az Azure SQL Edge-példány kiszolgálójának címe *xxx. xxx. xxx. xxx, 1600*. A frissített Python-szkript:

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Ha a Windows rendszerű gépen futó SQL Server Management Studio használatával szeretne csatlakozni az Azure SQL Edge egy példányához, tekintse meg a következőt: [SQL Server Management Studio](/sql/linux/sql-server-linux-manage-ssms).

Ha az Azure SQL Edge egy példányához szeretne csatlakozni a Visual Studio Code használatával Windows, Mac vagy Linux rendszerű gépen, tekintse meg a [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)-ot.

Ha Windows, Mac vagy Linux rendszerű gépen Azure Data Studio használatával szeretne csatlakozni az Azure SQL Edge egy példányához, tekintse meg a [Azure Data Studio](/sql/azure-data-studio/quickstart-sql-server).

## <a name="next-steps"></a>Következő lépések

[Csatlakozás és lekérdezés](/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[SQL Server-eszközök telepítése Linux rendszeren](/sql/linux/sql-server-linux-setup-tools)