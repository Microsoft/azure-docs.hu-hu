---
title: 'Oktatóanyag: & fogadása az Azure-adatmegosztásról'
description: Oktatóanyag – az Azure-adatmegosztás használatával elfogadhatja és fogadhatja az adatfogadást
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: ccfda4975b6453ed67edc2640520bc0a76df5709
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644884"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Oktatóanyag: Adatok elfogadása és fogadása az Azure Data Share használatával  

Ebből az oktatóanyagból megtudhatja, hogyan fogadhatja el az adatmegosztási meghívásokat az Azure-adatmegosztás használatával. Megtudhatja, hogyan fogadhatja Önnel az Önnel megosztott adatmennyiséget, valamint hogyan engedélyezheti a rendszeres frissítési időközt annak biztosítása érdekében, hogy mindig a legfrissebb pillanatképet kapjon az Önnel megosztott adatokról. 

> [!div class="checklist"]
> * Azure-beli adatmegosztási meghívás elfogadása
> * Azure-beli adatmegosztási fiók létrehozása
> * Adja meg az adatai célhelyét
> * Előfizetés létrehozása az adatmegosztásra ütemezett frissítéshez

## <a name="prerequisites"></a>Előfeltételek
Az adatmegosztási Meghívások elfogadása előtt számos Azure-erőforrást kell kiépíteni, amelyek alább láthatók. 

