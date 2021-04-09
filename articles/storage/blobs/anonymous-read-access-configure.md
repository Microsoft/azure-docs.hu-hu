---
title: Névtelen nyilvános olvasási hozzáférés konfigurálása a tárolók és a Blobok számára
titleSuffix: Azure Storage
description: Megtudhatja, hogyan engedélyezheti vagy tilthatja a névtelen hozzáférést a Storage-fiók blob-adateléréséhez. Állítsa be a tároló nyilvános hozzáférési beállítását, hogy a tárolók és Blobok elérhetők legyenek a névtelen hozzáféréshez.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: feac7b890c973b1541c5362f860432687082953f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96533876"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Névtelen nyilvános olvasási hozzáférés konfigurálása a tárolók és a Blobok számára

Az Azure Storage támogatja az opcionális névtelen nyilvános olvasási hozzáférést a tárolók és a Blobok számára. Alapértelmezés szerint a névtelen hozzáférés nem engedélyezett az adataihoz. Ha explicit módon engedélyezi a névtelen hozzáférést, a tárolóra és a blobokra vonatkozó összes kérést engedélyezni kell. Ha a tároló nyilvános hozzáférési szintjének beállítását a névtelen hozzáférés engedélyezésére konfigurálja, az ügyfelek a kérelem engedélyezése nélkül is olvashatják az adott tárolóban tárolt adatolvasást.

> [!WARNING]
> Ha egy tároló nyilvános hozzáférésre van konfigurálva, akkor bármely ügyfél beolvashatja az adott tárolóban lévő összes ügyfelet. A nyilvános hozzáférés potenciális biztonsági kockázatot jelent, így ha a forgatókönyv nem igényli azt, a Microsoft azt javasolja, hogy ne engedélyezze azt a Storage-fiók esetében. További információ: a [Névtelen nyilvános olvasási hozzáférés megakadályozása a tárolók és a Blobok](anonymous-read-access-prevent.md)számára.

Ez a cikk a tárolók és a Blobok névtelen nyilvános olvasási hozzáférésének konfigurálását ismerteti. További információ a blob-adatok ügyfélalkalmazások általi névtelen eléréséről: a [nyilvános tárolók és Blobok névtelenül](anonymous-read-access-client.md)való elérése a .net-tel.

## <a name="about-anonymous-public-read-access"></a>Névtelen nyilvános olvasási hozzáférés

Alapértelmezés szerint az adataihoz való nyilvános hozzáférés mindig tiltott. A nyilvános hozzáférést két külön beállítás befolyásolja:

1. **A Storage-fiókhoz való nyilvános hozzáférés engedélyezése.** Alapértelmezés szerint a Storage-fiókok lehetővé teszik, hogy a felhasználók a megfelelő engedélyekkel engedélyezzék a tárolóhoz való nyilvános hozzáférést. A blob-információk nyilvános hozzáféréshez nem érhetők el, kivéve, ha a felhasználó a további lépésekkel explicit módon konfigurálja a tároló nyilvános hozzáférési beállításait.
1. **Konfigurálja a tároló nyilvános hozzáférési beállításait.** Alapértelmezés szerint a tároló nyilvános hozzáférésének beállítása le van tiltva, ami azt jelenti, hogy a tárolóhoz vagy annak minden kérelméhez engedély szükséges. A megfelelő engedélyekkel rendelkező felhasználók úgy módosíthatják a tároló nyilvános hozzáférési beállításait, hogy csak akkor engedélyezze a névtelen hozzáférést, ha a Storage-fiókhoz névtelen hozzáférés van engedélyezve.

Az alábbi táblázat összefoglalja, hogy mindkét beállítás hogyan befolyásolja a tárolók nyilvános elérését.

