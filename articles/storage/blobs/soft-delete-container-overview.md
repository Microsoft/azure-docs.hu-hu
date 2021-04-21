---
title: Tárolók soft delete (előzetes verzió)
titleSuffix: Azure Storage
description: A tárolók helyreállítható törlése (előzetes verzió) védi az adatokat, így könnyebben helyreállíthatja az adatokat, ha azokat egy alkalmazás vagy egy másik tárfiók-felhasználó hibásan módosította vagy törölte.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 2efd673d26231e83d820f7971a740d06e9b2a1d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785414"
---
# <a name="soft-delete-for-containers-preview"></a>Tárolók soft delete (előzetes verzió)

A tárolók (előzetes verzió) által védett törlés megvédi az adatokat a véletlen vagy rosszindulatú törléstől. Ha a tárolókhoz engedélyezve van a tárfiókok helyreállítása, a törölt tárolók és tartalmaik az Ön által megadott időtartamra megmaradnak az Azure Storage-ban. A megőrzési időtartam alatt a korábban törölt tárolók helyreállíthatók. A tároló helyreállításakor a törléskor a tárolóban lévő blobok is helyre lesznek állítva.

A blobadatok végpontok elleni védelme érdekében a Microsoft a következő adatvédelmi funkciók engedélyezését javasolja:

- Tároló helyreállítható törlése a törölt tároló visszaállításához. A tárolókhoz való soft delete engedélyezéséről lásd: Tárolókhoz való soft [delete engedélyezése és kezelése.](soft-delete-container-enable.md)
- Blob verziószámozása a blobok korábbi verzióinak automatikus karbantartásához. Ha a blobok verziószámozása engedélyezve van, visszaállíthatja egy blob korábbi verzióját, hogy helyreállítsa az adatokat, ha azokat hibásan módosították vagy törölték. A blobok verziószámozásának engedélyezéséről a blobok verziószámozásának [engedélyezését és kezelését lásd:](versioning-enable.md).
- Blob helyreállítható törlése a törölt blobok vagy verziók visszaállításához. Ha szeretné megtudni, hogyan engedélyezheti a blobok soft delete parancsát, tekintse meg a blobok soft [delete engedélyezését](soft-delete-blob-enable.md)és kezelését.

> [!IMPORTANT]
> A tárolók végleges törlése jelenleg előzetes verzióban **érhető el.** A [bétaverzióban,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verzióban vagy más, általánosan elérhető Azure-funkciókra vonatkozó jogi feltételekért tekintse meg a kiegészítő használati feltételeket a Microsoft Azure előzetes verziókhoz.

## <a name="how-container-soft-delete-works"></a>A tárolókhoz való soft delete működése

Ha engedélyezi a tárolók soft delete funkcióját, megadhat egy 1 és 365 nap közötti megőrzési időszakot a törölt tárolókhoz. Az alapértelmezett megőrzési időtartam 7 nap. A megőrzési időtartam alatt a törölt tárolók a Tároló visszaállítása művelet hívásával **állíthatók** helyre.

A tárolók visszaállításakor a tároló blobja és a blobverziók is visszaállnak. A tároló helyreállítható törlésével azonban csak akkor lehet blobokat visszaállítani, ha maga a tároló lett törölve. Törölt blob visszaállításához, ha annak szülőtárolója még nem lett törölve, a blob helyreállítható törlését vagy a blob verziószámozását kell használnia.

> [!WARNING]
> A tároló helyreállítható törlése csak teljes tárolókat és azok tartalmát tudja visszaállítani a törléskor. A tárolókban törölt blobok nem állíthatók vissza a tároló helyreállítható törlésével. A Microsoft javasolja továbbá a blobok blobok soft delete és versioning (blobok verziószámozásának) engedélyezését a tárolóban lévő egyes blobok védelme érdekében.

Az alábbi ábra bemutatja, hogyan állítható vissza egy törölt tároló, ha a tároló helyreállítható törlése engedélyezve van:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="A helyreállítható módon törölt tároló visszaállítását bemutató ábra":::

Tároló visszaállításakor visszaállíthatja azt az eredeti nevére, ha a nevet nem használtuk újra. Ha az eredeti tárolónevet használta, akkor visszaállíthatja a tárolót egy új névvel.

A megőrzési időszak lejárta után a tároló véglegesen törlődik az Azure Storage-ból, és nem állítható helyre. Az óra a megőrzési időszaktól indul a tároló törlésekor. A megőrzési időtartam bármikor megváltoztatható, de ne feledje, hogy a frissített megőrzési időszak csak az újonnan törölt tárolókra vonatkozik. A korábban törölt tárolók véglegesen törlődnek azon megőrzési időtartam alapján, amely a tároló törlésekor volt érvényben.

A tárolók ideiglenes törlésének letiltása nem eredményezi a korábban törölt tárolók végleges törlését. A nem véglegesen törölt tárolók véglegesen törlődnek a tároló törlésekor érvényes megőrzési időszak lejártakor.

> [!IMPORTANT]
> A tárolók nem védettek a tárfiókok törlésével szemben. Csak az abban a fiókban található tárolók törlése ellen nyújt védelmet. A tárfiók törlés elleni védelméhez konfigurálja a tárfiók erőforrásának zárolását. A tárfiókok zárolásának további információiért lásd: [Azure Resource Manager zárolás alkalmazása tárfiókra.](../common/lock-account-resource.md)

## <a name="about-the-preview"></a>Az előzetes verzióról

A tárolók előzetes verziója minden Azure-régióban elérhető.

Az Azure Storage-fiók 2019-12-12-es vagy újabb verziója REST API támogatja a tárolók REST API törlését.

### <a name="storage-account-support"></a>Tárfiókok támogatása

A tárolók soft delete szolgáltatása a következő tárfióktípusokhoz érhető el:

- Általános célú v2- és v1-tárfiókok
- Blokkblob-tárfiókok
- Blob Storage-fiókok

Azok a tárfiókok is támogatottak, amelyeken engedélyezve van a Azure Data Lake Storage Gen2 névtér.

### <a name="register-for-the-preview"></a>Regisztráció az előzetes verzióra

Ha regisztrálni szeretne az előzetes verzióban a tárolók soft delete szolgáltatására, a PowerShell vagy az Azure CLI használatával küldjön el egy kérést a funkció előfizetésben való regisztrálására. A kérelem jóváhagyása után engedélyezheti a tárolókhoz való soft delete parancsot bármilyen új vagy meglévő általános célú v2-vel, Blob Storage-fiókkal vagy prémium szintű blokkblob-tárfiókkal.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell-regisztrációhoz hívja meg [a Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsot.

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-regisztrációhoz hívja az [az feature register](/cli/azure/feature#az_feature_register) parancsot.

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>A regisztráció állapotának ellenőrzése

A regisztráció állapotának ellenőrzéshez használja a PowerShellt vagy az Azure CLI-t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell-regisztráció állapotának ellenőrzéshez hívja meg a [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) parancsot.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-regisztráció állapotának ellenőrzéshez hívja meg az [az feature](/cli/azure/feature#az_feature_show) parancsot.

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Árak és számlázás

A tárolók nem törölhetők díjmentesen. A softdeleted tárolókban lévő adatok számlázása ugyanolyan sebességgel történik, mint az aktív adatok.

## <a name="next-steps"></a>Következő lépések

- [Tárolók végleges törlésének konfigurálása](soft-delete-container-enable.md)
- [Blobok helyreállítható törlése](soft-delete-blob-overview.md)
- [Blobok verziószámozása](versioning-overview.md)
