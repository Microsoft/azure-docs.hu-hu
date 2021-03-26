---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló'
description: Ebben a rövid útmutatóban egy Azure Database for PostgreSQL-kiszolgálót fog létrehozni és felügyelni a Azure Portal használatával.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 6b5b145e84a3844b646ec9adfb46f8a221c6d0a6
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606504"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Gyors útmutató: Azure Database for PostgreSQL-kiszolgáló létrehozása a Azure Portal használatával

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre egyetlen Azure Database for PostgreSQL kiszolgálót, és hogyan csatlakozhat hozzá.

## <a name="prerequisites"></a>Előfeltételek
Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
Azure Database for PostgreSQL egykiszolgálós adatbázis létrehozásához nyissa meg a [Azure Portal](https://portal.azure.com/) . Keresse meg és válassza ki *Azure Database for PostgreSQL-kiszolgálókat*.

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="Azure Database for PostgreSQL keresése.":::

1. Válassza a **Hozzáadás** lehetőséget.

2. A Azure Database for PostgreSQL létrehozása lapon válassza az  **egyetlen kiszolgáló** lehetőséget.

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="Egyetlen kiszolgáló kiválasztása":::

3. Most adja meg az **alapok** űrlapot a következő információkkal.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Képernyőkép, amely az alapvető beállítások lapot mutatja egyetlen kiszolgáló létrehozásához.":::

   |Beállítás|Ajánlott érték|Leírás|
   |:---|:---|:---|
   |Előfizetés|az előfizetés neve|Válassza ki a kívánt Azure-előfizetést.|
   |Erőforráscsoport|*myresourcegroup*| Új vagy meglévő erőforráscsoport az előfizetésből.|
   |Kiszolgálónév |*mydemoserver*|Egy egyedi név, amely az Azure Database for PostgreSQL-kiszolgálót azonosítja. A rendszer hozzáfűzi a *postgres.database.Azure.com* tartománynevet a megadott kiszolgálónévhez. A kiszolgáló neve csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Az értéknek 3 – 63 karaktert kell tartalmaznia.|
   |Adatforrás | Nincsenek | Válassza a **Nincs** lehetőséget egy teljesen új kiszolgáló létrehozásához. Csak akkor válassza a **biztonsági mentést** , ha egy meglévő kiszolgáló geo-biztonsági másolatából végez visszaállítást.|
   |Rendszergazdai felhasználónév |*myadmin*| Adja meg a kiszolgáló-rendszergazdai felhasználónevét. Nem kezdődhet **pg_** és ezek az értékek nem engedélyezettek: **azure_superuser**, **azure_pg_admin**, **Admin**, **Administrator**, **root**, **Guest** vagy **Public**.|
   |Jelszó |az ön jelszava| Új jelszó a kiszolgáló-rendszergazda felhasználó számára. 8 – 128 karaktert tartalmazhat a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számok (0 – 9) és nem alfanumerikus karakterek (például!, $, #,%).|
   |Hely|a kívánt hely| Válasszon ki egy helyet a legördülő listából.|
   |Verzió|A legújabb főverzió| A legújabb PostgreSQL-főverzió, hacsak nincsenek más konkrét követelmények.|
   |Számítás + tárolás | *az alapértelmezett beállítások használata*| Az alapértelmezett díjszabási csomag **4 virtuális mag** és **100 GB** tárhellyel **általános célú** . A biztonsági másolatok megőrzésének beállítása **7 nap** , **földrajzilag redundáns** biztonsági mentési lehetőséggel.<br/>Ismerje meg a [díjszabást](https://azure.microsoft.com/pricing/details/postgresql/server/) , és szükség esetén frissítse az alapértelmezett értékeket.|


   > [!NOTE]
   > Érdemes lehet az alapszintű díjszabást használni, ha a számítási feladathoz elegendő a könnyű számítás és az I/O. Vegye figyelembe, hogy az alapszintű díjszabásban létrehozott kiszolgálók később nem méretezhetők át általános célú vagy a memóriára optimalizált értékre.

5. Válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások áttekintéséhez. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A művelet eltarthat néhány percig.
    > [!NOTE]
    > A rendszer létrehoz egy üres adatbázist, a **postgres**. Emellett olyan **azure_maintenance** -adatbázist is talál, amely a felügyelt szolgáltatás folyamatainak elkülönítésére szolgál a felhasználói műveletekben. A **azure_maintenance** adatbázis nem érhető el.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="sikeres üzembe helyezés.":::

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>Tűzfalszabály konfigurálása
Alapértelmezés szerint a létrehozott kiszolgáló nem érhető el nyilvánosan. Engedélyeket kell adnia az IP-címéhez. Nyissa meg a kiszolgálói erőforrást a Azure Portalban, és válassza a **kapcsolatbiztonsági** lehetőséget a kiszolgáló erőforrásának bal oldali menüjében. Ha nem tudja, hogyan találja meg az erőforrását, tekintse [meg az erőforrások megnyitása](../azure-resource-manager/management/manage-resources-portal.md#open-resources)című témakört.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="A kapcsolatbiztonsági szabályokra vonatkozó tűzfalszabályok bemutató képernyőkép.":::

Válassza az **aktuális ügyfél IP-címének hozzáadása** lehetőséget, majd válassza a **Mentés** lehetőséget. További IP-címeket adhat hozzá, vagy megadhat egy IP-címtartományt, amely az IP-címekről a kiszolgálóhoz csatlakozik. További információ: [Tűzfalszabályok a Azure Database for PostgreSQL](./concepts-firewall-rules.md).

> [!NOTE]
> A kapcsolódási problémák elkerüléséhez ellenőrizze, hogy a hálózat engedélyezi-e a kimenő forgalmat az 5432-as porton keresztül. Azure Database for PostgreSQL a portot használja.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>Kapcsolódás a kiszolgálóhoz a psql használatával

Használhatja a [psql](http://postgresguide.com/utilities/psql.html) vagy a [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), amelyek népszerű PostgreSQL-ügyfelek. Ebben a rövid útmutatóban a Azure Portalon belüli [Azure Cloud Shell](../cloud-shell/overview.md) psql használatával fogunk kapcsolatot létesíteni.

1. Jegyezze fel a kiszolgáló nevét, a kiszolgáló-rendszergazdai bejelentkezési nevet, a jelszót és az újonnan létrehozott kiszolgáló előfizetés-AZONOSÍTÓját a kiszolgáló **Áttekintés** szakaszában.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="a kapcsolatfelvételi adatok beolvasása.":::


2. Nyissa meg Azure Cloud Shell a portálon a bal felső sarokban található ikon kiválasztásával.

   > [!NOTE]
   > Ha első alkalommal nyit meg Cloud Shell, megjelenik egy erőforráscsoport és egy Storage-fiók létrehozásának kérése. Ez egy egyszeri lépés, és a rendszer automatikusan csatolja az összes munkamenethez.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Képernyőkép, amely megjeleníti a kiszolgáló adatait és a Azure Cloud Shell megnyitásának ikonját.":::

3. Futtassa a következő parancsot a Azure Cloud Shell terminálon. Cserélje le az értékeket a tényleges kiszolgálónévre és a rendszergazda felhasználói bejelentkezési nevére. A következő formátumban használja az üres adatbázis- **postgres** a rendszergazda felhasználóval: `<admin-username>@<servername>` .

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   A tapasztalatok a Cloud Shell terminálon láthatók:

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. Ugyanebben a Azure Cloud Shell terminálban hozzon létre egy **vendég** nevű adatbázist.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Kapcsolja a kapcsolatokat az újonnan létrehozott **vendég** adatbázishoz.

   ```bash
   \c guest
   ```
6. Írja be `\q` a értéket, majd válassza ki az ENTER billentyűt a psql bezárásához.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Sikeresen létrehozott egy Azure Database for PostgreSQL-kiszolgálót egy erőforráscsoporthoz. Ha nem szeretné, hogy a jövőben is szükség lenne ezekre az erőforrásokra, törölheti őket az erőforráscsoport vagy a PostgreSQL-kiszolgáló törlésével.

Az erőforráscsoport törlése:

1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportok** elemet.
2. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.
3. Az erőforráscsoport **Áttekintés** lapján válassza az **erőforráscsoport törlése** elemet.
4. A megerősítő párbeszédpanelen adja meg az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

A kiszolgáló törléséhez kattintson a **Törlés** gombra a kiszolgáló **Áttekintés** lapján:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="A kiszolgáló törlésére szolgáló gombot megjelenítő képernyőkép.":::

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálás és importálás használatával](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [Adatbázis tervezése](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[Nem találja, amit keres? Tudassa velünk.](https://aka.ms/postgres-doc-feedback)