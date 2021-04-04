---
title: Mi az a személyazonossági életciklus kezelése Azure Active Directory? | Microsoft Docs
description: Útmutató az identitás-életciklus kezelésének áttekintéséhez.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65e1217041d85b66664792d9475cdfcb517559b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172435"
---
# <a name="what-is-identity-lifecycle-management"></a>Mi az az identitáséletciklus-felügyelet?

Az identitások szabályozása révén a szervezetek a termelékenység egyensúlyát érhetik el – milyen gyorsan férhet hozzá egy személy a szükséges erőforrásokhoz, például ha a szervezethez csatlakoznak? És biztonság – Hogyan változnak a hozzáférésük az idő múlásával, például az adott személy foglalkoztatási állapotának változásai miatt?

Az identitás- **életciklus kezelése** az identitások irányításának alapja, és a hatékony irányítás érdekében az alkalmazásokhoz szükséges az identitás-életciklus-kezelési infrastruktúra korszerűsítése. Az identitás-életciklus kezelése célja, hogy automatizálja és felügyelje a teljes digitális identitás életciklusának folyamatát. 

![Felhőbeli kiépítés](media/what-is-provisioning/cloud-1.png)

## <a name="what-is-a-digital-identity"></a>Mi az a digitális identitás?

A digitális identitás egy vagy több számítástechnikai erőforrás – például operációs rendszerek vagy alkalmazások – által használt entitásra vonatkozó információ. Ezek az entitások személyeket, szervezeteket, alkalmazásokat vagy eszközöket jelenthetnek.  Az identitást általában a hozzá társított attribútumok írják le, például a név, az azonosítók és a tulajdonságok, például a hozzáférés-kezeléshez használt szerepkörök.  Ezek az attribútumok segítenek a rendszereknek olyan meghatározások létrehozásában, akik hozzáférnek a szolgáltatáshoz, és akik számára engedélyezett a használata.  

## <a name="managing-the-lifecycle-of-digital-identities"></a>A digitális identitások életciklusának kezelése

A digitális identitások kezelése egy összetett feladat, különösen a valós objektumok, például egy személy és a szervezet alkalmazottaiként való kapcsolata a digitális ábrázolással.    A kisszervezetekben az identitást igénylő személyek digitális képviselete manuális folyamat lehet – ha valaki felvette, vagy egy vállalkozó megérkezik, az informatikai szakember létrehozhat egy fiókot a címtárban, és hozzárendelheti a szükséges hozzáférést.  Közép-és nagyvállalatok esetében azonban az Automation lehetővé teszi a szervezet számára a hatékonyabb skálázást és az identitások pontos megőrzését.

A szervezet identitás-életciklus-kezelésének szokásos folyamata a következő lépésekből áll:

1. Állapítsa meg, hogy van-e már rekordrendszer: olyan adatforrások, amelyeket a szervezet mérvadóként kezel.  Előfordulhat például, hogy a szervezetnek van egy HR munkaterülete, és ez a rendszer mérvadó az alkalmazottak aktuális listájának, valamint néhány tulajdonságának, például az alkalmazott nevének vagy részlegének biztosításához.  Vagy egy e-mail-rendszer, például az Exchange Online is mérvadó lehet az alkalmazott e-mail-címéhez.

2. Ezeket a rekordokat az alkalmazások által használt egy vagy több címtárral és adatbázissal, valamint a címtárak és a rekordok rekordjai közötti következetlenségek megoldásával kapcsolhatja össze. Előfordulhat például, hogy egy könyvtár elavult adattal rendelkezik, például egy korábbi alkalmazott fiókja, amelyre már nincs szükség. 

3. Határozza meg, hogy mely folyamatok használhatók a mérvadó információk megadásához egy rekordrendszer hiányában.  Ha például vannak digitális identitások, de a látogatók, de a szervezetnek nincs adatbázisa a látogatók számára, szükség lehet egy másik módszer megadására, amely alapján megállapíthatja, hogy mikor van már szükség a felhasználók digitális identitására.

4. Konfigurálja, hogy a rendszer a rekord vagy más folyamatok változásait replikálja minden olyan címtárba vagy adatbázisba, amelyre frissítés szükséges.

## <a name="identity-lifecycle-management-for-representing-employees-and-other-individuals-with-an-organizational-relationship"></a>Identitás-életciklus kezelése az alkalmazottak és más, szervezeti kapcsolattal rendelkező személyek képviseletéhez