| Nyilvános hozzáférés beállítása | A nyilvános hozzáférés le van tiltva egy tárolónál (alapértelmezett beállítás) | A tárolók nyilvános hozzáférése tárolóra van állítva | A tárolók nyilvános hozzáférése Blobra van állítva |
|--|--|--|--|
| A Storage-fiókhoz nem engedélyezett a nyilvános hozzáférés | Nincs nyilvános hozzáférés a Storage-fiókban található egyik tárolóhoz sem. | Nincs nyilvános hozzáférés a Storage-fiókban található egyik tárolóhoz sem. A Storage-fiók beállítása felülbírálja a tároló beállítását. | Nincs nyilvános hozzáférés a Storage-fiókban található egyik tárolóhoz sem. A Storage-fiók beállítása felülbírálja a tároló beállítását. |
| A Storage-fiók számára engedélyezett a nyilvános hozzáférés (alapértelmezett beállítás) | Nincs nyilvános hozzáférés ehhez a tárolóhoz (alapértelmezett konfiguráció). | A nyilvános hozzáférés engedélyezett a tároló és a Blobok számára. | A nyilvános hozzáférés engedélyezve van a tárolóban lévő Blobok számára, a tárolóban azonban nem. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Nyilvános olvasási hozzáférés engedélyezése vagy letiltása egy Storage-fiókhoz

Alapértelmezés szerint a Storage-fiók úgy van konfigurálva, hogy lehetővé tegye a felhasználók számára a megfelelő engedélyeket a tárolóhoz való nyilvános hozzáférés engedélyezéséhez. Ha a nyilvános hozzáférés engedélyezve van, a megfelelő engedélyekkel rendelkező felhasználók módosíthatják a tároló nyilvános hozzáférési beállításait, hogy a névtelen hozzáférést engedélyezzék a tárolóban lévők számára. A blob-információk soha nem érhetők el a nyilvános hozzáféréshez, kivéve, ha a felhasználó a további lépéseket a tároló nyilvános hozzáférési beállításának explicit módon történő konfigurálásához.

Ne feledje, hogy a tárolóhoz való nyilvános hozzáférés alapértelmezés szerint ki van kapcsolva, és explicit módon be kell állítani a névtelen kérelmek engedélyezését. A Storage-fiók beállításaitól függetlenül az adatai soha nem lesznek elérhetők nyilvános hozzáférésre, kivéve, ha egy megfelelő engedélyekkel rendelkező felhasználó ezt a további lépést teszi a nyilvános hozzáférés engedélyezésére a tárolón.

A Storage-fiókhoz való nyilvános hozzáférés letiltása megakadályozza a fiókban lévő összes tárolóhoz és blobhoz való névtelen hozzáférést. Ha a fiókhoz nem engedélyezett a nyilvános hozzáférés, nem lehet konfigurálni a tároló nyilvános hozzáférési beállításait a névtelen hozzáférés engedélyezéséhez. A fokozott biztonság érdekében a Microsoft azt javasolja, hogy a Storage-fiókok számára ne engedélyezze a nyilvános hozzáférést, kivéve, ha a forgatókönyv megköveteli, hogy a felhasználók névtelenül férhessenek hozzá a blob-erőforrásokhoz.

> [!IMPORTANT]
> A Storage-fiókhoz való nyilvános hozzáférés letiltása felülbírálja a Storage-fiókban lévő összes tároló nyilvános hozzáférési beállításait. Ha a Storage-fiókhoz való nyilvános hozzáférés nem engedélyezett, a fiókra vonatkozó jövőbeli névtelen kérelmek sikertelenek lesznek. A beállítás módosítása előtt ügyeljen arra, hogy Ismerje meg, hogy milyen hatással van az ügyfélalkalmazások névtelenül a Storage-fiókban lévő adatokhoz való hozzáférésre. További információ: a [Névtelen nyilvános olvasási hozzáférés megakadályozása a tárolók és a Blobok](anonymous-read-access-prevent.md)számára.

A Storage-fiókok nyilvános hozzáférésének engedélyezéséhez vagy letiltásához konfigurálja a fiók **AllowBlobPublicAccess** tulajdonságát. Ez a tulajdonság minden olyan Storage-fiókhoz elérhető, amely a Azure Resource Manager telepítési modellel lett létrehozva. További információ: a [Storage-fiók áttekintése](../common/storage-account-overview.md).

