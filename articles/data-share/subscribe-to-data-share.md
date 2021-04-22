---
title: 'Oktatóanyag: Az & fogadásának elfogadása – Azure Data Share'
description: Oktatóanyag – Adatok elfogadása és fogadása Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: d063a0870616b5b977df18c56d9d66515b03d0a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870858"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Oktatóanyag: Adatok elfogadása és fogadása az Azure Data Share használatával  

Ez az oktatóanyag bemutatja, hogyan fogadhat el adatmegoszlott meghívót a Azure Data Share. Megtudhatja, hogyan fogadhatja a megosztott adatokat, és hogyan engedélyezheti a rendszeres frissítési időközt, hogy mindig a legfrissebb pillanatképet kapja az adatokról, amelyeken az adatok meg vannak osztva. 

> [!div class="checklist"]
> * Meghívás elfogadása Azure Data Share
> * Új Azure Data Share létrehozása
> * Az adatok célhelyének megadása
> * Előfizetés létrehozása az adat-megosztáshoz ütemezett frissítéshez

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elfogadná az adatmegoszlott meghívót, ki kell osztania néhány Azure-erőforrást, amelyek alább vannak felsorolva. 

Az adatmegoszlott meghívó elfogadása előtt győződjön meg arról, hogy minden előfeltétel teljesítve van. 

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, [kezdés](https://azure.microsoft.com/free/) előtt hozzon létre egy ingyenes fiókot.
* A Data Share meghívása: A Microsoft Azure meghívása a "Azure Data Share meghívása **<yourdataprovider@domain.com>** innen: ".
* Regisztrálja a [Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) erőforrás-szolgáltatót abban az Azure-előfizetésben, ahol létre fog hozni egy Data Share-erőforrást és azt az Azure-előfizetést, ahol a cél Azure-adattárai találhatók.

### <a name="receive-data-into-a-storage-account"></a>Adatok fogadása tárfiókba

* Azure Storage-fiók: Ha még nem rendelkezik ilyen fiókkal, létrehozhat egy [Azure Storage-fiókot.](../storage/common/storage-account-create.md) 
* Engedély a Tárfiókba való íráshoz, amely a *Microsoft.Storage/storageAccounts/write* fájlban van. Ez az engedély a Közreműködő szerepkör részét képezi. 
* Engedély szerepkör-hozzárendelés hozzáadására a tárfiókhoz, amely a *Microsoft.Authorization/role assignments/write* fájlban van jelen. Ez az engedély a Tulajdonos szerepkör részét képezi.  

### <a name="receive-data-into-a-sql-based-target"></a>Adatok fogadása SQL-alapú célként
Ha úgy dönt, hogy adatokat fogad a Azure SQL Database, Azure Synapse Analytics alábbi lista tartalmazza az előfeltételeket. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Előfeltételek az adatok Azure SQL Database vagy Azure Synapse Analytics (korábban DW) Azure SQL fogadására

* Egy Azure SQL Database vagy Azure Synapse Analytics (korábban Azure SQL DW).
* Engedély az SQL-kiszolgálón található adatbázisokba való íráshoz, amely a *Microsoft.Sql/servers/databases/write* fájlban található. Ez az engedély a **Közreműködő** szerepkör részét képezi. 
* **Azure Active Directory SQL Server** rendszergazdája
* SQL Server tűzfal-hozzáférés. Ezt a következő lépésekkel lehet tenni: 
    1. Az SQL Server Azure Portal lépjen a *Tűzfalak és virtuális hálózatok lapra*
    1. Kattintson **az Igen gombra,** ha engedélyeznie kell az Azure-szolgáltatások és *-erőforrások számára a kiszolgálóhoz való hozzáférést.*
    1. Kattintson **a +Ügyfél IP-címének hozzáadása elemre.** Az ügyfél IP-címe változhat. Előfordulhat, hogy a következő alkalommal meg kell ismételni ezt a folyamatot, amikor sql-adatokat oszt meg Azure Portal. IP-címtartományt is hozzáadhat.
    1. Kattintson a **Mentés** gombra. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Az adatoknak a munkaterületi (Azure Synapse Analytics) SQL-készletbe való fogadásának előfeltételei

* Egy Azure Synapse Analytics (munkaterület) dedikált SQL-készlet. Az adatok kiszolgáló nélküli SQL-készletbe való fogadása jelenleg nem támogatott.
* Engedély az SQL-készletbe való íráshoz a *Microsoft.Synapse/workspaces/sqlPools/write fájlban található Synapse-munkaterületen.* Ez az engedély a **Közreműködő** szerepkör részét képezi.
* Engedély a Data Share erőforrás felügyelt identitásához a Synapse-munkaterület SQL-készletének eléréséhez. Ezt a következő lépésekkel lehet tenni: 
    1. A Azure Portal nyissa meg a Synapse-munkaterületet. Válassza ki az SQL Active Directory rendszergazdai szerepkört a bal oldali navigációs sávon, és állítsa be magát Azure Active Directory **rendszergazdának.**
    1. Nyissa Synapse Studio bal *oldali navigációs sávon a Kezelés* lehetőséget. A Biztonság alatt válassza a *Hozzáférés-vezérlés* lehetőséget. Rendelje hozzá saját **SQL-rendszergazdai** vagy **munkaterület-rendszergazdai szerepkörét.**
    1. A Synapse Studio bal *oldali navigációs sávon válassza* a Fejlesztés lehetőséget. Hajtsa végre a következő szkriptet az SQL-készletben az Data Share-erőforrás felügyelt identitásának "db_datareader, db_datawriter, db_ddladmin" db_ddladmin. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Vegye *figyelembe<share_acc_name>* hogy a Data Share neve. Ha még nem hozott létre Data Share erőforrást, később is visszatérhet erre az előfeltételre.  

* Synapse-munkaterület tűzfal-hozzáférése. Ezt a következő lépésekkel lehet tenni: 
    1. A Azure Portal nyissa meg a Synapse-munkaterületet. A *bal oldali navigációs panelen* válassza a Tűzfalak lehetőséget.
    1. Kattintson **a BE** gombra a Munkaterület elérésének engedélyezése Az Azure-szolgáltatások és *-erőforrások számára lehetőséget.*
    1. Kattintson **a +Ügyfél IP-címének hozzáadása elemre.** Az ügyfél IP-címe változhat. Előfordulhat, hogy a következő alkalommal meg kell ismételni ezt a folyamatot, amikor sql-adatokat oszt meg Azure Portal. Ip-címtartományt is hozzáadhat.
    1. Kattintson a **Mentés** gombra. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Adatok fogadása egy Azure Data Explorer fürtbe: 

* Egy Azure Data Explorer fürt ugyanabban az Azure-adatközpontban, mint az adatszolgáltató Adatkezelő-fürtjéhez: Ha még nem rendelkezik fürtöt, létrehozhat egy Azure Data Explorer [fürtöt.](/azure/data-explorer/create-cluster-database-portal) Ha nem ismeri az adatszolgáltató fürtjéhez az Azure-adatközpontot, a folyamat későbbi részen létrehozhatja a fürtöt.
* Engedély a Azure Data Explorer, amely a *Microsoft.Kusto/clusters/write* fájlban található. Ez az engedély a Közreműködő szerepkör részét képezi. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="open-invitation"></a>Meghívó megnyitása

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A meghívót e-mailből vagy közvetlenül a Azure Portal. 

   A meghívó e-mailből való megnyitásához jelölje be a bejövő üzenetben az adatszolgáltatótól kapott meghívót. A meghívás egy Microsoft Azure, a **Azure Data Share címe. <yourdataprovider@domain.com>** Kattintson a **Meghívás megtekintése elemre** a meghívás azure-beli megtekintéséhez. 

   Ha a meghívást Azure Portal megnyitni, keressen rá az **Data Share-meghívók kifejezésre** a Azure Portal. Ez a művelet a meghívók listájára Data Share viszi.

   ![Meghívók listája](./media/invitations.png "Meghívók listája") 

1. Válassza ki a megtekinteni kívánt megosztást. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Készítse elő az Azure CLI-környezetet, majd tekintse meg a meghívókat.

Először készítse elő a környezetet az Azure CLI-hez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Futtassa [az az datashare consumer invitation list parancsot](/cli/azure/datashare/consumer/invitation#az_datashare_consumer_invitation_list) az aktuális meghívókhoz:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Másolja ki a meghívóazonosítót a következő szakaszban való használatra.

---

## <a name="accept-invitation"></a>Meghívó elfogadása

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Ellenőrizze, hogy minden mező át van-e vizsgálni, beleértve a **használati feltételeket is.** Ha elfogadja a használati feltételeket, be kell pipának lennie, hogy jelezze az elfogadom. 

   ![Használati feltételek](./media/terms-of-use.png "Használati feltételek") 

1. A *Cél Data Share fiók alatt* válassza ki azt az előfizetést és erőforráscsoportot, amelybe üzembe Data Share erőforráscsoportot. 

   A Data Share **fiók mezőben** válassza az **Új** létrehozása lehetőséget, ha még nem Data Share fiókkal. Ellenkező esetben válasszon ki egy Data Share fiókot, amelybe el szeretné fogadni az adat megosztását. 

   A **Fogadott megosztás neve mezőben** meghagyhatja a megadott adatok által megadott alapértelmezett értéket, vagy megadhat egy új nevet a fogadott megosztásnak. 

   Miután elfogadta a használati feltételeket, és megadott egy Data Share-fiókot a fogadott megosztás kezeléséhez, válassza az Elfogadás és **konfigurálás lehetőséget.** Létrejön egy megosztási előfizetés. 

   ![Elfogadási beállítások](./media/accept-options.png "Beállítások elfogadása") 

   Ez a művelet a fiókban fogadott megosztásra Data Share viszi. 

   Ha nem szeretné elfogadni a meghívót, válassza az Elutasítás *lehetőséget.* 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja az [az datashare consumer share-subscription create parancsot](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_create) a Data Share.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>Fogadott megosztás konfigurálása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépésekkel konfigurálhatja, hogy hová szeretné megkapni az adatokat.

1. Válassza **az Adatkészletek lapot.** Jelölje be annak az adatkészletnek a jelölőnégyzetét, amelyhez célhelyet szeretne rendelni. **Céladattár kiválasztásához válassza** a + Leképezés célként lehetőséget. 

   ![Leképezés a célhoz](./media/dataset-map-target.png "Leképezés a célhoz") 

1. Válassza ki azt a céladattár-típust, amelybe az adatokat szeretné behozni. A céladattárban található, azonos elérési út és névvel lévő adatfájlok és táblák felül lesznek írva. Ha adatokat fogad az Azure SQL Database vagy Azure Synapse Analytics (korábban Azure SQL DW) számára, jelölje be az Allow Data Share to run the above **'create user' script on myhalf**(A felhasználó létrehozása a felhasználó létrehozása a nevében) jelölőnégyzetet.

   A helybeli megosztáshoz válasszon ki egy adattárat a megadott helyen. A Hely az az Azure-adatközpont, ahol az adatszolgáltató forrásadattára található. Miután leképezte az adatkészletet, a Cél elérési útja hivatkozással elérheti az adatokat.

   ![Cél tárfiók](./media/dataset-map-target-sql.png "Céltároló") 

1. Pillanatkép-alapú megosztás esetén, ha az adatszolgáltató létrehozott egy pillanatkép-ütemezést az adatok rendszeres frissítéséhez, akkor a Pillanatkép-ütemezés lapot választva is engedélyezheti a **pillanatkép-ütemezést.** Jelölje be a pillanatkép ütemezése melletti jelölőnégyzetet, és válassza **a + Engedélyezés lehetőséget.** Vegye figyelembe, hogy az első ütemezett pillanatkép az ütemezési időponttól számított egy percen belül indul el, a későbbi pillanatképek pedig az ütemezett időponttól számított másodpercekben indulnak el.

   ![Pillanatkép-ütemezés engedélyezése](./media/enable-snapshot-schedule.png "Pillanatkép-ütemezés engedélyezése")

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ezekkel a parancsokkal konfigurálhatja, hogy hová szeretné megkapni az adatokat.

1. Futtassa [az az datashare consumer share-subscription list-source-dataset](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_dataset) parancsot az adatkészlet azonosítójának lekértéhez:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Futtassa [az az storage account create](/cli/azure/storage/account#az_storage_account_create) parancsot egy tárfiók létrehozásához ehhez a Data Share:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. A [tárfiók azonosítóját az az storage account show](/cli/azure/storage/account#az_storage_account_show) paranccsal használhatja:

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. Az alábbi paranccsal lekérte az egyszerű fiók azonosítóját:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Az [az role assignment create paranccsal](/cli/azure/role/assignment#az_role_assignment_create) hozzon létre egy szerepkör-hozzárendelést a fióknévhez:

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Hozzon létre egy változót a leképezéshez az adatkészlet azonosítója alapján:

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. Használja az [az datashare consumer dataset-mapping create parancsot](/cli/azure/datashare/consumer/dataset-mapping#az_datashare_consumer_dataset_mapping_create) az adatkészlet-leképezés létrehozásához:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Futtassa [az az datashare consumer share-subscription synchronization start](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_start) parancsot az adatkészlet-szinkronizálás indításához.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Az [az datashare consumer share-subscription synchronization list paranccsal](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_list) tekintse meg a szinkronizálások listáját:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Az [az datashare consumer share-subscription list-source-share-synchronization-setting paranccsal](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) tekintse meg a megosztáson beállított szinkronizálási beállításokat.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Pillanatkép aktiválása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Ezek a lépések csak a pillanatkép-alapú megosztásra vonatkoznak.

1. Pillanatképet úgy aktiválhat, hogy kiválasztja a **Részletek lapot,** majd a **Trigger snapshot (Eseményindító pillanatképe) gombra.** Itt elindíthatja az adatok teljes vagy növekményes pillanatképét. Ha most először fogad adatokat az adatszolgáltatótól, válassza a teljes másolat lehetőséget. 

   ![Eseményindító pillanatképe](./media/trigger-snapshot.png "Eseményindító pillanatképe") 

1. Ha az utolsó futtatás állapota *sikeres,* a fogadott adatok megtekintéséhez a céladattárba kell átmenni. Válassza **az Adatkészletek** lehetőséget, majd kattintson a Target Path (Cél elérési útja) mezőben található hivatkozásra. 

   ![Fogyasztói adatkészletek](./media/consumer-datasets.png "Fogyasztói adatkészlet-leképezés") 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Futtassa [az az datashare consumer trigger create parancsot](/cli/azure/datashare/consumer/trigger#az_datashare_consumer_trigger_create) egy pillanatkép aktiválására:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Ezt a parancsot csak pillanatkép-alapú megosztáshoz használja.

---

## <a name="view-history"></a>Előzmények megtekintése
Ez a lépés csak a pillanatkép-alapú megosztásra vonatkozik. A pillanatképek előzményeinek megtekintéséhez válassza az **Előzmények** lapot. Itt az elmúlt 30 napban létrehozott összes pillanatkép előzményeit találja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforrásra már nincs szükség, az Áttekintés Data Share  **válassza** a Törlés lehetőséget az eltávolításához.

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan fogadhat el és fogadhat el Azure Data Share. Ha többet szeretne megtudni a Azure Data Share fogalmakról, folytassa a Azure Data Share szakkifejezésekkel.

> [!div class="nextstepaction"]
> [Azure Data Share alapfogalmak](terminology.md)