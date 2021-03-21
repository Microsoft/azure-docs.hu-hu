---
title: Hibrid Identity design – életciklus-bevezetési stratégia az Azure-ban | Microsoft Docs
description: Segít meghatározni a hibrid Identitáskezelés feladatait az egyes életciklus-fázisokhoz elérhető beállításoknak megfelelően.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf68406d4b0806e1d533e0bb8669a01939387989
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997749"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Hibrid identitás-életciklus bevezetési stratégiájának meghatározása
Ebben a feladatban a hibrid identitási megoldáshoz tartozó identitáskezelési stratégiát kell megadnia, hogy megfeleljen a [hibrid identitás-kezelési feladatok meghatározása](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)során meghatározott üzleti követelményeknek.

Ha meg szeretné határozni a hibrid Identitáskezelés feladatait az ebben a lépésben korábban bemutatott végpontok közötti identitási életciklusnak megfelelően, akkor figyelembe kell vennie az egyes életciklus-fázisokhoz elérhető lehetőségeket.

## <a name="access-management-and-provisioning"></a>Hozzáférés-kezelés és-kiépítés
A megfelelő fiók-hozzáférés-kezelési megoldással a szervezet pontosan nyomon követheti, hogy ki férhet hozzá a szervezethez tartozó információkhoz.

A hozzáférés-vezérlés egy központosított, egypontos kiépítési rendszer kritikus funkciója. A bizalmas információk védelme mellett a hozzáférés-vezérlések lehetővé teszik a nem jóváhagyott engedélyekkel rendelkező meglévő fiókok vagy a továbbiakban nem szükségesek védelmét. Az elavult fiókok szabályozása érdekében a kiépítési rendszerek a fiók adataival együtt összekapcsolják az adott fiókkal rendelkező felhasználókra vonatkozó mérvadó információkat. A mérvadó felhasználói azonosító adatokat általában az emberi erőforrások adatbázisaiban és könyvtáraiban tartják karban.

A kifinomult informatikai vállalatok fiókjai több száz paramétert tartalmaznak, amelyek meghatározzák a hatóságokat, és ezeket az adatokat a kiépítési rendszer is szabályozhatja. Az új felhasználók azonosíthatók a mérvadó forrásból származó adatokkal. A hozzáférési kérelem jóváhagyási funkciója kezdeményezi az erőforrások kiépítését jóváhagyó (vagy elutasító) folyamatokat.

| Életciklus-kezelési fázis | A helyszínen | Felhőbeli | Hibrid |
| --- | --- | --- | --- |
| Fiókok kezelése és kiépítés |Az Active Directory® tartományi szolgáltatások (AD DS) kiszolgálói szerepkör segítségével méretezhető, biztonságos és kezelhető infrastruktúrát hozhat létre a felhasználó- és erőforrás-kezeléshez, és támogatást nyújthat olyan címtárhasználatra képes alkalmazásokhoz, mint a Microsoft® Exchange Server. <br><br> [A AD DS egy Identity Manageren keresztül is kiépítheti a csoportokat](/previous-versions/mim/ff686261(v=ws.10)) <br>[A felhasználókat kiépítheti AD DS](/previous-versions/mim/ff686263(v=ws.10)) <br><br> A rendszergazdák a hozzáférés-vezérlés használatával biztonsági okokból kezelhetik a felhasználók hozzáférését a megosztott erőforrásokhoz. Active Directory a hozzáférés-vezérlést az objektum szintjén kell felügyelni, ha különböző hozzáférési szinteket, vagy engedélyeket, objektumokat, például teljes vezérlést, írást, olvasást vagy nincs hozzáférést kíván beállítani. A Active Directory hozzáférés-vezérlése határozza meg, hogy a különböző felhasználók hogyan használhatják Active Directory objektumokat. Alapértelmezés szerint a Active Directoryban lévő objektumokra vonatkozó engedélyek a legbiztonságosabb beállításra vannak beállítva. |Létre kell hoznia egy fiókot minden olyan felhasználó számára, aki hozzáfér a Microsoft Cloud Service-hez. Módosíthatja a felhasználói fiókokat is, vagy törölheti őket, ha már nincs rájuk szükség. Alapértelmezés szerint a felhasználók nem rendelkeznek rendszergazdai engedélyekkel, de igény szerint is hozzárendelhetők. <br><br> Azure Active Directory belül az egyik fő funkció az erőforrásokhoz való hozzáférés kezelése. Ezek az erőforrások képezhetik a címtár részét, például az objektumok kezelésére szolgáló engedélyek esetében, amelyeket a címtár szerepkörei biztosítanak, vagy lehetnek a címtáron kívüli erőforrások, például SaaS-alkalmazások, Azure-szolgáltatások, SharePoint-webhelyek vagy helyszíni erőforrások. <br><br> Azure Active Directory hozzáférés-kezelési megoldásának középpontjában a biztonsági csoport. Az erőforrás tulajdonosa (vagy a címtár rendszergazdája) hozzárendelhet egy csoportot a tulajdonában lévő erőforrásokhoz adott hozzáférési jogosultság biztosításához. A csoport tagjai megadják a hozzáférést, és az erőforrás tulajdonosa átruházhatja a jogot arra, hogy egy csoport tagjainak listáját felügyelje valaki másnak – például egy részleg kezelője vagy egy segélyszolgálat rendszergazdája<br> <br> A csoportok kezelése az Azure AD-ben című rész további információkat nyújt a hozzáférés csoportokon keresztül történő kezeléséről. |Active Directory identitások kiterjesztése a felhőbe szinkronizálás és összevonás használatával |

