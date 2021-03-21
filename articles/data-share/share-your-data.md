---
title: 'Oktatóanyag: megosztás a szervezeti környezeten kívül – Azure-adatmegosztás'
description: Oktatóanyag – az Azure-adatmegosztást használó ügyfelekkel és partnerekkel való adatmegosztás
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: 89c2a725b853b5a2a7578dccc1fd503917e12962
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659624"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Oktatóanyag: Adatok megosztása az Azure Data Share-rel  

Ebből az oktatóanyagból megtudhatja, hogyan állíthat be új Azure-adatmegosztást, és hogyan oszthatja meg adatait az Azure-szervezeten kívüli ügyfelekkel és partnerekkel. 

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Hozzon létre egy Data Share-t.
> * Adjon hozzá adathalmazokat a Data Share-hez.
> * Pillanatkép-ütemterv engedélyezése az adatmegosztáshoz. 
> * Adjon hozzá címzetteket a Data Share-hez. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* A címzett Azure bejelentkezési e-mail-címe (az e-mail alias használata nem fog működni).
* Ha az Azure-beli adattár egy másik Azure-előfizetésben található, mint amelyet az adatmegosztási erőforrás létrehozásához fog használni, regisztrálja a [Microsoft. DataShare erőforrás-szolgáltatót](concepts-roles-permissions.md#resource-provider-registration) abban az előfizetésben, amelyben az Azure-adattár található. 

### <a name="share-from-a-storage-account"></a>Megosztás egy Storage-fiókból

* Azure Storage-fiók: Ha még nem rendelkezik ilyennel, létrehozhat egy [Azure Storage-fiókot](../storage/common/storage-account-create.md)
* A Storage-fiókba való írásra vonatkozó engedély, amely megtalálható a *Microsoft. Storage/storageAccounts/Write* szolgáltatásban. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* Jogosultság a szerepkör-hozzárendelés hozzáadásához a Storage-fiókhoz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelésekben/írásban*. Ez az engedély a **Tulajdonos** szerepkör részét képezi. 


### <a name="share-from-a-sql-based-source"></a>Megosztás SQL-alapú forrásból
Az alábbi lista tartalmazza az SQL-forrásokból származó adatok megosztásának előfeltételeit. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>A Azure SQL Database vagy az Azure szinapszis Analytics (korábban Azure SQL DW) megosztásának előfeltételei
Az előfeltételek konfigurálásához kövesse a [lépésenkénti bemutató](https://youtu.be/hIE-TjJD8Dc) lépéseit.

* Egy Azure SQL Database vagy Azure szinapszis Analytics (korábban Azure SQL DW) a megosztani kívánt táblázatokkal és nézetekkel.
* A *Microsoft. SQL/Servers/Databases/Write* adatbázisban található SQL Server-adatbázisba való írásra vonatkozó engedély. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* Engedély az adatmegosztási erőforrás felügyelt identitásához az adatbázis eléréséhez. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon az SQL Serverre, és állítsa be magát a **Azure Active Directory-rendszergazdaként**.
    1. Kapcsolódjon a Azure SQL Database/adattárházhoz a [Lekérdezés-szerkesztő](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) használatával, vagy SQL Server Management Studio Azure Active Directory hitelesítéssel. 
    1. A következő szkript végrehajtásával adja hozzá az adatmegosztási erőforrás felügyelt identitását db_datareaderként. Active Directory használatával kell kapcsolódnia, nem SQL Server a hitelesítéshez. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Vegye figyelembe, hogy a *<share_acc_name>* az adatmegosztási erőforrás neve. Ha még nem hozott létre adatmegosztási erőforrást, később is visszatérhet ehhez az előfeltételhöz.  

* Egy Azure SQL Database **"db_datareader"** hozzáféréssel rendelkező felhasználó navigálhat, és kiválaszthatja a megosztani kívánt táblákat és/vagy nézeteket. 

* SQL Server tűzfal-hozzáférés. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon az SQL Server elemre. A bal oldali navigációs sávon válassza a *tűzfalak és virtuális hálózatok* lehetőséget.
    1. Az **Igen** gombra kattintva *engedélyezheti, hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a kiszolgálóhoz*.
    1. Kattintson az **+ ügyfél IP-** címének hozzáadása elemre. Az ügyfél IP-címének módosítása változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb megosztja az SQL-adatok Azure Portalból való megosztását. Hozzáadhat IP-címtartományt is.
    1. Kattintson a **Mentés** gombra. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Az Azure szinapszis Analytics (munkaterület) SQL-készlet megosztásának előfeltételei

* * Egy Azure szinapszis Analytics (munkaterület) dedikált SQL-készlet a megosztani kívánt táblázatokkal. A nézet megosztása jelenleg nem támogatott. A kiszolgáló nélküli SQL-készletből való megosztás jelenleg nem támogatott.
* Engedély a szinapszis munkaterületen található SQL-készletbe való írásra, amely megtalálható a *Microsoft. szinapszis/munkaterület/sqlPools/Write* fájlokban. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* Engedély az adatmegosztási erőforrás felügyelt identitásához a szinapszis-munkaterület SQL-készletének eléréséhez. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon a szinapszis munkaterületre. Válassza az SQL Active Directory-rendszergazda lehetőséget a bal oldali navigációs sávon, és állítsa be magát a **Azure Active Directory-rendszergazdaként**.
    1. Nyissa meg a szinapszis Studio alkalmazást, és válassza a *kezelés* lehetőséget a bal oldali navigációs sávon. Válassza a *hozzáférés-vezérlés* lehetőséget a biztonság területen. Rendeljen hozzá saját **SQL-rendszergazdai** vagy **munkaterület-rendszergazdai** szerepkört.
    1. A szinapszis Studióban válassza a *fejlesztés* lehetőséget a bal oldali navigációs sávon. Hajtsa végre a következő parancsfájlt az SQL-készletben az adatmegosztási erőforrás felügyelt identitásának db_datareader való hozzáadásához. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Vegye figyelembe, hogy a *<share_acc_name>* az adatmegosztási erőforrás neve. Ha még nem hozott létre adatmegosztási erőforrást, később is visszatérhet ehhez az előfeltételhöz.  

* A szinapszis munkaterület tűzfal-hozzáférése. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon a szinapszis munkaterületre. A bal oldali navigációs sávon válassza a *tűzfalak* lehetőséget.
    1. Ide **kattintva** *engedélyezheti, hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a munkaterülethez*.
    1. Kattintson az **+ ügyfél IP-** címének hozzáadása elemre. Az ügyfél IP-címének módosítása változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb megosztja az SQL-adatok Azure Portalból való megosztását. Hozzáadhat IP-címtartományt is.
    1. Kattintson a **Mentés** gombra. 


### <a name="share-from-azure-data-explorer"></a>Megosztás az Azure Data Explorerből
* Egy Azure Adatkezelő-fürt, amelynek adatbázisait meg szeretné osztani.
* Engedély az Azure Adatkezelő-fürtbe való írásra, amely megtalálható a *Microsoft. Kusto/fürtök/írás* szolgáltatásban. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* Jogosultság a szerepkör-hozzárendelés hozzáadásához az Azure Adatkezelő-fürthöz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelésekben/írásban*. Ez az engedély a **Tulajdonos** szerepkör részét képezi.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Adatmegosztási fiók létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Azure-beli adatmegosztási erőforrás létrehozása Azure-erőforráscsoporthoz.

1. Kattintson a portál bal felső sarkában található menü gombra, majd válassza az **erőforrás létrehozása** (+) lehetőséget.

1. Keresse meg az *adatmegosztást*.

1. Válassza az adatmegosztás lehetőséget, majd válassza a **Létrehozás** lehetőséget.

1. Töltse ki az Azure-beli adatmegosztási erőforrás alapvető adatait az alábbi információkkal. 

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válassza ki az adatmegosztási fiókhoz használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | *testresourcegroup* | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Hely | *USA 2. keleti régiója* | Válassza ki az adatmegosztási fiókhoz tartozó régiót.
    | Name | *datashareaccount* | Adja meg az adatmegosztási fiók nevét. |
    | | |

1. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget az adatmegosztási fiók kiépítéséhez. Az új adatmegosztási fiók üzembe helyezése általában körülbelül 2 percet vesz igénybe. 

1. Ha a telepítés befejeződött, válassza az **Ugrás erőforráshoz** lehetőséget.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure-beli adatmegosztási erőforrás létrehozása Azure-erőforráscsoporthoz.

Először készítse elő a környezetet az Azure CLI-hez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Az erőforrás létrehozásához használja az alábbi parancsokat:

1. Az az [Account set](/cli/azure/account#az_account_set) paranccsal állíthatja be az előfizetését az aktuális alapértelmezett előfizetésre:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Futtassa az az [Provider Register](/cli/azure/provider#az_provider_register) parancsot az erőforrás-szolgáltató regisztrálásához:

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. Futtassa az [az group create](/cli/azure/group#az_group_create) parancsot egy erőforráscsoport létrehozásához, vagy használjon egy meglévő erőforráscsoportot:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Futtassa az az [DataShare Account Create](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_create) parancsot egy adatmegosztási fiók létrehozásához:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Az adatmegosztási fiókok megtekintéséhez futtassa az az [DataShare Account List](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_list) parancsot:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Megosztás létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Navigáljon az adatmegosztás áttekintő oldalára.

    ![Az adatok megosztása](./media/share-receive-data.png "Az adatok megosztása") 

1. Válassza **az adatmegosztás megkezdése** lehetőséget.

1. Válassza a **Létrehozás** lehetőséget.   

1. Adja meg a megosztás részleteit. Adja meg a nevet, a megosztás típusát, a megosztási tartalmak leírását és a használati feltételeket (opcionális). 

    ![EnterShareDetails](./media/enter-share-details.png "Adja meg a megosztás részleteit") 

1. Válassza a **Folytatás** lehetőséget.

1. Az adatkészletek megosztáshoz való hozzáadásához válassza az **adatkészletek hozzáadása** elemet. 

    ![Adatkészletek hozzáadása a megosztáshoz](./media/datasets.png "Adathalmazok")

1. Válassza ki a hozzáadni kívánt adatkészlet típusát. Az adathalmazok eltérő listáját fogja látni az előző lépésben kiválasztott megosztási típustól (pillanatkép vagy helyben) függően. Ha Azure SQL Database vagy Azure szinapszis Analytics (korábban Azure SQL DW) megosztását kéri, a rendszer az SQL hitelesítő adatokat fogja kérni a táblák listázásához.

    ![AddDatasets](./media/add-datasets.png "Adatkészletek hozzáadása")    

1. Navigáljon a megosztani kívánt objektumhoz, és válassza az "adatkészletek hozzáadása" lehetőséget. 

    ![SelectDatasets](./media/select-datasets.png "Adatkészletek kiválasztása")    

1. A címzettek lapon adja meg az adatfogyasztó e-mail-címeit a "+ Címzett hozzáadása" lehetőség kiválasztásával. 

    ![AddRecipients](./media/add-recipient.png "Címzettek hozzáadása") 

1. Válassza a **Folytatás** lehetőséget.

1. Ha kiválasztotta a pillanatkép-megosztás típusát, beállíthatja a pillanatkép-ütemtervet, hogy az adatokra vonatkozó frissítéseket biztosítson az adatfogyasztónak. 

    ![EnableSnapshots](./media/enable-snapshots.png "Pillanatképek engedélyezése") 

1. Válassza ki a kezdési időt és az ismétlődési időközt. 

1. Válassza a **Folytatás** lehetőséget.

1. A felülvizsgálat + létrehozás lapon tekintse át a csomag tartalmát, a beállításokat, a címzetteket és a szinkronizálási beállításokat. Válassza a **Létrehozás** lehetőséget.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Az az [Storage Account Create](/cli/azure/storage/account#az_storage_account_create) parancs futtatásával hozzon létre egy adatmegosztást:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Az az [Storage Container Create](/cli/azure/storage/container#az_storage_container_create) paranccsal hozzon létre egy tárolót a megosztáshoz az előző parancsban:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Az adatmegosztás létrehozásához futtassa az az [DataShare Create](/cli/azure/ext/datashare/datashare#ext_datashare_az_datashare_create) parancsot:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Az az [DataShare meghívás Create](/cli/azure/ext/datashare/datashare/invitation#ext_datashare_az_datashare_invitation_create) paranccsal hozza létre a meghívót a megadott címen:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

Az Azure-beli adatmegosztás már létrejött, és az adatmegosztás címzettje most már készen áll a meghívás elfogadására.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforrásra már nincs szükség, lépjen az **adatmegosztás áttekintése** lapra, és válassza a **Törlés** lehetőséget az eltávolításához.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre Azure-beli adatmegosztást, és hogyan hívhat meg címzetteket. Ha szeretne többet megtudni arról, hogy az adatfogyasztó hogyan fogadhat és fogadhat adatmegosztást, folytassa az elfogadás és fogadás adatgyűjtéssel foglalkozó oktatóanyagot.

> [!div class="nextstepaction"]
> [Oktatóanyag: Adatok elfogadása és fogadása az Azure Data Share használatával](subscribe-to-data-share.md)
