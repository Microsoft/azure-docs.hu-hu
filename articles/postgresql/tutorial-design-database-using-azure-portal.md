---
title: 'Oktatóanyag: Azure Database for postgresql-hez az Azure portal használatával tervezése'
description: Ez az oktatóanyag bemutatja, hogyan tervezheti meg az első Azure Database for PostgreSQL szolgáltatását az Azure Portal használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: tutorial, mvc
ms.topic: tutorial
ms.date: 03/20/2018
ms.openlocfilehash: 441b56aa683ac01566d2fd2877b9b8b912194f3a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536525"
---
# <a name="tutorial-design-an-azure-database-for-postgresql-using-the-azure-portal"></a>Oktatóanyag: Azure Database for postgresql-hez az Azure portal használatával tervezése

A PostgreSQL-hez készült Azure Database felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure Portallal könnyedén kezelheti a kiszolgálót és tervezhet adatbázist.

Ebből az oktatóanyagból az Azure Portal használatával megtanulhatja a következőket:
> [!div class="checklist"]
> * Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogrammal
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

## <a name="prerequisites"></a>Előfeltételek
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Azure-adatbázis létrehozása PostgreSQL-hez

Az Azure-adatbázis PostgreSQL-kiszolgálóhoz [számítási és tárolási erőforrások](./concepts-compute-unit-and-storage.md) egy meghatározott készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül jön létre.

Kövesse az alábbi lépéseket az Azure-adatbázis PostgreSQL-kiszolgálóhoz létrehozásához:
1.  Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2.  Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **Azure-adatbázis PostgreSQL-kiszolgálóhoz** lehetőséget.
  ![Azure-adatbázis PostgreSQL-hez - Az adatbázis létrehozása](./media/tutorial-design-database-using-azure-portal/1-create-database.png)

3.  Adja meg az alábbi adatokat az új kiszolgálóűrlapon:

    ![A kiszolgáló létrehozása](./media/tutorial-design-database-using-azure-portal/2-create.png)

    - A kiszolgáló neve: **mydemoserver** (a kiszolgáló neve DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie) 
    - Előfizetés: Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van.
    - Erőforráscsoport: **myresourcegroup**
    - Az Ön által választott kiszolgálói rendszergazdai bejelentkezési név és jelszó
    - Hely
    - PostgreSQL-verzió

   > [!IMPORTANT]
   > A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia az oktatóanyag későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

4.  Az új kiszolgáló tarifacsomagjának megadásához kattintson a **Tarifacsomag** elemre. Ehhez az oktatóanyaghoz válassza a következőket: **Általános célú** tarifacsomag, **4. generációs** számítási generáció, 2 **virtuális mag**, 5 GB **tárhely** és 7 napos **megőrzési idő a biztonsági mentésekhez**. Ha azt szeretné, hogy a rendszer georedundáns tárhelyen tárolja a kiszolgáló automatikus biztonsági másolatait, válassza a **Georedundáns** lehetőséget.
 ![Azure Database for PostgreSQL – tarifacsomag kiválasztása](./media/tutorial-design-database-using-azure-portal/2-pricing-tier.png)

5.  Kattintson az **OK** gombra.

6.  A kiszolgáló üzembe helyezéséhez kattintson a **Létrehozás** elemre. Az üzembe helyezés eltarthat néhány percig.

7.  Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.
 ![Azure-adatbázis PostgreSQL-hez - Értesítések megtekintése](./media/tutorial-design-database-using-azure-portal/3-notifications.png)

   > [!TIP]
   > A **Rögzítés az irányítópulton** lehetőséggel egyszerűen nyomon követheti az üzembe helyezést.

   Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. 

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Az Azure Database for PostgreSQL szolgáltatás a kiszolgáló szintjén használja a tűzfalat. Alapértelmezés szerint ez a tűzfal megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha olyan tűzfalszabályt hoz létre, amely megnyitja a tűzfalat egy adott IP-címtartomány számára. 

