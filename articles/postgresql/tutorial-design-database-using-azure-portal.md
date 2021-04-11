---
title: 'Oktatóanyag: Azure Database for PostgreSQL – egyetlen kiszolgáló megtervezése – Azure Portal'
description: Ez az oktatóanyag bemutatja, hogyan tervezheti meg az első Azure Database for PostgreSQL-kiszolgálót a Azure Portal használatával.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: tutorial, mvc
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: dd7ae71d8c3bc5415654527d5a650e0f09a99168
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605815"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Oktatóanyag: Azure Database for PostgreSQL – egyetlen kiszolgáló tervezése a Azure Portal használatával

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

## <a name="create-an-azure-database-for-postgresql"></a>Azure-adatbázis létrehozása PostgreSQL-hez

Az Azure-adatbázis PostgreSQL-kiszolgálóhoz [számítási és tárolási erőforrások](./concepts-pricing-tiers.md) egy meghatározott készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül jön létre.

Kövesse az alábbi lépéseket az Azure-adatbázis PostgreSQL-kiszolgálóhoz létrehozásához:
1. Kattintson az **erőforrás létrehozása**  elemre a Azure Portal bal felső sarkában.
2. Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **Azure-adatbázis PostgreSQL-kiszolgálóhoz** lehetőséget.
   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/1-create-database.png" alt-text="Azure-adatbázis PostgreSQL-hez - Az adatbázis létrehozása":::

3. Válassza az **egykiszolgálós** telepítés lehetőséget.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/select-deployment-option.png" alt-text="Azure Database for PostgreSQL kiválasztása – egykiszolgálós üzembe helyezési lehetőség":::

4. Töltse ki az **alapok** űrlapot a következő információkkal:

    :::image type="content" source="./media/tutorial-design-database-using-azure-portal/create-basics.png" alt-text="A kiszolgáló létrehozása":::

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetésének neve|A kiszolgálóhoz használni kívánt Azure-előfizetés. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport|*myresourcegroup*| Egy új erőforráscsoport neve vagy egy meglévő az előfizetéséből.
    Kiszolgálónév |*mydemoserver*|Egy egyedi név, amely az Azure Database for PostgreSQL-kiszolgálót azonosítja. A rendszer hozzáfűzi a *postgres.database.azure.com* tartománynevet a megadott kiszolgálónévhez. A kiszolgáló neve csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Legalább 3, és legfeljebb 63 karakterből állhat.
    Adatforrás | *Nincs* | Válassza a *Nincs* lehetőséget egy teljesen új kiszolgáló létrehozásához. (Ha egy meglévő Azure Database for PostgreSQL-kiszolgáló georedundáns biztonsági mentéséből hoz létre kiszolgálót, válassza a *Biztonsági mentés* lehetőséget).
    Rendszergazdai felhasználónév |*myadmin*| A kiszolgálóhoz való csatlakozáshoz használt bejelentkezési fiókja. A rendszergazdai bejelentkezési név nem lehet **azure_superuser**, **azure_pg_admin**, **Admin**, **Administrator**, **root**, **Guest** vagy **Public**. Nem kezdődhet **pg_**.
    Jelszó |Az Ön jelszava| Egy új jelszó a kiszolgálói rendszergazdai fiók számára. A jelszó 8–128 karakterből állhat. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek (0–9) és nem alfanumerikus karakterek (!, $, #, % stb.).
    Hely|A felhasználókhoz legközelebb eső régió| A felhasználókhoz legközelebb eső hely.
    Verzió|A legújabb főverzió| A legújabb PostgreSQL-főverzió, hacsak nincsenek más konkrét követelmények.
    Számítás + tárolás | **Általános célú**, **5. generációs**, **2 virtuális mag**, **5 GB**, **7 nap**, **Georedundáns** | Az új kiszolgáló számítási, tárolási és biztonsági mentési konfigurációi. Válassza a **kiszolgáló konfigurálása** lehetőséget. Ezután válassza a **általános célú** fület. a *Gen 5*, *4 virtuális mag*, *100 GB* és *7 nap* a **számítási generáció**, a **virtuális mag**, a **tárolás** és a **biztonsági másolatok megőrzési időszakának** alapértelmezett értékei. Ezeket a csúszkákat meghagyhatja, vagy módosíthatja is azokat. A kiszolgáló georedundáns tárhelyre való biztonsági mentésének engedélyezéséhez válassza a **Biztonsági másolat redundanciabeállításai** területen a **Georedundáns** lehetőséget. A tarifacsomag beállításának mentéséhez válassza az **OK** gombot. A következő képernyőkép ezeket a beállításokat tartalmazza.

   > [!NOTE]
   > Érdemes lehet az alapszintű díjszabást használni, ha a számítási feladathoz elegendő a könnyű számítás és az I/O. Vegye figyelembe, hogy az alapszintű díjszabásban létrehozott kiszolgálók később nem méretezhetők át általános célú vagy a memóriára optimalizált értékre. További információért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/postgresql/) .
   > 

    :::image type="content" source="./media/quickstart-create-database-portal/2-pricing-tier.png" alt-text="A díjszabási szintek panelje":::

    > [!TIP]
    > Az **automatikus növekedés** lehetővé teszi, hogy a kiszolgáló növelje a tárterületet, ha közeledik a lefoglalt korláthoz, anélkül, hogy ez befolyásolná a munkaterhelést.

5. Válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások áttekintéséhez. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A művelet eltarthat néhány percig.

6. Az eszköztáron válassza az **Értesítések** ikont (csengő) az üzembehelyezési folyamat megfigyeléséhez. Az üzembe helyezés után kiválaszthatja a **Rögzítés az irányítópulton** lehetőséget, amellyel létrehoz egy csempét a kiszolgálóhoz az Azure Portal irányítópultján. A csempe a kiszolgáló **Áttekintés** oldalának közvetlen megnyitására szolgál. Az **Erőforrás megnyitása** lehetőség kiválasztásával megnyitja a kiszolgáló **Áttekintés** oldalát.

    :::image type="content" source="./media/quickstart-create-database-portal/3-notifications.png" alt-text="Az értesítések panel":::
   
   Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. (A másik alapértelmezett adatbázis az **azure_maintenance**. Az a feladata, hogy elkülönítse a felügyelt szolgáltatások folyamatait a felhasználói műveletektől. Ehhez az adatbázishoz nem lehet hozzáférni.)


## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Az Azure Database for PostgreSQL szolgáltatás a kiszolgáló szintjén használja a tűzfalat. Alapértelmezés szerint ez a tűzfal megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha olyan tűzfalszabályt hoz létre, amely megnyitja a tűzfalat egy adott IP-címtartomány számára. 

1. Miután befejeződött az üzembe helyezés, kattintson az **Összes erőforrás** elemre a bal oldali menüben, és írja be a **mydemoserver** nevet az újonnan létrehozott kiszolgáló megkereséséhez. Kattintson a keresési eredményekben listázott kiszolgálónévre. Megnyílik a kiszolgáló **Áttekintés** oldala, amely további konfigurációs lehetőségeket biztosít.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/4-locate.png" alt-text="Azure-adatbázis PostgreSQL-hez - Kiszolgáló keresés":::

2. A kiszolgáló lapon válassza a **kapcsolatbiztonsági** lehetőséget. 

3. Kattintson a **szabály neve** alatt található szövegmezőre, és adjon hozzá egy új tűzfalszabály-szabályt a kapcsolat IP-tartományának megadásához. Adja meg az IP-címtartományt. Kattintson a **Mentés** gombra.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/5-firewall-2.png" alt-text="Azure-adatbázis PostgreSQL-hez - Tűzfalszabály létrehozása":::

4. Kattintson a **Mentés** gombra, majd kattintson az **X**-re a **Kapcsolatbiztonság** oldal bezárásához.

   > [!NOTE]
   > Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL-adatbáziskiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 5432-es portot.
   >

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

Amikor létrehozta az Azure Database for PostgreSQL-kiszolgálót, az alapértelmezett **postgres** adatbázis is létrejött. A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.

1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** elemre, és keressen rá az újonnan létrehozott kiszolgálóra.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/4-locate.png" alt-text="Azure-adatbázis PostgreSQL-hez - Kiszolgáló keresés":::

2. Kattintson a **mydemoserver** kiszolgálónévre.

3. Válassza ki a kiszolgáló **Áttekintés** oldalát. Jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/6-server-name.png" alt-text="PostgreSQL-hez készült Azure-adatbázis – Kiszolgáló-rendszergazdai bejelentkezés":::


