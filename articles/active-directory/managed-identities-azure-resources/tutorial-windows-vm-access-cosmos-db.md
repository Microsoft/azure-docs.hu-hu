---
title: 'Oktatóanyag: felügyelt identitás használata az Azure Cosmos DB-Windows-Azure AD eléréséhez'
description: Az oktatóanyag azt ismerteti, hogyan lehet hozzáférni az Azure Cosmos DB-hez egy Windows VM-beli, rendszer által hozzárendelt felügyeltszolgáltatás-identitással.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04508f1aa8ee9d6b4f730f57c60d959fab209122
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093788"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Oktatóanyag: Hozzáférés az Azure Cosmos DB-hez egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan férhet hozzá a Cosmos DB-hez egy Windows rendszerű virtuális gép (VM) rendszer által hozzárendelt felügyelt identitásával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Cosmos DB-fiók létrehozása
> * Rendszer által hozzárendelt felügyelt identitáson alapuló hozzáférés biztosítása egy Windows VM-nek a Cosmos DB-fiók hozzáférési kulcsaihoz
> * Hozzáférési jogkivonat lekérése a Windows VM rendszer által hozzárendelt felügyelt identitásának használatával, majd az Azure Resource Manager meghívása annak használatával
> * Hozzáférési kulcsok lekérése az Azure Resource Managerből Cosmos DB-hívások indításához

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri az Azure-erőforrások felügyelt identitására vonatkozó funkciót, tekintse meg ezt az [áttekintést](overview.md). 
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A szükséges erőforrás-létrehozás és szerepkör-felügyelet végrehajtásához a fiókjának „Tulajdonos” jogosultságokkal kell rendelkeznie a megfelelő hatókörben (az előfizetésben vagy az erőforráscsoportban) Ha segítségre van szüksége a szerepkör-hozzárendeléssel kapcsolatban, tekintse meg az Azure [-szerepkörök hozzárendelése az Azure-előfizetés erőforrásaihoz való hozzáférés kezeléséhez](../../role-based-access-control/role-assignments-portal.md)című témakört
- A [Azure PowerShell](/powershell/azure/install-az-ps) legújabb verziójának telepítése
- Szükség van egy Windows rendszerű virtuális gépre is, amelyhez engedélyezve van a rendszerhez rendelt felügyelt identitások.
  - Ha létre kell hoznia egy virtuális gépet ehhez az oktatóanyaghoz, kövesse a [virtuális gép létrehozása rendszer által hozzárendelt identitással](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) című cikket.

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-fiók létrehozása 

Ha még nincs fiókja, hozzon létre egy Cosmos DB-fiókot. Ezt a lépést kihagyhatja, ha egy meglévő Cosmos DB-fiókot használ. 

1. Kattintson az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** gombra.
2. Kattintson az **Adatbázisok**, majd az **Azure Cosmos DB** lehetőségre. Ekkor megjelenik egy új, „Új fiók” nevű panel.
3. Adja meg a Cosmos DB-fiók **azonosítóját**, amelyet később használni fog.  
4. Az **API** értéke legyen „SQL”. Az ebben az oktatóanyagban ismertetett megközelítést más API-típusokkal is használhatja, de az itt szereplő lépések az SQL API-ra vonatkoznak.
5. Ellenőrizze, hogy az **Előfizetés** és az **Erőforráscsoport** mező értéke egyezik-e az előző lépésben a virtuális gép létrehozása során megadottakkal.  Válasszon ki egy olyan **helyet**, ahol a Cosmos DB elérhető.
6. Kattintson a **Létrehozás** lehetőségre.

### <a name="create-a-collection"></a>Gyűjtemény létrehozása 

Adjon hozzá egy adatgyűjteményt a Cosmos DB-fiókhoz, amelyet a későbbi lépések során lekérdezhet.

1. Lépjen az újonnan létrehozott Cosmos DB-fiókra.
2. Az **Áttekintés** lapon kattintson a **+/Gyűjtemény hozzáadása** gombra. Ekkor megjelenik a „Gyűjtemény hozzáadása” panel.
3. Adja meg a gyűjtemény adatbázis- és gyűjteményazonosítóját, válasszon ki egy tárkapacitást, adjon meg partíciókulcsot és átviteli sebességet, majd kattintson az **OK** gombra.  Ebben az oktatóanyagban elég a „Teszt” kifejezést használni az adatbázis és a gyűjtemény azonosítójaként, kiválasztani egy rögzített kapacitást és a legalacsonyabb átviteli sebességet (400 RU/s).  


## <a name="grant-access"></a>Hozzáférés biztosítása

Ez a szakasz bemutatja, hogyan biztosítható a Windows rendszerű virtuális gépekhez rendelt felügyelt identitás hozzáférése a Cosmos DB fiók hozzáférési kulcsaihoz. A Cosmos DB nem támogatja natív módon az Azure AD-hitelesítést. A rendszerhez rendelt felügyelt identitással azonban lekérhet egy Cosmos DB hozzáférési kulcsot a Resource Managerből, és a kulcs használatával férhet hozzá a Cosmos DBhoz. Ebben a lépésben hozzáférést biztosít a Windows VM rendszer által hozzárendelt felügyelt identitása számára a Cosmos DB-fiók kulcsaihoz.

