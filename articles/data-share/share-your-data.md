---
title: 'Oktatóanyag: Szervezeten kívüli megosztás – Azure Data Share'
description: Oktatóanyag – Adatok megosztása ügyfelekkel és partnerekkel a Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: b8d49e3b3c6f6407fe241e00ada5039bd94fd706
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870876"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Oktatóanyag: Adatok megosztása az Azure Data Share-rel  

Ez az oktatóanyag bemutatja, hogyan állíthat be új Azure Data Share és hogyan kezdheti meg az adatok megosztását az Azure-szervezeten kívüli ügyfelekkel és partnerekkel. 

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Hozzon létre egy Data Share-t.
> * Adjon hozzá adathalmazokat a Data Share-hez.
> * Pillanatkép-ütemezés engedélyezése a Data Share. 
> * Adjon hozzá címzetteket a Data Share-hez. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, [kezdés](https://azure.microsoft.com/free/) előtt hozzon létre egy ingyenes fiókot.
* A címzett Azure-beli bejelentkezési e-mail-címe (az e-mail-aliasuk használata nem fog működni).
* Ha Data Share forrás Azure-adattár egy másik Azure-előfizetésben található, mint az erőforrás létrehozásához használt, regisztrálja Data Share [Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) erőforrás-szolgáltatót abban az előfizetésben, amelyben az Azure-adattár található. 

### <a name="share-from-a-storage-account"></a>Megosztás tárfiókból

* Azure Storage-fiók: Ha még nem rendelkezik fiókkal, létrehozhat egy [Azure Storage-fiókot](../storage/common/storage-account-create.md)
* Engedély a Tárfiókba való íráshoz, amely a *Microsoft.Storage/storageAccounts/write* fájlban van. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* Engedély szerepkör-hozzárendelés hozzáadására a tárfiókhoz, amely a *Microsoft.Authorization/role assignments/write* fájlban van jelen. Ez az engedély a **Tulajdonos** szerepkör részét képezi. 


### <a name="share-from-a-sql-based-source"></a>Megosztás SQL-alapú forrásból
Az alábbi lista az SQL-forrásból származó adatok megosztásának előfeltételeit sorolja fel. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>A Azure SQL Database vagy Azure Synapse Analytics (korábban DW) Azure SQL előfeltételei

* Egy Azure SQL Database vagy Azure Synapse Analytics (Azure SQL DW) a megosztani kívánt táblákkal és nézetekkel.
* Engedély a *Microsoft.Sql/servers/databases/write* fájlban található SQL Server-adatbázisokba való íráshoz. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* **Azure Active Directory SQL Server** rendszergazdája
* SQL Server tűzfal-hozzáférés. Ezt a következő lépésekkel lehet tenni: 
    1. A Azure Portal nyissa meg az SQL Servert. Válassza *a Tűzfalak és virtuális hálózatok lehetőséget* a bal oldali navigációs sávon.
    1. Kattintson **az Igen gombra,** ha engedélyeznie kell az Azure-szolgáltatások és *-erőforrások számára a kiszolgálóhoz való hozzáférést.*
    1. Kattintson **a +Ügyfél IP-címének hozzáadása elemre.** Az ügyfél IP-címe változhat. Előfordulhat, hogy a következő alkalommal meg kell ismételni ezt a folyamatot, amikor sql-adatokat oszt meg Azure Portal. IP-címtartományt is hozzáadhat.
    1. Kattintson a **Mentés** gombra. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Előfeltételek a Azure Synapse Analytics (munkaterület) SQL-készletből való megosztáshoz

* * Egy Azure Synapse Analytics (munkaterület) dedikált SQL-készlet a megosztani kívánt táblákkal. A nézetmegosztás jelenleg nem támogatott. A kiszolgáló nélküli SQL-készletből való megosztás jelenleg nem támogatott.
* Engedély az SQL-készletbe való íráshoz a *Microsoft.Synapse/workspaces/sqlPools/write fájlban található Synapse-munkaterületen.* Ez az engedély a **Közreműködő** szerepkör részét képezi.
* Engedély a Data Share erőforrás felügyelt identitásához a Synapse-munkaterület SQL-készletének eléréséhez. Ezt a következő lépésekkel lehet tenni: 
    1. A Azure Portal nyissa meg a Synapse-munkaterületet. Válassza ki az SQL Active Directory rendszergazdai szerepkört a bal oldali navigációs sávon, és állítsa be magát Azure Active Directory **rendszergazdának.**
    1. Nyissa Synapse Studio bal *oldali navigációs sávon a Kezelés* lehetőséget. A Biztonság alatt válassza a *Hozzáférés-vezérlés* lehetőséget. Rendelje hozzá **saját SQL-rendszergazdai** vagy **munkaterület-rendszergazdai szerepkörét.**
    1. A Synapse Studio bal *oldali navigációs sávon válassza* a Fejlesztés lehetőséget. Hajtsa végre a következő szkriptet az SQL-készletben a felügyelt Data Share erőforrás db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Vegye *figyelembe<share_acc_name>* hogy a Data Share neve. Ha még nem hozott létre Data Share erőforrást, később is visszatérhet erre az előfeltételre.  

* Synapse-munkaterület tűzfal-hozzáférése. Ezt a következő lépésekkel lehet tenni: 
    1. A Azure Portal nyissa meg a Synapse-munkaterületet. A *bal oldali navigációs panelen* válassza a Tűzfalak lehetőséget.
    1. Kattintson **a BE** gombra a Munkaterület elérésének engedélyezése Az Azure-szolgáltatások és *-erőforrások számára lehetőséget.*
    1. Kattintson **a +Ügyfél IP-címének hozzáadása elemre.** Az ügyfél IP-címe változhat. Előfordulhat, hogy a következő alkalommal meg kell ismételni ezt a folyamatot, amikor sql-adatokat oszt meg Azure Portal. IP-címtartományt is hozzáadhat.
    1. Kattintson a **Mentés** gombra. 


### <a name="share-from-azure-data-explorer"></a>Megosztás az Azure Data Explorerből
* Egy Azure Data Explorer a megosztani kívánt adatbázisokkal.
* Engedély egy Azure Data Explorer, amely a *Microsoft.Kusto/clusters/write* fájlban található. Ez az engedély a **Közreműködő** szerepkör részét képezi.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Új Data Share létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Hozzon létre Azure Data Share erőforrást egy Azure-erőforráscsoportban.

1. Válassza a portál bal felső sarkában található menügombot, majd az **Erőforrás létrehozása** (+) lehetőséget.

1. Keressen rá a *Data Share.*

1. Válassza a Data Share, majd a **Létrehozás lehetőséget.**

1. Adja meg a Azure Data Share alapvető adatait. 

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válassza ki az adat share-fiókjához használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | *testresourcegroup* | Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. |
    | Hely | *USA 2. keleti régiója* | Válasszon ki egy régiót az adat share-fiókjához.
    | Name | *datashareaccount* | Adja meg az adat-megosztási fiók nevét. |
    | | |

1. Válassza **az Áttekintés + létrehozás,** majd a **Létrehozás** lehetőséget az adat share-fiók létrehozásához. Egy új adat share-fiók kiépítése általában körülbelül 2 percet vagy kevesebb időt vesz igénybe. 

1. Az üzembe helyezés befejezése után válassza az **Ugrás az erőforráshoz lehetőséget.**

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Hozzon létre Azure Data Share erőforrást egy Azure-erőforráscsoportban.

Először készítse elő a környezetet az Azure CLI-hez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Használja az alábbi parancsokat az erőforrás létrehozásához:

1. Az [az account set paranccsal](/cli/azure/account#az_account_set) állítsa be az előfizetését az aktuális alapértelmezett előfizetésre:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Futtassa [az az provider register](/cli/azure/provider#az_provider_register) parancsot az erőforrás-szolgáltató regisztráláshoz:

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. Futtassa az [az group create](/cli/azure/group#az_group_create) parancsot egy erőforráscsoport létrehozásához, vagy használjon egy meglévő erőforráscsoportot:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Futtassa [az az datashare account create parancsot](/cli/azure/datashare/account#az_datashare_account_create) egy új Data Share létrehozásához:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Futtassa [az az datashare account list parancsot](/cli/azure/datashare/account#az_datashare_account_list) a Data Share a fiókokat:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Megosztás létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Lépjen a Data Share Áttekintés lapjára.

    ![Az adatok megosztása](./media/share-receive-data.png "Az adatok megosztása") 

1. Válassza **a Start sharing your data (Az adatok megosztásának elkezdődjön) lehetőséget.**

1. Válassza a **Létrehozás** lehetőséget.   

1. Adja meg a megosztás adatait. Adja meg a nevet, a megosztás típusát, a megosztás tartalmának leírását és a használati feltételeket (nem kötelező). 

    ![EnterShareDetails](./media/enter-share-details.png "Adja meg a Megosztás részletei adatokat") 

1. Válassza a **Folytatás** lehetőséget.

1. Ha adatkészleteket szeretne hozzáadni a megosztáshoz, válassza az **Adatkészletek hozzáadása lehetőséget.** 

    ![Adatkészletek hozzáadása a megosztáshoz](./media/datasets.png "Adathalmazok")

1. Válassza ki a hozzáadni kívánt adatkészlettípust. Az adatkészlettípusok különböző listája fog megjelenik az előző lépésben kiválasztott megosztási típustól (pillanatkép vagy helyi). Ha a megosztás egy Azure SQL Database vagy Azure Synapse Analytics (korábbi nevén DW Azure SQL), a rendszer kérni fogja a hitelesítési módszert a táblák listához. Válassza az AAD-hitelesítés lehetőséget, és jelölje be az Allow Data Share to run the above **'create user' script on myhalf**. 

    ![AddDatasets (Adatkészletek hozzáadása)](./media/add-datasets.png "Adatkészletek hozzáadása")    

1. Keresse meg a megosztani kívánt objektumot, és válassza az "Adatkészletek hozzáadása" lehetőséget. 

    ![SelectDatasets (Adatkészletek kiválasztása)](./media/select-datasets.png "Adatkészletek kiválasztása")    

1. A Címzettek lapon a "+ Címzett hozzáadása" lehetőség kiválasztásával adja meg az adat fogyasztója e-mail-címét. 

    ![AddRecipients (Átmenetiek hozzáadása)](./media/add-recipient.png "Címzettek hozzáadása") 

1. Válassza a **Folytatás** lehetőséget.

1. Ha a pillanatkép-megosztás típusát választotta, konfigurálhatja úgy a pillanatkép-ütemezést, hogy az adatok frissítéseit az adat fogyasztója számára adja meg. 

    ![EnableSnapshots (Napshots engedélyezése)](./media/enable-snapshots.png "Pillanatképek engedélyezése") 

1. Válassza ki a kezdési időt és az ismétlődési időközt. 

1. Válassza a **Folytatás** lehetőséget.

1. Az Áttekintés + létrehozás lapon tekintse át a csomag tartalmát, a beállításokat, a címzetteket és a szinkronizálási beállításokat. Válassza a **Létrehozás** lehetőséget.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Futtassa [az az storage account create parancsot](/cli/azure/storage/account#az_storage_account_create) egy új Data Share:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Az [az storage container create paranccsal](/cli/azure/storage/container#az_storage_container_create) hozzon létre egy tárolót az előző parancsban található megosztáshoz:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Futtassa [az az datashare create](/cli/azure/datashare#az_datashare_create) parancsot a Data Share:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Az [az datashare invitation create paranccsal](/cli/azure/datashare/invitation#az_datashare_invitation_create) hozza létre a meghívást a megadott címhez:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

A Azure Data Share létrejött, és a címzettje Data Share készen áll a meghívás elfogadására.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforrásra már nincs szükség, az Áttekintés Data Share  **válassza** a Törlés lehetőséget az eltávolításához.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatja, hogyan hozhat létre Azure Data Share és hívhat meg címzetteket. Ha meg szeretne ismerkedni az adat fogyasztója által az adat megosztásának elfogadásával és fogadásának mikéntjére vonatkozó információkkal, folytassa az adatok fogadását és fogadását ismertető oktatóanyagot.

> [!div class="nextstepaction"]
> [Oktatóanyag: Adatok elfogadása és fogadása az Azure Data Share használatával](subscribe-to-data-share.md)
