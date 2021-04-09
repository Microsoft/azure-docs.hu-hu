---
title: A szolgáltatási erőforrások áthelyezése régiók között
titleSuffix: Azure Cognitive Search
description: Ez a cikk bemutatja, hogyan helyezheti át Azure Cognitive Search-erőforrásait az egyik régióból a másikba az Azure-felhőben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/10/2020
ms.openlocfilehash: a5050958f01743ff3c6fdcdecfee3067b1d2073e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934258"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Azure Cognitive Search-szolgáltatás áthelyezése másik Azure-régióba

Alkalmanként előfordulhat, hogy az ügyfelek egy másik régióba helyezik át a keresési szolgáltatást. Jelenleg nincs olyan beépített mechanizmus vagy eszköz, amely segítséget nyújt az adott feladathoz, de ez a cikk segít megérteni az azonos eredmény elérésének manuális lépéseit.

> [!NOTE]
> A Azure Portal minden szolgáltatás rendelkezik **exportálási sablon** paranccsal. Az Azure Cognitive Search esetében ez a parancs egy szolgáltatás alapszintű definícióját (név, hely, réteg, replika és partíciók száma) állítja elő, de nem ismeri fel a szolgáltatás tartalmát, és nem végez kulcsokat, szerepköröket vagy naplókat. Bár a parancs létezik, nem javasoljuk, hogy használja a keresési szolgáltatás áthelyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

+ Ellenőrizze, hogy a tárfiók által használt szolgáltatások és funkciók támogatottak-e a célként megadott régióban.

+ Az előzetes verziójú funkciók esetében győződjön meg arról, hogy az előfizetés jóvá van hagyva a célcsoport számára.

## <a name="prepare-and-move"></a>Előkészítés és áthelyezés

1. Azonosítsa a függőségeket és a kapcsolódó szolgáltatásokat, hogy megértsék a szolgáltatások áthelyezésének teljes hatását, ha többre van szüksége, mint az Azure Cognitive Search.

   Az Azure Storage a naplózáshoz, a Tudásbázis létrehozásához, valamint a mesterséges intelligencia-bővítéshez és-indexeléshez használatos külső adatforrás. Cognitive Services a mesterséges intelligencia-gazdagítás függősége. A Cognitive Services és a keresési szolgáltatásnak ugyanabban a régióban kell lennie, ha AI-dúsítást használ.

1. Hozzon létre leltárt a szolgáltatásban található összes objektumról, hogy tudja, mit kell áthelyezni: indexek, szinonimák térképek, indexelő, adatforrások, szakértelmével. Ha engedélyezte a naplózást, hozzon létre és archiváljon minden olyan jelentést, amelyre szüksége lehet egy korábbi rekordhoz.

1. Tekintse meg az árképzést és a rendelkezésre állást az új régióban az Azure Cognitive Search és az új régióban található kapcsolódó szolgáltatások elérhetőségének biztosítása érdekében. A funkciók többsége minden régióban elérhető, de bizonyos előzetes verziójú funkciók korlátozott rendelkezésre állással rendelkeznek.

1. Hozzon létre egy szolgáltatást az új régióban, és tegye közzé újból a forráskódból a meglévő indexeket, a szinonimákat, az indexelő, az adatforrásokat és a szakértelmével. Ne feledje, hogy a szolgáltatás nevének egyedinek kell lennie, hogy a meglévő nevet ne használja újra. Ellenőrizze az egyes készségkészlet, hogy a Cognitive Services kapcsolatok továbbra is érvényesek-e az azonos régióra vonatkozó követelmény szempontjából. Az Azure Storage-hoz készült kapcsolatok karakterláncait is megtekintheti, ha más szolgáltatást használ.

1. Indexek és tudásbázisok újratöltése, ha van ilyen. Az alkalmazás kódjának használatával leküldheti a JSON-adatok indexbe való küldését, vagy újrafuttathatja az indexelő dokumentumokat a külső forrásokból származó dokumentumok lekéréséhez. 

1. Engedélyezze a naplózást, és ha használja őket, hozza létre újra a biztonsági szerepköröket.

1. Az ügyfélalkalmazások és a tesztelési csomagok frissítése az új szolgáltatásnév és API-kulcsok használatára és az összes alkalmazás tesztelésére.

## <a name="discard-or-clean-up"></a>Elvetés vagy felesleges elemek eltávolítása

Törölje a régi szolgáltatást, ha az új szolgáltatás teljes körű tesztelése és működtetése megtörtént. A szolgáltatás törlése automatikusan törli a szolgáltatáshoz társított összes tartalmat.

## <a name="next-steps"></a>Következő lépések

A következő hivatkozások segítséget nyújtanak a fenti lépések elvégzéséhez szükséges további információk megkereséséhez.

+ [Az Azure Cognitive Search díjszabása és régiói](https://azure.microsoft.com/pricing/details/search/)
+ [Szint kiválasztása](search-sku-tier.md)
+ [Keresési szolgáltatás létrehozása](search-create-service-portal.md)
+ [Keresési dokumentumok betöltése](search-what-is-data-import.md)
+ [Naplózás engedélyezése](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is allowlisted for the target region. For more information about preview features, see [knowledge stores](./knowledge-store-concept-intro.md), [incremental enrichment](./cognitive-search-incremental-indexing-conceptual.md), and [private endpoint](./service-create-private-endpoint.md).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](../storage/common/storage-account-move.md?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](../storage/common/storage-account-move.md?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](./search-get-started-portal.md)

[Create a skillset](./cognitive-search-quickstart-blob.md)

[Create a knowledge store](./knowledge-store-create-portal.md) -->