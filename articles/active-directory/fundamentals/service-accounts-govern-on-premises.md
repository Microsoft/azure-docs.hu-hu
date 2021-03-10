---
title: A helyszíni szolgáltatásfiókok szabályozása | Azure Active Directory
description: Útmutató egy fiók életciklus-folyamatának létrehozásához és futtatásához a szolgáltatásfiókok számára
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ad7cf7fe2ca1ddcb592e895014b1d956e55e1b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557369"
---
# <a name="governing-on-premises-service-accounts"></a>Helyszíni szolgáltatásfiókok szabályozása

A Windows Active Directoryban négyféle helyszíni szolgáltatásfiók létezik:

* [Csoportosan felügyelt szolgáltatásfiókok](service-accounts-group-managed.md) (csoportosan felügyelt szolgáltatásfiókokat)

* [önálló felügyelt szolgáltatásfiókok](service-accounts-standalone-managed.md) (sMSAs)

* [Számítógépfiókok](service-accounts-computer.md)

* [Szolgáltatásfiókokként működő felhasználói fiókok](service-accounts-user-on-premises.md)


Fontos, hogy a szolgáltatásfiókok az alábbiakhoz legyenek szorosan irányítva: 

* A szolgáltatási fiókok a használati esetekre vonatkozó követelmények és céljuk alapján védhetők.

* Kezelheti a szolgáltatásfiókok életciklusát és a hitelesítő adataikat.

* A szolgáltatásfiókok kiértékelése azon kockázat alapján, amelyet a rendszer megtesz, és az általuk végzett engedélyek 

* Győződjön meg arról, hogy a Active Directory és a Azure Active Directory nem rendelkezik olyan elavult szolgáltatásokkal, amelyek potenciálisan messzemenő engedélyekkel rendelkeznek.

## <a name="principles-for-creating-a-new-service-account"></a>Új szolgáltatásfiók létrehozásának alapelvei

Új szolgáltatásfiók létrehozásakor használja az alábbi feltételeket.

| Alapelvek| Megfontolandó szempontok | 
| - |- | 
| Szolgáltatásfiók-hozzárendelés| A szolgáltatásfiók egyetlen szolgáltatáshoz, alkalmazáshoz vagy parancsfájlhoz való összekapcsolása. |
| Tulajdonjog| Győződjön meg arról, hogy van olyan tulajdonos, aki kéri a fiókot, és vállalja a felelősséget. |
| Hatókör| A hatókört egyértelműen definiálja, és a szolgáltatási fiók használatának időtartama várható. |
| Cél| Szolgáltatásfiókok létrehozása egyetlen konkrét célra. |
| Privilege| Alkalmazza a legalacsonyabb jogosultsági szint elvét a következők szerint: <br>Soha ne rendeljen hozzá olyan beépített csoportokhoz, mint a rendszergazdák.<br> Ha szükséges, távolítsa el a helyi gép jogosultságait.<br>A hozzáférés testreszabása és Active Directory delegálás használata a címtár-hozzáféréshez.<br>Szemcsés hozzáférési engedélyek használata.<br>A fiók lejáratának és a helyadatok alapján megjelenő korlátozások beállítása a felhasználó alapú szolgáltatásfiókok esetében |
| Figyelés és naplózás használat| A bejelentkezési adatok figyelése és annak ellenőrzése, hogy az megfelel-e a kívánt használati értéknek. Riasztások beállítása rendellenes használathoz. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>A minimális jogosultságok betartatása a felhasználói fiókokhoz és a fiók túlzott korlátozása

A következő beállításokat használhatja szolgáltatásfiókokként használt felhasználói fiókokkal:

* [**Fiók lejárata**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps): állítsa be, hogy a szolgáltatásfiók a felülvizsgálati időszak után automatikusan lejárjon egy meghatározott időtartamot, kivéve, ha megállapítható, hogy folytatni kell

*  **LogonWorkstations**: korlátozza azokat az engedélyeket, amelyekre a szolgáltatásfiók be tud jelentkezni. Ha helyileg fut a gépen, és csak az adott gépen lévő erőforrásokhoz fér hozzá, korlátozza azt a bejelentkezést bárhol máshol.

* A [**jelszó nem módosítható**](/powershell/module/addsadministration/set-aduser): akadályozza meg, hogy a szolgáltatásfiók ne módosítsa a saját jelszavát úgy, hogy a paraméter hamis értékre van állítva.

 
## <a name="build-a-lifecycle-management-process"></a>Életciklus-kezelési folyamat létrehozása

A szolgáltatásfiókok biztonságának fenntartása érdekében azokat a szükséges idő alapján kell felügyelni, amíg el nem végzik a leszerelést. 

A következő eljárással kezelheti a szolgáltatásfiókok életciklus-kezelését:

1. A fiók használati adatainak összegyűjtése
1. A szolgáltatásfiók és az alkalmazás előkészítése a Configuration Management-adatbázisra (CMDB)
1. Kockázatértékelés vagy hivatalos felülvizsgálat végrehajtása
1. Hozzon létre egy szolgáltatásfiókot, és alkalmazza a korlátozásokat.
1. Ismétlődő értékelések ütemezett és elvégzése. Szükség szerint módosítsa az engedélyeket és a hatóköröket.
1. A fiók kiépítése, ha szükséges.

### <a name="collect-usage-information-for-the-service-account"></a>A szolgáltatásfiók használati adatainak összegyűjtése

Gyűjtse össze a kapcsolódó üzleti adatokat az egyes szolgáltatásfiókok számára. Az alábbi táblázat a gyűjteni kívánt minimális információkat mutatja be, de a fiókok létezéséhez szükséges mindent össze kell gyűjtenie.

| Adatok| Részletek |
| - | - |
| Tulajdonos| A szolgáltatásfiók számára felelős felhasználó vagy csoport |
| Cél| A szolgáltatásfiók célja |
| Engedélyek (hatókörök)| Várt engedélyek készlete |
| Configuration Management Database-(CMDB-) hivatkozások| Összekapcsolható szolgáltatási fiók a célként megadott parancsfájllal, alkalmazással és tulajdonossal |
| Kockázat| Kockázat és üzleti hatás pontozás a biztonsági kockázatértékelés alapján |
| Élettartama| A fiók lejáratának vagy újraminősítésének ütemezését lehetővé tevő, várhatóan maximális élettartam |


 

Ideális esetben végezze el a kérést egy önkiszolgáló fiókhoz, és kérje meg a szükséges információkat. A tulajdonos, aki lehet alkalmazás vagy üzleti tulajdonos, informatikai tag vagy infrastruktúra-tulajdonos. Ha a fiók jóvá van hagyva, egy eszköz, például a Microsoft űrlapjainak használata ehhez a kérelemhez és a kapcsolódó információkhoz egyszerűen elvégezhető a CMDB leltári eszközre való csatlakozás.

### <a name="onboard-service-account-to-cmdb"></a>A CMDB bevezetési szolgáltatás fiókja

Tárolja az összegyűjtött adatokat egy CMDB alkalmazásban. Az üzleti információk mellett minden függőséget más infrastruktúrára, alkalmazásokra és folyamatokra is kiterjed.  Ez a központi tárház megkönnyíti a következőket:

* Mérje fel a kockázatot.

* Konfigurálja a szolgáltatásfiókot a szükséges korlátozásokkal.

* A kapcsolódó funkcionális és biztonsági függőségek megismerése.

* Rendszeres felülvizsgálatokat végezhet a biztonság és a folyamatos igények tekintetében.

* A szolgáltatásfiók áttekintéséhez, kivonásához és módosításához forduljon a tulajdonos (ok) hoz.

Vegyünk egy olyan szolgáltatásfiókot, amely egy webhely futtatására szolgál, és rendelkezik jogosultságokkal egy vagy több SQL-adatbázishoz való kapcsolódáshoz. A szolgáltatásfiók CMDB tárolt információ a következő lehet:

|Adatok | Részletek|
| - | - |
| Tulajdonos, helyettes| John Bloom, Anna Mayers |
| Cél| Futtassa a HR-weblapot, és kapcsolódjon a HR-adatbázisokhoz. A végfelhasználók számára az adatbázisok elérésekor megszemélyesítheti a felhasználókat. |
| Engedélyek, hatókörök| HR-webkiszolgáló: helyi bejelentkezés, weblap futtatása<br>HR-SQL1: bejelentkezés helyileg, olvasás az összes HR * adatbázisban<br>HR-SQL2: helyi bejelentkezés, beolvasás a FIZETÉSre * adatbázis |
| Cost Center| 883944 |
| Kockázat kiértékelése| Közepes Üzleti hatás: közepes; magánjellegű információk; Közepes |
| Fiókokra vonatkozó korlátozások| Bejelentkezés ide: csak a fenti kiszolgálók; A jelszó nem módosítható; MBI-Password szabályzat; |
| Élettartama| korlátlan |
| Felülvizsgálati ciklus| Bi-évenként (tulajdonos, biztonsági csapat szerint, adatvédelem szerint) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>Kockázatelemzés vagy a szolgáltatásfiók használatának formális áttekintése

A jogosultságok és a cél alapján mérje fel, hogy a fiók milyen kockázatot jelenthet a kapcsolódó alkalmazáshoz vagy szolgáltatáshoz, illetve az infrastruktúrához, ha az biztonsága sérül. Vegye figyelembe a közvetlen és a közvetett kockázatot is. 

* Mi lenne, ha egy támadó közvetlen hozzáférést szerez?

* Milyen egyéb információk vagy rendszerek férhetnek hozzá a szolgáltatásfiók eléréséhez?

* Használhatja a fiókot további engedélyek megadására?

