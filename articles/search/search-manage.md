---
title: A szolgáltatás felügyelete a portálon
titleSuffix: Azure Cognitive Search
description: Felügyelheti az Azure Cognitive Search szolgáltatást, amely egy üzemeltetett felhőalapú keresési szolgáltatás a Microsoft Azureon a Azure Portal használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 98e0137c8e48696737cd5d8d1fd4d3de925b9f7f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579795"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Szolgáltatás-felügyelet az Azure Cognitive Search a Azure Portal

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Portál](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Az Azure Cognitive Search egy teljes körűen felügyelt, felhőalapú keresési szolgáltatás, amellyel gazdag keresési élményt hozhat létre egyéni alkalmazásokba. Ez a cikk a már létrehozott keresési szolgáltatás [Azure Portal](https://portal.azure.com) elvégezhető felügyeleti feladatokat ismerteti. A portál lehetővé teszi, hogy a szolgáltatással kapcsolatos összes [felügyeleti feladatot](#management-tasks) , valamint a tartalomkezelést és a feltárást is végrehajtsa. Így a portál széles spektrumú hozzáférést biztosít a keresési szolgáltatás műveleteinek valamennyi aspektusához.

Minden keresési szolgáltatás önálló erőforrásként van kezelve. Az alábbi képen egy "demo-Search-SVC" nevű egyetlen ingyenes keresési szolgáltatás portáljának oldalai láthatók. Bár lehet, hogy megszokta, hogy Azure PowerShell vagy Azure CLI-t használ a Service Management szolgáltatáshoz, érdemes megismerni a portál oldalain elérhető eszközöket és képességeket. Néhány feladat egyszerűen és gyorsabban végezhető el a portálon, mint a kód. 

## <a name="overview-home-page"></a>Áttekintés (Kezdőlap) oldal

Az Áttekintés lap az egyes szolgáltatások "Kezdőlap" lapja. Az alábbiakban a képernyőn megjelenő területek piros mezőben jelennek meg a gyakran használt feladatok, eszközök és csempék, különösen akkor, ha új a szolgáltatáshoz.

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="A keresési szolgáltatások portáljának oldalai" border="true":::

| Terület | Leírás |
|------|-------------|
| 1  | Az **Essentials (alapvető** szolgáltatások) szakasz megjeleníti a szolgáltatás tulajdonságait, beleértve a kapcsolatok beállításakor használt végpontot is. Emellett a rétegek, a replika és a partíciók számát is megjeleníti egy pillantással. |
| 2 | A lap tetején számos parancs található az interaktív eszközök meghívásához vagy a szolgáltatás kezeléséhez. Az [importálási](search-get-started-portal.md) és a [keresési kezelő](search-explorer.md) is általában a prototípus-készítéshez és a feltáráshoz használatos. |
| 3 | Az **Essentials** szakasz alatt több füles oldal található, amelyekkel gyorsan elérhetők a használati statisztikák, a szolgáltatási állapot metrikái, valamint a szolgáltatáshoz tartozó összes meglévő index, indexelő, adatforrás és szakértelmével. Ha egy indexet vagy egy másik objektumot választ, további lapok válnak elérhetővé az objektumok összeállításának, beállításainak és állapotának megjelenítéséhez (ha van ilyen). |
| 4 | A bal oldalon olyan hivatkozásokat érhet el, amelyek további oldalakat nyitnak meg a kapcsolatokon használt API-kulcsok eléréséhez, a szolgáltatás konfigurálásához, a biztonság konfigurálásához, a figyelési műveletekhez, a feladatok automatizálásához és a támogatáshoz. |

### <a name="read-only-service-properties"></a>Írásvédett szolgáltatás tulajdonságai

A keresési szolgáltatás több aspektusa is meg van határozva, ha a szolgáltatás kiépítve van, és nem módosítható:

* Szolgáltatás neve (a keresési szolgáltatás nem nevezhető át)
* Szolgáltatás helye (nem helyezhető át könnyen egy érintetlen keresési szolgáltatás egy másik régióba. Bár van egy sablon, a tartalom áthelyezése manuális folyamat.
* Szolgáltatási szintet (nem lehet átváltani S1-ről S2-re, például új szolgáltatás létrehozása nélkül)

## <a name="management-tasks"></a>Felügyeleti feladatok

A szolgáltatás felügyeletét könnyű megtervezni, és gyakran a szolgáltatáshoz képest elvégezhető feladatok határozzák meg:

* A [kapacitás módosítása](search-capacity-planning.md) replikák és partíciók hozzáadásával vagy eltávolításával
* Rendszergazdai és lekérdezési műveletekhez használt [API-kulcsok kezelése](search-security-api-keys.md)
* [Rendszergazdai műveletekhez való hozzáférés szabályozása](search-security-rbac.md) Szerepköralapú biztonság használatával
* [IP-tűzfalszabályok konfigurálása](service-configure-firewall.md) a hozzáférés IP-cím szerinti korlátozásához
* [Privát végpont konfigurálása](service-create-private-endpoint.md) az Azure Private link és egy privát virtuális hálózat használatával
* A [szolgáltatás állapotának figyelése](search-monitor-usage.md): tárterület, lekérdezési kötetek és késés

A szolgáltatásban létrehozott összes objektum enumerálása is lehetséges: indexek, indexelő, adatforrások, szakértelmével stb. A portál áttekintő lapja a szolgáltatásban található összes tartalmat megjeleníti. A keresési szolgáltatás műveleteinek túlnyomó többsége tartalommal kapcsolatos.

A portálon végrehajtott felügyeleti feladatok a [felügyeleti REST API](/rest/api/searchmanagement/)-kon keresztül is kezelhetők, az [az. Search PowerShell-modul](search-manage-powershell.md), [az Search Azure CLI-modul](search-manage-azure-cli.md), valamint a .net, a Python, a Java és a JavaScript Azure SDK-k használatával. A felügyeleti feladatok teljes egészében képviseltetik magukat a Portálon és az összes programozott felületen. Nincs olyan konkrét felügyeleti feladat, amely csak egy módozatban érhető el.

A Cognitive Search további Azure-szolgáltatásokat is használ részletesebb monitorozáshoz és felügyelethez. Önmagában a keresési szolgáltatásban tárolt egyetlen adatforrás objektum tartalma (indexek, indexelő és adatforrás-definíciók és egyéb objektumok). A portál oldalain jelentett mérőszámok a 30 napos időszakon belüli belső naplókból vannak kihúzva. A felhasználó által vezérelt naplózás és a további események esetében [Azure monitorra](../azure-monitor/index.yml)lesz szüksége. A keresési szolgáltatás diagnosztikai naplózásának beállításával kapcsolatos további információkért lásd: a [naplófájlok adatainak összegyűjtése és elemzése](search-monitor-logs.md).

## <a name="administrator-permissions"></a>Rendszergazdai engedélyek

Amikor megnyitja a keresési szolgáltatás áttekintése lapot, a fiókhoz rendelt engedélyek határozzák meg, hogy mely lapok érhetők el Önnek. A cikk elején található áttekintő lap megjeleníti a portál azon lapjait, amelyeket a rendszergazda vagy a közreműködő látni fog.

Az Azure-erőforrásokban a rendszergazdai jogosultságok a szerepkör-hozzárendeléseken keresztül adhatók meg. Az Azure Cognitive Search környezetében a [szerepkör-hozzárendelések](search-security-rbac.md) határozzák meg, hogy ki oszthat ki replikákat és partíciókat, illetve hogyan kezelhet API-kulcsokat, függetlenül attól, hogy a portált, a [PowerShellt](search-manage-powershell.md), az [Azure CLI](search-manage-azure-cli.md)-t vagy a [felügyeleti REST API-kat](/rest/api/searchmanagement/search-howto-management-rest-api)használják:

> [!TIP]
> A szolgáltatás kiépítés vagy leszerelése egy Azure-előfizetés rendszergazdája vagy egy társ-rendszergazda által végezhető el. Az Azure-ra kiterjedő mechanizmusok használatával zárolhatja az előfizetést vagy az erőforrást, így megakadályozhatja a keresési szolgáltatás véletlen vagy jogosulatlan törlését rendszergazdai jogosultságokkal rendelkező felhasználók számára. További információ: [erőforrások zárolása a váratlan törlés megakadályozása érdekében](../azure-resource-manager/management/lock-resources.md).

## <a name="next-steps"></a>Következő lépések

* A portálon elérhető [figyelési képességek](search-monitor-usage.md) áttekintése
* Automatizálás a [PowerShell](search-manage-powershell.md) vagy az [Azure CLI](search-manage-azure-cli.md) használatával
* A tartalom és a műveletek védelméhez szükséges [biztonsági funkciók](search-security-overview.md) áttekintése
* [Diagnosztikai naplózás](search-monitor-logs.md) engedélyezése a lekérdezési és indexelési számítási feladatok figyeléséhez