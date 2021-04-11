---
title: A költségek optimalizálása az Azure Blob Storage hozzáférési szintjeinek automatizálásával
description: Hozzon létre automatizált szabályokat az adatok gyors és lassú elérésű és archív rétegek közötti áthelyezéséhez.
author: twooley
ms.author: twooley
ms.date: 10/29/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: e0b9f3b5728e4604d7c51c1d49196cfcf1161aef
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278031"
---
# <a name="optimize-costs-by-automating-azure-blob-storage-access-tiers"></a>A költségek optimalizálása az Azure Blob Storage hozzáférési szintjeinek automatizálásával

Az adatkészletek egyedi életciklusokkal rendelkeznek. Az életciklus korai szakaszában a felhasználók gyakran férnek hozzá bizonyos adatszolgáltatásokhoz. A hozzáférés azonban drasztikusan csökken, mint az adatvesztés. Egyes adatforgalom üresjáratban marad a felhőben, és a tárolása ritkán történik meg. Néhány adat a létrehozás után nappal vagy hónapokban lejár, míg más adathalmazok aktívan olvashatók és módosulnak az élettartamuk során. Az Azure Blob Storage életciklus-kezelési szolgáltatás a GPv2 és a blob Storage-fiókokra vonatkozó részletes, szabályon alapuló szabályzatot kínál. A szabályzat segítségével átválthatja az adatait a megfelelő hozzáférési rétegekbe, vagy lejárhat az adatéletciklus végén.

Az életciklus-kezelési házirend a következőket teszi lehetővé:

- A Blobok gyors átállása azonnal, ha a teljesítmény optimalizálása érdekében elérhető 
- Az áttérési Blobok, blob-verziók és blob-Pillanatképek egy hűvösebb tárolási rétegre (gyors elérésű, gyors archiválásra vagy lassú elérésű archiválásra), ha nem férnek hozzá, vagy nem módosítanak egy ideig
- Blobok, blob-verziók és blob-Pillanatképek törlése az életciklusuk végén
- A Storage-fiók szintjén naponta egyszer futtatandó szabályok definiálása
- Szabályok alkalmazása a tárolók vagy a Blobok egy részhalmaza számára (név előtaggal vagy [blob-index címkékkel](storage-manage-find-blobs.md) szűrőként használva)

Vegyünk például egy olyan forgatókönyvet, amelyben az adatmennyiség az életciklus korai szakaszában, de csak két hét múlva alkalmanként válik elérhetővé. Az első hónapban a rendszer ritkán fér hozzá az adatkészlethez. Ebben az esetben a gyors tárolás a korai fázisokban a legjobb. A ritka elérésű tárolás a legmegfelelőbb az alkalmi hozzáféréshez. Az Archive Storage a legjobb lehetőség az adatvesztést követően a hónapban. Ha a tárolási rétegek mennyiségét az adatmennyiség tekintetében állítja be, az igényeinek leginkább megfelelő tárolási lehetőségeket is megtervezheti. Az áttérés eléréséhez az életciklus-kezelési házirend szabályai elérhetők az adatvesztéshez a hűvösebb rétegekbe.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

>[!NOTE]
>Ha olyan adatokra van szüksége, amelyek olvashatók maradnak, például ha a StorSimple használja, ne állítson be olyan házirendet, amely a blobokat az archív szintre helyezi át.

## <a name="availability-and-pricing"></a>Rendelkezésre állás és díjszabás

Az életciklus-kezelési funkció az összes Azure-régióban elérhető az általános célú v2 (GPv2) fiókokhoz, a blob Storage-fiókokhoz, a prémium szintű blokk blob Storage-fiókokhoz és a Azure Data Lake Storage Gen2 fiókokhoz. A Azure Portal egy meglévő általános célú (GPv1-) fiókot frissíthet egy GPv2-fiókra. A Storage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiók áttekintése](../common/storage-account-overview.md).

