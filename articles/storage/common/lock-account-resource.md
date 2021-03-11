---
title: Azure Resource Manager zárolásának alkalmazása Storage-fiókra
titleSuffix: Azure Storage
description: Megtudhatja, hogyan alkalmazhat egy Azure Resource Manager zárolást egy Storage-fiókra.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9d80c0b8d4d913322c47d1ad278d6dbc033d2409
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620061"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>Azure Resource Manager zárolásának alkalmazása Storage-fiókra

A Microsoft az összes Storage-fiók zárolását javasolja egy Azure Resource Manager zárolással, hogy elkerülje a Storage-fiók véletlen vagy rosszindulatú törlését. A Azure Resource Manager erőforrás-zárolások két típusa létezik:

- A **CannotDelete** zárolás megakadályozza a felhasználók számára a Storage-fiókok törlését, de lehetővé teszi a konfiguráció olvasását és módosítását.
- Egy **írásvédett** zárolás megakadályozza a felhasználókat a Storage-fiókok törlésében vagy a konfiguráció módosításában, de lehetővé teszi a konfiguráció olvasását.

A Azure Resource Manager zárolásokkal kapcsolatos további információkért lásd: [erőforrások zárolása a módosítások megelőzése érdekében](../../azure-resource-manager/management/lock-resources.md).

> [!IMPORTANT]
> A Storage-fiók zárolása nem biztosítja a fiókban lévő adatok frissítését vagy törlését.

## <a name="configure-an-azure-resource-manager-lock"></a>Azure Resource Manager zárolásának konfigurálása

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Az alábbi lépéseket követve konfigurálhatja a Storage-fiókok zárolását a Azure Portal használatával:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. A **Beállítások** szakaszban válassza a **zárolások** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget.
1. Adja meg az erőforrás-zárolás nevét, és adja meg a zárolás típusát. Ha szükséges, vegyen fel egy megjegyzést a zárolásról.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="Képernyőfelvétel a Storage-fiókok CannotDelete zárolásával történő zárolásáról":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha a PowerShell használatával szeretne zárolást beállítani egy Storage-fiókban, először telepítse az az [PowerShell-modult](https://www.powershellgallery.com/packages/Az). Ezután hívja meg a [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) parancsot, és adja meg a létrehozni kívánt zárolás típusát, ahogy az az alábbi példában is látható:

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel rendelkező Storage-fiókok zárolásának konfigurálásához hívja meg az az [Lock Create](/cli/azure/lock#az_lock_create) parancsot, és adja meg a létrehozni kívánt zárolás típusát, az alábbi példában látható módon:

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>Az adatműveletek engedélyezése, ha írásvédett zárolás van érvényben

Ha **írásvédett** zárolást alkalmaz egy Storage-fiókra, a [kulcsok listázása](/rest/api/storagerp/storageaccounts/listkeys) művelet le van tiltva a Storage-fiók esetében. A **kulcsok listázása** művelet egy https post művelet, és az összes post művelet meg lesz akadályozva, ha **írásvédett** zárolás van konfigurálva a fiókhoz. A **kulcsok listázása** művelet a fiók-hozzáférési kulcsokat adja vissza, amelyek ezután a Storage-fiókban lévő adatok olvasására és írására használhatók.

Ha az ügyfél a fiók hozzáférési kulcsainak birtokában van, amikor a zárolást a Storage-fiókra alkalmazza, akkor az ügyfél továbbra is használhatja a kulcsokat az adateléréshez. A kulcsokhoz nem hozzáférő ügyfeleknek azonban Azure Active Directory (Azure AD) hitelesítő adatokat kell használniuk a blob-vagy üzenetsor-adatok eléréséhez a Storage-fiókban.

A Azure Portal felhasználói a **írásvédett** zárolás alkalmazása esetén érinthetik, ha korábban már hozzáfértek blob-vagy üzenetsor-információhoz a portálon a fiók hozzáférési kulcsaival. A zárolás alkalmazása után a portál felhasználóknak Azure AD-beli hitelesítő adatokkal kell rendelkezniük a blob-vagy üzenetsor-adatok eléréséhez a portálon. Ehhez a felhasználónak legalább két RBAC szerepkörrel kell rendelkeznie: a Azure Resource Manager olvasó szerepkört legalább egy Azure Storage-adatelérési szerepkörhöz. További információkért lásd a következő cikkek egyikét:

- [Válassza ki, hogyan engedélyezze a blob-adathozzáférést a Azure Portal](../blobs/authorize-data-operations-portal.md)
- [Válassza ki, hogyan engedélyezze a hozzáférést az üzenetsor-információhoz a Azure Portal](../queues/authorize-data-operations-portal.md)

Előfordulhat, hogy a Azure Files vagy a Table serviceban lévő adatok elérhetetlenné válnak azon ügyfelek számára, akik korábban már hozzáfértek a fiók kulcsaihoz. Ajánlott eljárás: Ha **írásvédett** zárolást kell alkalmaznia egy Storage-fiókra, akkor helyezze át a Azure filest, és Table Service munkaterheléseket egy olyan Storage-fiókba, amely nem zárolt **írásvédett** zárolással.

## <a name="next-steps"></a>Következő lépések

[Erőforrások zárolása a módosítások megakadályozása érdekében](../../azure-resource-manager/management/lock-resources.md)
