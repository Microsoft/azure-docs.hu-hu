---
title: Névtelen nyilvános olvasási hozzáférés konfigurálása tárolókhoz és blobokhoz
titleSuffix: Azure Storage
description: Megtudhatja, hogyan engedélyezheti vagy engedélyezheti a tárfiók blobadatokhoz való névtelen hozzáférését. Állítsa be a tároló nyilvános hozzáférési beállítását, hogy a tárolók és blobok névtelen hozzáféréssel is elérhetők legyen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 31812a7b2dddad474ab5cd422a15f6e5368dba5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774628"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Névtelen nyilvános olvasási hozzáférés konfigurálása tárolókhoz és blobokhoz

Az Azure Storage támogatja a tárolók és blobok opcionális névtelen nyilvános olvasási hozzáférését. Alapértelmezés szerint az adatokhoz való névtelen hozzáférés soha nem engedélyezett. Ha nem engedélyezi explicit módon a névtelen hozzáférést, a tárolóra és annak blobjaira vonatkozó összes kérést engedélyezni kell. Ha egy tároló nyilvános hozzáférési szintű beállítását névtelen hozzáférésre konfigurálja, az ügyfelek a kérés hitelesítése nélkül olvashatják az abban található adatokat.

> [!WARNING]
> Ha egy tároló nyilvános hozzáférésre van konfigurálva, bármely ügyfél olvashatja a tárolóban található adatokat. A nyilvános hozzáférés potenciális biztonsági kockázatot jelent, így ha a forgatókönyvhöz nincs szükség rá, a Microsoft azt javasolja, hogy a tárfiók esetében ne. További információ: Tárolók és blobok névtelen nyilvános olvasási [hozzáférésének megakadályozása.](anonymous-read-access-prevent.md)

Ez a cikk azt ismerteti, hogyan konfigurálható névtelen nyilvános olvasási hozzáférés egy tárolóhoz és annak blobjaihoz. A blobadatok ügyfélalkalmazásból névtelen hozzáférésével kapcsolatos információkért lásd: Nyilvános tárolók és blobok névtelen elérése [a .NET használatával.](anonymous-read-access-client.md)

## <a name="about-anonymous-public-read-access"></a>Információk a névtelen nyilvános olvasási hozzáférésről

Az adatokhoz való nyilvános hozzáférés alapértelmezés szerint mindig tiltott. A nyilvános hozzáférést két külön beállítás befolyásolja:

1. **Nyilvános hozzáférés engedélyezése a tárfiókhoz.** Alapértelmezés szerint a tárfiókok lehetővé teszik a megfelelő engedélyekkel rendelkező felhasználók számára a tároló nyilvános hozzáférésének engedélyezését. A blobadatok csak akkor érhetők el nyilvános hozzáféréshez, ha a felhasználó a további lépéssel explicit módon konfigurálja a tároló nyilvános hozzáférési beállítását.
1. **Konfigurálja a tároló nyilvános hozzáférési beállítását.** Alapértelmezés szerint a tároló nyilvános hozzáférési beállítása le van tiltva, ami azt jelenti, hogy a tárolóhoz vagy annak adataihoz minden kéréshez engedélyezés szükséges. A megfelelő engedélyekkel rendelkező felhasználók csak akkor módosíthatják a tároló nyilvános hozzáférési beállítását, hogy névtelen hozzáférést engedélyezzenek, ha a tárfiók névtelen hozzáférése engedélyezett.

Az alábbi táblázat összefoglalja, hogy a két beállítás együttesen milyen hatással van a tároló nyilvános hozzáférésére.

