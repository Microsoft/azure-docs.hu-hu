---
title: Azure AD Connect Cloud Sync – gyakori kérdések
description: Ez a dokumentum a Cloud Sync szolgáltatással kapcsolatos gyakori kérdéseket ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39d1554fd1b6cac1a90a794cfd93def97e494bfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613531"
---
# <a name="azure-active-directory-connect-cloud-sync-faq"></a>Azure Active Directory Connect Cloud Sync – gyakori kérdések

További információ az Azure Active Directory (Azure AD) összekapcsolásával kapcsolatos gyakori kérdésekről: felhőalapú szinkronizálás.

## <a name="general-installation"></a>Általános telepítés

**K: milyen gyakran fut a Cloud Sync?**

A Felhőbeli kiépítés ütemezve van, hogy 2 percenként fusson. 2 percenként minden felhasználó, csoport és jelszó kivonata az Azure AD-be lesz kiépítve.

**K: a jelszó-kivonatolási szinkronizálás hibáinak észlelése az első futtatáskor. miért?**

Ez a várható eredmény. A hibák oka, hogy a felhasználói objektum nem szerepel az Azure AD-ben. Ha a felhasználó az Azure AD-be lett kiépítve, a jelszó-kivonatokat a következő futtatáskor kell kiépíteni. Várjon néhány futtatást, és győződjön meg arról, hogy a jelszó-kivonat szinkronizálása már nem rendelkezik a hibákkal.

**K: mi történik, ha a Active Directory-példány olyan attribútumokkal rendelkezik, amelyeket nem támogat a Cloud Sync (például a címtár-bővítmények)?**

A Felhőbeli kiépítés futtatja és kiépíti a támogatott attribútumokat. A nem támogatott attribútumok nem lesznek kiépítve az Azure AD-be. Tekintse át a Active Directory Directory-bővítményeit, és győződjön meg arról, hogy nincs szüksége ezekre az attribútumokra az Azure AD-ba való adatforgalomhoz Ha egy vagy több attribútumra van szükség, érdemes lehet Azure AD Connect szinkronizálást használni, vagy áthelyezni a szükséges információkat a támogatott attribútumok egyikére (például a 1-15-es bővítmény-attribútumokra).

**K: mi a különbség a Azure AD Connect Sync és a Cloud Sync között?**

A Azure AD Connect Sync szolgáltatással a kiépítés a helyszíni szinkronizáló kiszolgálón fut. A konfigurációt a helyszíni szinkronizáló kiszolgáló tárolja. Azure AD Connect Cloud Sync szolgáltatásban a létesítési konfiguráció a felhőben tárolódik, és a felhőben fut az Azure AD kiépítési szolgáltatás részeként. 

**K: használhatom a Cloud Sync szolgáltatást több Active Directory erdőből való szinkronizáláshoz?**

Igen. A Felhőbeli kiépítés több Active Directory erdőből való szinkronizálásra is használható. A többerdős környezetben az összes referenciának (például a vezetőnek) a tartományon belül kell lennie.  

**K: Hogyan frissült az ügynök?**

Az ügynököket a Microsoft automatikusan frissíti. Az informatikai csapat számára ez csökkenti az új ügynökök verzióinak tesztelésére és érvényesítésére vonatkozó terheket. 

**K: le tudom tiltani az automatikus frissítést?**

Nincs támogatott mód az automatikus frissítés letiltására.

**K: módosíthatom a Cloud Sync forrás-horgonyát?**

Alapértelmezés szerint a Felhőbeli szinkronizálás ms-DS-konzisztencia-GUID azonosítót használ a forrásként szolgáló ObjectGUID való tartalékként. Nincs támogatott mód a forrás-horgony módosítására.

**K: a Cloud Sync használatakor az Active Directory-tartománynév (ek) ben új egyszerű szolgáltatásokat látok. Várható?**

Igen, a Cloud Sync létrehoz egy egyszerű szolgáltatásnevet a kiépítési konfigurációhoz a tartománynévvel együtt. Ne módosítsa az egyszerű szolgáltatásnév konfigurációját.

**K: mi történik, ha szinkronizált felhasználóra van szükség a jelszó módosítására a következő bejelentkezéskor?**

