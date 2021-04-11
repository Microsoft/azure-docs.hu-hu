---
title: Az Azure Defender tároláshoz konfigurálása
titleSuffix: Azure Storage
description: Konfigurálja az Azure Defender for Storage-t a fiókban észlelt rendellenességek észlelése érdekében, és értesítse a fiókjához való hozzáférésre potenciálisan ártalmas kísérleteket.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e2f044ab267365885260b031638572846184bc83
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063185"
---
# <a name="configure-azure-defender-for-storage"></a>Az Azure Defender tároláshoz konfigurálása

Az Azure Defender for Storage a biztonsági intelligencia újabb rétegét kínálja, amely észleli a tárfiókok elérésére vagy kihasználására tett szokatlan és esetleg kártékony próbálkozásokat. Ez a védelmi réteg biztonsági szakértelem vagy fejlett biztonsági figyelőrendszerek üzemeltetése nélkül is lehetővé teszi Önnek, hogy reagáljon a fenyegetésekre.

A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. Ezek a biztonsági riasztások integrálva vannak [Azure Security Centerekkel](https://azure.microsoft.com/services/security-center/), és e-mailben is elküldjük az előfizetés-rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálásával és javításával kapcsolatos ajánlásokkal együtt.

A szolgáltatás betölti az olvasási, írási és törlési kérelmek erőforrás-naplóit a blob Storage-ba, és Azure Files a veszélyforrások észleléséhez. Az Azure Defender által indított riasztások vizsgálatához Storage Analytics naplózás használatával tekintheti meg a kapcsolódó tárolási tevékenységeket. További információ: a **naplózás konfigurálása** a [Storage-fiók figyelése a Azure Portalban](./manage-storage-analytics-logs.md#configure-logging).

## <a name="availability"></a>Rendelkezésre állás

Az Azure Defender for Storage jelenleg a Blob Storage, az Azure Files és Azure Data Lake Storage Gen2 szolgáltatásokhoz érhető el. Azure Defendert támogató fióktípusok többek között az általános célú v2, a blokkblob és a Blob Storage-fiókok. Az Azure Defender for Storage elérhető minden nyilvános felhőben és az USA-beli kormányzati felhőkben, de nem érhető el más szuverén vagy Azure Government-felhőrégiókban.

Azon hierarchikus névtereket tartalmazó fiókok, amelyekben a Data Lake Storage engedélyezett, egyaránt támogatják az Azure Blob Storage API-kat és a Data Lake Storage API-kat. Az Azure-fájlmegosztások támogatják az SMB protokollon keresztül végbemenő tranzakciókat.

A díjszabással kapcsolatos részletekért, beleértve az ingyenes 30 napos próbaverziót is, tekintse meg a [Azure Security Center díjszabási oldalát](https://azure.microsoft.com/pricing/details/security-center/).

Az alábbi lista összefoglalja az Azure Defender for Storage szolgáltatás elérhetőségét:

- Kiadás állapota:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (általánosan elérhető)
  - [Azure Files](../files/storage-files-introduction.md) (általánosan elérhető)
  - Azure Data Lake Storage Gen2 (általánosan elérhető)
- Felhők<br>
    ✔ Kereskedelmi felhők<br>
    ✔ US Gov<br>
    ✘ China gov, egyéb gov

## <a name="set-up-azure-defender"></a>Az Azure Defender beállítása

Az Azure Defendert a következő szakaszokban ismertetett módon állíthatja be a Storage szolgáltatáshoz.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Az Azure Defender be van építve Azure Security Centerba. Ha engedélyezi az Azure Defender használatát az előfizetésében, az Azure Defender for Azure Storage szolgáltatás automatikusan engedélyezve lesz az összes Storage-fiókhoz. Az Azure Defender a következő módon engedélyezhető vagy tiltható le a Storage-fiókok számára egy adott előfizetésben:

1. A [Azure Portal](https://portal.azure.com) **Azure Security Center** elindítása.
1. A főmenü **felügyelet** területén válassza a **díjszabás & beállítások** lehetőséget.
1. Válassza ki azt az előfizetést, amelyhez engedélyezni vagy letiltani szeretné az Azure Defendert.
1. Válassza az **Azure Defender** lehetőséget, hogy engedélyezze az Azure Defendert az előfizetéshez.
1. Az **Azure Defender-csomag kiválasztása erőforrás típusa** területen keresse meg a **tárolási** sort, és válassza az **engedélyezve** lehetőséget a **terv** oszlopban.
1. Mentse a módosításokat.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Képernyőfelvétel: az Azure Defender engedélyezése a Storage-ban Security Center":::

Az Azure Defender mostantól engedélyezve van az előfizetésben található összes Storage-fiókhoz.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Indítsa el a [Azure Portal](https://portal.azure.com/).
1. Nyissa meg a tárfiókot. A **Beállítások** területen válassza a **Speciális biztonság** lehetőséget.
1. Válassza az **Azure Defender for Storage bekapcsolása** lehetőséget.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Az Azure Defender Azure Storage-fiókhoz való engedélyezését bemutató képernyőkép":::

Az Azure Defender mostantól engedélyezve van ehhez a Storage-fiókhoz.

### <a name="template"></a>[Sablon](#tab/template)

Egy Azure Resource Manager sablon használatával üzembe helyezhet egy Azure Storage-fiókot az Azure Defenderrel. További információ: Storage- [fiók komplex veszélyforrások elleni védelemmel](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Egy Azure Policy használatával engedélyezheti az Azure Defender számára a Storage-fiókokat egy adott előfizetés vagy erőforráscsoport alatt.

1. Indítsa el az Azure **Policy-fogalommeghatározások** lapot.
1. Keresse meg az **Azure Defender üzembe helyezése a Storage-fiókokra** vonatkozó házirendet.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Házirend alkalmazása az Azure Defender Storage-fiókok engedélyezéséhez":::

1. Válasszon ki egy Azure-előfizetést vagy erőforráscsoportot.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Válassza ki az előfizetést vagy az erőforráscsoportot a házirend hatóköréhez ":::

1. Rendelje hozzá a szabályzatot.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Házirend kiosztása az Azure Defender tárterületének engedélyezéséhez":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha engedélyezni szeretné az Azure Defender számára a PowerShell-lel rendelkező Storage-fiókot, először telepítse az az [. Security](https://www.powershellgallery.com/packages/Az.Security) modult. Ezután hívja meg az [enable-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection) parancsot. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

A PowerShell-lel rendelkező Storage-fiókhoz tartozó Azure Defender-beállítás megadásához hívja meg a [Get-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection) parancsot. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha engedélyezni szeretné az Azure Defender számára a Storage-fiókot az Azure CLI-vel, hívja meg az az [Security ATP Storage Update](/cli/azure/security/atp/storage#az_security_atp_storage_update) parancsot. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

Ha egy Storage-fiók Azure Defender-beállításait az Azure CLI-vel szeretné megtekinteni, hívja meg az az [Security ATP Storage show](/cli/azure/security/atp/storage#az_security_atp_storage_show) parancsot. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>A biztonsági rendellenességek bemutatása

Ha a tárolási tevékenységekben rendellenességek lépnek fel, e-mailben kap értesítést a gyanús biztonsági eseményről. Az esemény részletei a következők:

- A rendellenesség természete
- A tárfiók neve
- Esemény időpontja
- Tárolás típusa
- Lehetséges okok
- Vizsgálati lépések
- Az elhárítás lépései

Az e-mail tartalmazza továbbá a lehetséges okokat, valamint a lehetséges fenyegetések kivizsgálásához és enyhítéséhez ajánlott műveleteket is.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Azure Defender tárolási riasztási e-mail":::

Az aktuális biztonsági riasztásokat a Azure Security Center [biztonsági riasztások csempéről](../../security-center/security-center-managing-and-responding-alerts.md)tekintheti meg és kezelheti. Ha egy adott riasztásra kattint, a rendszer részletesen ismerteti az aktuális fenyegetés kivizsgálásával és a jövőbeli fenyegetésekkel kapcsolatos műveleteket.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Azure Defender tárolási riasztás":::

## <a name="security-alerts"></a>Biztonsági riasztások

A riasztásokat szokatlan és potenciálisan ártalmas kísérletek generálják a Storage-fiókok eléréséhez vagy kiaknázásához. Az Azure Storage-hoz kapcsolódó riasztások listáját az [Azure Storage-riasztások](../../security-center/alerts-reference.md#alerts-azurestorage)című témakörben tekintheti meg.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Defender tároláshoz bemutatása](../../security-center/defender-for-storage-introduction.md)
- [Azure Security Center](../../security-center/security-center-introduction.md)
- [Naplók az Azure Storage-fiókokban](/rest/api/storageservices/About-Storage-Analytics-Logging)
