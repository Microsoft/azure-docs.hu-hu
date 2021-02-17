---
title: Azure Defender Resource Managerhez – az előnyök és funkciók
description: Ismerje meg a Resource Managerhez készült Azure Defender előnyeit és funkcióit
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 922189909da21aac65d1e2e898df58598868b929
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590464"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>A Resource Managerhez készült Azure Defender bemutatása

[Azure Resource Manager](../azure-resource-manager/management/overview.md) az Azure üzembe helyezési és kezelési szolgáltatása. Egy olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-fiókban lévő erőforrások létrehozását, frissítését és törlését. Az üzembe helyezés után az erőforrások biztonságossá tételéhez és rendszerezéséhez olyan felügyeleti funkciókat használhat, mint a hozzáférés-vezérlés, a zárolások és a címkék.

A felhőalapú felügyeleti réteg az összes felhőalapú erőforráshoz kapcsolódó kulcsfontosságú szolgáltatás. Emiatt a támadók számára is lehetséges célpont. Ezért azt javasoljuk, hogy a biztonsági műveleti csapatok szorosan figyelik az erőforrás-kezelési réteget. 

Az Azure Defender for Resource Manager automatikusan figyeli a szervezet erőforrás-kezelési műveleteit, függetlenül attól, hogy azok a Azure Portalon, az Azure REST API-kon, az Azure CLI-n vagy más Azure programozott ügyfeleken keresztül történnek-e. Az Azure Defender speciális biztonsági elemzéseket futtat a fenyegetések észleléséhez és a gyanús tevékenységekkel kapcsolatos riasztásokhoz.

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Előnézet<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Árképzési|A **Resource Managerhez készült Azure Defender** számlázása [a díjszabási oldalon](security-center-pricing.md) látható.|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>Milyen előnyökkel jár az Azure Defender for Resource Manager?

A Resource Managerhez készült Azure Defender a következő problémákkal véd:

- **Gyanús erőforrás-kezelési műveletek**, például a gyanús IP-címekből származó műveletek, a virtuálisgép-bővítményekben futó antimalware és gyanús parancsfájlok letiltása
- Felhasználási **segédanyagok, például a PowerZure használata**
- **Oldalirányú mozgás** az Azure felügyeleti rétegből az Azure-erőforrások adatsíkjával

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Azure Resource Manager áttekintő diagram":::

Az Azure Defender for Resource Manager által biztosított riasztások teljes listája a [riasztások hivatkozási oldalán](alerts-reference.md#alerts-resourcemanager)található.


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>Riasztások vizsgálata a Resource Managerhez készült Azure Defenderből

A Resource Managerhez készült Azure Defender biztonsági riasztásai a figyelési Azure Resource Manager műveletek alapján észlelt fenyegetéseken alapulnak. Az Azure Defender a Azure Resource Manager belső naplózási forrásait, valamint az Azure-beli platform-naplót használja az Azure-ban, amely betekintést nyújt az előfizetési szintű eseményekre.

További információ az [Azure Activity log](../azure-monitor/essentials/activity-log.md)-ről.

Az Azure Defender for Resource Manager biztonsági értesítéseinek vizsgálata:

1. Nyissa meg az Azure-tevékenység naplóját.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Az Azure-tevékenység naplójának megnyitása":::

1. Az események szűrése a következőre:
    - A riasztásban említett előfizetés
    - Az észlelt tevékenység időkerete
    - A kapcsolódó felhasználói fiók (ha van ilyen)

1. Keresse meg a gyanús tevékenységeket.

> [!TIP]
> A jobb és gazdagabb vizsgálati élmény érdekében továbbíthatja Azure-tevékenységeit az Azure Sentinel szolgáltatásba az [adatok összekapcsolása az Azure](../sentinel/connect-azure-activity.md)-ban című témakörben leírtak szerint.



## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan használható az Azure Defender a Resource Managerhez. Kapcsolódó anyagok esetében tekintse meg a következő cikket: 

- Előfordulhat, hogy a biztonsági riasztásokat a Security Center vagy a különböző biztonsági termékekből Security Center fogadja. Ha az összes riasztást az Azure Sentinelbe, harmadik féltől származó SIEM-re vagy más külső eszközre kívánja exportálni, kövesse a [riasztások a Siem](continuous-export.md)-ben való exportálásának utasításait.

- > [!div class="nextstepaction"]
    > [Az Azure Defender engedélyezése](security-center-pricing.md#enable-azure-defender)