Az alkalmazottak, illetve a szervezeti kapcsolattal (például egy vállalkozóval vagy tanulóval) rendelkező más személyek számára az identitás-életciklus kezelésének megtervezése során számos szervezet modellezi az "illesztés, áthelyezés és távozás" folyamatot.  Ezek a következők:
    
   - Join (csatlakozás) – Ha egy adott személy a hozzáférésre szoruló hatókörbe tartozik, az alkalmazások identitására van szükség, így előfordulhat, hogy az új digitális identitást létre kell hozni, ha még nem érhető el
   - Áthelyezés – ha az egyes határok között mozog, amelyek további hozzáférési engedélyeket igényelnek a digitális identitás hozzáadásához vagy eltávolításához
   - Szabadság – ha egy adott személy elhagyja a hozzáférésre szoruló hatókört, előfordulhat, hogy el kell távolítania a hozzáférést, és ezt követően az identitásnak nem kell megadnia az ellenőrzést vagy a kriminalisztikai célokat eltérő alkalmazások számára.

Így például, ha egy új alkalmazott csatlakozik a szervezethez, és korábban még soha nem csatlakozott a szervezethez, az alkalmazottnak új digitális identitást kell használnia, amely felhasználói fiókként jelenik meg az Azure AD-ben.  A fiók létrehozása egy "asztalos" folyamatba esik, ami automatizálható, ha olyan rekordrendszer van, mint például a munkanap, amely az új alkalmazott működésének megkezdése esetén jelezheti.  Később, ha a szervezete egy alkalmazottról szól, a marketingbe való értékesítés, a "mozgató" folyamat fog esni.  Ehhez olyan hozzáférési jogokat kell eltávolítania, amelyekre már nincs szükségük az értékesítési szervezeten belül, és meg kell adni számukra a jogokat az új szükséges marketing-szervezet számára.

## <a name="identity-lifecycle-management-for-guests"></a>Identitás-életciklus kezelése a vendégek számára

Hasonló folyamatokra is szükség van a vendégek és más felhasználók számára.  Az Azure AD-jogosultságok kezelése az Azure AD-beli vállalatok közötti (B2B) funkciókkal biztosítja a szervezeten kívüli személyekkel való együttműködéshez szükséges életciklus-vezérlést, és hozzáférést igényel a szervezet erőforrásaihoz. Az Azure AD B2B-vel a külső felhasználók a saját címtárában hitelesítik magukat, de rendelkeznek egy képviselettel a címtárban. A címtárban lévő ábrázolás lehetővé teszi, hogy a felhasználó hozzáférjen az erőforrásokhoz.  A jogosultságok kezelése lehetővé teszi, hogy a szervezeten kívüli személyek hozzáférhessenek a hozzáféréshez, és szükség szerint digitális identitást hozzanak létre. A rendszer automatikusan eltávolítja ezeket a digitális identitásokat, amikor a felhasználó elveszti a hozzáférést.  

## <a name="how-does-azure-ad-automate-identity-lifecycle-management"></a>Hogyan automatizálja az Azure AD az identitás-életciklus felügyeletét?

Az Azure AD jelenleg a következő funkciókat biztosítja:

* Az alkalmazottakat képviselő felhasználók automatikusan létrehozhatók és frissíthetők az Azure AD-ben, és Active Directory a [HR-alapú kiépítés](what-is-hr-driven-provisioning.md) használatával
* A Active Directoryban már meglévő felhasználók automatikusan létrehozhatók és karbantarthatók az Azure AD-ben az [Inter-Directory kiépítés](what-is-inter-directory-provisioning.md) használatával
* A felhasználók a tulajdonságok alapján, [dinamikus csoportok](../external-identities/use-dynamic-groups.md#what-are-dynamic-groups) használatával automatikusan hozzárendelhetők csoportokba, és igény szerint csoportokhoz, csapatokhoz, Azure ad-szerepkörökhöz, Azure-erőforrás-szerepkörökhöz és SharePoint Online-webhelyekhez is hozzárendelhetők, a [jogosultságok kezelése](entitlement-management-scenarios.md) és a [Privileged Identity Management](../privileged-identity-management/pim-configure.md)
* A felhasználók frissítései automatikusan elküldhetők több alkalmazásba az alkalmazások [kiépítés](what-is-app-provisioning.md) használatával

## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [A külső felhasználók hozzáférésének szabályozása az Azure AD-jogosultságok kezelésében](./entitlement-management-external-users.md)
- [Mi az a HR-alapú kiépítés?](what-is-hr-driven-provisioning.md)
- [Mi az az alkalmazás-üzembehelyezés?](what-is-app-provisioning.md)
- [Mi az a címtárak közötti üzembe helyezés?](what-is-inter-directory-provisioning.md)