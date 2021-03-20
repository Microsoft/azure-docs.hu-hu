---
title: Felfüggesztett tartományok a Azure AD Domain Servicesban | Microsoft Docs
description: Ismerkedjen meg az Azure AD DS felügyelt tartományának különböző állapotával és a felfüggesztett tartomány visszaállításával.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: b0b48e7ad494386052e6d94c32d7215b3f4f0202
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618824"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Az állapot megismerése és a felfüggesztett tartományok feloldása Azure Active Directory Domain Services

Ha Azure Active Directory Domain Services (Azure AD DS) nem tud hosszú ideig kiszolgálni egy felügyelt tartományt, a felügyelt tartományt felfüggesztett állapotba helyezi. Ha egy felügyelt tartomány felfüggesztett állapotban marad, automatikusan törlődik. Az Azure AD DS felügyelt tartomány Kifogástalan állapotba helyezéséhez és a felfüggesztés elkerüléséhez a riasztásokat a lehető leggyorsabban oldja fel.

Ez a cikk a felügyelt tartományok felfüggesztését és a felfüggesztett tartomány helyreállítását ismerteti.

## <a name="overview-of-managed-domain-states"></a>A felügyelt tartományi állapotok áttekintése

A felügyelt tartomány életciklusán keresztül különböző állapotok jelennek meg, amelyek jelzik az állapotukat. Ha a felügyelt tartomány hibát jelez, gyorsan oldja meg az alapul szolgáló okot, hogy leállítsa az állapotot, hogy továbbra is csökkenhet.