1.  Miután befejeződött az üzembe helyezés, kattintson az **Összes erőforrás** elemre a bal oldali menüben, és írja be a **mydemoserver** nevet az újonnan létrehozott kiszolgáló megkereséséhez. Kattintson a keresési eredményekben listázott kiszolgálónévre. Megnyílik a kiszolgáló **Áttekintés** oldala, amely további konfigurációs lehetőségeket biztosít.

   ![Azure-adatbázis PostgreSQL-hez - Kiszolgáló keresés ](./media/tutorial-design-database-using-azure-portal/4-locate.png)

2.  A kiszolgáló lapján válassza a **Kapcsolatbiztonság** elemet. 

3.  Kattintson a **Szabálynév** alatti szövegmezőbe, és adjon hozzá egy új tűzfalszabályt az IP-címtartomány összekapcsolhatóságának engedélyezéséhez. Adja meg az IP-címtartományt. Kattintson a **Save** (Mentés) gombra.

   ![Azure-adatbázis PostgreSQL-hez - Tűzfalszabály létrehozása](./media/tutorial-design-database-using-azure-portal/5-firewall-2.png)

4.  Kattintson a **Mentés** gombra, majd kattintson az **X**-re a **Kapcsolatbiztonság** oldal bezárásához.

   > [!NOTE]
   > Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 5432-es portot.
   >

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

Amikor létrehozta az Azure Database for PostgreSQL-kiszolgálót, az alapértelmezett **postgres** adatbázis is létrejött. A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.

1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** elemre, és keressen rá az újonnan létrehozott kiszolgálóra.

   ![Azure-adatbázis PostgreSQL-hez - Kiszolgáló keresés ](./media/tutorial-design-database-using-azure-portal/4-locate.png)

2. Kattintson a **mydemoserver** kiszolgálónévre.

3. Válassza ki a kiszolgáló **Áttekintés** oldalát. Jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**.

   ![Azure-adatbázis PostgreSQL-hez - Kiszolgáló-rendszergazdai bejelentkezés](./media/tutorial-design-database-using-azure-portal/6-server-name.png)


## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Csatlakozás a PostgreSQL-adatbázishoz a psql használatával a Cloud Shell-ben

Használjuk a [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) parancssori segédprogramot az Azure Database for PostgreSQL-kiszolgálóhoz való kapcsolódáshoz. 
1. Indítsa el az Azure Cloud Shell-t a felső navigációs ablakban található terminálikonnal.

   ![Azure-adatbázis PostgreSQL-hez - Azure Cloud Shell terminálikon](./media/tutorial-design-database-using-azure-portal/7-cloud-shell.png)

2. Az Azure Cloud Shell megnyílik a böngészőben, amely lehetővé teszi a bash-parancsok beírását.

   ![Azure-adatbázis PostgreSQL-hez - Azure Shell Bash parancssor](./media/tutorial-design-database-using-azure-portal/8-bash.png)