Ha a jelszó-kivonat szinkronizálása engedélyezve van a Cloud Sync szolgáltatásban, és a szinkronizált felhasználó a helyi AD-ben a következő bejelentkezéshez szükséges jelszó megváltoztatására van szükség, a Felhőbeli szinkronizálás nem teszi lehetővé az Azure AD-hoz való "módosítható" jelszó-kivonat létrehozását. Ha a felhasználó megváltoztatja a jelszót, a rendszer az AD-ből az Azure AD-be kiépíti a felhasználói jelszó kivonatát.

**K: a Cloud Sync támogatja az MS-DS-consistencyGUID visszaírási bármely objektum esetében?**

Nem, a Cloud Sync nem támogatja az MS-DS-consistencyGUID visszaírási bármely objektumhoz (beleértve a felhasználói objektumokat is). 

**K: a felhasználókat a Cloud Sync használatával kell kiépíteni. Törölte a konfigurációt. Miért látom tovább a régi szinkronizált objektumokat az Azure AD-ben?** 

A konfiguráció törlésekor a Cloud Sync nem távolítja el automatikusan a szinkronizált objektumokat az Azure AD-ben. Annak biztosítása érdekében, hogy a régi objektumok ne legyenek, módosítsa a konfiguráció hatókörét egy üres csoportra vagy szervezeti egységre. Miután a kiépítés megtisztítja az objektumokat, tiltsa le és törölje a konfigurációt. 

**K: mit jelent az, hogy az Exchange Hybrid nem támogatott?**

Az Exchange hibrid üzembe helyezési funkciója lehetővé teszi az Exchange-postaládák együttes létezését helyszíni és Microsoft 365 is. Az Azure AD Connect visszaszinkronizálja az attribútumok egy adott halmazát az Azure AD szolgáltatásból a helyszíni címtárba.  A felhőalapú kiépítési ügynök jelenleg nem szinkronizálja ezeket az attribútumokat a helyszíni címtárba, így a Azure AD Connect helyett nem használható.

**K: telepíthetem a Cloud kiépítési ügynököt a Windows Server Core-ra?**

Nem, a Server Core-ügynök telepítése nem támogatott.

**K: használhatok átmeneti kiszolgálót a felhőalapú kiépítési ügynökkel?**

Nem, az átmeneti kiszolgálók nem támogatottak.

**K: szinkronizálhatom a vendég felhasználói fiókokat?**

Nem, a vendég felhasználói fiókok szinkronizálása nem támogatott.

**K: Ha Áthelyezek egy felhasználót egy olyan szervezeti egységből, amely a felhőalapú szinkronizálásra van korlátozva egy olyan szervezeti egységre, amelynek hatóköre Azure AD Connect, mi történik?**

A rendszer törli és újból létrehozza a felhasználót.  A Felhőbeli szinkronizálásra hatókörrel rendelkező szervezeti egység felhasználóinak áthelyezése törlési műveletként lesz megtekintve.  Ha a felhasználót Azure AD Connect által felügyelt szervezeti egységre helyezi át, a rendszer újra kiépíti az Azure AD-ba, és létrehoz egy új felhasználót.

**K: Ha átnevezem vagy áthelyezem a Felhőbeli szinkronizálási szűrő hatókörében lévő szervezeti egységet, mi történik az Azure AD-ben létrehozott felhasználóval?**

Semmit nem fogok észlelni.  A rendszer nem törli a felhasználókat, ha a szervezeti egységet átnevezték vagy áthelyezték.

**K: Azure AD Connect Cloud Sync támogatja a nagyméretű csoportokat?**

Igen. Ma a szervezeti egység hatókörének szűrésével akár 50 000 csoport tagjait is támogatjuk. Ugyanakkor ha a csoport hatókörének szűrését használja, javasoljuk, hogy a csoport méretét a 1500-nál kevesebb taggal tartsa. Ennek az az oka, hogy bár egy nagyméretű csoportot is szinkronizálhat a csoport hatóköri szűrője részeként, ha a csoport tagjait a 1500-nál nagyobb kötegekben adja hozzá, a különbözeti szinkronizálás sikertelen lesz. 

## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
