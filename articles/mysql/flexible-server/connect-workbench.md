---
title: 'Gyors útmutató: kapcsolat – MySQL Workbench – Azure Database for MySQL – rugalmas kiszolgáló'
description: Ez a rövid útmutató ismerteti a MySQL Workbench használatával történő kapcsolódást és az adatok lekérdezését Azure Database for MySQL rugalmas kiszolgálóról.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 95ecb64165075a7effe0c6eaf568ee172ad67b9b
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226294"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server-preview"></a>Rövid útmutató: a MySQL Workbench használata a Azure Database for MySQL rugalmas kiszolgálóval való kapcsolódáshoz és adatlekérdezéshez (előzetes verzió)


> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a rövid útmutató bemutatja, hogyan csatlakozhat egy Azure Database for MySQL rugalmas kiszolgálóhoz a MySQL Workbench alkalmazás használatával.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató az alábbi útmutatók valamelyikében létrehozott erőforrásokat használja kiindulópontként:

- [Azure Database for MySQL rugalmas kiszolgáló létrehozása Azure Portal használatával](./quickstart-create-server-portal.md)
- [Azure Database for MySQL rugalmas kiszolgáló létrehozása az Azure CLI-vel](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Az ügyfél munkaállomásának előkészítése
- Ha a rugalmas kiszolgálót *privát hozzáféréssel (VNet-integrációval)* hozta létre, akkor a kiszolgálóval egy olyan erőforráshoz kell csatlakoznia, amely a kiszolgálóval megegyező VNet belül található. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a rugalmas kiszolgálóval létrehozott VNet. Lásd: [Azure Database for MySQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése az Azure CLI-](./how-to-manage-virtual-network-cli.md)vel.
- Ha a rugalmas kiszolgálót *nyilvános hozzáféréssel (engedélyezett IP-címekkel)* hozta létre, akkor a helyi IP-címét felveheti a kiszolgálón található tűzfalszabályok listájára. Tekintse át [Azure Database for MySQL rugalmas kiszolgálói tűzfalszabályok létrehozása és kezelése az Azure CLI használatával](./how-to-manage-firewall-cli.md)című témakört.

- Töltse le és telepítse a MySQL Workbench alkalmazást a számítógépére [a MySQL webhelyről](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

A rugalmas kiszolgálóhoz való kapcsolódáshoz szükséges kapcsolati adatok lekérése. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Azure Portal bal oldali menüjében válassza a **minden erőforrás** elemet, majd keresse meg a létrehozott kiszolgálót (például **mydemoserver**).
3. Válassza ki a kiszolgálónevet.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>Kapcsolódás a kiszolgálóhoz a MySQL Workbench használatával

Kapcsolódás Azure Database for MySQL rugalmas kiszolgálóhoz a MySQL Workbench használatával:

1. Indítsa el a MySQL Workbench alkalmazást a számítógépen.

2. A **Setup New Connection** (Új kapcsolat beállítása) párbeszédpanelen adja meg a következő információkat a **Parameters** (Paraméterek) lapon:

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="új kapcsolat beállítása":::

    | **Paraméterek** | **Ajánlott érték** | **Mező leírása** |
    |---|---|---|
    |    Kapcsolat neve | Bemutató kapcsolat | Adjon meg egy címkét a kapcsolathoz. |
    | Connection Method (Kapcsolati módszer) | Standard (TCP/IP) | A Standard (TCP/IP) elégséges. |
    | Hostname (Gazdanév) | *kiszolgáló neve* | Adja meg azt a kiszolgálónevet, amelyet korábban a MySQL-hez készült Azure-adatbázis létrehozásakor használt. Az itt látható példakiszolgáló a mydemoserver.mysql.database.azure.com. Használja a teljes tartománynevet (\*.mysql.database.azure.com), ahogyan az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit.  |
    | Port | 3306 | A MySQL-hez készült Azure-adatbázishoz való csatlakozáskor mindig a 3306-os portot használja. |
    | Felhasználónév |  *kiszolgáló-rendszergazdai bejelentkezési név* | Írja be a kiszolgáló-rendszergazdai bejelentkezési felhasználónevet, amelyet korábban a MySQL-hez készült Azure-adatbázis létrehozásakor adott meg. A példában szereplő Felhasználónév a myadmin. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit.
    | Jelszó | az ön jelszava | A jelszó mentéséhez kattintson a **tár tárolóban..** . gombra. |

3. Kattintson a **Kapcsolat tesztelése** lehetőségre, hogy tesztelje, minden paraméter helyesen lett-e konfigurálva.

4. A kapcsolódás mentéséhez kattintson **az OK** gombra.

5. A **MySQL Connections** (MySQL kapcsolatok) listában kattintson a kiszolgáló csempéjére, majd várja meg a kapcsolat létrejöttét.

    Megnyílik egy új SQL lap egy üres szerkesztővel, ahol beírhatja a lekérdezéseket.

> [!NOTE]
> A TLS 1,2 protokollt használó titkosított kapcsolatok szükségesek és érvényben vannak a Azure Database for MySQL rugalmas kiszolgálón. Bár a MySQL Workbench a kiszolgálóhoz való kapcsolódáshoz általában nem szükséges TLS/SSL-tanúsítványokkal, a TLS/SSL HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány kötését javasoljuk a MySQL Workbench használatával. További információ: a [TLS/SSL használatával történő kapcsolat](./how-to-connect-tls-ssl.md)

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

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="MySQL Workbench SQL lap SQL-mintakód futtatásához":::

2. A SQL-mintakód futtatásához kattintson az **SQL File** (SQL-fájl) lévő eszköztár villám ikonjára.
3. Figyelje meg az oldal közepén, a **Result Grid** (Eredménytáblázat) szakaszban lévő többlapos eredményeket.
4. Figyelje meg az oldal alján lévő **Output** (Kimenet) listát. Láthatja az egyes parancsok állapotát.

Most, hogy a MySQL Workbench használatával csatlakoztatta Azure Database for MySQL rugalmas kiszolgálóhoz, és az SQL nyelv használatával kérdezi le az adatlekérdezéseket.

## <a name="next-steps"></a>Következő lépések
- [Titkosított kapcsolat Transport Layer Security (TLS 1,2) használatával Azure Database for MySQL-rugalmas kiszolgálón](./how-to-connect-tls-ssl.md).
- További információ a [Azure Database for MySQL rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md).
- [Azure Database for MySQL rugalmas kiszolgálói tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./how-to-manage-firewall-portal.md).
- [Azure Database for MySQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése Azure Portal használatával](./how-to-manage-virtual-network-portal.md).
