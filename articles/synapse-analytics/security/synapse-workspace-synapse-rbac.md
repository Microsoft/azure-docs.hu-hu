---
title: A szinapszis szerepköralapú hozzáférés-vezérlése
description: Az Azure szinapszis Analytics szerepkör-alapú hozzáférés-vezérlését ismertető cikk
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9e96d6decba679c7a4764a77f1e9720000faf78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105147"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>Mi az a szinapszis szerepköralapú hozzáférés-vezérlés (RBAC)?

A szinapszis RBAC kiterjeszti az [Azure RBAC](../../role-based-access-control/overview.md) képességeit a szinapszis-munkaterületekhez és azok tartalmához. 

Az Azure RBAC segítségével felügyelheti, hogy kik hozhatnak létre, frissíthetnek és törölhetnek a szinapszis munkaterületet, valamint az SQL-készleteket, Apache Spark készleteket és az integrációs modulokat.

A szinapszis RBAC az alábbiak kezelésére szolgál:
- Kód-összetevők közzététele, illetve a közzétett kódok listázása és elérése 
- Kód végrehajtása Apaches Spark-készleteken és integrációs futtatókörnyezeteken,
- A hitelesítő adatokkal védett csatolt (adatszolgáltatások) szolgáltatások elérése 
- A feladatok végrehajtásának figyelése vagy megszakítása, a feladat kimenetének áttekintése és a végrehajtási naplók.  

>[!Note]
>Míg a szinapszis RBAC a közzétett SQL-parancsfájlokhoz való hozzáférés kezelésére szolgál, csak korlátozott hozzáférés-vezérlést biztosít a kiszolgáló nélküli SQL-készletekhez, és _nem_ használja a dedikált SQL-készletek elérésének szabályozását.  Az SQL-készletekhez való hozzáférés elsődlegesen az SQL-biztonsággal van vezérelve.

## <a name="what-can-i-do-with-synapse-rbac"></a>Mire használhatom a szinapszis RBAC?

Íme néhány példa arra, hogy mit tehet a szinapszis RBAC:
  - Lehetővé teszi a felhasználó számára, hogy a Apache Spark notebookokon és feladatokon végrehajtott módosításokat az élő szolgáltatásba tegye közzé.
  - A jegyzetfüzetek és a Spark-feladatok egy adott Apache Spark készleten való futtatásának és megszakításának engedélyezése a felhasználó számára.
  - Lehetővé teszi, hogy a felhasználók meghatározott hitelesítő adatokat használjanak, így a munkaterület rendszeridentitása által védett folyamatokat futtathatnak, és a társított szolgáltatásokban tárolt adatokhoz hitelesítő adatokat biztosítanak. 
  - Lehetővé teszi, hogy a rendszergazda felügyelje, figyelje és megszakítsa a feladatok végrehajtását adott Spark-készleteken.    

## <a name="how-synapse-rbac-works"></a>A szinapszis RBAC működése
Mint az Azure RBAC, a szinapszis RBAC szerepkör-hozzárendelések létrehozásával működik. Egy szerepkör-hozzárendelés három elemből tevődik össze: egy rendszerbiztonsági tagból, egy szerepkör-definícióból és egy hatókörből.  

### <a name="security-principals"></a>Rendszerbiztonsági tag

A _rendszerbiztonsági tag_ egy felhasználó, csoport, egyszerű szolgáltatásnév vagy felügyelt identitás.

### <a name="roles"></a>Szerepkörök
 
A _szerepkör_ olyan engedélyek vagy műveletek gyűjteménye, amelyek meghatározott erőforrástípusok vagy összetevő-típusok alapján végezhetők el.

A szinapszis olyan beépített szerepköröket biztosít, amelyek a különböző personák igényeihez illeszkedő műveletek gyűjteményeit határozzák meg:
- A rendszergazdák teljes körű hozzáférést kapnak a munkaterület létrehozásához és konfigurálásához 
- A fejlesztők létrehozhatnak, frissíthetnek és kereshetnek SQL-parancsfájlokat, jegyzetfüzeteket, folyamatokat és adatfolyamok, de nem tudják közzétenni vagy végrehajtani ezt a kódot a termelési számítási erőforrásokon/adatokon.
- A kezelők a rendszerállapotot, az alkalmazások végrehajtását és a felülvizsgálati naplókat a kód vagy a kimenetek a végrehajtásból való elérése nélkül tudják figyelni és kezelni.
- A biztonsági személyzet képes a végpontok felügyeletére és konfigurálására anélkül, hogy hozzá kellene férnie a kód, a számítási erőforrások vagy az adatokhoz.