* Hogyan fogja tudni, hogy Mikor változnak az engedélyek?

A kockázatértékelés, miután végzett és dokumentált, hatással lehet a következőre:

* Fiókokra vonatkozó korlátozások

* Fiók élettartama

* Fiók-felülvizsgálati követelmények (lépésszám és felülvizsgálók)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>Szolgáltatásfiók létrehozása és a fiókokra vonatkozó korlátozások alkalmazása

Csak azt követően hozzon létre szolgáltatásfiókot a CMDB, hogy dokumentálja a megfelelő információkat, és elvégzi a kockázatértékelést. A fiókra vonatkozó korlátozásokat a kockázatértékeléshez kell igazítani. Az értékeléshez kapcsolódóan vegye figyelembe a következő korlátozásokat:

* [Fiók lejárata](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)

   * A szolgáltatásfiókokként használt összes felhasználói fiók esetében adjon meg egy reális és határozott záró dátumot a használathoz. Ezt a "fiók lejárata" jelzővel állíthatja be. További részletekért tekintse[ meg a set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration)című témakört. 

* Bejelentkezés ([LogonWorkstation](/powershell/module/addsadministration/set-aduser))

* [Jelszóházirend](../../active-directory-domain-services/password-policy.md) -követelmények

* Létrehozás egy [szervezeti egységben](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) , amely csak a Kiemelt jogosultságú felhasználók felügyeletét biztosítja

* A szolgáltatásfiók módosításait és a [szolgáltatásfiók használatát](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html) [észlelő](/windows/security/threat-protection/auditing/audit-directory-service-changes) naplózás beállítása és gyűjtése.

Ha készen áll az éles környezetbe való üzembe helyezésre, biztonságos hozzáférést biztosít a szolgáltatásfiók számára. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>Szolgáltatásfiókok rendszeres felülvizsgálatának ütemezett időpontja

A közepes és magas kockázatnak besorolt szolgáltatásfiókok rendszeres felülvizsgálatának beállítása. A felülvizsgálatoknak tartalmazniuk kell a következőket: 

* A tulajdonos igazolja a fiók folyamatos igényét, valamint a jogosultságok és hatókörök indoklását.

* Tekintse át az adatvédelmi és biztonsági csapatokat, beleértve a felsőbb rétegbeli és az alsóbb szintű kapcsolatok értékelését.

* A naplózásból származó adatok, amelyek biztosítják, hogy a rendszer csak felhasználási célokra használja

### <a name="deprovision-service-accounts"></a>Szolgáltatásfiókok kiépítése

A megszüntetési folyamat során először távolítsa el az engedélyeket és a figyelőt, és ha szükséges, távolítsa el a fiókot.

Szolgáltatásfiókok kiépítése, ha:

* A szolgáltatáshoz létrehozott parancsfájl vagy alkalmazás ki lett vonva.

* A parancsfájlon vagy alkalmazáson belüli függvény, amelyet a szolgáltatásfiók használ (például egy adott erőforráshoz való hozzáférés).

* A szolgáltatási fiókot egy másik szolgáltatásfiók váltotta fel.

Az összes engedély eltávolítása után ezt a folyamatot használhatja a fiók eltávolításához.

1. Miután a társított alkalmazást vagy parancsfájlt megszüntette, figyelje a bejelentkezéseket és az erőforrás-hozzáférést a társított szolgáltatásfiók (ok) hoz, hogy a rendszer ne használja azt más folyamatokban. Ha biztos benne, hogy már nincs rá szükség, folytassa a következő lépéssel.

2. Tiltsa le a szolgáltatásfiók bejelentkezését, és győződjön meg róla, hogy már nincs rá szükség. Az időfiókokhoz tartozó üzleti szabályzatot le kell tiltani.

3. A szolgáltatási fiók törlése a maradó letiltott házirend betartása után. 

   * A MSAs a felügyelt szolgáltatásfiók-tárolóból [eltávolíthatja](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps) a PowerShell használatával, vagy manuálisan is törölheti azt.

   * A számítógép-vagy felhasználói fiókok esetében manuálisan is törölheti a fiókot Active Directory.

## <a name="next-steps"></a>Következő lépések
A szolgáltatásfiókok biztonságossá tételéről a következő cikkekben olvashat.

* [A helyszíni szolgáltatásfiókok bemutatása](service-accounts-on-premises.md)

* [Biztonságos csoportosan felügyelt szolgáltatásfiókok](service-accounts-group-managed.md)

* [Önálló felügyelt szolgáltatásfiókok biztonságossá tétele](service-accounts-standalone-managed.md)

* [Számítógép-fiókok biztonságossá tétele](service-accounts-computer.md)

* [Biztonságos felhasználói fiókok](service-accounts-user-on-premises.md)

* [Helyszíni szolgáltatásfiókok szabályozása](service-accounts-govern-on-premises.md)