3. A Cloud Shell parancssornál csatlakozzon az Azure-adatbázis PostgreSQL-kiszolgálóhoz a psql-parancsok használatával. A következő formátum segítségével kapcsolódhat egy [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogrammal rendelkező Azure-adatbázis PostgreSQL-kiszolgálóhoz:
   ```bash
   psql --host=<myserver> --port=<port> --username=<server admin login> --dbname=<database name>
   ```

   Például a következő parancs a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a **mydemoserver.postgres.database.azure.com** PostgreSQL-kiszolgálón a hozzáférési hitelesítő adatok használatával. Adja meg a kiszolgáló-rendszergazdai jelszavát, amikor a rendszer kéri.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

## <a name="create-a-new-database"></a>Új adatbázis létrehozása
Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist, amikor a rendszer kéri.
```bash
CREATE DATABASE mypgsqldb;
```

Amikor a rendszer kéri, hajtsa végre a következő parancsot, a kapcsolat átváltásához az újonnan létrehozott adatbázisra **mypgsqldb**.
```bash
\c mypgsqldb
```
## <a name="create-tables-in-the-database"></a>Táblák létrehozása az adatbázisban
Most, hogy tudja, hogyan csatlakozhat az Azure Database for PostgreSQL szolgáltatáshoz, elvégezhet néhány alapvető feladatot:

Először hozzunk létre egy táblát, és töltsük fel adatokkal. Hozzunk létre egy táblát a leltáradatok nyomon követéséhez az alábbi SQL-kóddal:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Az újonnan létrehozott tábla a táblák listájában való megtekintéséhez írja be a következőt:
```sql
\dt
```

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba
Most, hogy már rendelkezünk egy táblával, szúrjunk be néhány adatot. A megnyitott parancssori ablakban futtassa a következő lekérdezést néhány adatsor beszúrásához.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Most két mintaadatsorral rendelkezünk a korábban létrehozott készlettáblában.

## <a name="query-and-update-the-data-in-the-tables"></a>A táblákban lévő adatok lekérdezése és frissítése
Hajtsa végre a következő lekérdezést a készlet-adatbázistáblában lévő információk lekéréséhez. 
```sql
SELECT * FROM inventory;
```

A táblában lévő adatokat frissítheti is.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A frissített értékeket az adatok lekérésekor tekintheti meg.
```sql
SELECT * FROM inventory;
```

## <a name="restore-data-to-a-previous-point-in-time"></a>Adatok visszaállítása egy korábbi időpontra
Tegyük fel, hogy véletlenül töröltünk egy fontos adatbázistáblát. Ebből a helyzetből nem lehet könnyen helyreállni. Az Azure Database for PostgreSQL segítségével bármely olyan időpontra vissza lehet térni, amelyen a kiszolgálóról biztonsági mentés készült (ez a biztonsági mentések megőrzési idejéhez megadott beállításoktól függ), és az adott időpontra jellemző állapotot vissza lehet állítani egy új kiszolgálóra. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. Az alábbi lépések a készlettábla hozzáadása előtti időpontra állítják vissza a **mydemoserver** kiszolgálót.

1.  A kiszolgálóhoz tartozó Azure Database for PostgreSQL **Áttekintés** lapján kattintson az eszköztár **Visszaállítás** elemére. Megnyílik a **Visszaállítás** oldal.

   ![Azure Portal – A Visszaállítás űrlap beállításai](./media/tutorial-design-database-using-azure-portal/9-azure-portal-restore.png)

2.  Töltse ki a **Visszaállítás** űrlapot a szükséges információkkal:

   ![Azure Portal – A Visszaállítás űrlap beállításai](./media/tutorial-design-database-using-azure-portal/10-azure-portal-restore.png)

   - **Visszaállítási pont**: Válasszon egy pont kötött, amely a kiszolgáló módosítása előtti
   - **Célkiszolgáló**: Adja meg a visszaállítani kívánt új kiszolgáló nevét
   - **Hely**: Nem választhat ki a régiót, alapértelmezés szerint ugyanaz, mint a forráskiszolgálón
   - **A tarifacsomag**: Ez az érték nem módosítható, ha a kiszolgáló visszaállítása. Ugyanaz, mint a forráskiszolgálóé. 
3.  Az **OK** gombra kattintva [visszaállíthatja a kiszolgálót a tábla törlése előtti időpontra](./howto-restore-server-portal.md). A kiszolgáló egy másik időpontra való visszaállítása létrehozza a kiszolgáló megadott időpontra vonatkozó duplikált új másolatát, amennyiben az adott pont a [tarifacsomag](./concepts-pricing-tiers.md) adatmegőrzési időszakán belül esik.

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket az Azure Portal és más segédprogramok használatával:
> [!div class="checklist"]
> * Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogrammal
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

Ezután megtudhatja, hogyan végezhet el hasonló feladatokat az Azure CLI használatával, tekintse át az oktatóanyag: [Az első Azure Database for postgresql-hez az Azure CLI használatával tervezése](tutorial-design-database-using-azure-cli.md)