## <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés
Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szerepköröket és kiépítési szabályzatokat használ az üzleti folyamatok kiértékeléséhez, teszteléséhez és érvénybe léptetéséhez, valamint a felhasználókhoz való hozzáférés biztosításához. A kulcsfontosságú rendszergazdák kiépítési házirendeket hoznak létre, és felhasználókat rendelnek hozzá a szerepkörökhöz, amelyek a jogosultságok készleteit határozzák meg a szerepkörök erőforrásaihoz. Az Azure RBAC kibővíti az Identitáskezelés megoldását a szoftveres folyamatok használatához, és csökkenti a felhasználói manuális interakciót a kiépítési folyamat során.
Az Azure RBAC lehetővé teszi, hogy a vállalat korlátozza azon műveletek számát, amelyeket az egyes felhasználók a Azure Portalhoz való hozzáférésük után elvégezhetnek. Ha az Azure RBAC használatával szabályozza a portálhoz való hozzáférést, a rendszergazdák hitelesítésszolgáltatója delegálja a hozzáférést a következő hozzáférés-kezelési megközelítésekkel:

* **Csoport alapú szerepkör-hozzárendelés**: a helyi Active Directoryról szinkronizálható Azure ad-csoportokhoz is hozzárendelhet hozzáférést. Így kihasználhatja a szervezete által a csoportok kezelésére szolgáló eszközök és folyamatok során létrehozott meglévő befektetéseket. Használhatja a prémium szintű Azure AD meghatalmazott csoportjának felügyeleti szolgáltatását is.
* **Beépített szerepkörök használata az Azure-ban**: három szerepkört – tulajdonost, közreműködőt és olvasót – használhat, így biztosítva, hogy a felhasználók és a csoportok csak a feladataik elvégzéséhez szükséges feladatokat használják.
* **Az erőforrásokhoz való részletes hozzáférés**: szerepköröket rendelhet hozzá felhasználókhoz és csoportokhoz egy adott előfizetéshez, erőforráscsoporthoz vagy egyéni Azure-erőforráshoz, például webhelyhez vagy adatbázishoz. Így biztosíthatja, hogy a felhasználók hozzáférhessenek az összes szükséges erőforráshoz, és ne férhessenek hozzájuk a nem szükséges erőforrásokhoz.

## <a name="provisioning-and-other-customization-options"></a>Kiépítés és egyéb testreszabási lehetőségek
A csapata üzleti terveket és követelményeket is felhasználhat annak eldöntésére, hogy mekkora mértékben szabhatja testre az identitási megoldást. Egy nagyméretű vállalat például a munkafolyamatok és az egyéni adapterek szakaszos kiépítési tervét igényli, amely egy időben, a földrajzilag széles körben használt alkalmazások fokozatos kiépítéséhez szükséges. A sikeres tesztelés után egy másik testreszabási terv is megadhatja, hogy két vagy több alkalmazás legyen kiépítve egy teljes szervezeten belül. A felhasználó és az alkalmazás közötti interakció testreszabható, és az erőforrások kiépítési eljárásai úgy módosíthatók, hogy az automatikus kiépítés is alkalmazkodjon.

