---
title: A sík és az adatsík műveletek vezérlése
description: Leírja a vezérlési sík és az adatsík műveletek közötti különbséget. A vezérlési sík műveleteit Azure Resource Manager kezeli. Az adatsík-műveleteket egy szolgáltatás kezeli.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 76304c81a1af1eef87d12cfd4130867851a61d28
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544094"
---
# <a name="azure-control-plane-and-data-plane"></a>Azure-vezérlési sík és adatsík

Az Azure-műveletek két kategóriára oszthatók: vezérlő síkja és adatsík. Ez a cikk a két típusú művelet közötti különbségeket ismerteti.

Az előfizetése erőforrásainak kezeléséhez használja a vezérlési síkot. Az adatsíkon az erőforrástípus példányai által közzétett képességeket használhatja.

Például:

* A virtuális gépet a vezérlési síkon kell létrehoznia. A virtuális gép létrehozása után az adatsík műveletein keresztül kommunikál, például RDP protokoll (RDP).

* A vezérlési síkon létre kell hoznia egy Storage-fiókot. Az adatsíkon a Storage-fiókban lévő információk olvasására és írására használható.

* A Cosmos-adatbázist a vezérlési síkon kell létrehoznia. A Cosmos adatbázisban lévő adatlekérdezéshez használja az adatsíkon.

## <a name="control-plane"></a>Vezérlősík

A rendszer a vezérlési sík műveleteire vonatkozó összes kérelmet elküldi a Azure Resource Manager URL-címre. Ez az URL-cím az Azure-környezettől függ.

* Az Azure Global esetében az URL-cím a következő: `https://management.azure.com` .
* Azure Government esetén az URL-cím: `https://management.usgovcloudapi.net/` .
* Az Azure Germany esetében az URL-cím a következő: `https://management.microsoftazure.de/` .
* Microsoft Azure China 21Vianet esetében az URL-cím a következő: `https://management.chinacloudapi.cn` .

Az Azure Resource Manager URL-címet használó műveletek felderítéséhez tekintse meg az [Azure REST API](/rest/api/azure/). A MySql-hez készült [create vagy Update művelet](/rest/api/mysql/databases/createorupdate) például egy vezérlési sík művelet, mert a kérelem URL-címe:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure Resource Manager kezeli az összes vezérlési sík kérelmét. A szolgáltatás automatikusan alkalmazza az Ön által megvalósított Azure-szolgáltatásokat az erőforrások kezeléséhez, például:

* [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [Felügyeleti zárolások](lock-resources.md)
* [Tevékenységnaplók](view-activity-logs.md)

A kérelem hitelesítése után Azure Resource Manager elküldi azt az erőforrás-szolgáltatónak, amely befejezi a műveletet.

A vezérlő síkja két forgatókönyvet tartalmaz a kérelmek kezeléséhez – "zöld mező" és "barna mező". A zöld mező új erőforrásokra hivatkozik. A Brown mező a meglévő erőforrásokra hivatkozik. Az erőforrások telepítése során Azure Resource Manager megismerheti, hogy mikor kell új erőforrásokat létrehoznia, és mikor kell frissíteni a meglévő erőforrásokat. Nem kell aggódnia, hogy azonos erőforrások jönnek létre.

## <a name="data-plane"></a>Adatsík

Az adatsík-műveletekre vonatkozó kéréseket a rendszer a példányra jellemző végpontra küldi. Például az [észlelési nyelv művelet](/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection) Cognitive Services egy adatsík-művelet, mert a kérelem URL-címe:

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

Az adatsík-műveletek nem korlátozódnak REST APIra. További hitelesítő adatokat igényelhetnek, például egy virtuális gépre vagy adatbázis-kiszolgálóra való bejelentkezést.

Előfordulhat, hogy a felügyeletet és a szabályozást kikényszerítő funkciók nem alkalmazhatók az adatsík műveleteire. Meg kell fontolnia, hogy a felhasználók milyen módon kommunikálnak a megoldásaival. Például egy olyan zárolás, amely megakadályozza, hogy a felhasználók egy adatbázist töröljenek, nem akadályozza meg, hogy a felhasználók lekérdezéseken keresztül töröljék az adatfájlokat.

Egyes szabályzatok segítségével szabályozhatja az adatsík-műveleteket. További információ: [Azure Policy erőforrás-szolgáltatói módok (előzetes verzió)](../../governance/policy/concepts/definition-structure.md#resource-provider-modes).

## <a name="next-steps"></a>Következő lépések

* A Azure Resource Manager áttekintése: [Mi az Azure Resource Manager?](overview.md)

* Ha többet szeretne megtudni a szabályzat-definíciók új erőforrásokra és meglévő erőforrásokra gyakorolt hatásáról, tekintse meg [az új Azure Policy definíció hatásának kiértékelését](../../governance/policy/concepts/evaluate-impact.md)ismertető témakört.
