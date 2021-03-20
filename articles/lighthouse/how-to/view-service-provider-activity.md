---
title: Szolgáltatói tevékenység megtekintése
description: Az ügyfelek megtekinthetik a naplózott tevékenységeket a szolgáltatók által az Azure-beli delegált erőforrás-kezelésen keresztül végrehajtott műveletek megtekintéséhez.
ms.date: 12/11/2020
ms.topic: how-to
ms.openlocfilehash: 40deca310eea2fc9618cfc83d34caadcf2b2b14d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571746"
---
# <a name="view-service-provider-activity"></a>Szolgáltatói tevékenység megtekintése

Azok az ügyfelek, akik az [Azure világítótoronyhoz](../overview.md) delegált előfizetésekkel rendelkeznek, [megtekinthetik az Azure-tevékenység naplójának](../../azure-monitor/essentials/platform-logs-overview.md) adatait az összes végrehajtott művelet megtekintéséhez. Ez teljes körű láthatóságot biztosít az ügyfeleknek a szolgáltatók számára az Azure-beli [delegált erőforrás-kezelésen](../concepts/azure-delegated-resource-management.md)keresztül, valamint az ügyfél saját Azure Active Directory (Azure ad) bérlője által végzett műveletek végrehajtásával.

> [!TIP]
> Emellett Azure Policy beépített szabályzat-definíciókat is biztosítunk, amelyekkel [korlátozható a delegálás a bérlők kezelésére](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json) , valamint a [hatókörök delegálásának naplózása egy adott bérlőre](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). További információ: [a delegálások naplózása a környezetben](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Tevékenységnapló adatainak megtekintése

A [tevékenység naplóját](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) a Azure Portal **figyelő** menüjéből tekintheti meg. Ha az eredményeket egy adott előfizetésre szeretné korlátozni, használja a szűrőket egy adott előfizetés kiválasztásához. [A tevékenységek naplózási eseményeit programozott módon is megtekintheti és lekérheti](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) .

> [!NOTE]
> A szolgáltató bérlője által biztosított felhasználók megtekinthetik a tevékenység naplójának eredményét egy ügyfél-bérlőben lévő delegált előfizetésre vonatkozóan, ha az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört (vagy egy másik beépített szerepkört is tartalmaznak, amely olvasói hozzáférést is tartalmaz), amikor az előfizetés bekerült az Azure Lighthouse szolgáltatásba.

A tevékenység naplójában látni fogja a művelet nevét és állapotát, valamint a végrehajtás dátumát és időpontját. Az oszlop **által kezdeményezett esemény** azt jeleníti meg, hogy melyik felhasználó végezte el a műveletet, függetlenül attól, hogy a szolgáltató bérlője az Azure világítótoronyon vagy az ügyfél saját bérlője által elindított felhasználó. Vegye figyelembe, hogy a felhasználó neve jelenik meg, nem pedig a bérlő vagy a felhasználó által az előfizetéshez hozzárendelt szerepkör.

A naplózott tevékenység a Azure Portal az elmúlt 90 napban érhető el. Az adatok 90 napnál hosszabb ideig történő tárolásával kapcsolatban tekintse meg [Az Azure-beli tevékenység-naplók gyűjtése és elemzése log Analytics munkaterületen](../../azure-monitor/essentials/activity-log.md)című témakört.

> [!NOTE]
> A szolgáltató felhasználói a tevékenység naplójában jelennek meg, de a felhasználók és a hozzájuk tartozó szerepkör-hozzárendelések nem jelennek meg **Access Control (iam)** vagy a szerepkör-hozzárendelési adatok API-kon keresztüli lekérdezése során.

## <a name="set-alerts-for-critical-operations"></a>Riasztások beállítása kritikus műveletekhez

Annak érdekében, hogy a szolgáltatók (vagy a saját bérlők felhasználói) által végzett kritikus műveletekkel tisztában legyenek, javasoljuk, hogy hozzon létre [műveletnapló-riasztásokat](../../azure-monitor/alerts/activity-log-alerts.md). Előfordulhat például, hogy nyomon szeretné követni az előfizetések összes felügyeleti műveletét, vagy értesítést kap, ha egy adott erőforráscsoport valamelyik virtuális gépe törölve van. Ha riasztásokat hoz létre, azok magukban foglalják a felhasználók által az ügyfél saját bérlője által végrehajtott műveleteket, valamint a bérlők felügyeletét is.

További információ: [műveletnapló riasztások létrehozása és kezelése](../../azure-monitor/alerts/alerts-activity-log.md).

## <a name="create-log-queries"></a>Naplóbeli lekérdezések létrehozása

Létrehozhat lekérdezéseket a naplózott tevékenység elemzéséhez, vagy adott elemekre koncentrálhat. Előfordulhat például, hogy egy naplózáshoz jelentést kell készítenie az előfizetésen végrehajtott összes felügyeleti szintű műveletről. Létrehozhat egy lekérdezést, amely csak ezeket a műveleteket szűri, és az eredményeket felhasználó, dátum vagy más érték szerint rendezi.

További információ: [Azure monitorban található naplók áttekintése](../../azure-monitor/logs/log-query-overview.md).

## <a name="view-user-activity-across-domains"></a>Felhasználói tevékenységek megtekintése tartományokon keresztül

Az egyes felhasználók tevékenységeit több tartományból is megtekintheti, ha a [tevékenység naplóit a tartományi](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) minta munkafüzetben használja

Az eredmények tartománynév alapján szűrhetők. További szűrőket is alkalmazhat, például kategóriát, szintet vagy erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitorról](../../azure-monitor/index.yml).
- Megtudhatja, hogyan [tekintheti meg és kezelheti](view-manage-service-providers.md) a szolgáltatói ajánlatokat a Azure Portalban.
