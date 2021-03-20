---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure Portal – Azure Database for MySQL'
description: Ez a cikk lépésről lépésre bemutatja, hogyan hozhat létre egy minta Azure-adatbázist MySQL-kiszolgálóhoz az Azure Portal használatával öt perc alatt.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: b8f3a8e06bb27051417205c4dc3141948960bb00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98567794"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Gyors útmutató: Azure Database for MySQL-kiszolgáló létrehozása a Azure Portal használatával

A Azure Database for MySQL felügyelt szolgáltatás, amellyel a felhőben futtathatja, kezelheti és méretezheti a magasan elérhető MySQL-adatbázisokat. Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Database for MySQL egyetlen kiszolgálót a Azure Portal használatával. Azt is bemutatja, hogyan csatlakozhat a kiszolgálóhoz.

## <a name="prerequisites"></a>Előfeltételek
Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Azure Database for MySQL önálló kiszolgáló létrehozása
1. Nyissa meg a [Azure Portal](https://portal.azure.com/) egy MySQL-kiszolgáló adatbázisának létrehozásához. **Azure Database for MySQL** keresése és kiválasztása:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Azure Database for MySQL keresése":::

1. Válassza a **Hozzáadás** lehetőséget.

2. A **Azure Database for MySQL központi telepítési lehetőség kiválasztása** lapon válassza az  **egyetlen kiszolgáló** lehetőséget:
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Képernyőkép, amely az egyetlen kiszolgáló lehetőséget jeleníti meg.":::

3. Adja meg az új egyetlen kiszolgáló alapszintű beállításait:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="A MySQL-kiszolgáló létrehozása lapot megjelenítő képernyőkép.":::

   **Beállítás** | **Ajánlott érték** | **Leírás**
   ---|---|---
   Előfizetés | Az Ön előfizetése | Válassza ki a kívánt Azure-előfizetést.
   Erőforráscsoport | **myresourcegroup** | Adjon meg egy új erőforráscsoportot vagy egy meglévőt az előfizetésből.
   Kiszolgálónév | **mydemoserver** | Adjon meg egy egyedi nevet. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Az értéknek 3 – 63 karaktert kell tartalmaznia.
   Adatforrás |**Nincs** | Válassza a **Nincs** lehetőséget egy teljesen új kiszolgáló létrehozásához. Csak akkor válassza a **biztonsági mentést** , ha egy meglévő kiszolgáló geo-biztonsági másolatából végzi a visszaállítást.
   Hely |A kívánt hely | Válasszon ki egy helyet a listából.
   Verzió | A legújabb főverzió| Használja a legújabb főverziót. Tekintse meg [az összes támogatott verziót](concepts-supported-versions.md).
   Számítás + tárolás | Az alapértelmezett beállítások használata| Az alapértelmezett díjszabási csomag **4 virtuális mag** és **100 GB** tárhellyel **általános célú** . A biztonsági másolatok megőrzésének beállítása **7 nap**, a **földrajzilag redundáns** biztonsági mentési lehetőséggel.<br/>Tekintse át a [díjszabási](https://azure.microsoft.com/pricing/details/mysql/) oldalt, és ha szükséges, frissítse az alapértelmezett értékeket.
   Rendszergazdai felhasználónév | **mydemoadmin** | Adja meg a kiszolgáló rendszergazdájának felhasználónevét. A rendszergazdai felhasználónévhez nem használhatja **azure_superuser**, **Admin**, **Administrator**, **root**, **Guest** vagy **Public** .
   Jelszó | Jelszó | Új jelszó a kiszolgáló-rendszergazda felhasználó számára. A jelszónak 8 – 128 karakter hosszúnak kell lennie, és tartalmaznia kell kis-és nagybetűket, számokat és nem alfanumerikus karaktereket (!, $, #,% stb.).
  

   > [!NOTE]
   > Érdemes lehet az alapszintű díjszabást használni, ha a számítási feladathoz elegendő a könnyű számítás és az I/O. Vegye figyelembe, hogy az alapszintű díjszabásban létrehozott kiszolgálók később nem méretezhetők át általános célú vagy a memóriára optimalizált értékre.

4. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kiszolgáló kiépítéséhez.

5. Várjon, amíg befejeződik a portál oldalának megjelenítése a **központi telepítés befejezéséhez**. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy az újonnan létrehozott kiszolgáló oldalra lépjen:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="A telepítés befejezését bemutató képernyőkép.":::

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Alapértelmezés szerint az új kiszolgáló tűzfallal védett. A kapcsolódáshoz meg kell adnia a hozzáférést az IP-címhez a következő lépések végrehajtásával:

1. A kiszolgáló erőforrásának bal oldali ablaktábláján lépjen a **kapcsolatbiztonsági** elemre. Ha nem tudja, hogyan keresheti meg az erőforrást, olvassa el az [erőforrás megnyitását](../azure-resource-manager/management/manage-resources-portal.md#open-resources)ismertető témakört.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="A kapcsolatbiztonsági > tűzfalszabályok oldalát bemutató képernyőkép.":::

2. Válassza az **aktuális ügyfél IP-címének hozzáadása** lehetőséget, majd válassza a **Mentés** lehetőséget.

   > [!NOTE]
   > A kapcsolódási problémák elkerüléséhez ellenőrizze, hogy a hálózat engedélyezi-e a kimenő forgalmat az 3306-as porton keresztül, amelyet a Azure Database for MySQL használ. 

További IP-címeket adhat hozzá, vagy megadhat egy IP-címtartományt, amely az adott IP-címekről csatlakozik a kiszolgálóhoz. További információ: a [Tűzfalszabályok kezelése egy Azure Database for MySQL kiszolgálón](./concepts-firewall-rules.md).


[Problémák léptek fel? Tudassa velünk](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Kapcsolódás a kiszolgálóhoz a mysql.exe használatával
A helyi környezetből [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) vagy [MySQL Workbench](./connect-workbench.md) használatával kapcsolódhat a kiszolgálóhoz. Ebben a rövid útmutatóban mysql.exet fogunk használni a [Azure Cloud Shell](../cloud-shell/overview.md) a kiszolgálóhoz való kapcsolódáshoz.


1. Nyissa meg Azure Cloud Shell a portálon az eszköztár első gombjának kiválasztásával, ahogy az alábbi képernyőképen is látható. Jegyezze fel a kiszolgáló nevét, a kiszolgáló rendszergazdájának nevét és az új kiszolgáló előfizetését az **Áttekintés** szakaszban, ahogy az a képernyőképen is látható.

    > [!NOTE]
    > Ha első alkalommal nyit meg Cloud Shell, a rendszer felszólítja az erőforráscsoport és a Storage-fiók létrehozására. Ez egy egyszeri lépés. A rendszer automatikusan csatolja az összes munkamenethez.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="A Azure Portal Cloud Shellét bemutató képernyőkép.":::
2. Futtassa a következő parancsot a Azure Cloud Shell terminálon. Cserélje le az itt látható értékeket a tényleges kiszolgálónévre és a rendszergazda felhasználónevére. Azure Database for MySQL esetében `@\<servername>` az itt látható módon hozzá kell adnia a rendszergazda felhasználónevet: 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      A Cloud Shell-terminálon a következőképpen néz ki:

      ```
      Requesting a Cloud Shell.Succeeded.
      Connecting terminal...

      Welcome to Azure Cloud Shell

      Type "az" to use Azure CLI
      Type "help" to learn about Cloud Shell

      user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
      Enter password:
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 64796
      Server version: 5.6.42.0 Source distribution

      Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
      Oracle is a registered trademark of Oracle Corporation and/or its
      affiliates. Other names may be trademarks of their respective
      owners.

      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
      mysql>
      ```
3. Ugyanebben a Azure Cloud Shell terminálban hozzon létre egy nevű adatbázist `guest` :
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Váltson az `guest` adatbázisra:
      ```
      mysql> USE guest;
      Database changed
      ```
5. Adja meg a értéket `quit` , majd válassza az **ENTER** billentyűt a MySQL kilépéséhez.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ezzel létrehozott egy Azure Database for MySQL-kiszolgálót egy erőforráscsoporthoz.  Ha nem szeretné, hogy a jövőben is szükség lenne ezekre az erőforrásokra, törölheti őket az erőforráscsoport törlésével, vagy egyszerűen törölheti a MySQL-kiszolgálót is. Az erőforráscsoport törléséhez hajtsa végre a következő lépéseket:
1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportok** elemet.
2. Az erőforráscsoportok listájában válassza ki az erőforráscsoport nevét.
3. Az erőforráscsoport **Áttekintés** lapján válassza az **erőforráscsoport törlése** lehetőséget.
4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

A kiszolgáló törléséhez válassza a **Törlés** lehetőséget a kiszolgáló **Áttekintés** lapján, ahogy az itt látható:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="A kiszolgáló áttekintés lapján található Törlés gombot megjelenítő képernyőkép.":::

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
>[PHP-alkalmazás létrehozása Windowson MySQL-sel](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[PHP-alkalmazás létrehozása Linuxon a MySQL-vel](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[Nem találja, amit keres? Tudassa velünk.](https://aka.ms/mysql-doc-feedback)