A Windows VM rendszerhez rendelt felügyelt identitás elérésének megadásához Azure Resource Manager PowerShell használatával a Cosmos DB fiókhoz frissítse a következő értékeket:

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>`

A Cosmos DB a részletesség két szintjét támogatja a hozzáférési kulcsok használatakor: a fiók írási/olvasási, illetve írásvédett hozzáférését.  Rendelje hozzá a `DocumentDB Account Contributor` szerepkört, ha a fiók írási/olvasási kulcsait szeretné lekérni, vagy rendelje hozzá a `Cosmos DB Account Reader Role` szerepkört, ha írásvédett hozzáférést szeretne a fiókhoz.  A jelen oktatóanyag esetében a `Cosmos DB Account Reader Role` szerepkört rendelje hozzá:

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> Ne feledje, hogy ha nem tud végrehajtani egy műveletet, lehet, hogy nem rendelkezik a megfelelő engedélyekkel. Ha írási hozzáférést szeretne a kulcsokhoz, olyan Azure-szerepkört kell használnia, mint például a DocumentDB-fiók közreműködője, vagy létre kell hoznia egy egyéni szerepkört. További információkért tekintse át [Az Azure szerepköralapú hozzáférés-vezérlését Azure Cosmos db](../../cosmos-db/role-based-access-control.md)

## <a name="access-data"></a>Adatok elérése

Ez a szakasz azt mutatja be, hogyan hívható meg Azure Resource Manager a Windows VM rendszerhez rendelt felügyelt identitáshoz tartozó hozzáférési jogkivonat használatával. Az oktatóanyag további részében a korábban létrehozott virtuális gépről dolgozunk. 

Telepítenie kell az [Azure CLI](/cli/azure/install-azure-cli) legújabb verzióját a Windows rendszerű virtuális gépre.

### <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

1. Az Azure Portalon lépjen a **Virtuális gépek** lapra, keresse meg a Windows rendszerű virtuális gépet, majd kattintson az **Áttekintés** lap tetején található **Csatlakozás** gombra. 
2. A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a Windows VM létrehozásakor adott meg. 
3. Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a PowerShellt a távoli munkamenetben.
4. A PowerShell Invoke-WebRequest parancsával küldjön kérést a helyi Azure-erőforrások felügyeltidentitási végpontjára, hogy lekérjen egy hozzáférési jogkivonatot az Azure Resource Managerhez.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > A „resource” paraméter értékének pontosan egyeznie kell az Azure AD által várt értékkel. Az Azure Resource Manager erőforrás-azonosítójának használatakor a záró perjelet is szerepeltetni kell az URI-ban.
    
   Ezután nyerje ki a „Content” elemet, amelyet egy JavaScript Object Notation (JSON) formátumú sztringként tárol a $response objektum. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   Ezután nyerje ki a hozzáférési jogkivonatot a válaszból.
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>Hozzáférési kulcsok beolvasása 

Ez a szakasz azt mutatja be, hogyan lehet hozzáférési kulcsokat lekérni a Azure Resource Managerról Cosmos DB-hívások létrehozásához. A PowerShell használatával hívja meg a Resource Managert az Cosmos DB fiók elérési kulcsának beolvasásához szükséges hozzáférési jogkivonattal. Amint megkaptuk a hozzáférési kulcsot, a Cosmos DB lekérdezhetővé válik. Használja a saját értékeit az alábbi bejegyzések lecseréléséhez:

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>` 
- Az `<ACCESS TOKEN>` paraméter értékét cserélje le a korábban lekért hozzáférési jogkivonattal. 

>[!NOTE]
>Ha olvasási/írási kulcsokat szeretne lekérni, akkor `listKeys` típusú kulcsműveleteket használjon.  Ha csak olvasható kulcsokat szeretne beolvasni, használja a kulcs típusú műveletet `readonlykeys` . Ha nem tudja használni a "listkeys műveletének beolvasása", ellenőrizze, hogy a [megfelelő szerepkört](../../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role) rendelte-e a felügyelt identitáshoz.

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/readonlykeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
A válasz megadja a kulcsok listáját.  Ha például írásvédett kulcsokat kap:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Most, hogy rendelkezik a Cosmos DB-fiók hozzáférési kulcsával, átadhatja azt egy Cosmos DB SDK-nak, és hívásokat indíthat a fiók elérése érdekében.  Átadhatja például a hozzáférési kulcsot az Azure CLI-nek.  A(z) `<COSMOS DB CONNECTION URL>` elemet az Azure Portalon, a Cosmos DB-fiók panelének **Áttekintés** lapjáról szerezheti be.  Cserélje le a(z) `<ACCESS KEY>` elemet az így beszerzett értékre:

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Ez a CLI-parancs a gyűjtemény részleteit adja vissza:

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```


## <a name="disable"></a>Letiltás

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Következő lépések

Az oktatóanyag bemutatta, hogyan használhatja egy Windows VM rendszer által hozzárendelt felügyelt identitásait a Cosmos DB eléréséhez.  További információ a Cosmos DB-ről:

> [!div class="nextstepaction"]
>[Azure Cosmos DB áttekintése](../../cosmos-db/introduction.md)