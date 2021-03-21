---
title: Mi az a Azure Active Directory-beli Inter-Directory kiépítés? | Microsoft Docs
description: Az identitások közötti címtár-kiépítés áttekintését ismerteti.
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
ms.openlocfilehash: 4246bc4c62fd8e5e73ff18e383b8bf115d25ede8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101643836"
---
# <a name="what-is-inter-directory-provisioning"></a>Mi az a címtárak közötti üzembe helyezés?

A címtár egy megosztott információs infrastruktúra, amely elemek és hálózati erőforrások keresésére, kezelésére, felügyeletére és rendszerezésére szolgál.  A címtárszolgáltatások szolgáltatást használó alkalmazások például a Microsoft Active Directory és az Azure AD.  Az identitások a címtár-rendszerek olyan meghatározásokat tesznek lehetővé, mint a mi, és akik számára engedélyezett az adott erőforrások használata.

A címtár közötti kiépítés két különböző címtárszolgáltatás-rendszer identitásának kiépítés útján történik.   A címtáron belüli kiépítés leggyakoribb forgatókönyve, ha egy felhasználó már Active Directory van kiépítve az Azure AD-be. A kiépítés olyan ügynökök által végezhető, mint például a Azure AD Connect Sync vagy Azure AD Connect Cloud kiépítés.

A címtár közötti kiépítés lehetővé teszi, hogy [hibrid identitású](../hybrid/whatis-hybrid-identity.md) környezeteket hozzon létre.


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Milyen típusú Inter-Directory-kiépítés nyújt támogatást az Azure AD-hez

Az Azure AD jelenleg három módszert támogat a könyvtárak közötti kiépítés végrehajtásához. Ezek a módszerek a következők:

- [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md) – a hibrid identitásának teljesítésére és megvalósítására tervezett Microsoft-eszköz, beleértve a Active Directoryról az Azure ad-re történő kiépítést.

- [Azure ad Connect Cloud kiépítés](../cloud-sync/what-is-cloud-sync.md) – egy új Microsoft-ügynök, amely a hibrid identitási célok teljesítésére és megvalósítására lett tervezve.  A szolgáltatás Active Directory és az Azure AD közötti, kis-és nagyszámú Inter-Directory kiépítési élményt nyújt.

- [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) – a Microsoft helyszíni identitás-és hozzáférés-kezelési megoldása, amely segít a felhasználók, a hitelesítő adatok, a házirendek és a hozzáférés kezelésében a szervezeten belül. Emellett a a többkönyvtáros kiépítés lehetővé teszi a hibrid identitási környezetek elérését Active Directory, az Azure AD és más címtárakban.

### <a name="key-benefits"></a>Főbb előnyök

A könyvtárak közötti kiépítés ezen képessége a következő jelentős üzleti előnyöket kínálja:

- [Jelszó kivonatának szinkronizálása](../hybrid/whatis-phs.md) – a bejelentkezési módszer, amely egy helyszíni ad-jelszó kivonatát szinkronizálja az Azure ad-vel.
- [Átmenő hitelesítés](../hybrid/how-to-connect-pta.md) – a bejelentkezési módszer, amely lehetővé teszi, hogy a felhasználók ugyanazt a jelszót használják a helyszínen és a felhőben, de nincs szükség összevont környezet további infrastruktúrájának használatára.
- [Összevonási integráció](../hybrid/how-to-connect-fed-whatis.md) – a hibrid környezetek helyszíni AD FS infrastruktúra használatával történő konfigurálására használható. Emellett AD FS felügyeleti képességeket is biztosít, mint például a tanúsítvány megújítása és a AD FS-kiszolgáló további telepítései.
- [Szinkronizálás](../hybrid/how-to-connect-sync-whatis.md) – a felhasználók, csoportok és egyéb objektumok létrehozásához felelős.  Továbbá a helyszíni felhasználók és csoportok személyazonossági információinak biztosítása a felhővel egyező.  Ez a szinkronizálás jelszó-kivonatokat is tartalmaz.
- [Állapotfigyelő – robusztus](../hybrid/whatis-azure-ad-connect.md) figyelést biztosíthat, és központi helyet biztosíthat a Azure Portal a tevékenység megtekintéséhez. 


## <a name="next-steps"></a>Következő lépések 
- [Mi az Identity Lifecycle Management?](what-is-identity-lifecycle-management.md)
- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az a HR-alapú kiépítés?](what-is-hr-driven-provisioning.md)
- [Mi az az alkalmazás-üzembehelyezés?](what-is-app-provisioning.md)