A **AllowBlobPublicAccess** tulajdonság alapértelmezés szerint nincs beállítva a Storage-fiókhoz, és nem ad vissza értéket, amíg explicit módon be nem állítja azt. A Storage-fiók engedélyezi a nyilvános hozzáférést, ha a tulajdonság értéke **Null** vagy **igaz**.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A Azure Portal a Storage-fiókok nyilvános hozzáférésének engedélyezéséhez vagy letiltásához kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. Keresse meg a **konfigurációs** beállítást a **Beállítások** területen.
1. A **blob nyilvános hozzáférésének** beállítása **engedélyezett** vagy **Letiltva**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="A fiókhoz tartozó nyilvános blob-hozzáférés engedélyezését és letiltását bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha engedélyezni vagy engedélyezni szeretné a nyilvános hozzáférést egy Storage-fiókhoz a PowerShell-lel, telepítse [Azure PowerShell 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) vagy újabb verzióját. Ezután konfigurálja a **AllowBlobPublicAccess** tulajdonságot egy új vagy meglévő Storage-fiókhoz.

A következő példa létrehoz egy Storage-fiókot, és explicit módon beállítja a **AllowBlobPublicAccess** tulajdonságot **igaz** értékre. Ezután frissíti a Storage-fiókot, hogy **hamis** értékre állítsa a **AllowBlobPublicAccess** tulajdonságot. A példa az egyes esetekben a tulajdonság értékét is lekéri. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha engedélyezni vagy letiltani szeretné a nyilvános hozzáférést egy Storage-fiókhoz az Azure CLI-vel, telepítse az Azure CLI Version 2.9.0 vagy újabb verzióját. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ezután konfigurálja a **allowBlobPublicAccess** tulajdonságot egy új vagy meglévő Storage-fiókhoz.

