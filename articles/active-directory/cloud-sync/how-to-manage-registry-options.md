---
title: 'Azure AD Connect Cloud kiépítési ügynök: a beállításjegyzék beállításainak kezelése | Microsoft Docs'
description: Ez a cikk bemutatja, hogyan kezelheti a beállításjegyzék beállításait a Azure AD Connect Cloud kiépítési ügynökben.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.reviewer: chmutali
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f39ef611e2ea15ef3bc3dbfcf09e9624cbcf8b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678221"
---
# <a name="manage-agent-registry-options"></a>Ügynök beállításjegyzék-beállításainak kezelése

Ez a szakasz azokat a beállításjegyzék-beállításokat ismerteti, amelyekkel szabályozhatja az Azure AD Connect létesítési ügynök futásidejű feldolgozási viselkedését. 

## <a name="configure-ldap-connection-timeout"></a>Az LDAP-kapcsolat időtúllépésének konfigurálása
Ha a konfigurált Active Directory tartományvezérlőkön végez LDAP-műveleteket, alapértelmezés szerint a kiépítési ügynök az alapértelmezett kapcsolati időtúllépési értéket használja 30 másodpercnél. Ha a tartományvezérlő több időt vesz igénybe, a következő hibaüzenet jelenik meg az ügynök naplófájljában: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

Az LDAP-keresési műveletek hosszabb időt vehetnek igénybe, ha a keresési attribútum nincs indexelve. Első lépésként, ha a fenti hiba jelenik meg, először ellenőrizze, hogy a keresési/keresési attribútum [indexelt](/windows/win32/ad/indexed-attributes)-e. Ha a keresési attribútumok indexelve vannak, és a hiba továbbra is fennáll, az alábbi lépések végrehajtásával növelheti az LDAP-kapcsolat időtúllépését: 

1. Jelentkezzen be rendszergazdaként az Azure AD Connect üzembe helyezési ügynököt futtató Windows-kiszolgálón.
1. A *Futtatás* menüpont használatával nyissa meg a beállításszerkesztőt (regedit.exe) 
1. Keresse meg a kulcs mappáját **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Kattintson a jobb gombbal, és válassza a "New-> string Value" lehetőséget.
1. Adja meg a nevet: `LdapConnectionTimeoutInMilliseconds`
1. Kattintson duplán az **érték nevére** , és adja meg az értéket `60000` ezredmásodpercben.
    > [!div class="mx-imgBorder"]
    > ![LDAP-kapcsolat időtúllépése](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Indítsa újra a Azure AD Connect kiépítési szolgáltatást a *szolgáltatások* konzolról.
1. Ha több kiépítési ügynököt telepített, alkalmazza a beállításjegyzék módosítását az összes ügynökre a konzisztencia érdekében. 

## <a name="configure-referral-chasing"></a>Hivatkozói átirányítás konfigurálása
Alapértelmezés szerint az Azure AD Connect kiépítési ügynök nem üldözi az [átirányítást](/windows/win32/ad/referrals). Előfordulhat, hogy engedélyezni szeretné az átirányítást, hogy támogassa bizonyos HR bejövő kiépítési forgatókönyveket, például a következőket: 
* UPN egyediségének ellenőrzése több tartományon belül
* A tartományok közötti kezelőre vonatkozó hivatkozások feloldása

Az alábbi lépéseket követve bekapcsolhatja az átirányítást:

1. Jelentkezzen be rendszergazdaként az Azure AD Connect üzembe helyezési ügynököt futtató Windows-kiszolgálón.
1. A *Futtatás* menüpont használatával nyissa meg a beállításszerkesztőt (regedit.exe) 
1. Keresse meg a kulcs mappáját **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Kattintson a jobb gombbal, és válassza a "New-> string Value" lehetőséget.
1. Adja meg a nevet: `ReferralChasingOptions`
1. Kattintson duplán az **érték nevére** , és adja meg a Value (érték `96` ) értéket. Ez az érték felel meg a konstans értékének, `ReferralChasingOptions.All` és megadja, hogy a rendszer a részfa és az alapszintű hivatkozásokat is követi az ügynök. 
    > [!div class="mx-imgBorder"]
    > ![Hivatkozó átirányítás](media/how-to-manage-registry-options/referral-chasing.png)
1. Indítsa újra a Azure AD Connect kiépítési szolgáltatást a *szolgáltatások* konzolról.
1. Ha több kiépítési ügynököt telepített, alkalmazza a beállításjegyzék módosítását az összes ügynökre a konzisztencia érdekében.

## <a name="skip-gmsa-configuration"></a>GMSA-konfiguráció kihagyása
Az ügynök 1.1.281.0 + verziójának használatakor alapértelmezés szerint az ügynök konfigurációja varázsló futtatásakor a rendszer felszólítja a [csoportosan felügyelt szolgáltatásfiók (GMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)beállítására. A varázsló által a GMSA beállítás minden szinkronizálási és kiépítési művelethez használatos. 

Ha az ügynök egy korábbi verziójáról frissít, és egyéni szolgáltatásfiókot állított be a Active Directory topológiára vonatkozó delegált szervezeti szintű engedélyekkel, érdemes kihagyni/elhalasztania a GMSA konfigurációját, és terveznie a módosítást. 

> [!NOTE]
> Ez az útmutató kifejezetten azokra az ügyfelekre vonatkozik, akik a 1.1.281.0 előtt konfigurálták a HR (munkanap/SuccessFactors) szolgáltatást, és egyéni szolgáltatásfiókot állítanak be az ügynök műveleteihez. Hosszú távon ajánlott eljárásként váltson a GMSA-re.  

Ebben az esetben továbbra is frissítheti az ügynök bináris fájljait, és kihagyhatja a GMSA-konfigurációt az alábbi lépések végrehajtásával: 

1. Jelentkezzen be rendszergazdaként az Azure AD Connect üzembe helyezési ügynököt futtató Windows-kiszolgálón.
1. Az új ügynök bináris fájljainak telepítéséhez futtassa az ügynök telepítőjét. Az ügynök konfigurálása varázsló bezárásával automatikusan megnyílik a telepítés sikeres elindítása után. 
1. A *Futtatás* menüpont használatával nyissa meg a beállításszerkesztőt (regedit.exe) 
1. Keresse meg a kulcs mappáját **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Kattintson a jobb gombbal, és válassza a "New-> DWORD Value" lehetőséget.
1. Adja meg a nevet: `UseCredentials`
1. Kattintson duplán az **érték nevére** , és adja meg a Value (érték `1` ) értéket.  
    > [!div class="mx-imgBorder"]
    > ![Hitelesítő adatok használata](media/how-to-manage-registry-options/use-credentials.png)
1. Indítsa újra a Azure AD Connect kiépítési szolgáltatást a *szolgáltatások* konzolról.
1. Ha több kiépítési ügynököt telepített, alkalmazza a beállításjegyzék módosítását az összes ügynökre a konzisztencia érdekében.
1. Az asztal rövid kivágása alatt futtassa az ügynök konfigurációja varázslót. A varázsló kihagyja a GMSA-konfigurációt. 


> [!NOTE]
> A beállításjegyzék beállításait a [részletes naplózás](how-to-troubleshoot.md#log-files)engedélyezésével ellenőrizheti. Az ügynök indításakor kibocsátott naplók megjelenítik a beállításjegyzékből kiválasztott konfigurációs értékeket. 

## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)

