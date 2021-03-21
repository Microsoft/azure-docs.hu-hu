---
title: A külső felhasználók hozzáférésének szabályozása az Azure AD-jogosultságok kezelésében – Azure Active Directory
description: További információ a Azure Active Directory jogosultságok kezelésében a külső felhasználókhoz való hozzáférés szabályozásához megadható beállításokról.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7c78dcbc34deca769739f82964df41ebfc596ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176790"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>A külső felhasználók hozzáférésének szabályozása az Azure AD-jogosultságok kezelésében

Az Azure AD-jogosultságok kezelése az [Azure ad vállalatközi (B2B)](../external-identities/what-is-b2b.md) szolgáltatásával együttműködik a szervezeten kívüli személyekkel egy másik címtárban. Az Azure AD B2B-vel a külső felhasználók a saját címtárában hitelesítik magukat, de rendelkeznek egy képviselettel a címtárban. A címtárban lévő ábrázolás lehetővé teszi, hogy a felhasználó hozzáférjen az erőforrásokhoz.

Ez a cikk a külső felhasználók hozzáférésének szabályozására megadható beállításokat ismerteti.

## <a name="how-entitlement-management-can-help"></a>Hogyan segíthet a jogosultságok kezelése?

Az [Azure ad B2B](../external-identities/what-is-b2b.md) -Meghívási felületének használatakor már ismernie kell azon külső vendég felhasználók e-mail-címeit, akikkel az erőforrás-címtárba be szeretné állítani a szolgáltatást. Ez remekül működik, ha kisebb vagy rövid távú projekttel dolgozik, és már ismeri az összes résztvevőt, de ez nehezebben kezelhető, ha sok felhasználóval szeretne dolgozni, vagy ha a résztvevők idővel változnak.  Előfordulhat például, hogy egy másik szervezettel dolgozik, és egy kapcsolattartási ponttal rendelkezik az adott szervezethez, de az adott szervezet további felhasználói számára is hozzáférésre van szüksége.

A jogosultságok kezelésével meghatározhatja azt a szabályzatot, amely lehetővé teszi, hogy a felhasználók az Ön által megadott szervezetek felhasználói számára egy hozzáférési csomagot saját maguk igényelhetnek. Megadhatja, hogy szükséges-e a jóváhagyás, valamint a hozzáférés lejárati dátuma. Ha jóváhagyásra van szükség, a külső szervezet egy vagy több felhasználóját is meghívhatja a címtárba, és jóváhagyhatja őket – mivel valószínűleg tudni fogják, hogy mely külső felhasználók férhetnek hozzá a szervezethez. A hozzáférési csomag konfigurálása után elküldheti a hozzáférési csomag hivatkozását a kapcsolattartó személyének (szponzorának) a külső szervezeten belül. Ez a kapcsolat a külső szervezet más felhasználóival is megosztható, és ezt a hivatkozást használhatja a hozzáférési csomag igényléséhez. Az adott szervezet azon felhasználói, akik már meghívást kaptak a címtárba, a hivatkozást is használhatják.

A kérések jóváhagyása esetén a jogosultságok kezelése a szükséges hozzáféréssel fogja kiépíteni a felhasználót, amely magában foglalhatja a felhasználó meghívását, ha még nem szerepelnek a címtárban. Az Azure AD automatikusan létrehoz egy B2B vendég fiókot. Vegye figyelembe, hogy a rendszergazdák korábban már korlátozták az együttműködésre engedélyezett szervezeteket, ha egy [B2B engedélyezési vagy megtagadási listát](../external-identities/allow-deny-list.md) állítanak be a más szervezetek számára történő meghívások engedélyezéséhez vagy letiltásához.  Ha a felhasználó számára nem engedélyezett az engedélyezési vagy a tiltási lista, a rendszer nem kéri le őket.

Mivel nem szeretné, hogy a külső felhasználó hozzáférhessen az utolsó Forever-hez, meg kell adnia egy lejárati dátumot a szabályzatban, például 180 nap. 180 nap után, ha a hozzáférésük nincs kiterjesztve, a jogosultságok kezelése eltávolítja a hozzáférési csomaghoz társított összes hozzáférést. Alapértelmezés szerint, ha a jogosultságok kezelésében meghívott felhasználó nem rendelkezik más hozzáférési csomag-hozzárendelésekkel, akkor amikor elvesztik az utolsó hozzárendelést, a vendég fiókjuk letiltja a bejelentkezést 30 napig, és ezt követően eltávolítja azt. Ez megakadályozza a szükségtelen fiókok elterjedését. A következő szakaszokban leírtak szerint ezek a beállítások konfigurálhatók.