| Nyilvános hozzáférés beállítása | A nyilvános hozzáférés le van tiltva egy tárolóhoz (alapértelmezett beállítás) | A tároló nyilvános hozzáférése Tárolóra van állítva | Nyilvános hozzáférés: A tároló beállítása Blob |
|--|--|--|--|
| A tárfiók nyilvános hozzáférése nem engedélyezett | A tárfiók egyik tárolója sem nyilvános hozzáféréssel rendelkezik. | A tárfiók egyik tárolója sem nyilvános hozzáféréssel rendelkezik. A tárfiók beállítása felülírja a tárolóbeállítást. | A tárfiók egyik tárolója sem nyilvános hozzáféréssel rendelkezik. A tárfiók beállítása felülírja a tárolóbeállítást. |
| A tárfiókhoz engedélyezett a nyilvános hozzáférés (alapértelmezett beállítás) | Nincs nyilvános hozzáférés ehhez a tárolóhoz (alapértelmezett konfiguráció). | Ehhez a tárolóhoz és annak blobjaihoz nyilvános hozzáférés engedélyezett. | Ebben a tárolóban a nyilvános hozzáférés engedélyezett a blobok számára, de magának a tárolónak nem. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Tárfiók nyilvános olvasási hozzáférésének engedélyezése vagy engedélyezése

Alapértelmezés szerint a tárfiók úgy van konfigurálva, hogy engedélyezze a megfelelő engedélyekkel rendelkező felhasználók számára a tároló nyilvános hozzáférését. Ha a nyilvános hozzáférés engedélyezve van, a megfelelő engedélyekkel rendelkező felhasználók módosíthatják a tároló nyilvános hozzáférési beállítását, hogy névtelen nyilvános hozzáférést engedélyezzenek a tárolóban található adatokhoz. A blobadatok csak akkor érhetők el nyilvános hozzáféréshez, ha a felhasználó a további lépéssel explicit módon konfigurálja a tároló nyilvános hozzáférési beállítását.

Ne feledje, hogy a tárolók nyilvános hozzáférése alapértelmezés szerint mindig ki van kapcsolva, és explicit módon kell konfigurálni a névtelen kérések igénylésének engedélyét. A tárfiók beállításától függetlenül az adatok soha nem lesznek elérhetők nyilvános hozzáférésre, hacsak egy megfelelő engedélyekkel rendelkező felhasználó nem teszi meg ezt a további lépést a tároló nyilvános hozzáférésének engedélyezéséhez.

A tárfiók nyilvános hozzáférésének letiltása megakadályozza a névtelen hozzáférést a fiókban lévő összes tárolóhoz és blobhoz. Ha a fiók számára nem engedélyezett a nyilvános hozzáférés, nem konfigurálható a tároló nyilvános hozzáférési beállítása a névtelen hozzáféréshez. A nagyobb biztonság érdekében a Microsoft javasolja, hogy ne követeli meg a tárfiókok nyilvános hozzáférését, kivéve, ha a forgatókönyv megköveteli, hogy a felhasználók névtelenül hozzáférjenek a blob-erőforrásokhoz.

> [!IMPORTANT]
> A tárfiókok nyilvános hozzáférésének 900. 10. 000.000 között a nyilvános hozzáférés 2016-ban való 10000000000000000000000 nem lesz elérhető. Ha a tárfiók nem rendelkezik nyilvános hozzáféréssel, a fiókra vonatkozó jövőbeli névtelen kérések meghiúsulnak. Mielőtt módosítja ezt a beállítást, mindenképpen tisztában kell lennie az olyan ügyfélalkalmazások hatásával, amelyek névtelenül férnek hozzá a tárfiókban az adatokhoz. További információ: [Tárolók és blobok névtelen nyilvános](anonymous-read-access-prevent.md)olvasási hozzáférésének megakadályozása.

Egy tárfiók nyilvános hozzáférésének engedélyezése vagy engedélyezése érdekében konfigurálja a fiók **AllowBlobPublicAccess tulajdonságát.** Ez a tulajdonság minden olyan tárfiókhoz elérhető, amely a Azure Resource Manager modellel jött létre. További információ: [Tárfiókok áttekintése.](../common/storage-account-overview.md)

