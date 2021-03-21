---
title: Azure-szerepkörök beállítása az Azure rendszergazdai hozzáféréshez
titleSuffix: Azure Cognitive Search
description: Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) a Azure Portal az Azure Cognitive Search felügyeletének felügyeleti feladatainak szabályozására és delegálására.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519499"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Azure-szerepkörök beállítása az Azure-beli rendszergazdai hozzáféréshez Cognitive Search

Az Azure [globális szerepköralapú engedélyezési modellt](../role-based-access-control/role-assignments-portal.md) biztosít a portálon vagy a Resource Manager API-kon keresztül felügyelt összes szolgáltatáshoz. A tulajdonos, közreműködő és olvasó szerepkörök határozzák meg az egyes szerepkörökhöz rendelt Active Directory felhasználók, csoportok és rendszerbiztonsági tag *szolgáltatások felügyeletének* szintjét. 

> [!Note]
> Nincs Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) a tartalom biztosításához a szolgáltatásban. A szolgáltatáshoz rendszergazdai API-kulcsot vagy lekérdezési API-kulcsot kell használnia a hitelesített kérésekhez. A keresési eredményeken alapuló, identitás-alapú hozzáféréshez biztonsági szűrőket hozhat létre, amelyek identitás alapján metszik az eredményeket, és eltávolíthatja azokat a dokumentumokat, amelyekhez a kérelmezőnek nincs hozzáférése. További információ: [biztonsági szűrők](search-security-trimming-for-azure-search.md).

## <a name="management-tasks-by-role"></a>Felügyeleti feladatok szerepkör szerint

Az Azure Cognitive Search esetében a szerepkörök a következő felügyeleti feladatokat támogató jogosultsági szintekhez vannak társítva:

| Szerepkör | Feladat |
| --- | --- |
| Tulajdonos |Hozza létre vagy törölje a szolgáltatást vagy a szolgáltatás bármely objektumát, beleértve az API-kulcsokat, az indexeket, az indexelő, az indexelő adatforrásokat és az indexelő-ütemterveket.<p>A szolgáltatás állapotának megtekintése, beleértve a darabszámot és a tárterület méretét.<p>Szerepkör-tagság hozzáadása vagy törlése (csak a tulajdonosok kezelhetik a szerepkör-tagságot).<p>Az előfizetés-rendszergazdák és a szolgáltatás tulajdonosai automatikus tagsággal rendelkeznek a tulajdonosi szerepkörben. |
| Közreműködő | Azonos szintű hozzáférés tulajdonosként, mínusz az Azure szerepkör-kezelés. A közreműködők például létrehozhatnak vagy törölhetnek objektumokat, vagy megtekinthetik és újragenerálják az [API-kulcsokat](search-security-api-keys.md), de nem módosíthatják a szerepkör-tagságokat.<br><br>[Search Service közreműködő](../role-based-access-control/built-in-roles.md#search-service-contributor) megegyezik az általános közreműködő beépített szerepkörével. |
| Olvasó |Tekintse meg a szolgáltatási Essentials szolgáltatást, például a szolgáltatási végpontot, az előfizetést, az erőforráscsoportot, a régiót, a szintet és a kapacitást. A figyelés lapon megtekintheti a szolgáltatás metrikáit, például az átlagos lekérdezések másodpercenkénti számát is. Ennek a szerepkörnek a tagjai nem tekinthetik meg az indexet, az indexelő, az adatforrás vagy a készségkészlet adatait. Ez magában foglalja az objektumok használati adatait, például azt, hogy hány index létezik a szolgáltatásban. |

A szerepkörök nem biztosítanak hozzáférési jogosultságot a szolgáltatási végpont számára. A keresési szolgáltatási műveleteket, például az indexelést, az indexelést és a keresési adatok lekérdezéseit az API-kulcsok vezérlik, nem pedig a szerepkörök. További információ: az [API-kulcsok kezelése](search-security-api-keys.md).

## <a name="permissions-table"></a>Engedélyek tábla

Az alábbi táblázat összefoglalja az Azure Cognitive Searchban engedélyezett műveleteket, és egy adott művelethez hozzáférő kulcs feloldja azokat.

Az Azure RBAC-engedélyek a portál műveleteire és szolgáltatásaira (szolgáltatás vagy API-kulcsok létrehozására, törlésére vagy módosítására) vonatkoznak. Az API-kulcsok a szolgáltatás létezése után jönnek létre, és a szolgáltatásban lévő tartalomra vonatkoznak. Emellett a portálon található tartalommal kapcsolatos műveletekhez, például objektumok létrehozásához vagy törléséhez az Azure RBAC-tulajdonos vagy a közreműködő a szolgáltatáshoz egy hallgatólagos felügyeleti API-kulccsal kommunikál.

| Művelet | Vezérli |
|-----------|-------------------------|
| Szolgáltatás létrehozása | Azure RBAC engedélyek: tulajdonos vagy közreműködő |
| Szolgáltatás méretezése | Azure RBAC engedélyek: tulajdonos vagy közreműködő|
| Szolgáltatás törlése | Azure RBAC engedélyek: tulajdonos vagy közreműködő |
| Rendszergazdai vagy lekérdezési kulcsok kezelése | Azure RBAC engedélyek: tulajdonos vagy közreműködő|
| Szolgáltatási információk megtekintése a portálon vagy egy felügyeleti API-ban | Azure RBAC engedélyek: tulajdonos, közreműködő vagy olvasó  |
| Objektum-információk és mérőszámok megtekintése a portálon vagy egy felügyeleti API-ban | Azure RBAC engedélyek: tulajdonos vagy közreműködő |
| Objektumok létrehozása, módosítása és törlése a szolgáltatásban: <br>Indexek és összetevők (beleértve az analizátor-definíciókat, a pontozási profilokat, a CORS lehetőségeket), az indexelő, az adatforrások, a szinonimák, a javaslatok | Rendszergazdai kulcs, ha API-t, Azure RBAC-tulajdonost vagy közreműködőt használ a portál használatakor |
| Index lekérdezése | Rendszergazdai vagy lekérdezési kulcs API-k, Azure RBAC-tulajdonos vagy közreműködő használata esetén a portál használata esetén |
| Adatok lekérdezése az objektumokról, például a statisztikai adatok, a darabszámok és az objektumok listájáról | Rendszergazdai kulcs, ha API-t, Azure RBAC-tulajdonost vagy közreműködőt használ a portál használatakor |

## <a name="next-steps"></a>Következő lépések

+ [Kezelés a PowerShell-lel](search-manage-powershell.md) 
+ [Teljesítmény és optimalizálás az Azure Cognitive Search](search-performance-optimization.md)
+ [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md).
