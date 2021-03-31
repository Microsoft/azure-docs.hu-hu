---
title: A vendég hozzáférésének kezelése hozzáférési felülvizsgálatokkal – Azure AD
description: Vendég felhasználók felügyelete csoport tagjaiként vagy Azure Active Directory hozzáférési felülvizsgálattal rendelkező alkalmazáshoz rendelve
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76aeb63feb006e6962efeb3687363f18acb59904
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746694"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Vendégfelhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal


A Azure Active Directory (Azure AD) segítségével könnyedén engedélyezheti a szervezeti határokon átívelő együttműködést az [Azure ad B2B szolgáltatásával](../external-identities/what-is-b2b.md). A más bérlők vendégeit a rendszergazdák vagy [más felhasználók](../external-identities/what-is-b2b.md)is [meghívhatják](../external-identities/add-users-administrator.md) . Ez a képesség a közösségi identitásokra, például a Microsoft-fiókokra is vonatkozik.

Azt is megteheti, hogy a vendég felhasználói megfelelő hozzáféréssel rendelkeznek. Megkérheti a vendégek saját maguk vagy döntéshozóik számára, hogy részt vegyenek a hozzáférési felülvizsgálatban, és hitelesítsék (vagy tanúsítják) a vendégek hozzáférését. A felülvizsgálatot végzők az Azure AD-ből származó javaslatok alapján dönthetnek arról, hogy az egyes felhasználóknak szükségük van-e folyamatos hozzáférésre. Ha egy hozzáférési felülvizsgálat elkészült, akkor módosításokat végezhet, és eltávolíthatja a hozzáférést azon vendégek számára, akiknek már nincs rá szükségük.

> [!NOTE]
> Ez a dokumentum a vendég felhasználók hozzáférésének áttekintésére koncentrál. Ha szeretné áttekinteni az összes felhasználó hozzáférését, ne csak vendégeket, tekintse meg [a felhasználói hozzáférés kezelése hozzáférési felülvizsgálatokkal](manage-user-access-with-access-reviews.md)című témakört. Ha szeretné áttekinteni a felhasználói tagságot a rendszergazdai szerepkörökben (például globális rendszergazda), tekintse meg a [hozzáférési felülvizsgálat elindítása Azure ad Privileged Identity Managementban](../privileged-identity-management/pim-how-to-start-security-review.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

- Prémium szintű Azure AD P2

További információk a [licencekre vonatkozó követelmények](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Hozzáférési felülvizsgálat létrehozása és végrehajtása a vendégek számára

Először hozzá kell rendelnie a következő szerepkörök egyikét:
- globális rendszergazda
- Felhasználói rendszergazda
- Előnézet A M365 vagy a HRE biztonsági csoportjának az áttekinthető csoportjának tulajdonosa

Ezután nyissa meg az [Identity irányítás lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) , és győződjön meg arról, hogy a hozzáférési felülvizsgálatok készen állnak a szervezet számára.

Az Azure AD számos forgatókönyvet tesz lehetővé a vendég felhasználók áttekintéséhez.

Tekintse át az alábbiakat:

 - Olyan csoport az Azure AD-ben, amely egy vagy több vendég tagként tagja.
 - Az Azure AD-hez csatlakoztatott alkalmazás, amelyhez egy vagy több vendég felhasználó van rendelve. 

Ezután eldöntheti, hogy az egyes vendégeknek szeretné-e áttekinteni a saját hozzáférését, vagy egy vagy több felhasználót, hogy ellenőrizze az összes vendég hozzáférését.

 Ezek a forgatókönyvek az alábbi fejezetekben találhatók.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Kérje meg a vendégeket, hogy vizsgálják felül a saját tagjaikat egy csoportban

A hozzáférési felülvizsgálatok segítségével biztosíthatja, hogy a csoportoknak meghívott és hozzáadott felhasználók továbbra is hozzáférjenek. Egyszerűen megkérheti a vendégeket, hogy tekintsék át a saját tagságát a csoportban.

1. Ha hozzáférési felülvizsgálatot szeretne létrehozni a csoport számára, válassza a felülvizsgálat lehetőséget, hogy csak a vendég felhasználói tagok szerepeljenek hozzá, és hogy a tagok maguk vizsgálják felül. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md).

2. Kérje meg az egyes vendégeket, hogy vizsgálják felül a saját tagjaikat. Alapértelmezés szerint minden olyan vendég, aki elfogadta a meghívót, e-mailt kap az Azure AD-től a hozzáférési felülvizsgálatra mutató hivatkozással. Az Azure AD utasításokkal rendelkezik a [csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintéséhez](perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md).

4. Azokon a felhasználókon kívül, akik megtagadták a felhasználóknak a folyamatos hozzáférést, eltávolíthatja azokat a felhasználókat is, akik nem válaszoltak. Nem válaszoló felhasználók számára lehetséges, hogy már nem kapnak e-mailt.

5. Ha a csoport nincs használatban a hozzáférés-kezeléshez, eltávolíthatja azokat a felhasználókat is, akik nem választották ki a részvételt a felülvizsgálatban, mert nem fogadták el a meghívót. Az elfogadás nem lehetséges, hogy a meghívott felhasználó e-mail-címe elírást kapott. Ha egy csoport terjesztési listaként van használatban, előfordulhat, hogy egyes vendég felhasználók nem voltak kijelölve a részvételre, mert az objektumok kapcsolatba lépnek.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Kérje meg a szponzort, hogy tekintse át a vendég tagságát egy csoportban

Kérhet egy szponzort, például egy csoport tulajdonosát, hogy áttekintse a vendégnek a csoportba való folyamatos tagság iránti igényét.

1. A csoport hozzáférési felülvizsgálatának létrehozásához válassza az Áttekintés lehetőséget, hogy csak a vendég felhasználói tagok szerepeljenek hozzá. Ezután meg kell adnia egy vagy több felülvizsgáló. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md).

