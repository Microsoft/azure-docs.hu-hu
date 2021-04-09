---
title: Az Azure Defender tároláshoz konfigurálása
titleSuffix: Azure Storage
description: Konfigurálja az Azure Defender for Storage-t a fiókban észlelt rendellenességek észlelése érdekében, és értesítse a fiókjához való hozzáférésre potenciálisan ártalmas kísérleteket.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: cdfc54b1eca3b07202148b7099884a04f35939ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698144"
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

Amikor előfizet a Azure Security Center Standard szintjére, az Azure Defender automatikusan beállítja az összes Storage-fiókját. Az Azure Defender a következő módon engedélyezhető vagy tiltható le a Storage-fiókok számára egy adott előfizetésben:

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

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Használjon REST API-parancsokat egy adott Storage-fiók Azure Defender-beállításának létrehozásához, frissítéséhez vagy beszerzéséhez.

- [Komplex veszélyforrások elleni védelem – létrehozás](/rest/api/securitycenter/advancedthreatprotection/create)
- [Komplex veszélyforrások elleni védelem – Get](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Használja a következő PowerShell-parancsmagokat:

- [Komplex veszélyforrások elleni védelem engedélyezése](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Komplex veszélyforrások elleni védelem](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [A komplex veszélyforrások elleni védelem letiltása](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

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

- További információ az [Azure Storage-fiókok naplóiról](/rest/api/storageservices/About-Storage-Analytics-Logging)
- További információ a [Azure Security Center](../../security-center/security-center-introduction.md)