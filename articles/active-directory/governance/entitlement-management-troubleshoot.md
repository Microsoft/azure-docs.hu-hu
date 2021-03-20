---
title: Jogosultsági felügyelet – Azure AD
description: Ismerkedjen meg néhány olyan elemmel, amelyeket érdemes ellenőriznie, hogy segítsen a Azure Active Directory jogosultságok felügyeletének megoldásában.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b01321c2cbb15be5526bbe9ed04f52238390574
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516889"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Az Azure AD-jogosultságok kezelése – problémamegoldás

Ez a cikk néhány olyan elemet ismertet, amely segítséget nyújt az Azure Active Directory (Azure AD) jogosultságok kezelésének hibaelhárításában.

## <a name="administration"></a>Felügyelet

* Ha hozzáférés-megtagadási üzenetet kap a jogosultságok kezelésének konfigurálásakor, és Ön globális rendszergazda, győződjön meg arról, hogy a címtárban van egy [prémium szintű Azure ad P2 (vagy EMS E5) licenc](entitlement-management-overview.md#license-requirements).

* Ha hozzáférési csomagok létrehozásakor vagy megtekintésekor kap hozzáférés-megtagadási üzenetet, és Ön egy katalógus-létrehozó csoport tagja, létre kell [hoznia egy katalógust](entitlement-management-catalog-create.md) az első hozzáférési csomag létrehozása előtt.

## <a name="resources"></a>Erőforrások

* Az alkalmazások szerepköreit maguk az alkalmazások határozzák meg, a kezelésük pedig az Azure AD-ben történik. Ha egy alkalmazás nem rendelkezik erőforrás-szerepkörökkel, a jogosultságok kezelése a felhasználókat egy **alapértelmezett hozzáférési** szerepkörhöz rendeli.

    Vegye figyelembe, hogy a Azure Portal olyan szolgáltatások egyszerű szolgáltatásait is megjelenítheti, amelyek nem választhatók ki alkalmazásként.  Az **Exchange Online** és a **SharePoint Online** például szolgáltatások, nem pedig a címtárban erőforrás-szerepkörökkel rendelkező alkalmazások, ezért nem szerepelhetnek hozzáférési csomagban.  Ehelyett csoportalapú licencelés használatával hozzon létre egy megfelelő licencet azon felhasználók számára, akiknek hozzáférésre van szükségük a kérdéses szolgáltatásokhoz.

* Ahhoz, hogy egy csoport erőforrásként belefoglalható legyen egy hozzáférési csomagba, módosíthatónak kell lennie az Azure AD-ben.  A helyszíni Active Directoryból származó csoportok nem rendelhetők hozzá erőforrásként, mert a tulajdonosi vagy tagi attribútumaik nem módosíthatók az Azure AD-ben.   Az Exchange Online-ból származó terjesztési csoportok szintén nem módosíthatók az Azure AD-ben. 

* SharePoint Online-dokumentumtárak és egyedi dokumentumok nem adhatók hozzá erőforrásként.  Ehelyett hozzon létre egy [Azure ad biztonsági csoportot](../fundamentals/active-directory-groups-create-azure-portal.md), amely tartalmazza azt a csoportot és egy hely szerepkört a hozzáférési csomagban, a SharePoint Online-ban pedig ezt a csoportot használja a dokumentumtárhoz vagy dokumentumhoz való hozzáférés szabályozásához.

* Ha vannak olyan felhasználók, amelyek már hozzá lettek rendelve egy hozzáférési csomaggal felügyelni kívánt erőforráshoz, akkor meg kell győződni arról, hogy a felhasználók megfelelő szabályzat használatával lettek-e hozzárendelve a hozzáférési csomaghoz. Például előfordulhat, hogy a hozzáférési csomagba belefoglalni kívánt csoportban már találhatók felhasználók. Ha az adott csoportban lévő felhasználók folyamatos hozzáférést igényelnek, akkor megfelelő szabályzattal kell rendelkezniük a hozzáférési csomagok esetében, hogy ne veszítsék el a csoporthoz való hozzáférésüket. A hozzáférési csomagok hozzárendeléséhez megkérheti a felhasználókat, hogy kérelmezzék az adott erőforrást tartalmazó hozzáférési csomagot, vagy közvetlenül hozzárendelheti őket a hozzáférési csomaghoz. További információ: [hozzáférési csomag kérésének és jóváhagyási beállításainak módosítása](entitlement-management-access-package-request-policy.md).

* Egy csoport tagjainak eltávolításakor a rendszer eltávolítja azokat a Microsoft 365 csoportból is. Előfordulhat, hogy a csapat csevegési funkciójából való eltávolítás késleltetve történik meg. További [információ: csoporttagság](/microsoftteams/office-365-groups#group-membership).


## <a name="access-packages"></a>Hozzáférési csomagok

* Ha egy hozzáférési csomagot vagy házirendet próbál törölni, és olyan hibaüzenetet kap, amely szerint aktív hozzárendelések találhatók, ha nem látja a hozzárendeléssel rendelkező felhasználókat, ellenőrizze, hogy a közelmúltban törölt felhasználók továbbra is vannak-e hozzárendelések. A felhasználó törlése után a 30 napos időszak alatt a felhasználói fiók visszaállítható.   

## <a name="external-users"></a>Külső felhasználók

* Ha egy külső felhasználó hozzáférést szeretne kérni egy hozzáférési csomaghoz, győződjön meg arról, hogy a hozzáférési csomaghoz tartozó **saját hozzáférési portál hivatkozást** használják. További információ: [megosztási hivatkozás a hozzáférési csomag igényléséhez](entitlement-management-access-package-settings.md). Ha egy külső felhasználó csak a **myaccess.microsoft.com** látogat, és nem használja a teljes saját hozzáférési portál hivatkozást, akkor a saját szervezetében, és nem a szervezeten belül elérhető hozzáférési csomagokat fogja látni.

* Ha egy külső felhasználó nem tud hozzáférést kérni egy hozzáférési csomaghoz, vagy nem fér hozzá az erőforrásokhoz, ügyeljen arra, hogy ellenőrizze a [külső felhasználók beállításait](entitlement-management-external-users.md#settings-for-external-users).

* Ha egy új, külső felhasználó, amely korábban még nem jelentkezett be a címtárba, SharePoint Online-webhelyet tartalmazó hozzáférési csomagot kap, akkor ez a hozzáférési csomag nem teljesen kézbesítettként jelenik meg mindaddig, amíg a felhasználó fiókja nincs létrehozva a SharePoint Online-ban. A beállítások megosztásával kapcsolatos további információkért lásd: [a SharePoint Online külső megosztási beállításainak áttekintése](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Kérelmek

* Ha a felhasználó hozzáférést szeretne kérni egy hozzáférési csomaghoz, győződjön meg róla, hogy a hozzáférési csomaghoz tartozó **saját hozzáférési portál hivatkozást** használja. További információ: [megosztási hivatkozás a hozzáférési csomag igényléséhez](entitlement-management-access-package-settings.md).

* Ha privát/inkognitó módba állított böngészőből nyitja meg a Saját hozzáférés portált, a beállítás problémát okozhat a bejelentkezési viselkedés esetében. Azt javasoljuk, hogy a Saját hozzáférés portál megnyitásakor ne használja a böngésző privát/inkognitó módját.

* Ha egy, a címtárban még nem szereplő felhasználó jelentkezik be a Saját hozzáférés portálra hozzáférési csomag igénylése céljából, akkor meg kell győződni arról, hogy a hitelesítést a szervezeti fiók használatával végzi el. A szervezeti fiók lehet egy fiók az erőforráskönyvtárban vagy egy olyan könyvtárban, amely a hozzáférési csomag valamelyik szabályzatában szerepel. Ha a felhasználó fiókja nem szervezeti fiók, vagy a hitelesítés helyéül szolgáló könyvtár nem szerepel a szabályzatban, akkor a felhasználó nem fogja látni a hozzáférési csomagot. További információ: hozzáférés [kérése egy hozzáférési csomaghoz](entitlement-management-request-access.md).

* Ha a felhasználó számára le van tiltva az erőforráskönyvtárba való bejelentkezés, akkor nem fog tudni hozzáférést kérni a Saját hozzáférés portálon. Ahhoz, hogy a felhasználó hozzáférést igényelhessen, el kell távolítani a bejelentkezés letiltását a felhasználó profiljából. A bejelentkezési blokk eltávolításához kattintson a Azure Portal **Azure Active Directory**, majd a **felhasználók** elemre, majd a felhasználó elemre, végül a **profil** elemre. Szerkessze a **Settings (beállítások** ) szakaszt, és módosítsa a **Letiltás bejelentkezést** a **nem** értékre. További információ: [felhasználói profil adatainak hozzáadása vagy frissítése Azure Active Directory használatával](../fundamentals/active-directory-users-profile-azure-portal.md).  Azt is megtekintheti, hogy a felhasználó blokkolva lett-e egy [Identity Protection-házirend](../identity-protection/howto-identity-protection-remediate-unblock.md)miatt.

* Ha a saját hozzáférési portálon, ha a felhasználó egy kérelmező és egy jóváhagyó is, a **jóváhagyások** lapon nem fogja látni a hozzáférési csomagra vonatkozó kérelmét. Ez a viselkedés szándékos – a felhasználók nem hagyhatják jóvá a saját kéréseiket. Győződjön meg arról, hogy a felhasználó által kérelmezett hozzáférési csomaghoz további jóváhagyók vannak konfigurálva a szabályzatban. További információ: [hozzáférési csomag kérésének és jóváhagyási beállításainak módosítása](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Kérelem kézbesítési hibáinak megtekintése

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa, Access Package Manager vagy Access Package hozzárendelés-kezelő

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek** elemre.

1. Válassza ki a megtekinteni kívánt kérelmet.

    Ha a kérelem kézbesítési hibával rendelkezik, a kérelem állapota nem **kézbesítve** vagy **részben kézbesítve** lesz.

    Ha kézbesítési hibák merülnek fel, a kérelem részletei ablaktábláján megjelennek a kézbesítési hibák száma.

1. Kattintson a számra az összes kérelem kézbesítési hibájának megjelenítéséhez.

### <a name="reprocess-a-request"></a>Kérelem újrafeldolgozása

Ha a hozzáférési csomag újrafeldolgozására vonatkozó kérelem elindítását követően hiba történt, meg kell várnia, amíg a rendszer feldolgozza a kérést. A rendszer többször is megpróbál újradolgozni több órán keresztül, így ez idő alatt nem kényszerítheti az újrafeldolgozást. 

Csak olyan kérelmeket lehet újra feldolgozni, amelyeknek a **kézbesítési állapota nem sikerült** , vagy **részlegesen kézbesítve** lett, és egy héttel rövidebb befejezési dátummal rendelkezik. Az **újrafeldolgozás** gomb egyébként szürkén jelenik meg.

![Szürkén jelenik meg a gomb újrafeldolgozása](./media/entitlement-management-troubleshoot/cancel-reprocess-grayedout.png)

- Ha a hiba a próbaverziók ablakában van kijavítva, a kérelem állapota a **kézbesítés** értékre változik. A kérés a felhasználó további műveletei nélkül újra fel lesz dolgozva.

- Ha a hiba nem lett javítva a próbaverziók ablakban, előfordulhat, hogy a kérelem **kézbesítése nem sikerült** vagy **részben kézbesítve**. Ezután használhatja az **újrafeldolgozás** gombot. A kérelem újrafeldolgozása hét nap múlva elvégezhető.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa, Access Package Manager vagy Access Package hozzárendelés-kezelő

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek** elemre.

1. Kattintson az újradolgozni kívánt kérelemre.

1. A kérelem részletei ablaktáblán kattintson a **kérelem újrafeldolgozása** elemre.

    ![Sikertelen kérelem újrafeldolgozása](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Csak olyan függőben lévő kérést lehet megszakítani, amely még nem lett kézbesítve, vagy a kézbesítés sikertelen volt. Ellenkező esetben a **Mégse** gomb szürkén jelenik meg.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa, Access Package Manager vagy Access Package hozzárendelés-kezelő

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek** elemre.

1. Kattintson a megszakítani kívánt kérelemre.

1. A kérelem részletei ablaktáblán kattintson a **kérelem megszakítása** elemre.

## <a name="multiple-policies"></a>Több házirend

* A jogosultságok kezelése a legalacsonyabb jogosultsági szintű ajánlott eljárásokat követi. Ha egy felhasználó olyan hozzáférési csomaghoz kér hozzáférést, amelyhez több házirend is tartozik, a jogosultságok kezelése magában foglalja a logikát, hogy a szigorúbb vagy konkrétabb házirendek elsőbbséget élveznek az általános házirendek felett. Ha a szabályzat általános, akkor előfordulhat, hogy a jogosultságok kezelése nem jeleníti meg a házirendet a kérelmezőnek, vagy a szigorúbb szabályzatot is automatikusan kijelöli.

* Vegyünk például egy olyan hozzáférési csomagot, amely két házirendet alkalmaz a belső alkalmazottak számára, amelyekben mindkét házirend a kérelmezőre vonatkozik. Az első szabályzat a kérelmezőt tartalmazó konkrét felhasználókra vonatkozik. A második szabályzat a címtár minden olyan felhasználója számára, aki tagja a kérelmezőnek. Ebben az esetben az első házirendet a rendszer automatikusan kiválasztja a kérelmező számára, mert szigorúbb. A kérelmező nem kap lehetőséget a második szabályzat kiválasztására.

* Ha több házirend van érvényben, az automatikusan kiválasztott házirend vagy a kérelmezőnek megjelenő házirendek a következő prioritási logikán alapulnak:

    | Házirend prioritása | Hatókör |
    | --- | --- |
    | P1 | Adott felhasználók és csoportok a címtárban vagy az adott csatlakoztatott szervezeteknél |
    | P2 | A címtárban lévő összes tag (a vendégek kivételével) |
    | P3 | A címtárban lévő összes felhasználó (beleértve a vendégeket is) vagy adott csatlakoztatott szervezetek |
    | P4 | Minden konfigurált csatlakoztatott szervezet vagy minden felhasználó (minden csatlakoztatott szervezet + bármely új külső felhasználó) |
    
    Ha bármelyik szabályzat magasabb prioritású kategóriába tartozik, a rendszer figyelmen kívül hagyja az alacsonyabb prioritású kategóriákat. Ha például azt szeretné, hogy az azonos prioritású házirendek hogyan jelenjenek meg a kérelmező számára, tekintse meg a szabályzat [kiválasztása](entitlement-management-request-access.md#select-a-policy)című témakört.

## <a name="next-steps"></a>Következő lépések

- [Külső felhasználók hozzáférésének szabályozása](entitlement-management-external-users.md)
- [A felhasználók hozzáférési jogosultságok kezelésével kapcsolatos jelentések megtekintése](entitlement-management-reports.md)