2. Kérje meg a felülvizsgálókat, hogy nyilvánítsanak véleményt. Alapértelmezés szerint ezek mindegyike egy, a hozzáférési panelre mutató hivatkozást tartalmazó e-mailt kap az Azure AD-től, ahol [áttekintik a csoportokhoz vagy alkalmazásokhoz való hozzáférést](perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Kérje meg a vendégeket, hogy tekintsék át a saját hozzáférését egy alkalmazáshoz

A hozzáférési felülvizsgálatok segítségével biztosíthatja, hogy az adott alkalmazáshoz meghívott felhasználók továbbra is hozzáférjenek. Egyszerűen megkérheti a vendégeket, hogy tekintsék át a saját hozzáférési igényét.

1. Ha hozzáférési felülvizsgálatot szeretne létrehozni az alkalmazáshoz, válassza a felülvizsgálatot, hogy csak a vendégek szerepeljenek, és hogy a felhasználók a saját hozzáférését vizsgálják felül. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md).

2. Kérje meg az egyes vendégeket, hogy tekintsék át a saját hozzáférését az alkalmazáshoz. Alapértelmezés szerint minden olyan vendég, aki elfogadta a meghívót, e-mailt kap az Azure AD-től. Ez az e-mail a szervezet hozzáférési paneljén található hozzáférési felülvizsgálatra mutató hivatkozást tartalmaz. Az Azure AD utasításokkal rendelkezik a [csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintéséhez](perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md).

4. Azon felhasználók mellett, akik megtagadták a saját maguktól a folyamatos hozzáférést, el is távolíthatja a vendég felhasználókat, akik nem válaszoltak. Nem válaszoló felhasználók számára lehetséges, hogy már nem kapnak e-mailt. Eltávolíthat olyan vendég felhasználókat is, akiket nem választottak ki a részvételre, különösen akkor, ha nem kérték a közelmúltban. Ezek a felhasználók nem fogadták el a meghívót, így nem férnek hozzá az alkalmazáshoz. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Kérje meg a szponzort, hogy tekintse át a vendég hozzáférését egy alkalmazáshoz