Az életciklus-kezelési szolgáltatás díjmentes. A blob szintű API-hívások [beállításakor](/rest/api/storageservices/set-blob-tier) a rendszer a szokásos működési költséget számítja fel. A törlési művelet ingyenes. A díjszabással kapcsolatos további információkért lásd a [Blobok díjszabásának blokkolása](https://azure.microsoft.com/pricing/details/storage/blobs/)című témakört.

## <a name="add-or-remove-a-policy"></a>Szabályzat hozzáadása vagy eltávolítása

A szabályzatokat a következő módszerek bármelyikével adhatja hozzá, szerkesztheti vagy távolíthatja el:

* [Azure Portalra](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](/cli/azure/install-azure-cli)
* [REST API-k](/rest/api/storagerp/managementpolicies)

A szabályzatok teljes mértékben olvashatók vagy írhatók. A részleges frissítések nem támogatottak. 

> [!NOTE]
> Ha engedélyezi a tűzfalszabályok beállításait a Storage-fiókjához, előfordulhat, hogy az életciklus-kezelési kérelmek le lesznek tiltva. A kérések blokkolását úgy oldhatja fel, ha kivételeket ad meg a megbízható Microsoft-szolgáltatásokra vonatkozóan. További információt a [tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md#exceptions)című témakör kivételek című szakaszában talál.

Ez a cikk bemutatja, hogyan kezelheti a szabályzatokat a portál és a PowerShell-metódusok használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Két módon adhat hozzá házirendet a Azure Portalon keresztül. 

* [Azure Portal listanézet](#azure-portal-list-view)
* [Azure Portal kód nézet](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure Portal listanézet

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Azure Portal keresse meg és válassza ki a Storage-fiókját. 

1. A **blob Service** területen válassza az **életciklus-kezelés** lehetőséget a szabályok megtekintéséhez vagy módosításához.

1. Válassza a **listanézet** lapot.

1. Válassza a **szabály hozzáadása** lehetőséget, és nevezze el a szabályt a **részletek** űrlapon. Megadhatja a **szabály hatókörét**, a **blob típusát** és a **blob altípusának** értékeit is. A következő példa a Blobok szűrési hatókörét állítja be. Ennek hatására a rendszer hozzáadja a **szűrő beállítása** lapot.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="Életciklus-kezelés: szabály részleteinek hozzáadása lap Azure Portal":::

1. A szabály feltételeinek megadásához válassza az **Alap Blobok** lehetőséget. A következő példában a blobokat a rendszer a lassú tárterületre helyezi át, ha 30 napig nem módosították őket.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="Életciklus-kezelési alap Blobok lapja Azure Portal":::

   Az **utolsó hozzáférés** lehetőség a következő régiókban érhető el előzetes verzióban:

    - Közép-Franciaország
    - Kelet-Kanada
    - Közép-Kanada

   > [!IMPORTANT]
   > A legutóbbi hozzáférési idő követésének előzetes verziója csak a nem éles használatra használható. Az üzemi szolgáltatási szintű szerződések (SLA-kat) jelenleg nem érhetők el.
   
   Az **utolsó elérhető** beállítás használatához a Azure Portal **életciklus-kezelés** lapján válassza a **hozzáférés-követés engedélyezése** lehetőséget. További információ az **utolsó elért** lehetőségről: az [adatok áthelyezése az utolsó hozzáférés dátuma (előzetes verzió) alapján](#move-data-based-on-last-accessed-date-preview).

1. Ha a **részletek** lapon a **Blobok korlátozása szűrőkkel** lehetőséget választotta, akkor a szűrő **beállítása** elemre kattintva hozzáadhat egy opcionális szűrőt. A következő példa a *mylifecyclecontainer* tárolóban lévő blobokra szűri a "log" kezdetű blobokat.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="Életciklus-felügyeleti szűrő beállított lapja Azure Portal":::

1. Az új szabályzat hozzáadásához válassza a **Hozzáadás** lehetőséget.

#### <a name="azure-portal-code-view"></a>Azure Portal kód nézet
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Azure Portal keresse meg és válassza ki a Storage-fiókját.

1. A **blob Service** területen válassza az **életciklus-kezelés** lehetőséget a szabályzat megtekintéséhez vagy módosításához.

1. A következő JSON-példa egy olyan házirendre mutat, amely beilleszthető a **Code View (kód nézet** ) lapra.

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. Kattintson a **Mentés** gombra.

1. A JSON-példával kapcsolatos további információkért tekintse meg a [szabályzatok](#policy) és [szabályok](#rules) szakaszt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A következő PowerShell-szkripttel adhat hozzá szabályzatot a Storage-fiókhoz. A `$rgname` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a Storage-fiók nevével.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[Sablon](#tab/template)

Az életciklus-kezelést megadhatja Azure Resource Manager sablonok használatával. Itt látható egy sablon az RA-GRS GPv2 Storage-fiók életciklus-kezelési házirenddel való üzembe helyezéséhez.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>Szabályzat

Az életciklus-kezelési szabályzat egy JSON-dokumentum szabályainak gyűjteménye:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

A szabályzatok a szabályok gyűjteményei:

| Paraméter neve | Paramétertípus | Jegyzetek |
|----------------|----------------|-------|
| `rules`        | Szabály objektumainak tömbje | Egy házirendben legalább egy szabályra van szükség. Egy házirendben legfeljebb 100 szabályt adhat meg.|

A szabályzaton belüli szabályok több paraméterrel rendelkeznek:

| Paraméter neve | Paramétertípus | Jegyzetek | Kötelező |
|----------------|----------------|-------|----------|
| `name`         | Sztring |A szabály neve legfeljebb 256 alfanumerikus karaktert tartalmazhat. A szabály neve megkülönbözteti a kis-és nagybetűket. Egy szabályzaton belül egyedinek kell lennie. | Igaz |
| `enabled`      | Logikai | Egy nem kötelező logikai érték, amely lehetővé teszi egy szabály ideiglenes letiltását. Az alapértelmezett érték igaz, ha nincs beállítva. | Hamis | 
| `type`         | Enumerálási érték | A jelenlegi érvényes típus: `Lifecycle` . | Igaz |
| `definition`   | Az életciklus-szabályt meghatározó objektum | Mindegyik definíció egy szűrő készletből és egy műveleti készletből áll. | Igaz |

## <a name="rules"></a>Szabályok

Mindegyik szabály definíciója tartalmaz egy szűrőt és egy műveleti készletet. A [szűrési készlet](#rule-filters) korlátozza a szabályok műveleteit egy adott objektumra a tárolón vagy az objektumok nevein belül. A [művelet beállítja](#rule-actions) a szintet vagy a DELETE műveletet a szűrt objektumokra.

### <a name="sample-rule"></a>Minta szabály

A következő minta szabály úgy szűri a fiókot, hogy a műveleteit a-ben és a-ben létező objektumokon futtassa `container1` `foo` .

>[!NOTE]
>- Az életciklus-kezelés támogatja a Blobok blokkolását és a blob-típusok hozzáfűzését.<br>
>- Az életciklus-kezelés nem érinti a rendszertárolókat, például a $logs és a $web.

- Rétegbeli blob – a legutolsó módosítás után 30 nappal a lehűtési szintig
- Szintű blob az archiválási szintre 90 nappal az utolsó módosítás után
- A blob 2 555 nap (hét év) törlése az utolsó módosítás után
- Korábbi blob-verziók törlése a létrehozás után 90 nappal

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "rulefoo",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "container1/foo"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Szabályok szűrői

A szűrő korlátozza a szabályok műveleteit a Blobok egy részhalmazára a Storage-fiókon belül. Ha egynél több szűrő van definiálva, az `AND` összes szűrőn egy logikai fut.

A szűrők a következők:

| Szűrő neve | Szűrő típusa | Jegyzetek | Kötelező |
|-------------|-------------|-------|-------------|
| blobTypes   | Előre definiált enumerálási értékek tömbje. | A jelenlegi kiadás támogatja `blockBlob` és `appendBlob` . A csak a törlést támogatja `appendBlob` , a set szintű beállítás nem támogatott. | Igen |
| prefixMatch | Karakterláncok tömbje az előtagok megfeleltetéséhez. Mindegyik szabály legfeljebb 10 előtagot tud definiálni. Egy előtag-karakterláncnak a tároló nevével kell kezdődnie. Ha például egy szabályhoz tartozó összes blobot szeretné egyeztetni `https://myaccount.blob.core.windows.net/container1/foo/...` , a prefixMatch a következő: `container1/foo` . | Ha nem határoz meg prefixMatch, a szabály a Storage-fiókban lévő összes blobra vonatkozik. | Nem |
| blobIndexMatch | A blob index címke kulcsát és a hozzájuk illeszkedő értékeket tartalmazó szótárak tömbje. Az egyes szabályok legfeljebb 10 blob-index címkét adhatnak meg. Ha például az összes blobot `Project = Contoso` egy szabály alá szeretné egyeztetni `https://myaccount.blob.core.windows.net/` , a blobIndexMatch a következő: `{"name": "Project","op": "==","value": "Contoso"}` . | Ha nem határoz meg blobIndexMatch, a szabály a Storage-fiókban lévő összes blobra vonatkozik. | Nem |

> [!NOTE]
> A blob index nyilvános előzetes verzióban érhető el, és a **Közép**-Kanada, **Kelet-Kanada**, **Közép**-Franciaország és Dél- **Franciaország** régiójában érhető el. Ha többet szeretne megtudni erről a szolgáltatásról, valamint az ismert problémákról és a korlátozásokról, tekintse meg [Az Azure Blob Storage a blob index (előzetes verzió) használatával történő kezelésével és keresésével](storage-manage-find-blobs.md)kapcsolatos információkat.

### <a name="rule-actions"></a>Szabály műveletei

Ha a futtatási feltétel teljesül, a rendszer a szűrt blobokra alkalmazza a műveleteket.

Az életciklus-kezelés támogatja a Blobok, a korábbi blob-verziók és a blob-Pillanatképek leválasztását és törlését. Adjon meg legalább egy műveletet az alapblobok, a korábbi blob-verziók vagy a blob-Pillanatképek minden szabályához.

| Művelet                      | Alap blob                                  | Pillanatkép      | Verzió
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | Támogatott: `blockBlob`                  | Támogatott     | Támogatott     |
| enableAutoTierToHotFromCool | Támogatott: `blockBlob`                  | Nem támogatott | Nem támogatott |
| tierToArchive               | Támogatott: `blockBlob`                  | Támogatott     | Támogatott     |
| delete                      | Támogatott `blockBlob` és `appendBlob` | Támogatott     | Támogatott     |

>[!NOTE]
>Ha ugyanazon a blobon több műveletet is definiál, az életciklus-kezelés a legkevesebb költséges műveletet alkalmazza a blobra. Például a művelet `delete` olcsóbb a műveletnél `tierToArchive` . `tierToArchive`A művelet olcsóbb a műveletnél `tierToCool` .

A futtatási feltételek életkoron alapulnak. Az alapblobok az utolsó módosítás időpontját használják, a blob-verziók a verzió létrehozási idejét használják, a blob-Pillanatképek pedig a létrehozási időt használják a kor nyomon követéséhez.

| Művelet futtatási feltétele               | Feltétel értéke                          | Leírás                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| daysAfterModificationGreaterThan   | Egész számú érték, amely a kora napokat jelzi | Az alap blob-műveletek feltétele                                              |
| daysAfterCreationGreaterThan       | Egész számú érték, amely a kora napokat jelzi | A blob-verzió és a blob-pillanatkép műveleteinek feltétele                         |
| daysAfterLastAccessTimeGreaterThan | Egész számú érték, amely a kora napokat jelzi | előnézet Az alap blob-műveletek feltétele az utolsó hozzáférés idejének engedélyezésekor |

## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan lehet kezelni a gyakori forgatókönyveket életciklus-szabályzatokkal.

### <a name="move-aging-data-to-a-cooler-tier"></a>Az adatvesztést a hűvösebb szintjére helyezheti át

Ez a példa azt mutatja be, hogyan lehet áttérni a blokk Blobok előre rögzített `container1/foo` vagy `container2/bar` . A házirend olyan blobokat vált át, amelyek több mint 30 nap alatt nem lettek módosítva a lassú tároláshoz, és a Blobok 90 nap alatt nem módosultak az archiválási szintre:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-date-preview"></a>Adatok áthelyezése az utolsó elért dátum alapján (előzetes verzió)

Az utolsó hozzáférés időpontjának nyomon követésével engedélyezheti a blob utolsó olvasásának vagy írásának rekordját. A legutóbbi hozzáférési időt szűrőként használhatja a blob-adatok szintjeinek és megőrzésének kezeléséhez.

Az **utolsó hozzáférés** lehetőség a következő régiókban érhető el előzetes verzióban:

 - Közép-Franciaország
 - Kelet-Kanada
 - Közép-Kanada

> [!IMPORTANT]
> A legutóbbi hozzáférési idő követésének előzetes verziója csak a nem éles használatra használható. Az üzemi szolgáltatási szintű szerződések (SLA-kat) jelenleg nem érhetők el.

Az **utolsó elérhető** beállítás használatához a Azure Portal **életciklus-kezelés** lapján válassza a **hozzáférés-követés engedélyezése** lehetőséget.

#### <a name="how-last-access-time-tracking-works"></a>Az utolsó hozzáférés időpontjának nyomon követése

Ha az utolsó hozzáférés időpontjának nyomon követése engedélyezve van, a rendszer a Blobok `LastAccessTime` beolvasása vagy írása esetén frissíti a nevű blob-tulajdonságot. Egy [Get blob](/rest/api/storageservices/get-blob) művelet egy hozzáférési műveletnek minősül. A [blob tulajdonságainak](/rest/api/storageservices/get-blob-properties)beolvasása, a [Blobok metaadatainak](/rest/api/storageservices/get-blob-metadata)beolvasása és a [Blobok beolvasása](/rest/api/storageservices/get-blob-tags) nem érhető el, ezért nem frissíti a legutóbbi hozzáférési időt.

Az olvasási hozzáférési késés hatásának csökkentése érdekében csak az utolsó 24 óra első olvasása frissül a legutóbbi hozzáférési időpontnál. Az egyazon 24 órás időszakban a következő olvasások nem frissítik a legutóbbi hozzáférési időt. Ha módosítanak egy blobot az olvasások között, a legutóbbi hozzáférési idő a két érték újabb része.

A következő példában a blobokat a rendszer a lassú tárterületre helyezi át, ha 30 napig nem voltak elérhetők. A `enableAutoTierToHotFromCool` tulajdonság egy logikai érték, amely azt jelzi, hogy egy blobot automatikusan kell-e felvenni a lassú elérésű állapotba, ha azt követően ismét elérhetővé válik.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>A Storage-fiók támogatása

A legutóbbi hozzáférési idő nyomon követése a következő típusú Storage-fiókok esetében érhető el:

 - Általános célú v2 Storage-fiókok
 - BLOB Storage-fiókok letiltása
 - Blob Storage-fiókok

Ha a Storage-fiók egy általános célú v1-fiók, a Azure Portal használatával frissítsen egy általános célú v2-fiókra.

A Azure Data Lake Storage Gen2 használatával használható hierarchikus névtérrel rendelkező Storage-fiókok mostantól támogatottak.

#### <a name="pricing-and-billing"></a>Árak és számlázás

A legutóbbi hozzáférési idő frissítése egy [másik műveletnek](https://azure.microsoft.com/pricing/details/storage/blobs/)számít.

### <a name="archive-data-after-ingest"></a>Adatok archiválása betöltés után

Egyes adatforgalom üresjáratban marad a felhőben, és ritkán, ha még egyszer is hozzáfér a tárolóhoz. A következő életciklus-házirend úgy van konfigurálva, hogy a betöltés után röviddel az adatok archiválását. Ez a példa a tárolóban lévő Storage-fiókban lévő Blobok archiválási szintre való átváltását eredményezi `archivecontainer` . Az áttérést az utolsó módosítás időpontja után 0 nappal a Blobok alapján hajtja végre a rendszer:

> [!NOTE] 
> Javasoljuk, hogy a blobokat közvetlenül az archív szintre töltse fel, hogy hatékonyabb legyen. A [PutBlob](/rest/api/storageservices/put-blob) és a [PutBlockList](/rest/api/storageservices/put-block-list) x-MS-Access-réteg fejléce az 2018-11-09-as és újabb verziókkal, illetve a blob Storage-beli legújabb verziójával is elvégezhető. 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Az életkor alapján lejár az adatvesztés

A létrehozás után bizonyos adatértékek várhatóan lejárnak a napokban vagy hónapokban. Az életciklus-kezelési házirendet beállíthatja úgy, hogy az adatkor alapján törléssel lejárjon az adatvesztés. Az alábbi példa egy olyan házirendet mutat be, amely a 365 napnál régebbi összes blokk blobot törli.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>BLOB-indexekkel rendelkező adattörlési Címkék
Egyes adatmennyiségeket csak akkor lehet lemondani, ha explicit módon meg van jelölve törlésre. Az életciklus-kezelési házirendet konfigurálhatja úgy, hogy lejárjon a blob index kulcs/érték attribútumaival címkézett adatvesztéssel. Az alábbi példa egy olyan házirendet mutat be, amely törli a címkével ellátott összes blokk blobot `Project = Contoso` . További információ a blob indexről: az [Azure Blob Storage adatainak kezelése és keresése a blob indexével (előzetes verzió)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>Verziók kezelése

Azon adatmennyiségek esetében, amelyek élettartama során rendszeresen módosítják és hozzáfértek, engedélyezheti a blob Storage verziószámozását, hogy automatikusan fenntartsa az objektum korábbi verzióit. Létrehozhat egy szabályzatot a korábbi verziók kiválasztásához vagy törléséhez. A verziók korát a verzió létrehozási idejének kiértékelésével határozzuk meg. Ez a házirend-szabály a tárolóban lévő korábbi verziókat `activedata` , amelyek 90 nappal vagy annál régebbiek, miután a verzió létrehozása a ritka szintre történik, és törli az előző, 365 napos vagy régebbi verziókat.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="faq"></a>GYIK

**Létrehoztam egy új szabályzatot, miért nem azonnal futnak a műveletek?**

A platform naponta egyszer futtatja az életciklus-szabályzatot. Miután konfigurálta a házirendet, akár 24 órát is igénybe vehet, hogy egyes műveletek első alkalommal fussanak.

**Ha frissítek egy meglévő szabályzatot, mennyi ideig tart a műveletek futtatása?**

A frissített szabályzat akár 24 óráig is eltarthat. Ha a házirend érvényben van, akár 24 óráig is eltarthat a műveletek futtatása. Ezért a házirend-műveletek végrehajtása akár 48 óráig is eltarthat.

**Manuálisan rehidratáltam egy archivált blobot, hogyan tudom megakadályozni, hogy átmenetileg visszakerüljön az archiválási szintre?**

Ha egy blobot egy hozzáférési rétegből egy másikba helyez át, az utolsó módosítás időpontja nem változik. Ha az archivált blobokat manuálisan rehidratálja a gyors szintre, az életciklus-kezelő motor vissza fogja helyezni az archiválási szintre. Tiltsa le az ezt a blobot érintő szabályt ideiglenesen annak megakadályozása érdekében, hogy az archiválható legyen. Engedélyezze újra a szabályt, ha a blob biztonságosan visszahelyezhető az archiválási szintre. Azt is megteheti, hogy a blobot egy másik helyre másolja, ha a gyors vagy lassú elérésű szinten kell maradni.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan állíthatja helyre az adatokat a véletlen törlés után:

- [Az Azure Storage-blobok helyreállítható törlése](./soft-delete-blob-overview.md)

Ismerje meg, hogyan kezelheti és keresheti meg az adatblob-indexet:

- [Adatok keresése és kezelése az Azure Blob Storage-ban blobindex használatával](storage-manage-find-blobs.md)