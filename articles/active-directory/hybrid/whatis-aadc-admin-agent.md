---
title: Mi a Azure AD Connect felügyeleti ügynök – Azure AD Connect | Microsoft Docs
description: Ismerteti a helyszíni környezet Azure AD-vel való szinkronizálásához és figyeléséhez használt eszközöket.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da4e1cfc68dff4ad2bc0552c6d35fe1230779306
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91312957"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Mi az az Azure AD Connect felügyeleti ügynök? 
A Azure AD Connect felügyeleti ügynök a Azure Active Directory Connect egy új összetevője, amely Azure Active Directory Connect-kiszolgálóra telepíthető. A szolgáltatás a Active Directory-környezetből származó adatok összegyűjtésére szolgál, amely segít a Microsoft támogatási szakemberének a támogatási eset megnyitásakor felmerülő problémák megoldásában. 

>[!NOTE]
>A rendszergazdai ügynök nincs telepítve, és alapértelmezés szerint nincs engedélyezve.  Telepítenie kell az ügynököt, hogy adatokat gyűjtsön a támogatási esetekkel való segítségnyújtás érdekében.

Ha telepítve van, a Azure AD Connect felügyeleti ügynök megvárja a Azure Active Directorytól érkező adatokra vonatkozó kérelmeket, beolvassa a kért adatokat a szinkronizálási környezetből, és elküldi azt a Azure Active Directorynak, ahol a Microsoft támogatási szakembere kapja meg. 

A Azure AD Connect felügyeleti ügynöknek a környezetből való lekérése nem történik meg semmilyen módon. a szolgáltatás csak a Microsoft támogatási szakembere számára jelenik meg, hogy segítse őket a Azure Active Directory Connect kapcsolódó támogatási eset kivizsgálása és hibaelhárítása során, amikor megnyitotta a Azure AD Connect felügyeleti ügynököt, alapértelmezés szerint nincs telepítve a Azure AD Connect-kiszolgálón. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Telepítse a Azure AD Connect felügyeleti ügynököt a Azure AD Connect-kiszolgálóra 

Előfeltételek:
1.    Azure AD Connect telepítve van a kiszolgálón
2.    Azure AD Connect Health telepítve van a kiszolgálón

![felügyeleti ügynök](media/whatis-aadc-admin-agent/adminagent0.png)

A Azure AD Connect felügyeleti ügynök bináris fájljait a HRE-összekötő kiszolgálója helyezi el. Az ügynök telepítéséhez tegye a következőket:

1.    A PowerShell megnyitása rendszergazdai módban
2.    Navigáljon ahhoz a könyvtárhoz, ahol az alkalmazás található CD "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.    ConfigureAdminAgent.ps1 futtatása

Ha a rendszer kéri, adja meg az Azure AD globális rendszergazdai hitelesítő adatait. A Azure AD Connect telepítés során megadott hitelesítő adatoknak kell szerepelniük.

Az ügynök telepítése után a következő két új program jelenik meg a "programok telepítése és törlése" listában a kiszolgáló Vezérlőpultján: 

![Képernyőfelvétel: a programok telepítése és törlése lista, amely tartalmazza a hozzáadott új programokat.](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>A szinkronizálási szolgáltatásom milyen adatai jelennek meg a Microsoft Service engineerben? 
Amikor megnyit egy támogatási esetet, az Microsoft ügyfélszolgálata mérnök láthatja, hogy egy adott felhasználó számára a Active Directory, a Active Directory-összekötő területe a Azure Active Directory Connect-kiszolgálón, a Azure Active Directory-kiszolgáló és a metaverse a Azure Active Directory Connect-kiszolgálón. 

A Microsoft ügyfélszolgálata mérnök nem módosíthatja a rendszeren lévő összes információt, és nem láthat jelszavakat. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Mi a teendő, ha nem szeretném, hogy a Microsoft támogatási szakembere hozzáférjen az adataim eléréséhez? 
Ha az ügynök telepítése után nem szeretné, hogy a Microsoft szolgáltatási mérnöke hozzáférjen az adataihoz egy támogatási híváshoz, a szolgáltatás konfigurációs fájljának módosításával letilthatja a funkciót a következő módon: 

1. Nyissa meg a **C:\Program Files\Microsoft Azure ad Connect felügyeleti Agent\AzureADConnectAdministrationAgentService.exe.config** a Jegyzettömbben.
2. Tiltsa le a **UserDataEnabled** beállítást az alább látható módon. Ha a **UserDataEnabled** -beállítás létezik, és igaz értékre van állítva, állítsa hamis értékre. Ha a beállítás nem létezik, adja hozzá a beállítást az alább látható módon.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3. Mentse a konfigurációs fájlt.
4. Azure AD Connect felügyeleti ügynök szolgáltatás újraindítása az alább látható módon

![Képernyőfelvétel: az Azure AD Administrator Agent szolgáltatás újraindításának helye.](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
