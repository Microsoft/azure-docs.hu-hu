---
title: Ajánlott eljárások az Azure AD-szerepkörökhöz – Azure Active Directory
description: Ajánlott eljárások Azure Active Directory szerepkörök használatához.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f4642b8f9f1ede65766a0026940c0af0f01ec2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111779"
---
# <a name="best-practices-for-azure-ad-roles"></a>Ajánlott eljárások az Azure AD-szerepkörökhöz

Ez a cikk a Azure Active Directory szerepköralapú hozzáférés-vezérlés (Azure AD RBAC) használatának ajánlott eljárásait ismerteti. Ezek az ajánlott eljárások az Azure AD RBAC és az ügyfelek, például saját felhasználói élmények tapasztalataiból származnak. Javasoljuk, hogy olvassa el a részletes biztonsági útmutatást is, amely az [Azure ad-ben a hibrid és a Felhőbeli üzembe helyezésre vonatkozó emelt szintű hozzáférést biztosítja](security-planning.md).

## <a name="1-manage-to-least-privilege"></a>1. felügyelet a legalacsonyabb jogosultsággal

A hozzáférés-vezérlési stratégia megtervezése során ajánlott eljárás a minimális jogosultságok kezelése. A legalacsonyabb jogosultság azt jelenti, hogy a rendszergazdáknak pontosan a feladatuk elvégzéséhez szükséges engedélyt kell megadniuk. Három szempontot kell figyelembe venni, amikor szerepköröket rendel hozzá a rendszergazdákhoz: egy adott hatókörön belül egy adott időszakra vonatkozó engedélyek meghatározott készletét. Ne rendeljen szélesebb hatókörű szerepköröket, még akkor is, ha az kezdetben kényelmesebbnek tűnik. A szerepkörök és hatókörök korlátozásával korlátozhatja, hogy milyen erőforrásokra van veszélye, ha a rendszerbiztonsági tag még mindig biztonságban van. Az Azure AD-RBAC több mint 65 [beépített szerepkört](permissions-reference.md)támogat. Azure AD-szerepkörökkel kezelhetők a címtár-objektumok, például a felhasználók, csoportok és alkalmazások, valamint Microsoft 365 szolgáltatások (például az Exchange, a SharePoint és az Intune) felügyelete is. Az Azure AD beépített szerepköreinek jobb megismeréséhez lásd: [szerepkörök megismerése Azure Active Directoryban](concept-understand-roles.md). Ha nincs olyan beépített szerepkör, amely megfelel az Ön igényeinek, létrehozhat saját [Egyéni szerepköröket](custom-create.md)is.  
 
### <a name="finding-the-right-roles"></a>A megfelelő szerepkörök megkeresése

A megfelelő szerepkör megtalálásához kövesse az alábbi lépéseket.

1. A Azure Portalban nyissa meg a [szerepkörök és rendszergazdák](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) lehetőséget az Azure ad-szerepkörök listájának megtekintéséhez.

1. A **szolgáltatás** szűrő használatával Szűkítse le a szerepkörök listáját.

    ![Szerepkörök és rendszergazdák lap az Azure AD-ben a Service Filter megnyitva](./media/best-practices/roles-administrators.png)