Az **AllowBlobPublicAccess** tulajdonság alapértelmezés szerint nincs beállítva a tárfiókhoz, és csak akkor ad vissza értéket, ha explicit módon be van állítva. A tárfiók akkor engedélyezi a nyilvános hozzáférést, ha a tulajdonság értéke **null vagy** **igaz.**

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A tárfiók nyilvános hozzáférésének engedélyezése vagy Azure Portal kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. Keresse meg **a Konfiguráció beállítást** a Beállítások **alatt.**
1. A **Blob nyilvános hozzáférés beállítása Legyen** Engedélyezve **vagy** **Letiltva.**

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Képernyőkép a blobok nyilvános hozzáférésének fiókhoz való engedélyezése vagy mellőzését bemutató képernyőfelvétel":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha engedélyezni vagy engedélyeznie kell egy tárfiók nyilvános hozzáférését a PowerShell használatával, telepítse a Azure PowerShell [4.4.0-s](https://www.powershellgallery.com/packages/Az/4.4.0) vagy újabb verzióját. Ezután konfigurálja az **AllowBlobPublicAccess tulajdonságot** egy új vagy meglévő tárfiókhoz.

Az alábbi példa létrehoz egy tárfiókot, és explicit módon true (igaz) értékre állítja az **AllowBlobPublicAccess** **tulajdonságot.** Ezután frissíti a tárfiókot, hogy az **AllowBlobPublicAccess** tulajdonságot **hamisra állítsa.** A példa emellett minden esetben lekéri a tulajdonság értékét. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire:

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

Ha engedélyezni vagy engedélyeznie kell egy tárfiók nyilvános hozzáférését az Azure CLI használatával, telepítse az Azure CLI 2.9.0-s vagy újabb verzióját. További információ: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli) Ezután konfigurálja az **allowBlobPublicAccess tulajdonságot** egy új vagy meglévő tárfiókhoz.

Az alábbi példa létrehoz egy tárfiókot, és explicit módon true (igaz) értékre állítja az **allowBlobPublicAccess** **tulajdonságot.** Ezután frissíti a tárfiókot, hogy **az allowBlobPublicAccess** tulajdonságot false **(hamis) állapotúra állítsa.** A példa emellett minden esetben lekéri a tulajdonság értékét. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire:

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

Ha egy sablonnal engedélyezni vagy engedélyeznie kell egy tárfiók nyilvános hozzáférését, hozzon létre egy sablont **az AllowBlobPublicAccess** tulajdonság **true** (igaz) vagy **false**(hamis) értékével. Az alábbi lépések azt ismertetik, hogyan hozhat létre sablont a Azure Portal.

1. A Azure Portal válassza az **Erőforrás létrehozása lehetőséget.**
1. A **Keresés a Marketplace-en mezőbe írja** be a sablon üzembe **helyezését,** majd nyomja le az ENTER **billentyűt.**
1. Válassza Template deployment (üzembe helyezés egyéni **sablonokkal) (előzetes verzió)** lehetőséget, válassza a **Létrehozás** lehetőséget, majd válassza a Saját sablon létrehozása lehetőséget a **szerkesztőben.**
1. A sablonszerkesztőben illessze be a következő JSON-t egy új fiók létrehozásához, és állítsa az **AllowBlobPublicAccess** tulajdonságot **true (igaz)** vagy **false (hamis) értékre.** Ne felejtse el lecserélni a szögletes zárójelben lévő helyőrzőket a saját értékeire.

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
1. Adja meg az erőforráscsoport paraméterét, majd válassza a **Felülvizsgálat + létrehozás** gombot a sablon üzembe helyezéséhez, és hozzon létre egy tárfiókot az **allowBlobPublicAccess** tulajdonság konfigurálva.

---

> [!NOTE]
> A tárfiókok nyilvános hozzáférésének 100.000-hez való hozzáférésének mellőzését a tárfiókban üzemeltetett statikus webhelyek nem érintik. A **$web** tároló mindig nyilvánosan elérhető.
>
> A tárfiók nyilvános hozzáférési beállításának frissítése után akár 30 másodperc is eltelhet a módosítás teljes propagálása előtt.

