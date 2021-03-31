---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2b28cc742bb981e0db535d39cb0c7fbf027470ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83673764"
---
### <a name="identity-tier"></a>Identitási rétegek 

Az Microsoft-Oracle partneri együttműködés lehetővé teszi, hogy egységes identitást állítson be az Azure-ban, a OCI-ben és az Oracle-alkalmazásban. A JD Edwards EnterpriseOne vagy PeopleSoft alkalmazáscsomag esetében az Oracle HTTP-kiszolgáló (OHS) egy példánya szükséges az Azure AD és az Oracle IDCS közötti egyszeri bejelentkezés beállításához.

A OHS fordított proxyként működik az alkalmazási szinten, ami azt jelenti, hogy a végfelhasználók felé irányuló összes kérelem áthalad. Az Oracle Access Manager WebGate egy OHS webkiszolgáló beépülő modul, amely minden, a befejezési alkalmazásra vonatkozó kérelmet elfogja. Ha egy hozzáférő erőforrás védett (hitelesített munkamenet szükséges), a WebGate a felhasználó böngészőjén keresztül kezdeményezi a OIDC-hitelesítési folyamatot az Identity Cloud Service használatával. Az OpenID Connect WebGate által támogatott folyamatokkal kapcsolatos további információkért tekintse meg az [Oracle Access Manager dokumentációját](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327).

Ezzel a beállítással egy, az Azure AD-be már bejelentkezett felhasználó a JD Edwards EnterpriseOne vagy a PeopleSoft alkalmazásba való belépés nélkül, az Oracle Identity Cloud Service-en keresztül is megnyitható. A megoldást telepítő ügyfelek az egyszeri bejelentkezés előnyeit vehetik igénybe, beleértve a hitelesítő adatok egy készletét, a továbbfejlesztett bejelentkezési élményt, a jobb biztonságot és a csökkentett ügyfélszolgálati költségeket.

Ha többet szeretne megtudni a JD Edwards EnterpriseOne vagy a PeopleSoft Azure AD-vel való egyszeri bejelentkezés beállításáról, tekintse meg a kapcsolódó [Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)-tanulmányt.