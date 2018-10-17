---
title: Mi az az Azure AD Privileged Identity Management? | Microsoft Docs
description: Áttekintést nyújt az Azure Active Directory Privileged Identity Managementről (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: pim
ms.topic: overview
ms.date: 03/07/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: af93ade2a7031aeda5b4108649c59a8d6c1393ce
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465860"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Mi az az Azure AD Privileged Identity Management?

Az Azure Active Directory (AD) Privileged Identity Management segítségével kezelheti, irányíthatja és felügyelheti a szervezeten belüli hozzáféréseket. Ebbe beletartozik az Azure AD, az Azure-erőforrások és más online Microsoft-szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való hozzáférés.

> [!NOTE]
> Ha engedélyezi a Privileged Identity Management használatát a bérlője számára, akkor a szolgáltatást használó vagy annak előnyeiben részesülő összes felhasználónak rendelkeznie kell egy érvényes Azure AD Premium P2 vagy az Enterprise Mobility + Security E5 licenccel, amely lehet fizetett vagy próbaverzió is. Ez azokra a felhasználókra vagy csoporthoz tartozó felhasználókra vonatkozik, akik:
>
>- Kiemelt szerepkörű rendszergazda szerepkörrel rendelkeznek 
>- Jogosultak a PIM segítségével kezelhető egyéb címtár-szerepkörök betöltésére 
>- Jogosultak kérelmek jóváhagyására vagy elutasítására a PIM-ben 
>- Egy igény szerinti vagy közvetlen (időalapú) hozzáféréssel rendelkező Azure-erőforrás-szerepkörrel rendelkeznek  
>- Egy hozzáférési felülvizsgálathoz vannak hozzárendelve
>
>További információk: [Azure Active Directory editions](../fundamentals/active-directory-whatis.md) (Azure Active Directory-kiadások).

A vállalatok igyekeznek csökkenteni a bizalmas információkhoz vagy erőforrásokhoz hozzáféréssel rendelkező személyek számát, mivel ezáltal csökkenthető annak az esélye, hogy egy rosszindulatú felhasználó hozzáférést szerezzen, vagy egy jogosult felhasználó véletlenül kárt tegyen egy bizalmas erőforrásban.  Azonban a felhasználóknak ettől függetlenül el kell végezniük bizonyos emelt szintű műveleteket az Azure AD-, Azure-, Office 365- vagy SaaS-alkalmazásokban. A vállalatoknak lehetőségük van arra, hogy emelt szintű hozzáférést adjanak a felhasználóknak olyan Azure-erőforrásokhoz, mint az előfizetések vagy az Azure AD. Ilyenkor felügyelni kell, hogy a felhasználók mire használják a rendszergazdai jogosultságaikat. Az Azure AD Privileged Identity Management segít csökkenteni a hozzáférési jogosultságok túlzott, szükségtelen vagy nem megfelelő felhasználásának kockázatát.

Az Azure AD Privileged Identity Management a következő lehetőségeket kínálja a vállalatok számára:

- Megmutatja, mely felhasználók rendelkeznek kiemelt szerepkörökkel az Azure-erőforrások felügyeletéhez, valamint mely felhasználók rendelkeznek rendszergazdai jogosultságokkal az Azure AD-ben
- Lehetővé teszi az igény szerinti rendszergazdai hozzáférést a Microsoft Online Serviceshez, például az Office 365-höz és az Intune-hoz, valamint az előfizetések, az erőforráscsoportok és az egyéni erőforrások, például a Virtual Machines Azure-erőforrásaihoz 
- Megjeleníti a rendszergazdai aktiválási előzményeket, beleértve a rendszergazdák által az Azure-erőforrásokon elvégzett módosításokat
- Riasztásokat küld a rendszergazdai hozzárendelések módosításairól
- Jóváhagyáshoz köti az Azure AD kiemelt rendszergazdai szerepköreinek aktiválását
- Felülvizsgálati lehetőséget nyújt a rendszergazdai szerepkörökhöz és kötelezi a felhasználókat arra, hogy a tagságuk fenntartásához igazolásokat mutassanak be

Az Azure AD környezetben az Azure AD Privileged Identity Management segítségével felügyelhetők a beépített Azure AD vállalati szerepkörökhöz, például a globális rendszergazdai szerepkörhöz rendelt felhasználók. Az Azure környezetben az Azure AD Privileged Identity Management segítségével felügyelhetők az Azure RBAC-szerepkörökhöz, például a tulajdonosi és közreműködői szerepkörökhöz tartozó felhasználók és csoportok.

## <a name="just-in-time-administrator-access"></a>Igény szerinti rendszergazdai hozzáférés

Hagyományosan a felhasználókat a rendszergazdai szerepkörökhöz az Azure Portalon, más Microsoft Online Services-portálokon vagy a Windows PowerShellben az Azure AD-parancsmagok használatával lehetett hozzárendelni. Ezekkel a módszerekkel a felhasználó **állandó rendszergazdává** vált, aki mindig aktív a hozzárendelt szerepkörben. Az Azure AD Privileged Identity Management bevezeti a **jogosult rendszergazda** fogalmát. Jogosult rendszergazdák azok a felhasználók lehetnek, akiknek időnként szükségük van emelt szintű hozzáférésre, de nem folyamatosan, napi szinten. A szerepkör inaktív, amíg a felhasználónak nincs szüksége a hozzáférésre, majd szükség esetén a felhasználó egy aktiválási folyamat teljesítésével válhat aktív rendszergazdává egy előre meghatározott időtartamra. Egyre több vállalat használja ezt a megközelítést a kiemelt szerepkörök állandó rendszergazdai hozzáférésének csökkentése vagy megszüntetése céljából.


## <a name="terminology"></a>Terminológia

*Jogosult szerepkörű felhasználó* – A jogosult szerepkörű felhasználók olyan felhasználók a szervezeten belül, akik jogosultként lettek az Azure AD szerepkörhöz rendelve (a szerepkört aktiválni kell).

*Meghatalmazott jóváhagyó* – A meghatalmazott jóváhagyó egy vagy több személy vagy csoport az Azure AD-n belül, akik a szerepkör-engedélyezési kérelmek jóváhagyásáért felelnek.

## <a name="scenarios"></a>Forgatókönyvek

A Privileged Identity Management a következő forgatókönyveket támogatja:

**Kiemelt szerepkörű rendszergazdaként a következőkre jogosult:**

- Jóváhagyás engedélyezése speciális szerepkörökhöz
- Kérelmek jóváhagyására jogosult felhasználók és/vagy csoportok meghatározása
- Speciális szerepkörökhöz tartozó kérelmek és jóváhagyások előzményeinek megtekintése

**Kijelölt jóváhagyóként a következőkre jogosult:**

- Függőben lévő jóváhagyások (kérelmek) megtekintése
- Szerepkör-kiterjesztésre irányuló kérelmek jóváhagyása vagy visszautasítása (egyszerre egy és/vagy több)
- Indoklás fűzése a jóváhagyáshoz/elutasításhoz 

**Jogosult szerepkörű felhasználóként a következőkre jogosult:**

- Jóváhagyást igénylő szerepkör aktiválásának kérelme
- Az aktiválási kérelem állapotának megtekintése
- Az aktiválás jóváhagyása után feladatok végrehajtása az Azure AD-ben

## <a name="enable-privileged-identity-management-for-your-directory"></a>A Privileged Identity Management engedélyezése a saját címtárhoz

Az Azure AD Privileged Identity Management használatát az [Azure Portalon](https://portal.azure.com/) lehet megkezdeni.

> [!NOTE]
> Az Azure AD Privileged Identity Management címtáron való engedélyezéséhez egy globális rendszergazdai jogosultságú vállalati fiók (például @yourdomain.com) szükséges, nem pedig egy Microsoft-fiók (például @outlook.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a címtára globális rendszergazdájaként.
2. Ha a szervezet több címtárral rendelkezik, akkor kattintson a felhasználónevére az Azure Portal jobb felső sarkában. Válassza ki azt a címtárat, amelyben az Azure AD Privileged Identity Management használatát tervezi.
3. Válassza a **Minden szolgáltatás** lehetőséget, és a Szűrő szövegmezővel keresse meg az **Azure AD Privileged Identity Management** elemet.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

Ha Ön az első, aki a címtárban az Azure AD Privileged Identity Management alkalmazást használja, akkor az Azure AD-címtárszerepkörökre lépve egy [biztonsági varázsló](pim-security-wizard.md) végigvezeti a hozzárendelés kezdeti lépésein. Ezután automatikusan Ön lesz a címtár első **biztonsági rendszergazdája** és **kiemelt szerepkörű rendszergazdája**.

Az Azure AD-szerepkörök esetében csak egy kiemelt szerepkörű rendszergazda kezelheti más rendszergazdák hozzárendeléseit az Azure AD PIM-ben. Megadhatja [más felhasználóknak a jogosultságot a címtárszerepkörök kezelésére a PIM-ben](pim-how-to-give-access-to-pim.md). A globális rendszergazdák, a biztonsági rendszergazdák és a biztonsági olvasók megtekinthetik az Azure AD-szerepkörök hozzárendeléseit az Azure AD PIM-ben.
Az Azure RBAC-szerepkörök esetében csak egy előfizetés-rendszergazda, egy erőforrás-tulajdonos vagy egy erőforrások felhasználói hozzáféréseiért felelős rendszergazda felügyelheti más rendszergazdák hozzárendeléseit az Azure AD PIM-ben.  A kiemelt szerepkörű rendszergazdák, a biztonsági rendszergazdák és a biztonsági olvasók alapértelmezés szerint nem rendelkeznek hozzáféréssel az Azure RBAC-szerepkörökhöz való hozzárendelések megtekintéséhez az Azure AD PIM-ben.

## <a name="privileged-identity-management-overview-entry-point"></a>A Privileged Identity Management áttekintése (belépési pont)

Az Azure AD Privileged Identity Management támogatja az Azure AD-címtárakhoz tartozó szerepkörök és az Azure-erőforrások szerepköreinek adminisztrációját. Az Azure-erőforrások szerepkörei más funkciókat töltenek be, mint az Azure AD rendszergazdai szerepkörei. Az Azure-erőforrások szerepkörei részletes engedélyeket biztosítanak a hozzájuk tartozó erőforrásokhoz és azok összes alárendelt erőforrásához is az erőforrás-hierarchiában (ezt más néven öröklődésnek nevezzük). [További tudnivalók az RBAC-ről, az erőforrás-hierarchiáról és az öröklődésről](../../role-based-access-control/role-assignments-portal.md) Az Azure AD-címtár-szerepkörökhöz és az Azure-erőforrások szerepköreihez a PIM egyaránt a PIM Áttekintés belépési pontjának bal oldali navigációs menüjében, a Kezelés szakasz alatti megfelelő hivatkozáson keresztül érhető el.

A PIM kényelmes hozzáférést biztosít a szerepkörök aktiválásához, megjeleníti a függőben lévő aktiválásokat/kérelmeket, a függőben lévő jóváhagyásokat (az Azure AD-címtár-szerepkörökhöz) és a válaszra váró felülvizsgálatokat a bal oldali navigációs menü Feladatok szakaszán keresztül.

Ha az Áttekintés belépési pontjáról megnyitja a Feladatok menü bármely elemét, a megjelenő nézetben az Azure AD-címtár-szerepkörökre és az Azure-erőforrások szerepköreire vonatkozó eredmények is megjelennek.

![Első lépések](./media/pim-configure/quick-start.png)

A Saját szerepkörök oldalon található az Azure AD-címtár-szerepkörökhöz és az Azure-erőforrások szerepköreihez tartozó aktív és jogosult szerepkör-hozzárendelések listája. [További információk a jogosult szerepkör-hozzárendelések aktiválásáról](pim-how-to-activate-role.md)

Az Azure-erőforrások szerepkörei aktiválásának van egy új módja, amelynek segítségével a szerepkörök jogosult tagjai ütemezhetik a szerepkörök aktiválását egy jövőbeli dátumra és időpontra, illetve megadhatnak egy aktiválási időtartamot, a rendrendszergazdák által engedélyezett maximális értéken belül.

![](./media/pim-configure/activations.png)

Ha egy ütemezett aktiválásra már nincs szükség, a felhasználók visszavonhatják a függőben lévő kérelmüket a bal oldali navigációs menüben a függőben lévő kérelmek oldalára lépve, majd az adott kérelemmel egy sorban található Mégse gombra kattintva.

![Függőben lévő kérések](./media/pim-configure/pending-requests.png)

## <a name="privileged-identity-management-admin-dashboard"></a>A Privileged Identity Management rendszergazdai irányítópultja

Az Azure AD Privileged Identity Management rendszergazdai irányítópultján a következő fontos információk jelennek meg:

* A biztonság javításának lehetőségeire figyelmeztető riasztások
* Az egyes kiemelt szerepkörökhöz rendelt felhasználók száma  
* A jogosult és az állandó rendszergazdák száma
* Egy grafikon a kiemelt szerepkörök aktiválásáról a címtárban
* Az Azure-erőforrások szerepköreihez tartozó igény szerinti, időhöz kötött és állandó hozzárendelések száma
* Az elmúlt 30 nap során új szerepkör-hozzárendeléseket kapott felhasználók és csoportok (Azure-erőforrás-szerepkörök)


![A PIM irányítópultja – képernyőkép](./media/pim-configure/PIM_Admin_Overview.png)

## <a name="privileged-role-management"></a>A kiemelt szerepkörök kezelése

Az Azure AD Privileged Identity Management segítségével úgy kezelheti a rendszergazdákat, hogy állandó vagy jogosult rendszergazdákat adhat hozzá vagy távolíthat el az egyes Azure AD-címtár-szerepkörökből. Az Azure-erőforrásokhoz készült PIM használatával a tulajdonosok, a felhasználói hozzáférésekért felelős rendszergazdák és a globális rendszergazdák, akik engedélyezték az előfizetések kezelését a bérlőjükben, felhasználókat vagy csoportokat rendelhetnek hozzá az Azure-erőforrás-szerepkörökhöz. A felhasználók vagy csoportok lehetnek jogosultak (az igény szerinti eléréséhez), kaphatnak időhöz kötött hozzáférést egy adott kezdő és befejező időpont között (ehhez nem szükséges aktiválás), vagy rendelkezhetnek állandó hozzáféréssel (ha ez engedélyezve van a szerepkör-beállítások között).

![Rendszergazdák hozzáadása/eltávolítása a PIM-ben – képernyőkép](./media/pim-configure/PIM_AddRemove.png)

## <a name="configure-the-role-activation-settings"></a>A szerepkör-aktiválási beállítások konfigurálása

A [szerepkör-beállítások](pim-how-to-change-default-settings.md) használatával megadhatók a jogosult Azure AD-címtárszerepkörök aktiválásának következő tulajdonságai:

* A szerepkör aktiválásának időtartama
* A szerepkör aktiválásáról szóló értesítés
* Az információk, amelyeket a felhasználóknak meg kell adniuk a szerepkör aktiválásának folyamata során
* A szolgáltatásjegy vagy esemény száma
* [A jóváhagyási munkafolyamat követelményei](./azure-ad-pim-approval-workflow.md)

![A PIM beállításai – rendszergazda aktiválása – képernyőkép](./media/pim-configure/PIM_Settings_w_Approval_Disabled.png)

Figyelje meg, hogy a képen a **többtényezős hitelesítés** gombjai le vannak tiltva. Bizonyos magas jogosultságú szerepkörök esetében szükséges a többtényezős hitelesítés a fokozott védelem érdekében.

Az Azure-erőforrások szerepköreinek beállításaiban a rendszergazdák konfigurálhatják az igény szerinti és a közvetlen hozzárendelés beállításait, így a következőket is:

- Képesség a felhasználók vagy csoportok szerepkörökhöz való hozzárendelésére befejező dátum/időpont nélkül (állandó hozzárendelés)
- A hozzárendelések alapértelmezett időtartama (ha nem állandó)
- A maximális aktiválási időtartam (amikor egy jogosult szerepkör egy tagja aktiválja magát)
- Az információk, amelyeket a felhasználóknak meg kell adniuk a szerepkör aktiválásának folyamata során (igény szerinti hozzárendelések esetén) vagy a hozzárendelés folyamata során (közvetlen hozzárendelések esetén)

![](./media/pim-configure/role-settings-details.png)

## <a name="role-activation"></a>Szerepkörök aktiválása

Egy [szerepkör aktiválásához](pim-how-to-activate-role.md) egy jogosult rendszergazdának egy időhöz kötött „aktiválást” kell kérnie az adott szerepkörhöz. Az aktiválást az Azure AD Privileged Identity Managementben a **Saját szerepkör aktiválása** lehetőséggel lehet kérelmezni.

Ha egy rendszergazda aktiválni kíván egy szerepkört, inicializálnia kell az Azure AD Privileged Identity Managementet az Azure Portalon.

A szerepkörök aktiválása testre szabható. A PIM beállításaiban meg lehet határozni az aktiválás időtartamát, és hogy a rendszergazdának milyen adatokat kell megadnia a szerepkör aktiválásához.

![Rendszergazdai szerepkör aktiválásának kérelme a PIM-ben – képernyőkép](./media/pim-configure/PIM_RequestActivation.png)

## <a name="review-role-activity"></a>Szerepkör-tevékenységek áttekintése

Kétféle módon követhető nyomon, hogy az alkalmazottak és a rendszergazdák mire használják a kiemelt szerepköröket: Az első lehetőség a [Címtárszerepkörök előzménynaplójának](pim-how-to-use-audit-log.md) megtekintése. Az előzménynaplók nyomon követik a kiemelt szerepkörök hozzárendeléseit, a szerepkör-aktiválási előzményeket és az Azure-erőforrások szerepköreinek beállításait érintő módosításokat. 

![Aktiválási előzmények a PIM-ben – képernyőkép](./media/pim-configure/PIM_ActivationHistory.png)

A második lehetőség a rendszeres [hozzáférési felülvizsgálatok](pim-how-to-start-security-review.md) bevezetése. Ezeket a hozzáférési felülvizsgálatokat elvégezheti egy hozzárendelt felülvizsgáló (például egy csapatvezető), vagy az alkalmazottak saját magukat is felülvizsgálhatják. Ez a legjobb módszer annak monitorozására, hogy kinek van szüksége továbbra is hozzáférésre, és kinek nincs.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Az Azure AD PIM az előfizetések lejártakor

A bérlőknek rendelkezniük kell egy próbaverziós vagy fizetett, prémium szintű Azure AD P2- (vagy EMS E5-) előfizetéssel az Azure AD PIM használatához.  Ezenfelül licenceket kell hozzárendelni a bérlő rendszergazdáihoz.  Pontosabban az Azure AD PIM-mel felügyelt Azure AD-szerepkörökhöz tartozó rendszergazdákhoz, az Azure AD PIM-mel felügyelt Azure RBAC-szerepkörökhöz tartozó rendszergazdákhoz, valamint minden nem rendszergazda felhasználóhoz, aki hozzáférési felülvizsgálatokat végez.
Ha a vállalat nem újítja meg a Prémium szintű Azure AD P2-előfizetést, vagy ha a próbaidőszak lejárt, a bérlőben többé nem lesznek elérhetők az Azure AD PIM-funkciók, a rendszer eltávolítja a jogosult szerepkör-hozzárendeléseket, és a felhasználók nem lesznek képesek szerepköröket aktiválni. További információ: [Az Azure AD PIM előfizetési követelményei](./subscription-requirements.md)

## <a name="next-steps"></a>További lépések

- [Előfizetési követelmények a PIM használatához](subscription-requirements.md)
- [A PIM-ben kezelhető Azure AD-címtárszerepkörök](pim-roles.md)
- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
