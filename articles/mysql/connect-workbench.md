---
title: 'Gyors útmutató: kapcsolat – MySQL Workbench – Azure Database for MySQL'
description: Ez a rövid útmutató bemutatja, hogy a MySQL Workbench használatával hogyan csatlakozhat az Azure Database for MySQL szolgáltatáshoz, és hogyan kérdezhet le adatokat.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 5e27cfec0a3f0a58c1e94a822e0c831f4efa1b32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94535537"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rövid útmutató: a MySQL Workbench használata az Azure Database for MySQL-beli adatlekérdezéshez

Ebben a rövid útmutatóban azt szemléltetjük, hogy miként lehet a MySQL Workbench alkalmazás használatával csatlakozni egy Azure Database for MySQL kiszolgálóhoz.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató az alábbi útmutatók valamelyikében létrehozott erőforrásokat használja kiindulópontként:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> Győződjön meg arról, hogy az IP-cím, amelyhez csatlakozik, a [Azure Portal](./howto-manage-firewall-using-portal.md) vagy az [Azure CLI](./howto-manage-firewall-using-cli.md) használatával adja hozzá a kiszolgáló tűzfalszabály-szabályait.

## <a name="install-mysql-workbench"></a>A MySQL Workbench telepítése
Töltse le és telepítse a MySQL Workbench alkalmazást a számítógépére [a MySQL webhelyről](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).

2. Az Azure Portal bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például **mydemoserver**).

3. Kattintson a kiszolgálónévre.

4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="A MySQL-hez készült Azure Database-kiszolgáló neve":::

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Csatlakozás a kiszolgálóhoz a MySQL Workbench használatával 
Kapcsolódás az Azure MySQL-kiszolgálóhoz a MySQL Workbench GUI eszköz használatával:

1.    Indítsa el a MySQL Workbench alkalmazást a számítógépen. 

2.    A **Setup New Connection** (Új kapcsolat beállítása) párbeszédpanelen adja meg a következő információkat a **Parameters** (Paraméterek) lapon:

:::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="új kapcsolat beállítása":::

| **Beállítás** | **Ajánlott érték** | **Mező leírása** |
|---|---|---|
|    Kapcsolat neve | Bemutató kapcsolat | Adjon meg egy címkét a kapcsolathoz. |
| Connection Method (Kapcsolati módszer) | Standard (TCP/IP) | A Standard (TCP/IP) elégséges. |
| Hostname (Gazdanév) | *kiszolgáló neve* | Adja meg azt a kiszolgálónevet, amelyet korábban a MySQL-hez készült Azure-adatbázis létrehozásakor használt. Az itt látható példakiszolgáló a mydemoserver.mysql.database.azure.com. Használja a teljes tartománynevet (\*.mysql.database.azure.com), ahogyan az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit.  |
| Port | 3306 | A MySQL-hez készült Azure-adatbázishoz való csatlakozáskor mindig a 3306-os portot használja. |
| Felhasználónév |  *kiszolgáló-rendszergazdai bejelentkezési név* | Írja be a kiszolgáló-rendszergazdai bejelentkezési felhasználónevet, amelyet korábban a MySQL-hez készült Azure-adatbázis létrehozásakor adott meg. A példában szereplő felhasználónév a következő: myadmin@mydemoserver. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. A formátum a *username \@ servername*.
| Jelszó | az ön jelszava | A jelszó mentéséhez kattintson a **tár tárolóban..** . gombra. |

3.   Kattintson a **Kapcsolat tesztelése** lehetőségre, hogy tesztelje, minden paraméter helyesen lett-e konfigurálva. 

4.   A kapcsolódás mentéséhez kattintson **az OK** gombra. 

5.   A **MySQL Connections** (MySQL kapcsolatok) listában kattintson a kiszolgáló csempéjére, majd várja meg a kapcsolat létrejöttét.

        Megnyílik egy új SQL lap egy üres szerkesztővel, ahol beírhatja a lekérdezéseket.
    
        > [!NOTE]
        > Alapértelmezés szerint kötelezően SSL-kapcsolatbiztonságra van szükség az Azure Database for MySQL kiszolgálón. Bár általában nincs szükség az SSL-tanúsítványok további konfigurációjára ahhoz, hogy a MySQL Workbench csatlakozzon a kiszolgálóhoz, ajánlott az SSL-hitelesítésszolgáltatói tanúsítványt a MySQL Workbenchhez kötni. A tanúsítvány letöltéséről és kötéséről [az SSL-kapcsolatok alkalmazásban való konfigurálását ismertető, Azure Database for MySQL kiszolgálóra vonatkozó](./howto-configure-ssl.md) cikkben talál további információkat.  Ha le kell tiltania az SSL-t, látogasson el az Azure Portalra, és a Kapcsolatbiztonság lapon használja az SSL-kapcsolat kikényszerítése kapcsológombot a letiltásához.

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Tábla létrehozása, adatok beszúrása, adatok olvasása, adatok frissítése, adatok törlése
1. Másolja és illessze be az SQL-mintakódot egy üres SQL-lapra néhány mintaadat bemutatása érdekében.

    Ez a kód a quickstartdb nevű üres adatbázist hozza létre, majd létrehoz egy inventory nevű mintatáblát. Beszúr néhány sort, majd beolvassa a sorokat. Egy update utasítással módosítja az adatokat, és ismét beolvassa a sorokat. Végül töröl egy sort, majd ismét beolvassa a sorokat.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    A képernyőkép az SQL-példakódot és a futtatás utáni kimenetet mutatja az SQL Workbench-ben.
    
    :::image type="content" source="media/connect-workbench/3-workbench-sql-tab.png" alt-text="MySQL Workbench SQL lap SQL-mintakód futtatásához":::

2. A SQL-mintakód futtatásához kattintson az **SQL File** (SQL-fájl) lévő eszköztár villám ikonjára.
3. Figyelje meg az oldal közepén, a **Result Grid** (Eredménytáblázat) szakaszban lévő többlapos eredményeket. 
4. Figyelje meg az oldal alján lévő **Output** (Kimenet) listát. Láthatja az egyes parancsok állapotát. 

A MySQL Workbench használatával csatlakozott az Azure Database for MySQL kiszolgálóhoz, és az SQL nyelv használatával lekérdezte az adatokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a rövid útmutatóban használt összes erőforrás törléséhez törölje az erőforráscsoportot a következő parancs használatával:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./concepts-migrate-import-export.md)
