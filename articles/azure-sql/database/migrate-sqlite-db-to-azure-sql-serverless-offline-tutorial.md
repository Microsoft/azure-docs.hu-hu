---
title: 'Oktatóanyag: az SQLite-adatbázis migrálása Azure SQL Database kiszolgáló nélkülire'
description: Megtudhatja, hogyan hajthat végre offline áttelepítést az SQLite-ből Azure SQL Database kiszolgáló nélkülire Azure Data Factory használatával.
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: e2f240247cbba0f80254d504792df45be55c6a1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790406"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Az SQLite-adatbázis migrálása Azure SQL Database kiszolgáló nélkülire
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az SQLite sok ember számára teszi lehetővé az adatbázisok és az SQL programozás első élményét. Számos operációs rendszerbe és népszerű alkalmazásba való felvételsel a világ egyik legszélesebb körben üzembe helyezett és használt adatbázis-motorja is elérhetővé válik. Mivel valószínűleg az első adatbázismotor sok ember használja, gyakran a projektek vagy alkalmazások központi része lehet. Ezekben az esetekben, amikor a projekt vagy alkalmazás a kezdeti SQLite-implementációt fejleszti, előfordulhat, hogy a fejlesztőknek át kell telepíteniük az adataikat egy megbízható, központosított adattárba.

Azure SQL Database kiszolgáló nélküli az önálló adatbázisok számítási szintje, amely a számítási feladatok igény szerint automatikusan méretezi a számítást, és a másodpercenként felhasznált számítások mennyiségére vonatkozó számlákat. A kiszolgáló nélküli számítási szint emellett automatikusan szünetelteti az adatbázisokat az inaktív időszakok során, amikor csak a tárterület használati díját számlázzuk ki, és automatikusan folytatja az adatbázisok működését, amikor ismét előfordulnak tevékenységek.

Ha követte az alábbi lépéseket, az adatbázis Azure SQL Database kiszolgálóra lesz áttelepítve, így az adatbázis elérhetővé válik a felhőben lévő más felhasználók vagy alkalmazások számára, és csak a ténylegesen használt funkciókért kell fizetnie, az alkalmazás kódjának minimális változásával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés
- Az áttelepíteni kívánt SQLite2-vagy SQLite3-adatbázis
- Windows-környezet
  - Ha nem rendelkezik helyi Windows-környezettel, az áttelepítéshez használhat egy Windows rendszerű virtuális gépet az Azure-ban. Helyezze át, és tegye elérhetővé az SQLite-adatbázisfájlt a virtuális gépen Azure Files és Storage Explorer használatával.

## <a name="steps"></a>Lépések

1. Hozzon létre egy új Azure SQL Database a kiszolgáló nélküli számítási szinten.

    ![Képernyőfelvétel a Azure Portal Azure SQL Database kiszolgáló nélküli kiépítési példáját bemutató képernyőképről](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. Győződjön meg arról, hogy az SQLite-adatbázisfájl elérhető a Windows-környezetben. Telepítsen egy SQLite ODBC-illesztőt, ha még nem rendelkezik ilyennel (a nyílt forráskód számos elérhető, például: http://www.ch-werner.de/sqliteodbc/) .

3. Hozzon létre egy rendszeradatforrás-adatforrást az adatbázishoz. Győződjön meg arról, hogy a rendszerarchitektúrájának megfelelő adatforrás-rendszergazda alkalmazást használja (32-bit vs 64-bit). A rendszerbeállítások között megtekintheti, hogy melyik verziót futtatja.

    - Nyissa meg az ODBC-adatforrás rendszergazdáját a környezetben.
    - Kattintson a rendszerdsn fülre, és kattintson a "Hozzáadás" gombra.
    - Válassza ki a telepített SQLite ODBC-összekötőt, és adjon meg egy értelmes nevet a csatlakozásnak, például sqlitemigrationsource
    - Adja meg az adatbázis nevét az. db fájlba
    - Mentés és kilépés

4. Töltse le és telepítse a saját üzemeltetésű integrációs modult. Ennek a legegyszerűbb módja a dokumentációban részletezett expressz telepítési lehetőség. Ha manuális telepítést választ, meg kell adnia az alkalmazást egy hitelesítési kulccsal, amely az Data Factory-példányban az alábbiak szerint helyezhető el:

    - Az ADF elindítása (szerző és figyelő a szolgáltatásból a Azure Portal)
    - Kattintson a "Szerző" fülre (kék ceruza) a bal oldalon
    - Kattintson a kapcsolatok (bal alsó), majd az integrációs modulok elemre.
    - Vegyen fel új Self-Hosted Integration Runtime, adjon meg egy nevet, válassza a *2. lehetőséget*.

5. Hozzon létre egy új társított szolgáltatást a forrás SQLite-adatbázishoz a Data Factory.

    ![Képernyőfelvétel: üres társított szolgáltatások panel Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. A **kapcsolatok** területen a **társított szolgáltatás** alatt kattintson az **új** elemre.

7. Az "ODBC" összekötő keresése és kiválasztása

   ![A Azure Data Factory társított szolgáltatások paneljén látható ODBC-összekötő emblémáját ábrázoló képernyőfelvétel](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. Adjon egy értelmes nevet a társított szolgáltatásnak, például: "sqlite_odbc". Válassza ki az integrációs modult a "csatlakozás Integration Runtime használatával" legördülő menüből. Adja meg az alábbi értéket a kapcsolati karakterláncban, és cserélje le a kezdeti katalógus változót a. db fájl filepath, valamint az DSN-t a rendszeradatforrás-kapcsolat nevével:

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. A hitelesítés típusának beállítása névtelenként

10. A kapcsolat tesztelése

    ![A Azure Data Factory sikeres csatlakoztatását bemutató képernyőfelvétel](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. Hozzon létre egy másik társított szolgáltatást a kiszolgáló nélküli SQL-célként. Válassza ki az adatbázist a társított szolgáltatás varázslóval, és adja meg az SQL-hitelesítési hitelesítő adatokat.

    ![Képernyőfelvétel: Azure SQL Database kiválasztva Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. Bontsa ki a CREATE TABLE utasításokat az SQLite-adatbázisból. Ezt úgy teheti meg, hogy az alábbi Python-szkriptet futtatja az adatbázis-fájlján.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Hozza létre a lekérési táblákat a kiszolgáló nélküli SQL-célként a CREATE TABLE utasítást a CreateTables. SQL fájlból, és futtassa az SQL-utasításokat a Azure Portal lekérdezési szerkesztőjében.

14. Térjen vissza a Data Factory kezdőlapjára, és kattintson a "Adatok másolása" gombra a feladatok létrehozása varázsló futtatásához.

    ![A Adatok másolása varázsló emblémáját ábrázoló képernyőkép a Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. Jelölje ki az összes táblát a forrás SQLite-adatbázisból a jelölőnégyzetek használatával, és rendelje hozzá őket az Azure SQL-beli cél tábláihoz. Miután a feladatot futtatta, sikeresen áttelepítette az adatait az SQLite-ből az Azure SQL-be!

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg a rövid útmutató [: önálló adatbázis létrehozása Azure SQL Database a Azure Portal használatával](single-database-create-quickstart.md)című témakört.
- Az erőforrások korlátaival kapcsolatban lásd: [kiszolgáló nélküli számítási keret erőforrás-korlátai](./resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).