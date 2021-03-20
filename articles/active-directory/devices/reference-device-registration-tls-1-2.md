---
title: TLS 1,2 kényszerítés – Azure Active Directory regisztrációs szolgáltatás
description: A TLS 1,0 és 1,1 támogatásának eltávolítása az Azure AD-eszköz regisztrációs szolgáltatásához
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89268757"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>A TLS 1,2 alkalmazása az Azure AD regisztrációs szolgáltatáshoz

A Azure Active Directory (Azure AD) eszköz regisztrációs szolgáltatása az eszközök felhőhöz való csatlakoztatására szolgál az eszköz identitásával. Az Azure AD-eszközök regisztrálási szolgáltatása jelenleg a Transport Layer Security (TLS) 1,2-es használatát támogatja az Azure-hoz való kommunikációhoz. A biztonság és a legjobb szintű titkosítás biztosítása érdekében a Microsoft a TLS 1,0 és 1,1 letiltását javasolja. Ebből a dokumentumból megtudhatja, hogyan gondoskodhat arról, hogy az Azure AD-eszköz regisztrációs szolgáltatásával hogyan lehet a regisztráció befejezéséhez és a kommunikációhoz használni a TLS 1,2-t.

A TLS protokoll 1,2-es verziója egy olyan titkosítási protokoll, amely biztonságos kommunikáció biztosítására szolgál. A TLS protokoll elsősorban az adatvédelem és az adatintegritás biztosítását célozza. A TLS több, a 1,2-es verzióval ellátott iteráción esett át az [RFC 5246-ben (külső hivatkozás)](https://tools.ietf.org/html/rfc5246).

A kapcsolatok aktuális elemzése azt mutatja be, hogy kevés a TLS 1,1 és a 1,0 használat, de ezt az információt biztosítjuk, hogy az érintett ügyfeleket vagy kiszolgálókat szükség szerint frissíteni lehessen a TLS 1,1 és 1,0-es verzió támogatása előtt. Ha a helyszíni infrastruktúrát hibrid forgatókönyvekhez vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) használja, ellenőrizze, hogy az infrastruktúra támogatja-e a TLS 1,2-et használó bejövő és kimenő kapcsolatokat is.

## <a name="update-windows-servers"></a>Windows-kiszolgálók frissítése

Az Azure AD-eszköz regisztrációs szolgáltatását vagy a proxyt használó Windows-kiszolgálók esetében a következő lépésekkel biztosíthatja, hogy a TLS 1,2 engedélyezve legyen:

> [!IMPORTANT]
> A beállításjegyzék frissítése után újra kell indítani a Windows Servert, hogy a módosítások érvénybe lépnek.

### <a name="enable-tls-12"></a>TLS 1.2 engedélyezése

Győződjön meg arról, hogy a következő beállításjegyzék-karakterláncok konfigurálva vannak:

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ ügyfél
  - "DisabledByDefault" = DWORD: 00000000
  - "Engedélyezve" = DWORD: 00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ kiszolgáló
  - "DisabledByDefault" = DWORD: 00000000
  - "Engedélyezve" = DWORD: 00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\. NETFramework\v4.0.30319
  - "Alatt" = DWORD: 00000001

## <a name="update-non-windows-proxies"></a>Nem Windows rendszerű proxyk frissítése

Minden olyan gépen, amely az eszközök és az Azure AD-eszköz regisztrációs szolgáltatása között proxyként működik, biztosítania kell, hogy a TLS 1,2 engedélyezve legyen. A támogatás biztosításához kövesse a gyártó útmutatását.

## <a name="update-ad-fs-servers"></a>AD FS-kiszolgálók frissítése

Az Azure AD-eszközök regisztrálási szolgáltatásával folytatott kommunikációhoz használt AD FS-kiszolgálókon biztosítania kell, hogy a TLS 1,2 engedélyezve legyen. A konfiguráció engedélyezésével/ellenőrzésével kapcsolatos információkért lásd: [SSL/TLS protokollok és titkosító csomagok kezelése AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) .

## <a name="client-updates"></a>Ügyfelek frissítései

Mivel az összes ügyfél-kiszolgáló és böngésző-kiszolgáló kombinációnak a TLS 1,2-et kell használnia az Azure AD-eszköz regisztrációs szolgáltatásával való kapcsolódáshoz, előfordulhat, hogy frissítenie kell ezeket az eszközöket.

A következő ügyfelek ismertek, hogy nem tudják támogatni a TLS 1,2-et. Frissítse az ügyfeleket a zavartalan hozzáférés biztosításához.

- Android 4,3-es és korábbi verziók
- Firefox 5,0-es és korábbi verziók
- Az Internet Explorer 8-10-es verziója a Windows 7 és korábbi verziókon
- Internet Explorer 10 Windows Phone-telefon 8,0
- A Safari verziója 6.0.4 az OS X 10.8.4 és korábbi verziókban

## <a name="next-steps"></a>Következő lépések

[TLS/SSL – áttekintés (Schannel SSP)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)