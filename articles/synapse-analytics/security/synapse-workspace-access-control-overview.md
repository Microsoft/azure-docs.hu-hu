---
title: A szinapszis-munkaterület hozzáférés-vezérlésének áttekintése
description: Ez a cikk a szinapszis-munkaterületek és az általa tartalmazott erőforrások és kódok elérésének szabályozására szolgáló mechanizmusokat ismerteti.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 20614b1c397bdf24e807d48d3de33f0033da14bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105113"
---
# <a name="synapse-access-control"></a>A szinapszis hozzáférés-vezérlése 

Ez a cikk áttekintést nyújt a szinapszis számítási erőforrásokhoz és adatokhoz való hozzáférés szabályozásához rendelkezésre álló mechanizmusokról.  

## <a name="overview"></a>Áttekintés

A szinapszis átfogó és részletes hozzáférés-vezérlési rendszereket biztosít, amelyek a következőket integrálják: 
- **Azure-szerepkörök** az erőforrás-kezeléshez és a tárolóban tárolt adathozzáféréshez 
- **Szinapszis-szerepkörök** a kód és a végrehajtás élő elérésének kezeléséhez 
- SQL- **szerepkörök** az adatsíkokhoz az SQL-készletekben található adathoz való hozzáféréshez, valamint 
- **Git-engedélyek** a forráskód-vezérléshez, beleértve a folyamatos integrációt és a telepítés támogatását.  

A szinapszis-szerepkörök a különböző hatókörökön alkalmazható engedélyek készleteit biztosítják. Ez a részletesség lehetővé teszi a rendszergazdák, a fejlesztők, a biztonsági személyzet és a kezelők számára megfelelő hozzáférés megadását az erőforrások és az adatok kiszámításához.

A hozzáférés-vezérlést olyan biztonsági csoportok használatával lehet egyszerűsíteni, amelyek illeszkednek a felhasználók szerepköreihez. A hozzáférés kezeléséhez csak a megfelelő biztonsági csoportokból származó felhasználókat kell felvennie és eltávolítania.

## <a name="access-control-elements"></a>Hozzáférés-vezérlési elemek

### <a name="creating-and-managing-synapse-compute-resources"></a>Szinapszis számítási erőforrások létrehozása és kezelése

Az Azure-szerepkörök a következők felügyeletének vezérlésére használhatók: 
- Dedikált SQL-készletek, 
- Apache Spark készletek és 
- Integrációs modulok. 

Ezen erőforrások *létrehozásához* Azure-beli tulajdonosnak vagy közreműködőnek kell lennie az erőforráscsoporthoz. A  létrehozásuk után az erőforráscsoport vagy az egyes erőforrások Azure-tulajdonosának vagy közreműködőinek kell lennie. 

### <a name="developing-and-executing-code-in-synapse"></a>A kód fejlesztése és végrehajtása a Szinapszisban 

A szinapszis két fejlesztési modellt támogat.

- **Szinapszis élő fejlesztés**. A rendszer fejleszti és hibakeresési kódot készít a szinapszis Studióban, majd **közzéteszi** a Mentés és a végrehajtás során.  A szinapszis szolgáltatás az igazság forrása a kód szerkesztéséhez és végrehajtásához.  A nem közzétett munkák elvesznek a szinapszis Studio bezárásával.  
- **Git-kompatibilis fejlesztés**. A rendszer fejleszti és hibakeresési kódot készít a szinapszis Studióban, és végrehajtja a git-tárházban lévő munkaág **módosításait** . Egy vagy több ág munkája integrálva van egy együttműködési ágban, ahonnan a szolgáltatásba **közzéteszi** . A git-tárház az igazság forrása a kód szerkesztéséhez, míg a szolgáltatás az igazság forrása a végrehajtáshoz. A módosításokat véglegesíteni kell a git-tárházban, vagy közzé kell tenni a szolgáltatásban a szinapszis Studio bezárása előtt. [További](../cicd/continuous-integration-deployment.md) információ a szinapszis Analytics és a git használatával történő használatáról.

Mindkét fejlesztési modellben a szinapszis studióhoz hozzáféréssel rendelkező felhasználók programkódot hozhatnak létre. Azonban további engedélyekre van szüksége az összetevők a szolgáltatásban való közzétételéhez, a közzétett összetevők olvasásához, a git-módosítások elvégzéséhez, a kód végrehajtásához és a hitelesítő adatok által védett csatolt adatok eléréséhez.

### <a name="synapse-roles"></a>Szinapszis-szerepkörök

A szinapszis-szerepkörök segítségével szabályozható a szinapszis szolgáltatáshoz való hozzáférés, amely a következőket teszi lehetővé: 
- Közzétett kódrészletek listázása, 
- Kód-összetevők, társított szolgáltatások és hitelesítő adatok definícióinak közzététele
- A szinapszis számítási erőforrásokat használó kód vagy folyamatok végrehajtása
- A hitelesítő adatokkal védett csatolt adatokat elérő kód vagy folyamatok végrehajtása
- A közzétett kód összetevőihez társított kimenetek megtekintése
- A számítási erőforrás állapotának figyelése és a futásidejű naplók megtekintése.

A szinapszis-szerepkörök a munkaterület hatókörében vagy az adott szinapszis-erőforrásokhoz megadott engedélyek korlátozására szolgáló, finomabb hatókörű hatókörökhöz rendelhetők.

### <a name="git-permissions"></a>Git-engedélyek

A git-alapú fejlesztés git módban való használata esetén a git-engedélyek vezérlik, hogy elolvashatja és véglegesítheti-e a kód összetevőinek módosításait, beleértve a társított szolgáltatás és a hitelesítő adatok definícióit is.   
   
### <a name="accessing-data-in-sql"></a>Adatok elérése az SQL-ben

