---
title: Hibrid Identity design – címtár-szinkronizálási követelmények az Azure-ban | Microsoft Docs
description: Határozza meg, hogy milyen követelmények szükségesek ahhoz, hogy a rendszer szinkronizálja az összes felhasználót a (z) és a (z) között a vállalatnál.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 500d226fcb60646becc49144f206dcb0dee49bd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89278394"
---
# <a name="determine-directory-synchronization-requirements"></a>Címtár-szinkronizálási követelmények meghatározása
A szinkronizálás arról szól, hogy a felhasználók a helyszíni identitásuk alapján biztosítanak identitást a felhőben. Függetlenül attól, hogy szinkronizált fiókot használnak-e hitelesítéshez vagy összevont hitelesítéshez, a felhasználóknak továbbra is rendelkezniük kell identitással a felhőben.  Ezt az identitást rendszeresen karban kell tartani és frissíteni kell.  A frissítések több űrlapot is igénybe vehetnek, a cím módosításaitól a jelszó módosításaiig.  

Kezdje a helyszíni identitási megoldás és a felhasználói követelmények kiértékelésével. Ez az értékelés azért fontos, hogy meghatározza a felhasználói identitások létrehozásához és karbantartásához szükséges technikai követelményeket a felhőben.  A szervezetek többsége Active Directory helyszíni, és a felhasználók által szinkronizált helyszíni címtár lesz, azonban bizonyos esetekben ez nem lesz a helyzet.  

Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Van egy AD-erdő, több vagy nincs?
  
  * Hány Azure AD-címtárat fog szinkronizálni?
    
    1. Szűrést használ?
    2. Több Azure AD Connect-kiszolgálót is tervezett?
* Jelenleg van egy helyszíni szinkronizálási eszköze?
  
  * Ha igen, akkor a felhasználók, ha a felhasználók rendelkeznek virtuális könyvtárral/identitások integrálásával?
* Van-e olyan helyszíni címtár, amelyet szinkronizálni szeretne (például az LDAP-címtár, az HR-adatbázis stb.)?
  * Bármilyen GALSync lesz?
  * Mi a vállalata UPN-állapota? 
  * Van egy másik címtára, amelyet a felhasználók hitelesíteni tudnak?
  * A vállalata használja a Microsoft Exchange-et?
    * Tervezik a hibrid Exchange-telepítést?

Most, hogy már van egy ötlete a szinkronizálási követelményekről, meg kell határoznia, hogy melyik eszköz felel meg a követelményeknek.  A Microsoft számos eszközt biztosít a címtár-integráció és a szinkronizálás végrehajtásához.  További információért tekintse meg a [hibrid identitás címtár-integrációs eszközök összehasonlítási táblázatát](plan-hybrid-identity-design-considerations-tools-comparison.md) . 

Most, hogy már rendelkezik a szinkronizálási követelményekkel és a vállalata számára elérhető eszközzel, ki kell értékelnie az ezen címtárszolgáltatás-szolgáltatásokat használó alkalmazásokat. Ez az értékelés fontos az alkalmazások felhőbe integrálásához szükséges technikai követelmények meghatározásához. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* A rendszer áthelyezi ezeket az alkalmazásokat a felhőbe, és használja a könyvtárat?
* Vannak olyan speciális attribútumok, amelyeket szinkronizálni kell a felhővel, hogy ezek az alkalmazások sikeresen használhassák ezeket?
* Újra kell írni ezeket az alkalmazásokat a felhőalapú hitelesítés előnyeinek kihasználásához?
* Az alkalmazások továbbra is a helyszínen laknak, miközben a felhasználók a Felhőbeli identitás használatával férnek hozzájuk?

Emellett meg kell határoznia a biztonsági követelményeket és a megkötések címtár-szinkronizálását is. Ez az értékelés fontos azon követelmények listájának lekéréséhez, amelyek szükségesek a felhasználók Felhőbeli identitásának létrehozásához és karbantartásához. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Hol található a szinkronizálási kiszolgáló?
* A tartomány csatlakoztatva lesz?
* A kiszolgáló egy tűzfal mögötti korlátozott hálózaton, például egy DMZ-on található?
  * Megnyithatja a szükséges tűzfal-portokat a szinkronizálás támogatásához?
* Vész-helyreállítási terve van a szinkronizációs kiszolgálónak?
* Van olyan fiókja, amely a megfelelő engedélyekkel rendelkezik az összes olyan erdőhöz, amelyet szinkronizálni szeretne?
  * Ha a vállalat nem ismeri a kérdésre adott választ, tekintse át a "jelszó-szinkronizálási engedélyek" szakaszt a [Azure Active Directory szinkronizálási szolgáltatás telepítése](/previous-versions/azure/azure-services/dn757602(v=azure.100)#BKMK_CreateAnADAccountForTheSyncService) című cikkben, és állapítsa meg, hogy van-e ilyen engedélyekkel rendelkező fiókja, vagy ha létre kell hoznia egyet.
* Ha van megtalálhatjuk-erdő szinkronizálása, akkor a Szinkronizáló kiszolgáló képes az egyes erdőkbe bejutni?

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. Az [incidensekre adott válaszokra vonatkozó követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md) az elérhető lehetőségekre mutat. A kérdések megválaszolása után kiválaszthatja, hogy melyik lehetőség felel meg legjobban az üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>Következő lépések
[A multi-Factor Authentication követelményeinek meghatározása](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Lásd még
[Tervezési szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)