1. Tekintse meg az [Azure ad beépített szerepköreinek](permissions-reference.md) dokumentációját. Az egyes szerepkörökhöz társított engedélyek a jobb olvashatóság érdekében vannak felsorolva. A szerepkör-engedélyek szerkezetének és jelentésének megismeréséhez lásd: [a szerepkör engedélyeinek megismerése](permissions-reference.md#how-to-understand-role-permissions).

1. A feladat dokumentációjában tekintse meg a [legkevésbé Kiemelt szerepkört](delegate-by-task.md) .

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. a Privileged Identity Management használata az igény szerinti hozzáférés biztosításához

A legalacsonyabb jogosultsági szintű alapelvek egyike az, hogy a hozzáférést csak adott időtartamra kell megadni. [Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) segítségével igény szerinti hozzáférést biztosíthat a rendszergazdáknak. A Microsoft azt javasolja, hogy engedélyezze a PIM használatát az Azure AD-ben. A PIM használatával egy felhasználó jogosult tagja lehet egy Azure AD-szerepkörnek. A ezután a saját szerepkörét egy korlátozott időkeretre is aktiválhatja, amikor a használatot igényli. A rendszerjogosultságú hozzáférés automatikusan törlődik, amikor lejár az időkeret. A [PIM beállításait úgy is konfigurálhatja](../privileged-identity-management/pim-how-to-change-default-settings.md) , hogy jóváhagyást kérjen, vagy értesítő e-maileket kapjon, ha valaki aktiválja a szerepkör-hozzárendelést. Az értesítések riasztást küldenek, ha új felhasználókat adnak hozzá a magas jogosultsági szintű szerepkörökhöz. 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. a multi-Factor Authentication bekapcsolása az összes rendszergazdai fiókhoz

[Tanulmányok alapján](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)a fiókja 99,9%-kal kevésbé valószínű, ha többtényezős HITELESÍTÉST (MFA) használ. 
 
Az MFA-t az Azure AD szerepkörein két módszerrel engedélyezheti:
- [Szerepkör-beállítások](../privileged-identity-management/pim-how-to-change-default-settings.md) a Privileged Identity Management
- [Feltételes hozzáférés](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. konfigurálja az ismétlődő hozzáférési felülvizsgálatokat, hogy az idő múlásával visszavonja a szükségtelen engedélyeket

A hozzáférési felülvizsgálatok lehetővé teszik a szervezetek számára, hogy rendszeresen vizsgálják felül a rendszergazda hozzáférését, hogy csak a megfelelő személyek férhessenek hozzájuk. A rendszergazdák rendszeres naplózása a következő okok miatt létfontosságú:
- Egy rosszindulatú színész veszélyeztetheti a fiókot.
- A csapatok egy vállalaton belül mozognak. Ha nincs naplózás, akkor a szükségtelen hozzáférés az idő múlásával.
 
A szerepkörök hozzáférési felülvizsgálatával kapcsolatos információkért lásd: az [Azure ad-szerepkörök hozzáférési felülvizsgálatának létrehozása a PIM-ben](../privileged-identity-management/pim-how-to-start-security-review.md). További információ a szerepkörökhöz hozzárendelt csoportok hozzáférési [felülvizsgálatáról: csoportok és alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure ad hozzáférési](../governance/create-access-review.md)felülvizsgálatokban.

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. korlátozza a globális rendszergazdák számát 5-nél kevesebbre

Ajánlott eljárásként a Microsoft azt javasolja, hogy a globális rendszergazdai szerepkört a szervezetnél **kevesebb mint öt** személyhez rendelje. A globális rendszergazdák a Királyság számára biztosítják a kulcsokat, és az Ön érdeke, hogy a támadási felület alacsony legyen. Ahogy azt korábban említettük, az összes fiók védelmét a többtényezős hitelesítéssel kell védeni.

Alapértelmezés szerint, amikor egy felhasználó regisztrál a Microsoft Cloud Service szolgáltatásra, létrejön egy Azure AD-bérlő, és a felhasználó tagja lesz a globális rendszergazdák szerepkörnek. A globális rendszergazdai szerepkörrel rendelkező felhasználók elolvashatják és módosíthatják az Azure AD-szervezet minden felügyeleti beállítását. Néhány kivételtől eltekintve a globális rendszergazdák is elolvashatják és módosíthatják az Microsoft 365 szervezet összes konfigurációs beállítását. A globális rendszergazdák emellett lehetőségük van arra is, hogy az olvasási adatbevitelre is hozzáférjenek.

A Microsoft azt javasolja, hogy tartsa meg a globális rendszergazdai szerepkörhöz véglegesen hozzárendelt két break Glass-fiókot. Győződjön meg arról, hogy ezek a fiókok nem igénylik ugyanazt a többtényezős hitelesítési mechanizmust, mint a normál rendszergazdai fiókok a bejelentkezéshez, a következő témakörben leírtak szerint: az [Azure ad-beli segélyhívó hozzáférési fiókok kezelése](../roles/security-emergency-access.md) 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. csoportok használata az Azure AD szerepkör-hozzárendelésekhez és a szerepkör-hozzárendelés delegálásához

Ha olyan külső irányítási rendszerrel rendelkezik, amely kihasználja a csoportokat, akkor az egyes felhasználók helyett érdemes lehet szerepköröket hozzárendelni az Azure AD-csoportokhoz. Felügyelheti a PIM szerepkör-hozzárendelésre jogosult csoportjait is, így biztosíthatja, hogy ne legyenek állandó tulajdonosok vagy tagok ezekben a Kiemelt csoportokban. További információ: [felügyeleti képességek a Privileged Access Azure ad-csoportokhoz](../privileged-identity-management/groups-features.md).

Tulajdonost hozzárendelheti a szerepkörhöz hozzárendelhető csoportokhoz. A tulajdonos dönti el, hogy ki adja hozzá vagy távolítja el a csoportot, ezért közvetve nem határozza meg, hogy ki kapja meg a szerepkör-hozzárendelést. Így a globális rendszergazda vagy a Kiemelt szerepkörű rendszergazda szerepkör-felügyeletet delegálhat szerepkörök alapján a csoportok használatával. További információ: [felhőalapú csoportok használata a szerepkör-hozzárendelések Azure Active Directoryban való kezeléséhez](groups-concept.md).

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. több szerepkör aktiválása egyszerre Kiemelt hozzáférési csoportok használatával

Előfordulhat, hogy az egyes személyeknek öt vagy hat jogosult hozzárendelése van az Azure AD-szerepkörökhöz a PIM használatával. Mindegyik szerepkört egyenként kell aktiválni, ami csökkentheti a hatékonyságot. Ami még rosszabb, akár több tízezer Azure-erőforráshoz is hozzájuk rendelve, ami súlyosbítja a problémát.
 
Ebben az esetben az emelt [szintű hozzáférési csoportokat](../privileged-identity-management/groups-features.md)kell használnia. Hozzon létre egy emelt szintű hozzáférési csoportot, és biztosítson állandó hozzáférést több szerepkörhöz (Azure AD és/vagy Azure). Ezt a felhasználót a csoport jogosult tagjának vagy tulajdonosának kell lennie. Csak egy aktiválással férhet hozzá az összes kapcsolódó erőforráshoz.

![Kiemelt jogosultságú hozzáférési csoport diagramja, amely egyszerre több szerepkör aktiválását mutatja be](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. felhőalapú natív fiókok használata Azure AD-szerepkörökhöz

Kerülje a helyszíni szinkronizált fiókok használatát az Azure AD szerepkör-hozzárendelésekhez. Ha a helyszíni fiók biztonsága sérül, az Azure AD-erőforrásait is veszélyeztetheti.

## <a name="next-steps"></a>Következő lépések

- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](security-planning.md)