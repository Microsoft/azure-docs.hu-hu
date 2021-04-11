---
title: Tárolók törlése (előzetes verzió)
titleSuffix: Azure Storage
description: A tárolók (előzetes verzió) helyreállítható törlésével megvédheti adatait, így könnyebben állíthatja helyre az adatokat, amikor az alkalmazás vagy egy másik Storage-fiók felhasználója hibásan módosítja vagy törölte azokat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: af9d520bab3ff49b30672717414fbd651c915dd4
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552377"
---
# <a name="soft-delete-for-containers-preview"></a>Tárolók törlése (előzetes verzió)

A tárolók (előzetes verzió) helyreállítható törlésével megvédheti az adatait véletlenül vagy rosszindulatúan törölve. Ha a tároló-helyreállító törlés engedélyezve van egy Storage-fiókhoz, a rendszer megőrzi a törölt tárolót és annak tartalmát az Azure Storage-ban az Ön által megadott időszakra vonatkozóan. A megőrzési időtartam alatt a korábban törölt tárolók helyreállíthatók. A tároló helyreállításakor a törléskor a tárolóban lévő blobok is helyre lesznek állítva.

A blob-adatai teljes körű védelme érdekében a Microsoft a következő adatvédelmi funkciók engedélyezését javasolja:

- Tároló-helyreállítható törlés a törölt tárolók visszaállításához. A tárolók helyreállítható törlésének engedélyezéséről a tárolók helyreállítható [törlésének engedélyezése és kezelése](soft-delete-container-enable.md)című témakörben olvashat bővebben.
- BLOB verziószámozása, hogy automatikusan fenntartsa a blob korábbi verzióit. Ha a blob verziószámozása engedélyezve van, visszaállíthatja a blob egy korábbi verzióját az adatok helyreállításához, ha az hibásan van módosítva vagy törölve. A blob verziószámozásának engedélyezéséről a [blob verziószámozásának engedélyezése és kezelése](versioning-enable.md)című témakörben olvashat bővebben.
- BLOB törölje a törlést, hogy visszaállítsa a törölt blobot vagy verziót. Ha szeretné megtudni, hogyan engedélyezheti a Blobok törlését, olvassa el a következőt: Blobok eltávolításának [engedélyezése és kezelése](soft-delete-blob-enable.md).

> [!IMPORTANT]
> A tároló Soft delete jelenleg **előzetes** verzióban érhető el. Tekintse meg az Azure-szolgáltatásokra vonatkozó, a bétaverzióban, az előzetes verzióban, vagy más módon még nem közzétett, általánosan elérhetővé vált jogi feltételekhez tartozó [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

## <a name="how-container-soft-delete-works"></a>A tárolók törlésének működése

A tárolók helyreállított törlésének engedélyezésekor megadhat egy megőrzési időtartamot a törölt tárolók számára 1 és 365 nap között. Az alapértelmezett megőrzési időtartam 7 nap. A megőrzési időszak alatt helyreállíthatja a törölt tárolót a **tároló visszaállítása** művelet meghívásával.

A tárolók visszaállításakor a tároló blobok és a blob-verziók is visszaállíthatók. Ha azonban maga a tároló törlődött, csak a tárolók törlését használhatja a Blobok visszaállításához. Ha egy törölt blobot szeretne visszaállítani, ha a fölérendelt tároló nem lett törölve, a blob Soft DELETE vagy a blob verziószámozást kell használnia.

> [!WARNING]
> A tároló-helyreállító törlés csak a teljes tárolókat és azok tartalmát állíthatja vissza a törlés időpontjában. A törölt Blobok nem állíthatók vissza tárolón belül a tároló-helyreállító törlés használatával. A Microsoft azt javasolja, hogy a Blobok Soft delete és a blob verziószámozása is lehetővé tegye a tárolók egyedi blobjának a megvédését.

A következő ábra azt mutatja be, hogyan állítható vissza a törölt tároló, ha a tárolók Soft delete engedélyezve van:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Az a diagram, amely bemutatja, hogyan állítható vissza a helyreállítható tároló":::

Tároló visszaállításakor visszaállíthatja az eredeti nevét, ha a nevet nem használták fel újra. Ha az eredeti tároló nevét használták, a tárolót új névvel állíthatja vissza.

A megőrzési időszak lejárta után a tároló véglegesen törlődik az Azure Storage-ból, és nem állítható helyre. Az óra azon a megőrzési időtartamon kezdődik, amelyen a tároló törlődik. Bármikor módosíthatja a megőrzési időszakot, de ne feledje, hogy a frissített megőrzési idő csak az újonnan törölt tárolók esetében érvényes. A korábban törölt tárolók véglegesen törlődnek a tároló törlésének időpontjában érvényben lévő megőrzési időtartam alapján.

A tárolók törlésének letiltása nem eredményezi végleges törlését a korábban törölt tárolók számára. A rendszer véglegesen törli a törölt tárolókat a tároló törlésének időpontjában érvényben lévő megőrzési időszak lejártakor.

> [!IMPORTANT]
> A tároló Soft delete nem véd a Storage-fiókok törlésével. Csak az adott fiókban lévő tárolók törlésével véd. A Storage-fiók törlésével szembeni védelemhez állítson be egy zárolást a Storage-fiók erőforrásán. További információ a Storage-fiókok zárolásáról: [Azure Resource Manager zárolás alkalmazása egy Storage-fiókra](../common/lock-account-resource.md).

## <a name="about-the-preview"></a>Az előzetes verzió ismertetése

A tárolók Soft DELETE az összes Azure-régióban előzetes verzióban érhető el.

Az Azure Storage REST API 2019-12-12-es vagy újabb verziója támogatja a tárolók törlését.

### <a name="storage-account-support"></a>A Storage-fiók támogatása

A tárolók Soft Delete a következő típusú tárolási fiókok esetében érhető el:

- Általános célú v2 és v1 Storage-fiókok
- BLOB Storage-fiókok letiltása
- Blob Storage-fiókok

A Azure Data Lake Storage Gen2-vel való használatra engedélyezett hierarchikus névtérrel rendelkező Storage-fiókok is támogatottak.

### <a name="register-for-the-preview"></a>Regisztráljon az előzetes verzióra

Ha az előzetes verzióban szeretné regisztrálni a tárolót, a PowerShell vagy az Azure CLI használatával küldjön be egy kérést a szolgáltatás regisztrálásához az előfizetésében. A kérés jóváhagyása után engedélyezheti a tárolók helyreállítható törlését bármely új vagy meglévő általános célú v2-, blob Storage-vagy prémium szintű blokk blob Storage-fiókkal.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell-lel való regisztráláshoz hívja meg a [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsot.

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való regisztrációhoz hívja meg az az [Feature Register](/cli/azure/feature#az-feature-register) parancsot.

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>A regisztráció állapotának ellenõrzése

A regisztráció állapotának vizsgálatához használja a PowerShell vagy az Azure CLI-t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha ellenőriznie szeretné a regisztráció állapotát a PowerShell-lel, hívja meg a [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) parancsot.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való regisztráció állapotának megtekintéséhez hívja meg az az [Feature](/cli/azure/feature#az-feature-show) parancsot.

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Árak és számlázás

A tárolók törlésének engedélyezése nem díjköteles. A helyreállított törölt tárolókban lévő adatforgalom az aktív adatforgalommal megegyező sebességgel történik.

## <a name="next-steps"></a>Következő lépések

- [Tároló helyreállítható törlésének konfigurálása](soft-delete-container-enable.md)
- [Blobok helyreállítható törlése](soft-delete-blob-overview.md)
- [BLOB verziószámozása](versioning-overview.md)
