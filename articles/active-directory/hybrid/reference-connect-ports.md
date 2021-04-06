---
title: Hibrid identitáshoz szükséges portok és protokollok – Azure | Microsoft Docs
description: Ez a lap a Azure AD Connecthoz való megnyitásához szükséges portok technikai útmutató lapja.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 681d92ed162feff0510067556d7eeaa47b12d988
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520030"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Hibrid identitás – szükséges portok és protokollok
A következő dokumentum technikai útmutató a hibrid identitási megoldások megvalósításához szükséges portokról és protokollokról. Használja az alábbi ábrát, és tekintse meg a megfelelő táblát.

![Mi az az Azure AD Connect?](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>1. táblázat – Azure AD Connect és helyszíni AD
Ez a táblázat azokat a portokat és protokollokat ismerteti, amelyek a Azure AD Connect-kiszolgáló és a helyszíni AD közötti kommunikációhoz szükségesek.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |DNS-keresések a cél erdőben. |
| Kerberos |88 (TCP/UDP) |Kerberos-hitelesítés az AD-erdőben. |
| MS-RPC |135 (TCP) |A Azure AD Connect varázsló kezdeti konfigurálásakor használatos, amikor az AD-erdőhöz kötődik, és a jelszó-szinkronizálás során is. |
| LDAP |389 (TCP/UDP) |Az AD-ből származó adatok importálására szolgál. Az adattitkosítás Kerberos-bejelentkezési & pecséttel történik. |
| SMB | 445 (TCP) |A zökkenőmentes egyszeri bejelentkezés használatával egy számítógépfiók hozható létre az AD-erdőben. |
| LDAP/SSL |636 (TCP/UDP) |Az AD-ből származó adatok importálására szolgál. Az adatátvitel aláírása és titkosítása megtörténik. Csak akkor használatos, ha TLS-t használ. |
| RPC |49152 – 65535 (véletlenszerű magas RPC-port) (TCP) |Azure AD Connect kezdeti konfigurálásakor használatos, amikor az AD-erdőkhöz kötődik, és a jelszó-szinkronizálás során. Ha a dinamikus port módosult, akkor meg kell nyitnia a portot. További információért lásd: [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)és [KB224196](https://support.microsoft.com/kb/224196) . |
|WinRM  | 5985 (TCP) |Csak akkor használatos, ha Azure AD Connect varázslóval telepíti a AD FSt a gMSA-ben|
|Webszolgáltatások AD DS | 9389 (TCP) |Csak akkor használatos, ha Azure AD Connect varázslóval telepíti a AD FSt a gMSA-ben |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>2. táblázat – Azure AD Connect és az Azure AD
Ez a táblázat azokat a portokat és protokollokat ismerteti, amelyek a Azure AD Connect-kiszolgáló és az Azure AD közötti kommunikációhoz szükségesek.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTP |80 (TCP) |A visszavont tanúsítványok listáinak (visszavont tanúsítványok listáinak) letöltésére szolgál a TLS/SSL-tanúsítványok ellenőrzéséhez. |
| HTTPS |443 (TCP) |Az Azure AD-vel való szinkronizálásra szolgál. |

A tűzfalon megnyitni kívánt URL-címek és IP-címek listájáért lásd: [Office 365 URL-címek és IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) címtartományok, valamint [Hibaelhárítás Azure ad Connect kapcsolat](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>3. táblázat – Azure AD Connect és AD FS összevonási kiszolgálók/WAP
Ez a táblázat azokat a portokat és protokollokat ismerteti, amelyek szükségesek a Azure AD Connect-kiszolgáló és a AD FS összevonási/WAP-kiszolgálók közötti kommunikációhoz.  

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTP |80 (TCP) |A visszavont tanúsítványok listáinak (visszavont tanúsítványok listáinak) letöltésére szolgál a TLS/SSL-tanúsítványok ellenőrzéséhez. |
| HTTPS |443 (TCP) |Az Azure AD-vel való szinkronizálásra szolgál. |
| WinRM |5985 |WinRM-figyelő |

## <a name="table-4---wap-and-federation-servers"></a>4. táblázat – WAP-és összevonási kiszolgálók
Ez a táblázat az összevonási kiszolgálók és a WAP-kiszolgálók közötti kommunikációhoz szükséges portokat és protokollokat ismerteti.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443 (TCP) |Hitelesítéshez használatos. |

## <a name="table-5---wap-and-users"></a>5. táblázat – WAP és felhasználók
Ez a táblázat a felhasználók és a WAP-kiszolgálók közötti kommunikációhoz szükséges portokat és protokollokat ismerteti.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443 (TCP) |Az eszköz hitelesítéséhez használatos. |
| TCP |49443 (TCP) |Tanúsítványalapú hitelesítéshez használatos. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>6a. táblázat & 6b – átmenő hitelesítés egyszeri bejelentkezéssel (SSO) és jelszó-kivonatoló szinkronizálás egyszeri bejelentkezéssel (SSO)
Az alábbi táblázatok a Azure AD Connect és az Azure AD közötti kommunikációhoz szükséges portokat és protokollokat ismertetik.

### <a name="table-6a---pass-through-authentication-with-sso"></a>6a tábla – átmenő hitelesítés egyszeri bejelentkezéssel
|Protokoll|Portszám|Leírás
| --- | --- | ---
|HTTP|80|Engedélyezze a kimenő HTTP-forgalmat a biztonsági érvényesítéshez, például az SSL-hez. Az összekötő automatikus frissítési képességének megfelelő működéséhez is szükséges.
|HTTPS|443| Engedélyezze a kimenő HTTPS-forgalmat olyan műveletekhez, mint például a szolgáltatás engedélyezése és letiltása, összekötők regisztrálása, összekötő frissítéseinek letöltése és az összes felhasználói bejelentkezési kérelem feldolgozása.

Emellett a Azure AD Connectnak képesnek kell lennie közvetlen IP-kapcsolat létesítésére az [Azure adatközpont IP-tartományával](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Table 6b – jelszó-kivonat szinkronizálása SSO-val

|Protokoll|Portszám|Leírás
| --- | --- | ---
|HTTPS|443| Egyszeri bejelentkezéses regisztráció engedélyezése (csak az SSO regisztrációs folyamathoz szükséges).

Emellett a Azure AD Connectnak képesnek kell lennie közvetlen IP-kapcsolat létesítésére az [Azure adatközpont IP-tartományával](https://www.microsoft.com/download/details.aspx?id=41653). Ez megint csak az egyszeri bejelentkezéses regisztrációs folyamathoz szükséges.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7a. táblázat & 7b-Azure AD Connect Health ügynök (AD FS/Sync) és az Azure AD
Az alábbi táblázatok a Azure AD Connect Health-ügynökök és az Azure AD közötti kommunikációhoz szükséges végpontokat, portokat és protokollokat ismertetik.

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7a. táblázat – Azure AD Connect Health-ügynökhöz tartozó portok és protokollok (AD FS/Sync) és az Azure AD
Ez a táblázat a Azure AD Connect Health-ügynökök és az Azure AD közötti kommunikációhoz szükséges következő kimenő portokat és protokollokat ismerteti.  

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443 (TCP) |Kimenő |
| Azure Service Bus |5671 (TCP) |Kimenő |

Azure Service Bus 5671-es portra már nincs szükség az ügynök legújabb verziójára. A legújabb Azure AD Connect Health ügynök verziója csak a 443-es portot szükséges.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b-végpontok Azure AD Connect Health ügynökhöz (AD FS/Sync) és az Azure AD-hez
A végpontok listáját a [Azure ad Connect Health-ügynök követelményei című szakaszban](how-to-connect-health-agent-install.md#requirements)találja.