Törölheti a szolgáltatást vagy az összetevőt. Például a fiók megszüntetése azt jelenti, hogy a fiók törlődik egy erőforrásból.

A kiépítési erőforrások hibrid modellje ötvözi a kérelmeket és a szerepköralapú megközelítéseket, amelyek az Azure AD által támogatottak. Az alkalmazottak vagy a felügyelt rendszerek egy részhalmaza esetében előfordulhat, hogy a vállalat a szerepköralapú hozzárendeléssel automatizálni kívánja a hozzáférést. Egy vállalat az összes többi hozzáférési kérelmet vagy kivételt egy kérelem alapú modellen keresztül is kezelheti. Előfordulhat, hogy egyes vállalatok manuális hozzárendeléssel kezdődnek, és a hibrid modell irányába fejlődnek, és egy teljes körű szerepköralapú üzembe helyezést kívánnak kialakítani egy későbbi időpontban.

Előfordulhat, hogy más vállalatok az üzleti okok miatt nem tudják megvalósítani a teljes szerepköralapú kiépítés célját, és egy hibrid megközelítést céloznak meg. Továbbra is előfordulhat, hogy a többi vállalat csak a kérelmeken alapuló üzembe helyezést látja el, és nem kíván további erőfeszítéseket tenni a szerepköralapú, automatizált kiépítési házirendek definiálására és felügyeletére.

## <a name="license-management"></a>Licenckezelés
Az Azure AD-beli csoportos licencelési szolgáltatás lehetővé teszi, hogy a rendszergazdák a felhasználókat egy biztonsági csoporthoz rendelik, és az Azure AD automatikusan hozzárendeli a licenceket a csoport összes tagjához. Ha a felhasználót később hozzáadják vagy eltávolítják a csoportból, a rendszer automatikusan hozzárendeli vagy eltávolítja a licencet.

A helyszíni AD-ből vagy az Azure AD-ben való felügyeletből szinkronizálhatja a csoportokat. Ha a prémium szintű Azure AD-vel párosítja Self-Service a csoport kezelését, egyszerűen delegálhatja a licenc-hozzárendelést a megfelelő döntéshozóknak. Biztos lehet benne, hogy a rendszer automatikusan kirendezi a licencek és a hiányzó helyadatok közötti problémákat.

## <a name="self-regulating-user-administration"></a>Önszabályozó felhasználói felügyelet
Ha a szervezete elkezdi az erőforrások kiépítését az összes belső szervezeten belül, az önszabályozó felhasználói felügyeleti képességet is végrehajtja. A felhasználók szervezeti határokon átívelő kiépítési előnyei és előnyei is felhasználhatók. Ebben a környezetben a felhasználó állapotának változása automatikusan tükröződik a szervezeti határok és földrajzi területek hozzáférési jogosultságai között. Csökkentheti a kiépítési költségeket, és egyszerűsítheti a hozzáférési és jóváhagyási folyamatokat. A megvalósítás teljes körűen kihasználhatja a szerepköralapú hozzáférés-vezérlés megvalósításának lehetőségeit a szervezet végpontok közötti hozzáférés-kezeléséhez. A felhasználói kiépítés szabályozására szolgáló automatizált eljárásokat követve csökkentheti az adminisztrációs költségeket. A biztonsági házirendek kényszerítésének automatizálásával, valamint a nagyméretű felhasználói populációk használatának egyszerűsítésével és központosításával növelheti a biztonságot.

> [!NOTE]
> További információ: az Azure AD beállítása önkiszolgáló alkalmazás-hozzáférés kezeléséhez
> 
> 

A licenc-alapú (jogosultság-alapú) Azure AD-szolgáltatások működéséhez aktiválja az előfizetést az Azure AD-címtárban/szolgáltatás-bérlőben. Ha az előfizetés aktív, a szolgáltatás funkcióit a címtár/szolgáltatás-rendszergazdák kezelhetik, és a licenccel rendelkező felhasználók használják. 

## <a name="integration-with-other-3rd-party-providers"></a>Integráció más külső szolgáltatókkal

Azure Active Directory lehetővé teszi az egyszeri bejelentkezést és a továbbfejlesztett alkalmazás-hozzáférési biztonságot több ezer SaaS-alkalmazás és helyszíni webalkalmazás számára. További információ: [alkalmazások integrálása a Azure Active Directory](../develop/quickstart-register-app.md)