Dedikált és kiszolgáló nélküli SQL-készletek használata esetén az adatsíkok hozzáférését az SQL-engedélyek vezérlik. 

A munkaterület létrehozója Active Directory rendszergazdaként van hozzárendelve a munkaterületen. A létrehozást követően ezt a szerepkört hozzárendelheti egy másik felhasználóhoz vagy egy biztonsági csoporthoz is a Azure Portal.

**Kiszolgáló nélküli SQL-készletek**: a szinapszis-rendszergazdák a `db_owner` `DBO` "beépített" kiszolgáló nélküli SQL-készletre vonatkozó engedélyeket kapnak (). Ahhoz, hogy más felhasználók is hozzáférhessenek a kiszolgáló nélküli SQL-készletekhez, a szinapszis-rendszergazdáknak az SQL-parancsfájlokat kell futtatniuk minden kiszolgáló nélküli készleten.  

**DEDIKÁLT SQL-készletek**: Active Directory rendszergazdai engedély a munkaterület létrehozójának és a munkaterület MSI-nek.  A dedikált SQL-készletek elérésére vonatkozó engedély nincs másképpen megadva. Ha más felhasználóknak vagy csoportoknak szeretne hozzáférést adni a dedikált SQL-készletekhez, a Active Directory rendszergazdának SQL-parancsfájlokat kell futtatnia az egyes dedikált SQL-készleteken.

Lásd: a [szinapszisok beállítása Access Control](./how-to-set-up-access-control.md) SQL-parancsfájlok SQL-engedélyek SQL-készletekben való megadásához.  

 ### <a name="accessing-system-managed-data-in-storage"></a>A rendszerfelügyelt adatok elérése a Storage szolgáltatásban

A kiszolgáló nélküli SQL-készletek és a Apache Spark táblák a munkaterülethez társított ADLS Gen2 tárolóban tárolják az adataikat. A felhasználó által telepített Apache Spark kódtárakat is ugyanabban a Storage-fiókban felügyeli a rendszer. A használati esetek engedélyezéséhez a felhasználóknak és a munkaterület MSI-nek meg kell adni a **Storage blob-adatközreműködői** hozzáférést ehhez a munkaterülethez ADLS Gen2 Storage-tárolóhoz.  

## <a name="using-security-groups-as-a-best-practice"></a>Biztonsági csoportok használata ajánlott eljárásként

A hozzáférés-vezérlés kezelésének egyszerűbbé tétele érdekében biztonsági csoportok használatával rendelhet szerepköröket egyéni felhasználókhoz és csoportokhoz. Biztonsági csoportok hozhatók létre a szervezeten belüli Persons vagy functions-függvények tükrözéséhez, amelyeknek hozzáférésre van szükségük a szinapszis-erőforrásokhoz vagy-összetevőkhöz.  Ezek a person-alapú biztonsági csoportok ezután egy vagy több Azure-szerepkört, szinapszis-szerepköröket, SQL-engedélyeket vagy git-engedélyeket rendelhetnek hozzá. A jól kiválasztott biztonsági csoportokkal egyszerűen hozzárendelhet egy felhasználót a szükséges engedélyekhez, ha hozzáadja őket a megfelelő biztonsági csoporthoz. 

>[!Note]
>Ha biztonsági csoportok használatával kezeli a hozzáférést, a módosítások érvénybe léptetéséhez további késleltetést is Azure Active Directory. 

## <a name="access-control-enforcement-in-synapse-studio"></a>Hozzáférés-vezérlés kényszerítése a szinapszis Studióban

A szinapszis Studio az engedélyei és a jelenlegi mód alapján eltérően fog működni:
- **Szinapszis élő üzemmód:** A szinapszis Studio megakadályozza a közzétett tartalom megtekintését, a tartalmak közzétételét, vagy egyéb műveletek elvégzését, ha nem rendelkezik a szükséges engedéllyel.  Bizonyos esetekben megakadályozható a nem használható vagy menthető kód-összetevők létrehozása. 
- **Git mód:** Ha olyan git-engedélyekkel rendelkezik, amelyek segítségével véglegesítheti az aktuális ág módosításait, akkor a commit művelet akkor is engedélyezett lesz, ha nincs engedélye az élő szolgáltatás módosításának közzétételére.  

Bizonyos esetekben lehetőség van a kód-összetevők létrehozására vagy közzétételre vagy végrehajtásra való jogosultság nélkül is. Ez lehetővé teszi a kód futtatását (a szükséges végrehajtási engedélyekkel). [További](./synapse-workspace-understand-what-role-you-need.md) információ a gyakori feladatokhoz szükséges szerepkörökről. 

Ha egy szolgáltatás le van tiltva a szinapszis Studióban, egy elemleírás jelzi a szükséges engedélyt. A [SZINAPSZIS RBAC szerepkör-útmutató](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them) segítségével megkeresheti, hogy melyik szerepkörre van szükség a hiányzó engedély megadásához.


## <a name="next-steps"></a>Következő lépések

- További információ a [SZINAPSZIS RBAC](./synapse-workspace-synapse-rbac.md)
- További információ a [SZINAPSZIS RBAC szerepköreiről](./synapse-workspace-synapse-rbac-roles.md)
- Megtudhatja [, hogyan állíthatja be a szinapszis-munkaterület hozzáférés-vezérlését](./how-to-set-up-access-control.md) biztonsági csoportok használatával.
- Ismerje meg [, hogyan tekintheti át a SZINAPSZIS RBAC szerepkör-hozzárendeléseit](./how-to-review-synapse-rbac-role-assignments.md)
- Ismerje meg [, hogyan kezelheti a SZINAPSZIS RBAC szerepkör-hozzárendeléseit](./how-to-manage-synapse-rbac-role-assignments.md)