## <a name="connect-to-postgresql-database-using-psql"></a>Csatlakozás a PostgreSQL-adatbázishoz a psql használatával
Ha az ügyfélszámítógépen telepítve van a PostgreSQL, használhatja a [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html)helyi példányát vagy az Azure Cloud Console-t egy Azure PostgreSQL-kiszolgálóhoz való kapcsolódáshoz. Használjuk a psql parancssori segédprogramot az Azure-adatbázis PostgreSQL-kiszolgálóhoz való kapcsolódáshoz.

1. Futtassa a következő psql-parancsot az Azure Database for PostgreSQL-adatbázishoz való kapcsolódáshoz:
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Például a következő parancs a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a **mydemoserver.postgres.database.azure.com** PostgreSQL-kiszolgálón a hozzáférési hitelesítő adatok használatával. Adja meg a `<server_admin_password>` kiszolgálói rendszergazdai jelszót, amelyet a jelszó megadásakor választott.
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Ha inkább URL-elérési utat szeretne használni a postgres-hez való kapcsolódáshoz, az URL-cím kódolja a @ Sign nevet a következővel: `%40` . Például a psql tartozó kapcsolatok karakterlánca a következő:.
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist, amikor a rendszer erre kéri:
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. Amikor a rendszer kéri, hajtsa végre a következő parancsot, hogy az újonnan létrehozott **mypgsqldb** adatbázishoz kapcsolódhasson:
   ```sql
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
Tegyük fel, hogy véletlenül töröltünk egy fontos adatbázistáblát. Ebből a helyzetből nem lehet könnyen helyreállni. Az Azure Database for PostgreSQL segítségével bármely olyan időpontra vissza lehet térni, amelyről a kiszolgáló biztonsági mentéssel rendelkezik (ez a biztonsági mentések megőrzési idejéhez megadott beállításoktól függ), és ezt a pontot vissza lehet állítani egy új kiszolgálóra. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. Az alábbi lépések a készlettábla hozzáadása előtti időpontra állítják vissza a **mydemoserver** kiszolgálót.

1. A kiszolgálóhoz tartozó Azure Database for PostgreSQL **Áttekintés** lapján kattintson az eszköztár **Visszaállítás** elemére. Megnyílik a **visszaállítás** oldal.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/9-azure-portal-restore.png" alt-text="A kiszolgáló Azure Database for PostgreSQL * * áttekintés * * lapját megjelenítő képernyőkép, amely kiemeli a visszaállítás gombot.":::

2. Töltse ki a **Visszaállítás** űrlapot a szükséges információkkal:

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/10-azure-portal-restore.png" alt-text="Azure Portal – A Visszaállítás űrlap beállításai":::

   - **Visszaállítási pont**: válasszon olyan időpontot, amely a kiszolgáló módosítása előtt következik be
   - **Célkiszolgáló**: Adja meg az új kiszolgáló nevét, amelyre a biztonsági másolatot vissza kívánja állítani
   - **Hely**: Nem választhatja ki a régiót – alapértelmezés szerint ugyanaz lesz, mint a forráskiszolgálóé
   - **Tarifacsomag**: A kiszolgáló visszaállításakor nem módosíthatja ezt az értéket. Ugyanaz, mint a forráskiszolgálóé. 
3. Az **OK** gombra kattintva [visszaállíthatja a kiszolgálót a tábla törlése előtti időpontra](./howto-restore-server-portal.md). A kiszolgáló egy másik időpontra való visszaállítása létrehozza a kiszolgáló megadott időpontra vonatkozó duplikált új másolatát, amennyiben az adott pont a [tarifacsomag](./concepts-pricing-tiers.md) adatmegőrzési időszakán belül esik.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy kiszolgálócsoport számára. Ha nem várható, hogy a jövőben szüksége lesz ezekre az erőforrásokra, törölje a kiszolgálót. A kiszolgálócsoport *Áttekintés* lapján kattintson a *Törlés* gombra. Amikor a rendszer rákérdez egy előugró oldalra, erősítse meg a kiszolgálócsoport nevét, és kattintson a végleges *Törlés* gombra.

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket az Azure Portal és más segédprogramok használatával:
> [!div class="checklist"]
> * Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a **psql** segédprogram használatával
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

> [!div class="nextstepaction"]
>[Az első Azure Database for PostgreSQL megtervezése az Azure CLI használatával](tutorial-design-database-using-azure-cli.md)