Az adatmegosztási Meghívások elfogadása előtt győződjön meg arról, hogy az összes előfeltétel befejeződik. 

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Egy adatmegosztás meghívása: Microsoft Azure egy meghívót, melynek tárgya "Azure-adatmegosztási meghívás **<yourdataprovider@domain.com>** ".
* Regisztrálja a [Microsoft. DataShare erőforrás-szolgáltatót](concepts-roles-permissions.md#resource-provider-registration) az Azure-előfizetésben, amelyben létre fog hozni egy adatmegosztási erőforrást és az Azure-előfizetést, ahol a cél Azure-adattárolók találhatók.

### <a name="receive-data-into-a-storage-account"></a>Adatfogadás egy Storage-fiókba

* Azure Storage-fiók: Ha még nem rendelkezik ilyennel, létrehozhat egy [Azure Storage-fiókot](../storage/common/storage-account-create.md)is. 
* A Storage-fiókba való írásra vonatkozó engedély, amely megtalálható a *Microsoft. Storage/storageAccounts/Write* szolgáltatásban. Ez az engedély a Közreműködő szerepkör részét képezi. 
* Jogosultság a szerepkör-hozzárendelés hozzáadásához a Storage-fiókhoz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelésekben/írásban*. Ez az engedély a Tulajdonos szerepkör részét képezi.  

### <a name="receive-data-into-a-sql-based-target"></a>Adatfogadás SQL-alapú célhelyre
Ha úgy dönt, hogy befogadja az Azure SQL Databaseba az Azure szinapszis Analyticset, az alábbi lista tartalmazza az előfeltételek listáját. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Az Adatfogadás előfeltételei a Azure SQL Database vagy az Azure szinapszis Analytics szolgáltatásba (korábban Azure SQL DW)

* Egy Azure SQL Database vagy Azure szinapszis Analytics (korábban Azure SQL DW).
* A *Microsoft. SQL/Servers/Databases/Write* adatbázisban található SQL Server-adatbázisba való írásra vonatkozó engedély. Ez az engedély a **Közreműködő** szerepkör részét képezi. 
* Az SQL Server **Azure Active Directory rendszergazdája**
* SQL Server tűzfal-hozzáférés. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portal található SQL Serverben navigáljon a *tűzfalak és a virtuális hálózatok* területére.
    1. Az **Igen** gombra kattintva *engedélyezheti, hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a kiszolgálóhoz*.
    1. Kattintson az **+ ügyfél IP-** címének hozzáadása elemre. Az ügyfél IP-címének módosítása változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb megosztja az SQL-adatok Azure Portalból való megosztását. Hozzáadhat IP-címtartományt is.
    1. Kattintson a **Mentés** gombra. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Az Azure szinapszis Analytics (munkaterület) SQL-készletbe való adatfogadás előfeltételei

* Egy Azure szinapszis Analytics (munkaterület) dedikált SQL-készlet. Az Adatfogadás kiszolgáló nélküli SQL-készletbe jelenleg nem támogatott.
* Engedély a szinapszis munkaterületen található SQL-készletbe való írásra, amely megtalálható a *Microsoft. szinapszis/munkaterület/sqlPools/Write* fájlokban. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* Engedély az adatmegosztási erőforrás felügyelt identitásához a szinapszis-munkaterület SQL-készletének eléréséhez. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon a szinapszis munkaterületre. Válassza az SQL Active Directory-rendszergazda lehetőséget a bal oldali navigációs sávon, és állítsa be magát a **Azure Active Directory-rendszergazdaként**.
    1. Nyissa meg a szinapszis Studio alkalmazást, és válassza a *kezelés* lehetőséget a bal oldali navigációs sávon. Válassza a *hozzáférés-vezérlés* lehetőséget a biztonság területen. Rendeljen hozzá saját **SQL-rendszergazdai** vagy **munkaterület-rendszergazdai** szerepkört.
    1. A szinapszis Studióban válassza a *fejlesztés* lehetőséget a bal oldali navigációs sávon. Hajtsa végre a következő parancsfájlt az SQL-készletben az adatmegosztási erőforrás felügyelt identitásának "db_datareader, db_datawriter, db_ddladmin" értékkel való hozzáadásához. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Vegye figyelembe, hogy a *<share_acc_name>* az adatmegosztási erőforrás neve. Ha még nem hozott létre adatmegosztási erőforrást, később is visszatérhet ehhez az előfeltételhöz.  

* A szinapszis munkaterület tűzfal-hozzáférése. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon a szinapszis munkaterületre. A bal oldali navigációs sávon válassza a *tűzfalak* lehetőséget.
    1. Ide **kattintva** *engedélyezheti, hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a munkaterülethez*.
    1. Kattintson az **+ ügyfél IP-** címének hozzáadása elemre. Az ügyfél IP-címének módosítása változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb megosztja az SQL-adatok Azure Portalból való megosztását. Hozzáadhat IP-címtartományt is.
    1. Kattintson a **Mentés** gombra. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Az Azure Adatkezelő-fürtbe történő adatfogadás: 

* Azure Adatkezelő-fürt ugyanabban az Azure-adatközpontban, mint az adatszolgáltató Adatkezelő-fürtje: Ha még nem rendelkezik ilyennel, létrehozhat egy [azure adatkezelő-fürtöt](/azure/data-explorer/create-cluster-database-portal). Ha nem ismeri az adatszolgáltató fürtje Azure-adatközpontját, később a folyamat során is létrehozhatja a fürtöt.
* Engedély az Azure Adatkezelő-fürtbe való írásra, amely megtalálható a *Microsoft. Kusto/fürtök/írás* szolgáltatásban. Ez az engedély a Közreműködő szerepkör részét képezi. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="open-invitation"></a>Meghívás megnyitása

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A meghívót megnyithatja e-mailben, vagy közvetlenül a Azure Portalból. 

   Ha meg szeretné nyitni a meghívót az e-mailből, akkor az adatszolgáltatótól érkező meghívót a Beérkezett üzenetek közül A meghívó Microsoft Azure származik, és az **Azure-beli adatmegosztási meghívást kapott <yourdataprovider@domain.com>**. Kattintson a **meghívás megtekintése** elemre, hogy megtekintse az Azure-beli meghívót. 

   Ha közvetlenül Azure Portal szeretné megnyitni a meghívót, keresse meg az **adatmegosztási meghívásokat** a Azure Portalban. Ez a művelet végigvezeti az adatmegosztási meghívások listáján.

   ![Meghívások listája](./media/invitations.png "Meghívások listája") 

1. Válassza ki a megtekinteni kívánt megosztást. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Készítse elő az Azure CLI-környezetet, és tekintse meg a meghívókat.

Először készítse elő a környezetet az Azure CLI-hez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

A jelenlegi meghívások megtekintéséhez futtassa az az [DataShare Consumer meghívás List](/cli/azure/ext/datashare/datashare/consumer/invitation#ext_datashare_az_datashare_consumer_invitation_list) parancsot:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Másolja a meghívó AZONOSÍTÓját a következő szakaszban való használatra.

---

## <a name="accept-invitation"></a>Meghívás elfogadása

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Győződjön meg arról, hogy az összes mezőt felülvizsgálják, beleértve a használati **feltételeket** is. Ha elfogadja a használati feltételeket, a jelölőnégyzet bejelölésével jelezheti, hogy elfogadja. 

   ![Használati feltételek](./media/terms-of-use.png "Használati feltételek") 

1. A *cél adatmegosztási fiók* területen válassza ki azt az előfizetést és erőforráscsoportot, amelybe az adatmegosztást telepíteni fogja. 

   Az **adatmegosztási fiók** mezőben válassza az **új létrehozása** lehetőséget, ha nem rendelkezik meglévő adatmegosztási fiókkal. Ellenkező esetben válasszon ki egy meglévő adatmegosztási fiókot, amelyet el szeretne fogadni az adatmegosztásban. 

   A **Beérkezett megosztás neve** mezőben hagyhatja az alapértelmezett által megadott értéket, vagy megadhat egy új nevet a fogadott megosztáshoz. 

   Miután jóváhagyta a használati feltételeket, és megadott egy adatmegosztási fiókot a kapott megosztás kezeléséhez, válassza az **elfogadás és konfigurálás** lehetőséget. A rendszer létrehoz egy megosztási előfizetést. 

   ![Elfogadás beállításai](./media/accept-options.png "Elfogadás beállításai") 

   Ez a művelet átveszi a kapott megosztást az adatmegosztási fiókban. 

   Ha nem szeretné elfogadni a meghívót, válassza az *elutasítás* lehetőséget. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az adatmegosztás létrehozásához használja az az [DataShare fogyasztói megosztás – előfizetés létrehozása](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_create) parancsot.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>Fogadott megosztás konfigurálása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépésekkel konfigurálhatja, hogy hová kívánja fogadni az adatgyűjtést.

1. Válassza az **adatkészletek** lapot. Jelölje be azon adatkészlet melletti jelölőnégyzetet, amelyhez hozzá szeretné rendelni a célhelyet. A cél adattár kiválasztásához válassza a **+ Térkép** lehetőséget. 

   ![Leképezés célhelyre](./media/dataset-map-target.png "Leképezés célhelyre") 

1. Válassza ki azt a célként megadott adattár-típust, amelybe az adatterületet szeretné. A célként megadott adattárban lévő adatfájlokat vagy táblákat ugyanazzal az elérési úttal és névvel írja felül a rendszer. Ha Azure SQL Databaseba vagy Azure szinapszis Analyticsbe (korábban Azure SQL DW) fogadja az adatgyűjtést, jelölje be az **adatmegosztás engedélyezése a fenti "felhasználó létrehozása" szkript futtatása a saját nevében** jelölőnégyzetet.

   A helyi megosztáshoz válasszon egy adattárat a megadott helyen. A hely az az Azure-adatközpont, ahol az adatszolgáltató forrás-adattára található. Ha az adatkészlet le van képezve, a cél elérési úton található hivatkozásra kattintva érheti el az adatokat.

   ![Cél Storage-fiók](./media/dataset-map-target-sql.png "Cél tárterülete") 

1. A pillanatkép-alapú megosztáshoz, ha az adatszolgáltató pillanatkép-ütemtervet hozott létre az adatok rendszeres frissítéséhez, **akkor a pillanatkép-ütemterv** kiválasztásával is engedélyezheti a pillanatkép-ütemtervet. Jelölje be a pillanatkép-ütemterv melletti jelölőnégyzetet, majd válassza a **+ Engedélyezés** lehetőséget. Vegye figyelembe, hogy az első ütemezett pillanatkép az ütemezési idő egy percén belül indul el, és az azt követő Pillanatképek az ütemezett időponttól számított másodperceken belül kezdődnek.

   ![Pillanatkép-ütemterv engedélyezése](./media/enable-snapshot-schedule.png "Pillanatkép-ütemterv engedélyezése")

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ezekkel a parancsokkal konfigurálhatja, hogy hol kívánja fogadni az adatgyűjtést.

1. Az adatkészlet AZONOSÍTÓjának lekéréséhez futtassa az az [DataShare Consumer Share-Subscription List-Source-adatkészlet](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_dataset) parancsot:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Az az [Storage Account Create](/cli/azure/storage/account#az_storage_account_create) paranccsal hozzon létre egy Storage-fiókot ehhez az adatmegosztáshoz:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Használja az az [Storage Account show](/cli/azure/storage/account#az_storage_account_show) parancsot a Storage-fiók azonosítójának lekéréséhez:

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. Használja a következő parancsot a fiók egyszerű AZONOSÍTÓjának lekéréséhez:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Az az [role hozzárendelés Create](/cli/azure/role/assignment#az_role_assignment_create) paranccsal hozzon létre egy szerepkör-hozzárendelést az egyszerű fiókhoz:

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

1. Az adatkészlet-hozzárendelés létrehozásához használja az az [DataShare Consumer adatkészlet-Mapping Create](/cli/azure/ext/datashare/datashare/consumer/dataset-mapping#ext_datashare_az_datashare_consumer_dataset_mapping_create) parancsot:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Futtassa az az [DataShare Consumer Share-előfizetés synchronization Start](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_start) parancsot az adatkészlet szinkronizálásának indításához.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   A szinkronizálások listájának megtekintéséhez futtassa az az [DataShare fogyasztói megosztás – előfizetés szinkronizációs listája](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_list) parancsot:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   A megosztáson beállított szinkronizálási beállítások megtekintéséhez használja az az [DataShare fogyasztóvédelmi megosztás-előfizetés lista-forrás-megosztás-synchronization-Setting](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) parancsot.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Pillanatkép indítása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Ezek a lépések csak a pillanatkép-alapú megosztásra vonatkoznak.

1. A pillanatképek elindításához válassza a **részletek** lapot, majd az **trigger pillanatképét**. Itt aktiválhatja az adatok teljes vagy növekményes pillanatképét. Ha az első alkalommal fogadja az adatait az adatszolgáltatótól, válassza a teljes másolás lehetőséget. 

   ![Pillanatkép indítása](./media/trigger-snapshot.png "Pillanatkép indítása") 

1. Ha a legutóbbi futtatási állapot *sikeres*, nyissa meg a cél adattárt a kapott adatértékek megtekintéséhez. Válassza az **adatkészletek** lehetőséget, majd kattintson a hivatkozásra a cél elérési úton. 

   ![Fogyasztói adatkészletek](./media/consumer-datasets.png "Fogyasztói adatkészlet megfeleltetése") 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A pillanatkép elindításához futtassa az az [DataShare Consumer trigger Create](/cli/azure/ext/datashare/datashare/consumer/trigger#ext_datashare_az_datashare_consumer_trigger_create) parancsot:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Ezt a parancsot csak pillanatkép-alapú megosztásra használja.

---

## <a name="view-history"></a>Előzmények megtekintése
Ez a lépés csak a pillanatkép-alapú megosztásra vonatkozik. A pillanatképek előzményeinek megtekintéséhez válassza az **Előzmények** fület. Itt megtalálhatja az elmúlt 30 napban létrehozott összes pillanatkép előzményeit.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforrásra már nincs szükség, lépjen az **adatmegosztás áttekintése** lapra, és válassza a **Törlés** lehetőséget az eltávolításához.

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan fogadhat és fogadhat Azure-adatmegosztást. Ha többet szeretne megtudni az Azure-beli adatmegosztási fogalmakról, folytassa az Azure-beli adatmegosztási terminológiával.

> [!div class="nextstepaction"]
> [Az Azure-adatmegosztás fogalmai](terminology.md)