A következő példa létrehoz egy Storage-fiókot, és explicit módon beállítja a **allowBlobPublicAccess** tulajdonságot **igaz** értékre. Ezután frissíti a Storage-fiókot, hogy **hamis** értékre állítsa a **allowBlobPublicAccess** tulajdonságot. A példa az egyes esetekben a tulajdonság értékét is lekéri. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[Sablon](#tab/template)

Egy sablonhoz tartozó Storage-fiók nyilvános hozzáférésének engedélyezéséhez vagy letiltásához hozzon létre egy olyan sablont, amely a **AllowBlobPublicAccess** tulajdonság értéke **true** vagy **false**. A következő lépések azt ismertetik, hogyan lehet sablont létrehozni a Azure Portalban.

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
1. A **Keresés a piactéren** mezőbe írja be a **sablon központi telepítése** kifejezést, majd nyomja le az **ENTER** billentyűt.
1. Válassza **template Deployment (üzembe helyezés egyéni sablonok használatával) (előzetes verzió)**, válassza a **Létrehozás** lehetőséget, majd **a szerkesztőben válassza a saját sablon** létrehozása lehetőséget.
1. A sablon szerkesztőjében illessze be a következő JSON-t egy új fiók létrehozásához, és állítsa a **AllowBlobPublicAccess** tulajdonságot **true** vagy **false** értékre. Ne felejtse el lecserélni a helyőrzőket a saját értékeire a szögletes zárójelben.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Mentse a sablont.
1. Adja meg az erőforráscsoport paramétert, majd válassza a **felülvizsgálat + létrehozás** gombot a sablon telepítéséhez és a **allowBlobPublicAccess** tulajdonsággal konfigurált Storage-fiók létrehozásához.

---

> [!NOTE]
> A Storage-fiókhoz való nyilvános hozzáférés letiltása nem befolyásolja az adott Storage-fiókban üzemeltetett statikus webhelyeket. A **$web** tároló mindig nyilvánosan elérhető.
>
> A Storage-fiókhoz tartozó nyilvános hozzáférési beállítás frissítése után akár 30 másodpercig is eltarthat, amíg a változás teljes mértékben propagálva lesz.

A Blobok nyilvános hozzáférésének engedélyezéséhez vagy letiltásához az Azure Storage erőforrás-szolgáltató 2019-04-01-es vagy újabb verziójára van szükség. További információ: [Azure Storage erőforrás-szolgáltató REST API](/rest/api/storagerp/).

Az ebben a szakaszban szereplő példák azt mutatják be, hogyan lehet beolvasni a Storage-fiók **AllowBlobPublicAccess** tulajdonságát annak megállapítására, hogy a nyilvános hozzáférés jelenleg engedélyezett vagy nem engedélyezett. Ha többet szeretne megtudni arról, hogyan ellenőrizheti, hogy a fiók nyilvános hozzáférési beállítása a névtelen hozzáférés megakadályozása érdekében be van-e állítva, tekintse meg a [Névtelen nyilvános hozzáférés szervizelése](anonymous-read-access-prevent.md#remediate-anonymous-public-access)című témakört.

## <a name="set-the-public-access-level-for-a-container"></a>Egy tároló nyilvános hozzáférési szintjének beállítása

Ahhoz, hogy a névtelen felhasználók számára olvasási hozzáférést adjon egy tárolóhoz és a blobokhoz, először engedélyezze a nyilvános hozzáférést a Storage-fiókhoz, majd állítsa be a tároló nyilvános hozzáférési szintjét. Ha a Storage-fiókhoz való nyilvános hozzáférés megtagadva, nem konfigurálhatja a tárolók nyilvános hozzáférését.

Ha a nyilvános hozzáférés engedélyezett a Storage-fiókhoz, a következő engedélyekkel konfigurálhat egy tárolót:

- **Nincs nyilvános olvasási hozzáférés:** A tároló és a hozzá tartozó Blobok csak jogosult kéréssel érhetők el. Ez a beállítás az összes új tároló alapértelmezett értéke.
- **Nyilvános olvasási hozzáférés csak Blobok esetén:** A tárolóban lévő blobokat névtelen kérések is elolvashatják, de a tárolók nem érhetők el névtelenül. A névtelen ügyfelek nem tudják enumerálni a tárolóban lévő blobokat.
- **Nyilvános olvasási hozzáférés a tárolóhoz és a blobokhoz:** A tároló-és blob-adatokat névtelen kérések is elolvashatják, kivéve a tároló engedélyeinek beállításait és a tárolók metaadatait. Az ügyfelek a tárolóban lévő blobokat névtelen kérelem alapján enumerálják, de a tárolóban lévő tárolók nem sorolhatók fel.

Az egyes Blobok nyilvános hozzáférési szintje nem módosítható. A nyilvános hozzáférési szint csak a tároló szintjén állítható be. Megadhatja a tároló nyilvános hozzáférési szintjét a tároló létrehozásakor, vagy frissítheti egy meglévő tároló beállításait is.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A Azure Portal egy vagy több meglévő tárolójának nyilvános hozzáférési szintjének frissítéséhez kövesse az alábbi lépéseket:

1. A Azure Portalban navigáljon a Storage-fiók áttekintéséhez.
1. A menü panel **blob Service** területén válassza a **tárolók** lehetőséget.
1. Válassza ki azokat a tárolókat, amelyekhez a nyilvános hozzáférési szintet be szeretné állítani.
1. A **hozzáférési szint módosítása** gomb használatával jelenítse meg a nyilvános hozzáférési beállításokat.
1. Válassza ki a **nyilvános hozzáférési szint** legördülő menüből a kívánt nyilvános hozzáférési szintet, majd az OK gombra kattintva alkalmazza a módosítást a kijelölt tárolók elemre.

    ![A nyilvános hozzáférési szint megadását bemutató képernyőkép a portálon](./media/anonymous-read-access-configure/configure-public-access-container.png)

Ha a Storage-fiókhoz a nyilvános hozzáférés nem engedélyezett, a tároló nyilvános hozzáférési szintje nem állítható be. Ha megkísérli a tároló nyilvános hozzáférési szintjének beállítását, láthatja, hogy a beállítás le van tiltva, mert a fiókhoz a nyilvános hozzáférés nem engedélyezett.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Képernyőfelvétel, amely azt mutatja, hogy a tároló nyilvános hozzáférési szintje le van tiltva, ha a nyilvános hozzáférés nem engedélyezett":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha egy vagy több tárolóhoz a PowerShell használatával szeretné frissíteni a nyilvános hozzáférési szintet, hívja [meg a set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) parancsot. Engedélyezze a műveletet a fiók kulcsa, a kapcsolati karakterlánc vagy a közös hozzáférésű aláírás (SAS) megadásával. A tároló nyilvános hozzáférési szintjét [beállító tároló ACL](/rest/api/storageservices/set-container-acl) -művelete nem támogatja az Azure ad-vel való engedélyezést. További információ: [a blob-és üzenetsor-adatműveletek meghívására vonatkozó engedélyek](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

A következő példa egy nyilvános hozzáféréssel rendelkező tárolót hoz létre, majd frissíti a tároló nyilvános hozzáférési beállításait, hogy engedélyezze a névtelen hozzáférést a tárolóhoz és a blobokhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

Ha a Storage-fiókhoz a nyilvános hozzáférés nem engedélyezett, a tároló nyilvános hozzáférési szintje nem állítható be. Ha megpróbálja beállítani a tároló nyilvános hozzáférési szintjét, az Azure Storage olyan hibaüzenetet ad vissza, amely azt jelzi, hogy a nyilvános hozzáférés nem engedélyezett a Storage-fiókban.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha egy vagy több tárolóhoz az Azure CLI-vel szeretné frissíteni a nyilvános hozzáférési szintet, hívja meg az az [Storage Container set Permission](/cli/azure/storage/container#az-storage-container-set-permission) parancsot. Engedélyezze a műveletet a fiók kulcsa, a kapcsolati karakterlánc vagy a közös hozzáférésű aláírás (SAS) megadásával. A tároló nyilvános hozzáférési szintjét [beállító tároló ACL](/rest/api/storageservices/set-container-acl) -művelete nem támogatja az Azure ad-vel való engedélyezést. További információ: [a blob-és üzenetsor-adatműveletek meghívására vonatkozó engedélyek](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

A következő példa egy nyilvános hozzáféréssel rendelkező tárolót hoz létre, majd frissíti a tároló nyilvános hozzáférési beállításait, hogy engedélyezze a névtelen hozzáférést a tárolóhoz és a blobokhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

Ha a Storage-fiókhoz a nyilvános hozzáférés nem engedélyezett, a tároló nyilvános hozzáférési szintje nem állítható be. Ha megpróbálja beállítani a tároló nyilvános hozzáférési szintjét, az Azure Storage olyan hibaüzenetet ad vissza, amely azt jelzi, hogy a nyilvános hozzáférés nem engedélyezett a Storage-fiókban.

# <a name="template"></a>[Sablon](#tab/template)

N/A.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>A nyilvános hozzáférés beállítása tárolók készletéhez

A tárolók listázásával és a nyilvános hozzáférési beállítások ellenőrzésével ellenőrizhető, hogy egy vagy több Storage-fiók mely tárolókat konfigurálja nyilvános hozzáférésre. Ez a megközelítés praktikus megoldás, ha a Storage-fiók nem tartalmaz nagy mennyiségű tárolót, vagy ha kis számú Storage-fiókban ellenőrzi a beállítást. A teljesítmény azonban csökkenhet, ha nagy mennyiségű tárolót próbál számba venni.

A következő példa a PowerShell használatával kéri le a nyilvános hozzáférési beállítást egy Storage-fiókban lévő összes tárolóhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Következő lépések

- [Tárolók és Blobok névtelen nyilvános olvasási hozzáférésének tiltása](anonymous-read-access-prevent.md)
- [Nyilvános tárolók és Blobok elérése névtelenül a .NET-tel](anonymous-read-access-client.md)
- [Hozzáférés engedélyezése az Azure Storage-hoz](../common/storage-auth.md)
