---
title: 'Azure AD Connect: az Azure AD kapcsolódási problémáinak elhárítása | Microsoft Docs'
description: Ismerteti, hogyan lehet elhárítani a Azure AD Connect kapcsolódási problémáit.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 56e9820c5e3a750a35b7271b86750df00eb4784e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677055"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Az Azure AD-kapcsolat hibáinak megoldása
Ez a cikk azt ismerteti, hogyan működik a Azure AD Connect és az Azure AD közötti kapcsolat, és hogyan lehet elhárítani a kapcsolódási problémákat. Ezeket a problémákat legvalószínűbben a proxykiszolgáló fogja látni a környezetben.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Csatlakozási problémák elhárítása a telepítővarázsló
A Azure AD Connect a modern hitelesítést használja (a ADAL könyvtár használatával) a hitelesítéshez. A telepítővarázsló és a Szinkronizáló motor megfelelő működéséhez szükség van a machine.config megfelelő konfigurálására, mivel ez a két .NET-alkalmazás.

Ebben a cikkben bemutatjuk, hogyan kapcsolódhat a fabrikam az Azure AD-hez a proxyján keresztül. A proxykiszolgáló neve fabrikamproxy, és az 8080-es portot használja.

Először is győződjön meg arról, hogy a [**machine.config**](how-to-connect-install-prerequisites.md#connectivity) megfelelően van konfigurálva, és **Microsoft Azure ad a szinkronizálási szolgáltatást** egyszer újraindították a machine.config fájl frissítése után.
![A képernyőfelvétel a Machine dot konfigurációs fájljának egy részét jeleníti meg.](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> Néhány nem Microsoft-blogban dokumentálja, hogy ehelyett miiserver.exe.config módosításokat kell végezni. Ez a fájl azonban minden frissítéskor felül van írva, így még akkor is, ha az a kezdeti telepítés során is működik, a rendszer leáll az első frissítéskor. Emiatt a javaslat a machine.config frissítésére szolgál.
>
>

A proxykiszolgáló számára is meg kell nyitni a szükséges URL-címeket. A hivatalos listát az [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)dokumentálják.

Ezen URL-címek közül az alábbi táblázat az Azure AD-hez való kapcsolódáshoz szükséges abszolút minimális érték. A lista nem tartalmaz olyan választható szolgáltatásokat, mint a jelszó-visszaírási vagy a Azure AD Connect Health. Itt dokumentáljuk a kezdeti konfiguráció hibaelhárításának segítségét.

| URL-cím | Port | Leírás |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |CRL-listák letöltésére használatos. |
| \*. verisign.com |HTTP/80 |CRL-listák letöltésére használatos. |
| \*. entrust.net |HTTP/80 |Az MFA CRL-listáinak letöltésére használatos. |
| \*.windows.net |HTTPS/443 |Az Azure AD-be való bejelentkezéshez használatos. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |MFA esetében használatos. |
| \*.microsoftonline.com |HTTPS/443 |Az Azure AD-címtár konfigurálására, valamint az adatimportálás/exportálásra szolgál. |
| \*. crl3.digicert.com |HTTP/80 |A tanúsítványok ellenőrzéséhez használatos. |
| \*. crl4.digicert.com |HTTP/80 |A tanúsítványok ellenőrzéséhez használatos. |
| \*. ocsp.digicert.com |HTTP/80 |A tanúsítványok ellenőrzéséhez használatos. |
| \*. www.d-trust.net |HTTP/80 |A tanúsítványok ellenőrzéséhez használatos. |
| \*. root-c3-ca2-2009.ocsp.d-trust.net |HTTP/80 |A tanúsítványok ellenőrzéséhez használatos. |
| \*. crl.microsoft.com |HTTP/80 |A tanúsítványok ellenőrzéséhez használatos. |
| \*. oneocsp.microsoft.com |HTTP/80 |A tanúsítványok ellenőrzéséhez használatos. |
| \*. ocsp.msocsp.com |HTTP/80 |A tanúsítványok ellenőrzéséhez használatos. |

## <a name="errors-in-the-wizard"></a>Hibák a varázslóban
A telepítővarázsló két különböző biztonsági kontextust használ. Az oldalon az **Azure ad**-hez való kapcsolódáskor a jelenleg bejelentkezett felhasználót használja. Az oldalon **konfigurálja** azt a fiókot, amely a [szolgáltatást futtatja a Szinkronizáló motorhoz](reference-connect-accounts-permissions.md#adsync-service-account). Ha probléma merül fel, valószínűleg már a **Csatlakozás az Azure ad-hoz** a varázslóban, mivel a proxy konfigurálása globális.

A következő problémák a telepítővarázsló leggyakoribb hibái.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Nincs megfelelően konfigurálva a telepítővarázsló
Ez a hiba akkor jelenik meg, ha a varázsló nem tudja elérni a proxyt.
![A képernyőképen a következő hibaüzenet látható: nem lehet érvényesíteni a hitelesítő adatokat.](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Ha ezt a hibát látja, ellenőrizze, hogy helyesen konfigurálta-e a [machine.config](how-to-connect-install-prerequisites.md#connectivity) .
* Ha úgy tűnik, hogy helyes, kövesse a [proxy kapcsolatának ellenőrzése](#verify-proxy-connectivity) című témakör lépéseit, és ellenőrizze, hogy a probléma a varázslón kívül található-e.

### <a name="a-microsoft-account-is-used"></a>Microsoft-fiók van használatban
Ha **iskolai vagy szervezeti** fiók helyett **Microsoft-fiók** használ, általános hiba jelenik meg.
![A rendszer Microsoft-fiókot használ](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Az MFA-végpont nem érhető el
Ez a hiba akkor jelenik meg, ha a végpont **https://secure.aadcdn.microsoftonline-p.com** nem érhető el, és a globális rendszergazda engedélyezte az MFA-t.
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Ha ezt a hibát látja, ellenőrizze, hogy a végpont **Secure.aadcdn.microsoftonline-p.com** hozzá lett-e adva a proxyhoz.

### <a name="the-password-cannot-be-verified"></a>A jelszó nem ellenőrizhető
Ha a telepítővarázsló sikeresen csatlakozik az Azure AD-hoz, de a jelszót nem lehet ellenőrizni, a következő hibaüzenet jelenik meg: ![ helytelen jelszó.](./media/tshoot-connect-connectivity/badpassword.png)

* A jelszó ideiglenes jelszó, és meg kell változtatni? Valóban a helyes jelszó? Próbáljon bejelentkezni `https://login.microsoftonline.com` (a Azure ad Connect-kiszolgálót futtató másik számítógépre), és ellenőrizze, hogy a fiók használható-e.

### <a name="verify-proxy-connectivity"></a>Proxy kapcsolatának ellenőrzése
Annak ellenőrzéséhez, hogy a Azure AD Connect-kiszolgáló rendelkezik-e tényleges kapcsolattal a proxyval és az internettel, a PowerShell használatával ellenőrizze, hogy a proxy engedélyezi-e a webes kérelmeket. A PowerShell-parancssorban futtassa a parancsot `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc` . (Technikailag az első hívás, `https://login.microsoftonline.com` és ez az URI is működik, de a másik URI gyorsabban reagál.)

A PowerShell a machine.config fájl konfigurációját használja a proxyval való kapcsolatfelvételhez. A WinHTTP/Netsh beállításai nincsenek hatással ezekre a parancsmagokra.

Ha a proxy megfelelően van konfigurálva, akkor sikeres állapotot kell kapnia: ![ képernyőfelvétel, amely a sikeres állapotot jeleníti meg, ha a proxy megfelelően van konfigurálva.](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Ha nem **tud csatlakozni a távoli kiszolgálóhoz**, a PowerShell a proxy vagy a DNS használata nélkül próbál közvetlen hívást végrehajtani, és nem megfelelően van konfigurálva. Győződjön meg arról, hogy a **machine.config** fájl megfelelően van konfigurálva.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Ha a proxy helytelenül van konfigurálva, hibaüzenet jelenik meg: ![ proxy200 ](./media/tshoot-connect-connectivity/invokewebrequest403.png)
 ![ proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Hiba | Hiba szövege | Megjegyzés |
| --- | --- | --- |
| 403 |Forbidden |A kért URL-címhez nem lett megnyitva a proxy. Nyissa meg újra a proxy konfigurációját, és győződjön meg róla, hogy megnyitotta az [URL-címeket](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) . |
| 407 |Proxy hitelesítés szükséges |A proxykiszolgáló számára szükséges a bejelentkezés, és a nincs megadva. Ha a proxykiszolgáló hitelesítést igényel, győződjön meg arról, hogy a beállítás konfigurálva van a machine.config. Győződjön meg arról is, hogy tartományi fiókokat használ a varázslót és a szolgáltatásfiókot futtató felhasználó számára. |

### <a name="proxy-idle-timeout-setting"></a>Proxy tétlen időtúllépési beállítása
Ha a Azure AD Connect exportálási kérelmet küld az Azure AD-nak, az Azure AD akár 5 percet is igénybe vehet a kérelem feldolgozásához a válasz létrehozása előtt. Ez különösen akkor fordulhat elő, ha az adott exportálási kérelemben több csoport objektum is található nagy csoporttagság esetén. Győződjön meg arról, hogy a proxy üresjárati időkorlátja 5 percnél nagyobb értékre van konfigurálva. Ellenkező esetben előfordulhat, hogy az Azure AD időszakos kapcsolódási problémája észlelhető a Azure AD Connect kiszolgálón.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Azure AD Connect és az Azure AD közötti kommunikációs minta
Ha követte ezeket az előző lépéseket, és továbbra sem tud kapcsolatot létesíteni, előfordulhat, hogy ezen a ponton elkezdi a hálózati naplók megtekintését. Ez a szakasz egy normál és sikeres kapcsolati mintát dokumentál. Emellett olyan gyakori vörös heringeket is felsorol, amelyeket figyelmen kívül hagyhat a hálózati naplók olvasása során.

* Vannak hívások `https://dc.services.visualstudio.com` . A telepítés sikerességéhez nem szükséges, hogy az URL-cím ne legyen megnyitva a proxyban, és ezek a hívások figyelmen kívül hagyhatók.
* Láthatja, hogy a DNS-feloldás felsorolja a tényleges gazdagépeket a DNS-névtér nsatc.net és más, nem a microsoftonline.com alatt lévő névterekben. Azonban nincsenek webszolgáltatási kérelmek a tényleges kiszolgálók neveiben, és nem kell hozzáadnia ezeket az URL-címeket a proxyhoz.
* A végpontok adminwebservice és provisioningapi a felderítési végpontok, és a ténylegesen használandó végpont megtalálására szolgálnak. Ezek a végpontok a régiótól függően eltérőek.

### <a name="reference-proxy-logs"></a>Hivatkozási proxy naplói
Itt látható egy, a tényleges proxy naplóból és a telepítővarázsló oldaláról származó memóriakép, amelyből a rendszer létrehozta az adatokat (a duplikált bejegyzéseket ugyanahhoz a végponthoz eltávolították). Ez a szakasz a saját proxy-és hálózati naplókra mutató hivatkozásként használható. A tényleges végpontok eltérőek lehetnek a környezetben (különösen a *dőlt* URL-címeknél).

**Kapcsolódás az Azure AD-hez**

| Idő | URL-cím |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800 – Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-Relay*. microsoftonline.com:443 |

**Konfigurálás**

| Idő | URL-cím |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800 – Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900 – Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800 – Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-Relay*. microsoftonline.com:443 |

**Kezdeti szinkronizálás**

| Idő | URL-cím |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900 – Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800 – Anchor*. microsoftonline.com:443 |

## <a name="authentication-errors"></a>Hitelesítési hibák
Ez a szakasz azokat a hibákat mutatja be, amelyeket a ADAL (a Azure AD Connect által használt hitelesítési függvénytár) és a PowerShell adhat vissza. A hiba magyarázata segít megérteni a következő lépéseket.

### <a name="invalid-grant"></a>Érvénytelen engedélyezés
Érvénytelen felhasználónév vagy jelszó. További információ: [a jelszó nem ellenőrizhető](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Ismeretlen felhasználói típus
Az Azure AD-címtár nem található vagy nem oldható fel. Lehet, hogy megpróbál bejelentkezni egy nem ellenőrzött tartományba tartozó felhasználónévvel?

### <a name="user-realm-discovery-failed"></a>A felhasználói tartomány felderítése nem sikerült
Hálózati vagy proxy-konfigurációs problémák. A hálózat nem érhető el. Lásd: [csatlakozási problémák hibaelhárítása a telepítővarázsló](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>A felhasználói jelszó lejárt
A hitelesítő adatai lejártak. Módosítsa a jelszavát.

### <a name="authorization-failure"></a>Engedélyezési hiba
Nem sikerült engedélyezni a felhasználónak az Azure AD-ben végrehajtandó műveletet.

### <a name="authentication-canceled"></a>Hitelesítés megszakítva
A multi-Factor Authentication (MFA) kihívás megszakadt.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Nem sikerült csatlakozni az MS online-hoz
A hitelesítés sikeres volt, de az Azure AD PowerShell hitelesítési problémával rendelkezik.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Azure AD globális rendszergazdai szerepkör szükséges
A felhasználó hitelesítése sikerült. A felhasználóhoz azonban nincs hozzárendelve globális rendszergazdai szerepkör. Így [globális rendszergazdai szerepkört rendelhet](../roles/permissions-reference.md) a felhasználóhoz.

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Privileged Identity Management engedélyezve
A hitelesítés sikeres volt. A Privileged Identity Management engedélyezve van, és jelenleg nem globális rendszergazda. További információ: [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>A vállalati adatok nem érhetők el
A hitelesítés sikeres volt. Nem sikerült beolvasni a vállalati adatokat az Azure AD-ből.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>A tartományi adatok nem érhetők el
A hitelesítés sikeres volt. Nem sikerült beolvasni a tartományi adatokat az Azure AD-ből.

### <a name="unspecified-authentication-failure"></a>Meghatározatlan hitelesítési hiba
Váratlan hibaként jelenik meg a telepítési varázslóban. Akkor fordulhat elő, ha egy **iskolai vagy szervezeti fiók** helyett **Microsoft-fiókot** próbál használni.

## <a name="troubleshooting-steps-for-previous-releases"></a>A korábbi kiadásokkal kapcsolatos hibaelhárítási lépések.
A Build Number 1.1.105.0 (kiadás: február 2016) kezdődő kiadások esetében a Bejelentkezési segéd kivonásra került. Ezt a szakaszt és a konfigurációt már nem kötelező megadni, de hivatkozásként kell tárolni.

Az egyszeri bejelentkezési segéd működéséhez konfigurálni kell a WinHTTP-t. Ez a konfiguráció a [**netsh**](how-to-connect-install-prerequisites.md#connectivity)használatával végezhető el.
![A képernyőképen a netsh eszközt futtató parancssori ablak jelenik meg egy proxy beállításához.](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>A bejelentkezési Segéd nem lett megfelelően konfigurálva
Ez a hiba akkor jelenik meg, ha a bejelentkezési Segéd nem éri el a proxyt, vagy a proxy nem engedélyezi a kérést.
![A képernyőképen a következő hibaüzenet látható: nem sikerült ellenőrizni a hitelesítő adatokat, ellenőrizze a hálózati kapcsolatot, valamint a tűzfal vagy a proxy beállításait.](./media/tshoot-connect-connectivity/nonetsh.png)

* Ha ezt a hibát látja, tekintse meg a proxy konfigurációját a [netsh](how-to-connect-install-prerequisites.md#connectivity) -ben, és ellenőrizze, hogy helyes-e.
  ![Képernyőfelvétel: a netsh eszközt futtató parancssori ablak, amely a proxy konfigurációját jeleníti meg.](./media/tshoot-connect-connectivity/netshshow.png)
* Ha úgy tűnik, hogy helyes, kövesse a [proxy kapcsolatának ellenőrzése](#verify-proxy-connectivity) című témakör lépéseit, és ellenőrizze, hogy a probléma a varázslón kívül található-e.

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