![A felügyelt tartomány által a felfüggesztés felé irányuló állapotok állapota](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

A felügyelt tartomány a következő állapotok egyikében lehet:

* [Futó](#running-state)
* [Figyelmet igényel](#needs-attention-state)
* [Felfüggesztve](#suspended-state)
* [Törölve](#deleted-state)

## <a name="running-state"></a>Futó állapot

Olyan felügyelt tartomány, amely megfelelően van konfigurálva, és probléma nélkül *fut* állapotban van. Ez a felügyelt tartomány kívánt állapota.

### <a name="what-to-expect"></a>Amire számíthat

* Az Azure platform rendszeresen képes figyelni a felügyelt tartomány állapotát.
* A felügyelt tartományhoz tartozó tartományvezérlők frissítése és frissítése rendszeresen megtörténik.
* A Azure Active Directory változásai rendszeresen szinkronizálhatók a felügyelt tartományba.
* A felügyelt tartomány rendszeres biztonsági mentéseket végez.

## <a name="needs-attention-state"></a>Beavatkozási állapotot igényel

Egy vagy több kijavítani kívánt problémával rendelkező felügyelt tartomány *beavatkozási* állapotba szorul. A felügyelt tartomány állapot lapja felsorolja a riasztásokat, és jelzi, hogy van-e probléma.

Egyes riasztások átmenetiek, és az Azure platform automatikusan feloldja őket. Más riasztások esetén a probléma megoldásához kövesse a megadott megoldási lépéseket. Kritikus riasztásra van lehetőség, és további hibaelhárítási segítségért [Nyisson meg egy Azure-támogatási kérést][azure-support] .

A riasztások egyik példája, ha van egy korlátozó hálózati biztonsági csoport. Ebben a konfigurációban előfordulhat, hogy az Azure platform nem tudja frissíteni és figyelni a felügyelt tartományt. A rendszer riasztást hoz létre, és az állapotnak *figyelmet kell* fordítania.

További információ: Hogyan lehet [elhárítani a riasztásokat egy felügyelt tartományon][resolve-alerts].

### <a name="what-to-expect"></a>Amire számíthat

Ha egy felügyelt tartomány az *igényeknek megfelelő* , akkor előfordulhat, hogy az Azure-platform nem képes rendszeresen figyelni, javítani, frissíteni vagy biztonsági másolatot készíteni az adatbázisokról. Bizonyos esetekben, például érvénytelen hálózati konfiguráció esetén előfordulhat, hogy a felügyelt tartományhoz tartozó tartományvezérlők nem érhetők el.

* A felügyelt tartomány sérült állapotban van, és a folyamatos állapot figyelése leállhat a riasztás feloldása előtt.
* A felügyelt tartományhoz tartozó tartományvezérlőket nem lehet kijavítani vagy frissíteni.
* Előfordulhat, hogy a Azure Active Directory változásai nem szinkronizálhatók a felügyelt tartományba.
* Előfordulhat, hogy a felügyelt tartomány biztonsági mentései nem szükségesek.
* Ha a felügyelt tartományt érintő nem kritikus riasztásokat oldja fel, az állapotnak vissza kell térnie a *futó* állapotba.
* A kritikus riasztások olyan konfigurációs problémák esetén aktiválódnak, amelyekben az Azure platform nem tudja elérni a tartományvezérlőket. Ha a kritikus riasztások nem oldhatók fel 15 napon belül, a felügyelt tartomány a *felfüggesztett* állapotba kerül.

## <a name="suspended-state"></a>Felfüggesztett állapot

A felügyelt tartomány a következő okok egyike miatt **felfüggesztett** állapotba kerül:

* Egy vagy több kritikus riasztás 15 nap múlva nem lett feloldva.
    * A kritikus riasztásokat olyan helytelen konfiguráció okozhatja, amely blokkolja az Azure AD DS által igényelt erőforrásokhoz való hozzáférést. Például a riasztás [AADDS104: hálózati hiba][alert-nsg] megoldatlanul több, mint 15 napja a felügyelt tartományban.
* Az Azure-előfizetéssel kapcsolatos számlázási probléma, vagy az Azure-előfizetés lejárt.

A felügyelt tartományok felfüggesztése akkor történik meg, ha az Azure platform nem tudja kezelni, figyelni, javítani vagy biztonsági másolatot készíteni a tartományról. A felügyelt tartomány 15 napig *felfüggesztett* állapotban marad. A felügyelt tartományhoz való hozzáférés fenntartásához azonnal oldja fel a kritikus riasztásokat.

### <a name="what-to-expect"></a>Amire számíthat

Ha egy felügyelt tartomány *felfüggesztett* állapotban van, a következő viselkedés tapasztalható:

* A felügyelt tartomány tartományvezérlőinek kiépítve, és nem érhetők el a virtuális hálózaton belül.
* Secure LDAP a felügyelt tartományhoz való hozzáférés az interneten keresztül, ha engedélyezve van, a működése leáll.
* Hibák vannak a felügyelt tartományba való hitelesítéssel, a tartományhoz csatlakoztatott virtuális gépekre való bejelentkezéssel, illetve az LDAP/LDAPs-kapcsolaton keresztüli csatlakozással.
* A felügyelt tartományhoz tartozó biztonsági másolatok már nem lettek elvégezve.
* Az Azure AD-vel való szinkronizálás leáll.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Honnan tudhatja, hogy a felügyelt tartomány fel van-e függesztve?

A rendszer felfüggeszti a tartományt felfüggesztett Azure Portal Azure AD DS Health (állapot) lapján található [riasztást][resolve-alerts] . A tartomány állapota a *felfüggesztett* állapotot is megjeleníti.

### <a name="restore-a-suspended-domain"></a>Felfüggesztett tartomány visszaállítása

A *felfüggesztett* állapotú felügyelt tartomány állapotának visszaállításához hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza ki a **tartományi szolgáltatások** elemet.
1. Válassza ki a felügyelt tartományt a listából, például *aaddscontoso.com*, majd válassza az **állapot** lehetőséget.
1. Válassza ki a riasztást, például *AADDS503* vagy *AADDS504*, a felfüggesztés okának megfelelően.
1. Válassza ki a riasztásban megadott feloldási hivatkozást, és kövesse a lépéseket a megoldásához.

A felügyelt tartományokat csak a legutóbbi biztonsági mentés dátumára lehet visszaállítani. A legutóbbi biztonsági mentés dátuma megjelenik a felügyelt tartomány **állapot** lapján. A legutóbbi biztonsági mentést követően történt módosítások nem lesznek visszaállítva. A felügyelt tartomány biztonsági másolatait akár 30 napig tárolja a rendszer. A 30 napnál régebbi biztonsági mentések törlődnek.

Miután megoldotta a riasztásokat, amikor a felügyelt tartomány *felfüggesztett* állapotban van, [Nyisson meg egy Azure-támogatási kérést][azure-support] , amely Kifogástalan állapotba tér vissza. Ha a biztonsági mentés 30 napnál rövidebb ideig tart, az Azure-támogatás visszaállíthatja a felügyelt tartományt.

## <a name="deleted-state"></a>Törölt állapot

Ha egy felügyelt tartomány a *felfüggesztett* állapotban marad 15 napig, azt törli a rendszer. Ez a folyamat nem állítható helyre.

### <a name="what-to-expect"></a>Amire számíthat

Ha egy felügyelt tartomány belép a *törölt* állapotba, a következő viselkedés látható:

* A rendszer törli a felügyelt tartomány összes erőforrását és biztonsági mentését.
* A felügyelt tartomány nem állítható vissza. Az Azure AD DS újrafelhasználásához létre kell hoznia egy helyettesítő felügyelt tartományt.
* A törlése után a rendszer nem számláz a felügyelt tartományhoz.

## <a name="next-steps"></a>Következő lépések

A felügyelt tartomány Kifogástalan állapotba állításához és a felfüggesztése kockázatának csökkentéséhez Ismerje meg a [felügyelt tartományhoz tartozó riasztások feloldását][resolve-alerts].

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