A blobok nyilvános hozzáférésének engedélyezéséhez vagy engedélyezéséhez az Azure Storage erőforrás-szolgáltató 2019. 04. 01. vagy újabb verziója szükséges. További információ: [Azure Storage erőforrás-szolgáltató REST API.](/rest/api/storagerp/)

Az ebben a szakaszban található példák bemutatták, hogyan olvasható a tárfiók **AllowBlobPublicAccess** tulajdonsága annak megállapításához, hogy a nyilvános hozzáférés jelenleg engedélyezve van-e vagy sem. A névtelen hozzáférés megakadályozása érdekében a fiók nyilvános hozzáférési beállításainak konfigurálásával kapcsolatos további információkért lásd: Névtelen nyilvános [hozzáférés szervize.](anonymous-read-access-prevent.md#remediate-anonymous-public-access)

## <a name="set-the-public-access-level-for-a-container"></a>Tároló nyilvános hozzáférési szintjének beállítása

Ahhoz, hogy névtelen felhasználók számára olvasási hozzáférést biztosítson egy tárolóhoz és annak blobjaihoz, először engedélyezze a tárfiók nyilvános hozzáférését, majd állítsa be a tároló nyilvános hozzáférési szintjét. Ha a tárfiók nem rendelkezik nyilvános hozzáféréssel, nem konfigurálhatja a tároló nyilvános hozzáférését.

Ha a tárfiókhoz engedélyezett a nyilvános hozzáférés, a következő engedélyekkel konfigurálhat egy tárolót:

- **Nincs nyilvános olvasási hozzáférés:** A tároló és a blobok csak hitelesített kéréssel érhetők el. Ez az alapértelmezett beállítás minden új tárolóhoz.
- **Nyilvános olvasási hozzáférés csak blobok esetén:** A tárolóban lévő blobok névtelen kérésekkel olvashatók, de a tárolóadatok nem érhetők el névtelenül. A névtelen ügyfelek nem tudják enumerálni a tárolóban lévő blobokat.
- **Nyilvános olvasási hozzáférés a tárolóhoz és annak blobjaihoz:** A tároló- és blobadatok névtelen kérésekkel olvashatók, kivéve a tároló engedélybeállítását és a tároló metaadatait. Az ügyfelek névtelen kérések alapján számba tudják venni a tárolóban lévő blobokat, a tárfiókon belüli tárolókat azonban nem.

Az egyes blobok nyilvános hozzáférési szintje nem változtatható meg. A nyilvános hozzáférés szintje csak a tároló szintjén van beállítva. A tároló létrehozásakor beállíthatja a tároló nyilvános hozzáférési szintjét, vagy frissítheti egy meglévő tároló beállítását.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A nyilvános hozzáférési szint frissítéséhez kövesse az alábbi lépéseket a Azure Portal egy vagy több meglévő tárolóhoz:

1. Lépjen a tárfiók áttekintéséhez a Azure Portal.
1. A **Blob service** panelen válassza a **Tárolók lehetőséget.**
1. Válassza ki azokat a tárolókat, amelyekhez be szeretné állítani a nyilvános hozzáférési szintet.
1. A Hozzáférési **szint módosítása gombbal** jelenítse meg a nyilvános hozzáférési beállításokat.
1. Válassza ki a kívánt nyilvános hozzáférési szintet a **Nyilvános** hozzáférési szint legördülő menüből, majd kattintson az OK gombra a módosítás a kiválasztott tárolókra való alkalmazáshoz.

    ![A nyilvános hozzáférési szint portálon való beállítását bemutató képernyőkép](./media/anonymous-read-access-configure/configure-public-access-container.png)

Ha a tárfiók számára nem engedélyezett a nyilvános hozzáférés, nem lehet beállítani a tároló nyilvános hozzáférési szintjét. Ha megpróbálja beállítani a tároló nyilvános hozzáférési szintjét, láthatja, hogy a beállítás le van tiltva, mert a fiók számára nem engedélyezett a nyilvános hozzáférés.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Képernyőkép arról, hogy a tároló nyilvános hozzáférési szintjének beállítása le van tiltva, ha a nyilvános hozzáférés nem engedélyezett":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Egy vagy több tároló nyilvános hozzáférési szintjének a PowerShell használatával való frissítéséhez hívja meg a [Set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) parancsot. Engedélyezze ezt a műveletet a fiókkulcs, egy kapcsolati sztring vagy egy közös hozzáférésű jogosultság signature (SAS) megírásával. A [Tároló ACL beállítása](/rest/api/storageservices/set-container-acl) művelet, amely beállítja a tároló nyilvános hozzáférési szintjét, nem támogatja az Azure AD-val való engedélyezést. További információ: Engedélyek a [blob- és üzenetsor-adatműveletek hívásához.](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

Az alábbi példa egy letiltott nyilvános hozzáférésű tárolót hoz létre, majd frissíti a tároló nyilvános hozzáférési beállítását, hogy névtelen hozzáférést biztosít a tárolóhoz és annak blobjaihoz. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire:

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

Ha a tárfiók számára nem engedélyezett a nyilvános hozzáférés, nem lehet beállítani a tároló nyilvános hozzáférési szintjét. Ha megpróbálja beállítani a tároló nyilvános hozzáférési szintjét, az Azure Storage hibaüzenetet ad vissza, amely szerint a nyilvános hozzáférés nem engedélyezett a tárfiókon.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha frissítenie kell egy vagy több tároló nyilvános hozzáférési szintjét az Azure CLI használatával, hívja meg [az az storage container set permission parancsot.](/cli/azure/storage/container#az_storage_container_set_permission) Engedélyezze ezt a műveletet a fiókkulcs, egy kapcsolati sztring vagy egy közös hozzáférésű jogosultság signature (SAS) megírásával. A [Tároló ACL beállítása](/rest/api/storageservices/set-container-acl) művelet, amely beállítja a tároló nyilvános hozzáférési szintjét, nem támogatja az Azure AD-val való engedélyezést. További információ: Engedélyek a [blob- és üzenetsor-adatműveletek hívásához.](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

Az alábbi példa egy letiltott nyilvános hozzáférésű tárolót hoz létre, majd frissíti a tároló nyilvános hozzáférési beállítását, hogy névtelen hozzáférést biztosít a tárolóhoz és annak blobjaihoz. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire:

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

Ha a tárfiók számára nem engedélyezett a nyilvános hozzáférés, nem lehet beállítani a tároló nyilvános hozzáférési szintjét. Ha megpróbálja beállítani a tároló nyilvános hozzáférési szintjét, az Azure Storage hibaüzenetet ad vissza, amely szerint a nyilvános hozzáférés nem engedélyezett a tárfiókon.

# <a name="template"></a>[Sablon](#tab/template)

N/A.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Ellenőrizze a tárolók nyilvános hozzáférésre vonatkozó beállítását

A tárolók listázásával és a nyilvános hozzáférés beállításával ellenőrizheti, hogy egy vagy több tárfiók mely tárolói vannak nyilvános hozzáférésre konfigurálva. Ez a megközelítés praktikus megoldás, ha a tárfiókok nem tartalmaznak nagy számú tárolót, vagy ha néhány tárfiókon ellenőrzi a beállítást. Ha azonban nagy számú tárolót próbál enumerálni, a teljesítmény is eleshet.

Az alábbi példa a PowerShell használatával lekért nyilvános hozzáférési beállítást a tárfiókban található összes tárolóhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Következő lépések

- [Tárolókhoz és blobokhoz való névtelen nyilvános olvasási hozzáférés megakadályozása](anonymous-read-access-prevent.md)
- [Nyilvános tárolók és blobok névtelen elérése a .NET használatával](anonymous-read-access-client.md)
- [Az Azure Storage-hoz való hozzáférésre való jogosultságok](../common/storage-auth.md)