[További](./synapse-workspace-synapse-rbac-roles.md) információ a beépített szinapszis-szerepkörökről. 

### <a name="scopes"></a>Hatókörök

A _hatókör_ határozza meg azokat az erőforrásokat vagy összetevőket, amelyekre a hozzáférés vonatkozik.  A szinapszis támogatja a hierarchikus hatóköröket.  A magasabb szintű hatókörben megadott engedélyeket alacsonyabb szintű objektumok öröklik.  A szinapszis RBAC a legfelső szintű hatókör egy munkaterület.  Egy szerepkör munkaterület-hatókörrel való hozzárendelésével a munkaterület összes vonatkozó objektuma engedélyt kap.  

A munkaterületen belüli jelenlegi támogatott hatókörök a következők: Apache Spark Pool, Integration Runtime, társított szolgáltatás és hitelesítő adatok. 

A kódokhoz való hozzáférés a munkaterület hatókörével adható meg.  Az összetevők egy munkaterületen belüli gyűjteményekhez való hozzáférésének megadását egy későbbi kiadásban fogja támogatni a rendszer.

## <a name="resolving-role-assignments-to-determine-permissions"></a>Szerepkör-hozzárendelések feloldása az engedélyek meghatározásához

A szerepkör-hozzárendelés megadja a rendszerbiztonsági tag számára a szerepkör által a megadott hatókörben meghatározott engedélyeket.

A szinapszis RBAC egy olyan adalékanyag-modell, mint az Azure RBAC. Több szerepkör is hozzárendelhető egyetlen résztvevőhöz és különböző hatókörökhöz. Egy rendszerbiztonsági tag engedélyeinek kiszámításakor a rendszer a résztvevőhöz rendelt összes szerepkört, illetve azon csoportokat veszi figyelembe, amelyek közvetlenül vagy közvetve a rendszerbiztonsági tag részét képezik.  Emellett az egyes hozzárendelések hatókörét is figyelembe veszi az alkalmazandó engedélyek meghatározásakor.  

## <a name="enforcing-assigned-permissions"></a>Hozzárendelt engedélyek kényszerítése

A szinapszis Studióban előfordulhat, hogy bizonyos gombok vagy beállítások szürkén jelennek meg, vagy ha nem rendelkezik a szükséges engedélyekkel, a rendszer az engedélyek megadását is visszaadja. 

Ha egy gomb vagy lehetőség le van tiltva, a gombon vagy a kapcsolón keresztül vigyen fel egy elemleírást a szükséges engedélyekkel.  Vegye fel a kapcsolatot a szinapszis rendszergazdájával egy olyan szerepkör hozzárendeléséhez, amely megadja a szükséges engedélyt. [Itt](./synapse-workspace-synapse-rbac-roles.md)láthatja azokat a szerepköröket, amelyek konkrét műveleteket biztosítanak.

## <a name="who-can-assign-synapse-rbac-roles"></a>Ki rendelhet hozzá szinapszis RBAC-szerepköröket?

Csak egy szinapszis-rendszergazda rendelhet hozzá szinapszis RBAC-szerepköröket.  A munkaterület szintjén található szinapszis-rendszergazda bármely hatókörben engedélyezheti a hozzáférést.  A szinapszis-rendszergazda alacsonyabb szintű hatókörben csak az adott hatókörhöz biztosít hozzáférést. 

Új munkaterület létrehozásakor a létrehozó automatikusan megkapja a szinapszis rendszergazdai szerepkört a munkaterület hatókörében.   

## <a name="where-do-i-manage-synapse-rbac"></a>Hol kezelhetem a szinapszis RBAC?

A szinapszis RBAC kezelése a Microsoft-felügyeleti központ hozzáférés-vezérlési eszközeivel történik a szinapszis studión belül. 

## <a name="next-steps"></a>Következő lépések

Ismerje meg a beépített [SZINAPSZIS RBAC-szerepköröket](./synapse-workspace-synapse-rbac-roles.md).

Ismerje meg [, hogyan tekintheti át a munkaterületek szinapszis-RBAC szerepkör-hozzárendeléseit](./how-to-review-synapse-rbac-role-assignments.md) .

Tudnivalók [a SZINAPSZIS RBAC szerepköreinek hozzárendeléséről](./how-to-manage-synapse-rbac-role-assignments.md)