Kérhet egy szponzort, például egy alkalmazás tulajdonosát, hogy ellenőrizze, hogy a vendégnek továbbra is szüksége van-e az alkalmazáshoz való folyamatos hozzáférésre.

1. Ha hozzáférési felülvizsgálatot szeretne létrehozni az alkalmazáshoz, válassza a felülvizsgálatot, hogy csak a vendégek szerepeljenek. Ezután egy vagy több felhasználót kell megadnia felülvizsgáló. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md).

2. Kérje meg a felülvizsgálókat, hogy nyilvánítsanak véleményt. Alapértelmezés szerint ezek mindegyike egy, a hozzáférési panelre mutató hivatkozást tartalmazó e-mailt kap az Azure AD-től, ahol [áttekintik a csoportokhoz vagy alkalmazásokhoz való hozzáférést](perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Kérje meg a vendégeket, hogy tekintsék át a hozzáférési igényét általánosságban

Egyes szervezeteknél előfordulhat, hogy a vendégek nem ismerik a csoport tagságát.

> [!NOTE]
> A Azure Portal korábbi verziói nem engedélyezték a felhasználók rendszergazdai hozzáférését a vendég UserType. Bizonyos esetekben előfordulhat, hogy a címtárban lévő egyik rendszergazda módosította a vendég UserType értékét a PowerShell használatával. Ha korábban ez a változás történt a címtárban, előfordulhat, hogy az előző lekérdezés nem tartalmazza az összes olyan vendég felhasználót, aki történelmileg rendszergazdai hozzáférési jogosultságokkal rendelkezik. Ebben az esetben vagy módosítania kell a vendég UserType, vagy manuálisan kell belefoglalnia a vendéget a csoporttagság között.

1. Hozzon létre egy biztonsági csoportot az Azure AD-ben a vendégek tagjaként, ha még nem létezik megfelelő csoport. Létrehozhat például egy csoportot, amely manuálisan karbantartott tagságot biztosít. Emellett létrehozhat egy olyan dinamikus csoportot is, amelynek neve, például "a contoso vendégei" a contoso-bérlő azon felhasználói számára, akik a UserType attribútum értéke vendég.  A hatékonyság érdekében győződjön meg arról, hogy a csoport elsősorban a vendégek számára van kiválasztva – ne válasszon olyan csoportot, amely tagja felhasználókkal rendelkezik, mert a tagokat nem kell áttekintenie.  Azt is vegye figyelembe, hogy a csoport többi tagja láthatja a csoport többi tagját is.

2. Az adott csoport hozzáférési felülvizsgálatának létrehozásához válassza ki a véleményezőket a tagoknak. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md).

3. Kérje meg az egyes vendégeket, hogy vizsgálják felül a saját tagjaikat. Alapértelmezés szerint minden olyan vendég kap egy e-mailt az Azure AD-től, aki elfogadta a meghívót, és a hozzáférési felülvizsgálatra mutató hivatkozást a szervezet hozzáférési paneljén. Az Azure AD utasításokkal rendelkezik a [csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintéséhez](perform-access-review.md).  Azok a vendégek, akik nem fogadták el a meghívót, megjelennek a felülvizsgálati eredményekben "nem bejelentett" néven.

4. Ha a felülvizsgálók adatokat adnak meg, állítsa le a hozzáférési felülvizsgálatot. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md).

5. Távolítsa el a vendég hozzáférést a megtagadott vendégek számára, nem fejezte be a felülvizsgálatot, vagy korábban nem fogadta el a meghívót. Ha a vendégek némelyike olyan kapcsolattartó, aki kiválasztotta a felülvizsgálatban való részvételt, vagy korábban nem fogadtak el meghívót, letilthatja a fiókjaikat a Azure Portal vagy a PowerShell használatával. Ha a vendégnek már nincs szüksége a hozzáférésre, és nem egy partner, akkor a Azure Portal vagy a PowerShell használatával törölheti a felhasználói objektumot a címtárból a vendég felhasználói objektum törléséhez.

## <a name="next-steps"></a>Következő lépések

[Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)