## <a name="how-access-works-for-external-users"></a>Hogyan működik a hozzáférés a külső felhasználók számára

A következő ábra és lépések áttekintést nyújtanak arról, hogy a külső felhasználók hogyan kapnak hozzáférést egy hozzáférési csomaghoz.

![A külső felhasználók életciklusát bemutató ábra](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. [Hozzá kell adnia egy csatlakoztatott szervezetet](entitlement-management-organization.md) ahhoz az Azure ad-címtárhoz vagy-tartományhoz, amelyhez együttműködik.

1. Olyan hozzáférési csomagot hoz létre a címtárban, amely a [címtárban nem szereplő felhasználókra](entitlement-management-access-package-create.md#for-users-not-in-your-directory)vonatkozó szabályzatot tartalmaz.

1. A [saját hozzáférési portálra mutató hivatkozást](entitlement-management-access-package-settings.md) küld a külső szervezeten belüli partnernek, amelyet a felhasználók megoszthatnak a hozzáférési csomag igényléséhez.

1. Egy külső felhasználó (ebben a példában a **kérelmező** ) a My Access portál hivatkozást használja a hozzáférési csomaghoz [való hozzáférés kéréséhez](entitlement-management-request-access.md) . A felhasználó bejelentkezése a csatlakoztatott szervezetben definiált címtár vagy tartomány hitelesítési típusától függ.

1. [A jóváhagyó jóváhagyja a kérelmet](entitlement-management-request-approve.md) (vagy a kérést automatikusan jóváhagyják).

1. A kérés bekerül a [kézbesítési állapotba](entitlement-management-process.md).

1. A B2B-Meghívási folyamat használatával a rendszer létrehoz egy vendég felhasználói fiókot a címtárban (ebben a példában **a kérelmező a vendég)** . Ha meg van adva egy [engedélyezési lista vagy egy megtagadási lista](../external-identities/allow-deny-list.md) , a rendszer alkalmazza a lista beállítását.

1. A vendég felhasználó hozzá van rendelve a hozzáférési csomag összes erőforrásához. Az Azure AD-ben és más Microsoft Online szolgáltatásokban vagy csatlakoztatott SaaS-alkalmazásokban végzett módosítások elvégzése hosszabb időt is igénybe vehet. További információ: a [módosítások alkalmazása](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. A külső felhasználó egy e-mailt kap, amely jelzi, hogy a hozzáférésük [kézbesítése](entitlement-management-process.md)megtörtént.

1. Az erőforrásokhoz való hozzáféréshez a külső felhasználó az e-mailben található hivatkozásra kattintva vagy a Meghívási folyamat befejezéséhez közvetlenül a címtár erőforrásaihoz próbál hozzáférni.

1. A házirend-beállításoktól függően az idő múlásával a külső felhasználó hozzáférési csomagjának hozzárendelése lejár, és a rendszer eltávolítja a külső felhasználó hozzáférését.

1. A külső felhasználók beállításainak életciklusa alapján, ha a külső felhasználó már nem rendelkezik hozzáférési csomag-hozzárendelésekkel, a rendszer letiltja a külső felhasználót a bejelentkezéstől, és eltávolítja a vendég felhasználói fiókot a címtárból.

## <a name="settings-for-external-users"></a>Külső felhasználók beállításai

Annak biztosítása érdekében, hogy a szervezeten kívüli személyek hozzáférhessenek a hozzáférési csomagokhoz, és hozzáférjenek a hozzáférési csomagok erőforrásaihoz, bizonyos beállításoknak megfelelően be kell jelentkeznie.

### <a name="enable-catalog-for-external-users"></a>Katalógus engedélyezése külső felhasználók számára

- Alapértelmezés szerint az [új katalógus](entitlement-management-catalog-create.md)létrehozásakor engedélyezve van a külső felhasználók számára a hozzáférési csomagok kérése a katalógusban. Győződjön meg arról, hogy a **külső felhasználók számára engedélyezve** van-e az **Igen** érték.

    ![Katalógus beállításainak szerkesztése](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Az Azure AD B2B külső együttműködési beállításainak konfigurálása

- Lehetővé teszi, hogy a vendégek meghívjanak más vendégeket a címtárba, ami azt jelenti, hogy a vendég meghívása a jogosultsági felügyeleten kívül történhet Azt javasoljuk, hogy a **vendégek meghívhatják** a **nem** értéket, hogy csak a megfelelően szabályozott meghívókat engedélyezzék.
- Ha a B2B engedélyezési listát használja, gondoskodnia kell arról, hogy minden olyan tartomány hozzá legyen adva a jogosultságok kezelése szolgáltatással, amelyet a listához felvesznek. Ha a B2B megtagadási listát használja, meg kell győződnie arról, hogy a partnerrel nem rendelkező tartomány nincs felvéve a listára.
- Ha az **összes felhasználóra** vonatkozóan létrehoz egy jogosultsági felügyeleti szabályzatot (minden csatlakoztatott szervezet + bármely új külső felhasználó), és a felhasználó nem tartozik egy csatlakoztatott szervezethez a címtárban, akkor a rendszer automatikusan létrehoz egy csatlakoztatott szervezetet, amikor a csomagot kérik. Minden B2B engedélyezési vagy megtagadási lista beállításai elsőbbséget élveznek. Ezért ügyeljen arra, hogy a szabályzatban szerepeltetni kívánt tartományokat az engedélyezési listára vegye fel, ha Ön használ egyet, és kizárhatja őket a megtagadási listáról, ha megtagadási listát használ.
- Ha olyan jogosultság-kezelési szabályzatot szeretne létrehozni, amely **minden felhasználót** tartalmaz (minden csatlakoztatott szervezet + bármely új külső felhasználó), először engedélyeznie kell az e-mailek egyszeri jelszavas hitelesítését a címtárban. További információkért lásd az [e-mailek egyszeri jelszavas hitelesítését](../external-identities/one-time-passcode.md)ismertető témakört.
- Az Azure AD B2B külső együttműködési beállításaival kapcsolatos további információkért lásd: [B2B külső együttműködés engedélyezése és a vendégek meghívása](../external-identities/delegate-invitations.md)a felhasználók számára.

    ![Az Azure AD külső együttműködési beállításai](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>A feltételes hozzáférési szabályzatok áttekintése

- Ügyeljen arra, hogy kizárják a vendégeket minden olyan feltételes hozzáférési szabályzatból, amelyet az új vendég felhasználók nem fognak tudni kielégíteni, mert ezzel letiltja, hogy be tudja jelentkezni a címtárba. Előfordulhat például, hogy a vendégek nem rendelkeznek regisztrált eszközzel, és nem szeretnének újra regisztrálni a többtényezős hitelesítésre (MFA), ezért a feltételes hozzáférési szabályzatban szereplő követelmények hozzáadásával a vendégek letilthatják a jogosultságok kezelését. További információ: [Mi a feltételek a Azure Active Directory feltételes hozzáférésben?](../conditional-access/concept-conditional-access-conditions.md).

    ![Az Azure AD feltételes hozzáférési szabályzata kizárja a beállításokat](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>A SharePoint Online külső megosztási beállításainak áttekintése

- Ha a külső felhasználók hozzáférési csomagjaiban SharePoint Online-webhelyeket kíván felvenni, győződjön meg arról, hogy a szervezeti szintű külső megosztási beállítás **mindenki** számára van beállítva (a felhasználóknak nincs szükségük bejelentkezésre), vagy **új és meglévő vendégeknek** (a vendégeknek be kell jelentkezniük, vagy meg kell adniuk egy ellenőrző kódot). További információ: [külső megosztás be-és kikapcsolása](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Ha korlátozni szeretné a jogosultsági felügyeleten kívüli külső megosztást, megadhatja a külső megosztási beállítást a **meglévő vendégek** számára. Ezt követően csak a jogosultságok kezelésével meghívott új felhasználók férhetnek hozzá ezekhez a webhelyekhez. További információ: [külső megosztás be-és kikapcsolása](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Győződjön meg arról, hogy a hely szintű beállítások lehetővé teszik a vendég hozzáférését (ugyanazokat a beállításokat a korábban felsoroltak szerint). További információ: [külső megosztás be-és kikapcsolása egy adott helyen](/sharepoint/change-external-sharing-site).

### <a name="review-your-microsoft-365-group-sharing-settings"></a>Tekintse át Microsoft 365 csoport megosztási beállításait

- Ha a külső felhasználók hozzáférési csomagjaiban Microsoft 365 csoportokat szeretne felvenni, győződjön **meg** arról, hogy a **felhasználók új vendégeket adhatnak hozzá a szervezethez** , hogy engedélyezze a vendég hozzáférését. További információ: a [vendég hozzáférésének kezelése Microsoft 365 csoportokhoz](/Microsoft 365/admin/create-groups/manage-guest-access-in-groups?view=Microsoft 365-worldwide#manage-groups-guest-access).

- Ha azt szeretné, hogy a külső felhasználók hozzáférhessenek a SharePoint Online-webhelyhez és a Microsoft 365hoz társított erőforrásokhoz, akkor ügyeljen arra, hogy bekapcsolja a SharePoint Online külső megosztást. További információ: [külső megosztás be-és kikapcsolása](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- A következő témakörben talál további információt arról, hogyan állíthatja be a Microsoft 365-csoportok vendég házirendjét a PowerShell címtár szintjén: [példa: a vendég házirend konfigurálása csoportok számára a címtár szintjén](../enterprise-users/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>A csapatok megosztási beállításainak áttekintése

- Ha a külső felhasználók hozzáférési csomagjaiban csapatokat kíván felvenni, győződjön **meg** arról, hogy a vendég hozzáférés engedélyezése a **Microsoft Teams** szolgáltatásban beállítás engedélyezve értékre van állítva. További információ: [a vendég hozzáférésének konfigurálása a Microsoft Teams felügyeleti központban](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>A külső felhasználók életciklusának kezelése

Kiválaszthatja, hogy mi történjen, ha egy külső felhasználó, aki egy, a hozzáférési csomagra vonatkozó kérelem jóváhagyásával meghívta a címtárat, már nem rendelkezik hozzáférési csomag-hozzárendeléssel. Ez akkor fordulhat elő, ha a felhasználó lemond az összes hozzáférési csomagra vonatkozó hozzárendeléséről, vagy az utolsó hozzáférési csomag hozzárendelésének érvényessége lejár. Alapértelmezés szerint, ha egy külső felhasználó már nem rendelkezik hozzáférési csomag hozzárendeléseivel, a rendszer letiltja a címtárba való bejelentkezést. 30 nap elteltével a vendég felhasználói fiókja törlődik a címtárból.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben, a **jogosultságok kezelése** szakaszban kattintson a **Beállítások** elemre.

1. Kattintson a **Szerkesztés** gombra.

    ![A külső felhasználók életciklusának kezeléséhez szükséges beállítások](./media/entitlement-management-external-users/settings-external-users.png)

1. A **külső felhasználók életciklusának kezelése** szakaszban válassza ki a külső felhasználók különböző beállításait.

1. Ha egy külső felhasználó elveszíti az utolsó hozzárendelését bármely hozzáférési csomagba, ha le szeretné tiltani a címtárba való bejelentkezést, állítsa be a **külső felhasználó letiltása a címtárba való bejelentkezést** **Igen** értékre.

    > [!NOTE]
    > Ha a felhasználó blokkolva van a címtárba való bejelentkezéskor, akkor a felhasználó nem kérheti újra a hozzáférési csomagot, vagy további hozzáférést kérhet a címtárban. Ne állítsa be, hogy blokkolja a bejelentkezést, ha később más hozzáférési csomagokhoz is hozzáférést kell kérniük.

1. Ha egy külső felhasználó elveszíti az utolsó hozzárendelését bármely hozzáférési csomaghoz, ha el szeretné távolítani a vendég felhasználói fiókját ebben a címtárban, állítsa a **külső felhasználó eltávolítása** beállítást **Igen** értékre.

    > [!NOTE]
    > A jogosultságok kezelése csak azokat a fiókokat távolítja el, amelyeket a jogosultsági felügyeleten keresztül meghívtak. Azt is vegye figyelembe, hogy a rendszer letiltja a felhasználót a bejelentkezéshez, és onnan eltávolítja a címtárból, még akkor is, ha az adott felhasználó hozzá lett adva az ebben a könyvtárban található erőforrásokhoz, amelyek nem férnek hozzá a csomagok hozzárendeléseihez Ha a vendég ebben a könyvtárban található a hozzáférési csomag hozzárendeléseinek fogadása előtt, azok továbbra is megmaradnak. Ha azonban a vendéget egy hozzáférési csomagon keresztül kérték fel, és a meghívást követően a OneDrive vállalati vagy SharePoint Online-webhelyhez is hozzá lett rendelve, akkor továbbra is el lesznek távolítva.

1. Ha el szeretné távolítani a vendég felhasználói fiókot ebben a címtárban, beállíthatja, hogy hány nap elteltével távolítsa el a rendszer. Ha el szeretné távolítani a vendég felhasználói fiókot, amint elvesztik az utolsó hozzárendelését bármelyik hozzáférési csomaghoz, állítsa be a **napok számát, mielőtt eltávolítja a külső felhasználót a címtárból** **0-ra**.

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

- [Csatlakoztatott szervezet hozzáadása](entitlement-management-organization.md)
- [A címtárban nem szereplő felhasználók számára](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Hibaelhárítás](entitlement-management-troubleshoot.md)