## <a name="define-synchronization-management"></a>Szinkronizálási kezelés definiálása
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások hozzáféréséhez közös identitás áll a rendelkezésükre. Ezzel az integrációval a felhasználók és a szervezetek a következőket vehetik igénybe:

* A szervezetek közös hibrid identitással biztosíthatják a felhasználók számára a Windows Server Active Directoryt használó helyszíni vagy felhőalapú szolgáltatások, majd a Azure Active Directoryhoz való csatlakozást.
* A rendszergazdák feltételes hozzáférést biztosíthatnak az alkalmazás-erőforrás, az eszköz és a felhasználói identitás, a hálózati hely és a többtényezős hitelesítés alapján.
* A felhasználók a közös identitást az Azure AD-fiókokkal Microsoft 365, Intune-ban, SaaS-alkalmazásokban és külső alkalmazásokban is használhatják.
* A fejlesztők olyan alkalmazásokat készíthetnek, amelyek kihasználják a közös identitási modellt, az alkalmazások integrálását Active Directory helyszíni vagy Azure-beli felhőalapú alkalmazások számára

Az alábbi ábrán egy példa látható az identitás-szinkronizálási folyamat magas szintű nézetére.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

identitásszinkronizálás folyamat

A szinkronizálási beállítások összehasonlításához tekintse át a következő táblázatot:

| Szinkronizálási kezelési beállítás | Előnyök | Hátrányok |
| --- | --- | --- |
| Szinkronizálás-alapú (az rSync vagy a AADConnect használatával) |A helyszíni és a felhőből szinkronizált felhasználók és csoportok <br>  **Házirend-vezérlés**: a fiókházirend a Active Directoryon keresztül állítható be, amely lehetővé teszi a rendszergazda számára a jelszóházirendek, a munkaállomások, a korlátozások, a zárolási vezérlők és egyéb feladatok kezelését anélkül, hogy további feladatokat kellene végrehajtania a felhőben.  <br>  **Hozzáférés-vezérlés**: a felhőalapú szolgáltatáshoz való hozzáférést korlátozhatja, hogy a szolgáltatások elérhetők legyenek a vállalati környezetben, az online kiszolgálókon vagy mindkettőn keresztül. <br>  Csökkentett támogatási hívások: Ha a felhasználóknak kevesebb jelszava van, akkor kevésbé valószínű, hogy elfelejtik őket. <br>  Biztonság: a felhasználói identitások és információk védettek, mivel az egyszeri bejelentkezéshez használt összes kiszolgáló és szolgáltatás a helyszínen van elsajátítva és felügyelve. <br>  Erős hitelesítés támogatása: erős hitelesítést (más néven kétfaktoros hitelesítést) is használhat a felhőalapú szolgáltatással. Erős hitelesítés használata esetén azonban egyszeri bejelentkezést kell használnia. | |
| Összevonási alapú (AD FS) |A biztonsági jogkivonat szolgáltatás (STS) engedélyezte. Ha úgy konfigurálja az STS-t, hogy egyszeri bejelentkezéses hozzáférést biztosítson egy Microsoft Cloud Service-szolgáltatáshoz, összevont megbízhatósági kapcsolatot kell létrehoznia a helyszíni STS és az Azure AD-bérlőben megadott összevont tartomány között. <br> Lehetővé teszi a végfelhasználók számára, hogy ugyanazokat a hitelesítő adatokat használják több erőforrás elérésének megszerzéséhez <br>a végfelhasználóknak nem kell több hitelesítőadat-készletet fenntartaniuk. A felhasználóknak azonban meg kell adniuk a hitelesítő adataikat a résztvevő összes erőforrásához. a B2B és B2C forgatókönyvek támogatottak. |Speciális személyzetet igényel a dedikált helyszíni AD FS-kiszolgálók üzembe helyezéséhez és karbantartásához. Ha azt tervezi, hogy AD FSt használ az STS-hez, az erős hitelesítés használata korlátozott. További információ: [Speciális beállítások konfigurálása a AD FS 2,0-hez](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh237448(v=ws.10)). |

> [!NOTE]
> További információ: [a helyszíni identitások integrálása a Azure Active Directorysal](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)