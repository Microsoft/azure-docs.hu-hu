---
title: LDAP-szinkronizálás Azure Active Directory
description: Építészeti útmutató az LDAP-szinkronizálás megvalósításához Azure Active Directory használatával.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f34e734b315c7c05ce77f5e168a452fc1c1c547
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168678"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>LDAP-szinkronizálás Azure Active Directory

A Lightweight Directory Access Protocol (LDAP) egy címtárszolgáltatás protokoll, amely a TCP/IP-veremben fut. Az internetes címtárakhoz való kapcsolódáshoz, kereséshez és módosításhoz használható mechanizmust biztosít. Az LDAP-címtárszolgáltatás egy ügyfél-kiszolgáló modellen alapul, és a funkciója egy meglévő címtár elérésének engedélyezése. Számos vállalat függ a helyszíni LDAP-kiszolgálótól, hogy a felhasználók és csoportok a kritikus fontosságú üzleti alkalmazásokhoz legyenek tárolva. 

Azure Active Directory (Azure AD) lecserélheti az LDAP-szinkronizálást Azure AD Connect használatával. A Azure AD Connect szinkronizációs szolgáltatás minden olyan műveletet végrehajt, amely az identitási adatoknak a helyszíni környezetek és az Azure AD közötti szinkronizálására vonatkozik. 

## <a name="use-when"></a>A következő esetekben használja

Az azonosító adatokat szinkronizálnia kell a helyszíni LDAP v3-címtárak és az Azure AD között. 

![építészeti diagram](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>A System összetevői

* **Felhasználó**: hozzáfér egy olyan alkalmazáshoz, amely egy LDAP v3-címtár használatára támaszkodik a felhasználók és a jelszavak rendezéséhez.

* **Webböngésző**: az alkalmazás külső URL-címének eléréséhez a felhasználó által kommunikáló összetevő

* **Webalkalmazás**: az LDAP v3-címtárak függőségeivel rendelkező alkalmazás.

* **Azure ad**: az Azure ad a szervezet helyszíni LDAP-könyvtáraiból Azure ad Connecton keresztül szinkronizálja az identitási adatokat (felhasználókat, csoportokat, jelszavakat). 

* **Azure ad Connect**: a helyszíni identitás-infrastruktúrák Microsoft Azure ADhoz való csatlakoztatására szolgáló eszköz. A varázsló és az interaktív funkciók segítenek a csatlakozáshoz szükséges előfeltételek és összetevők üzembe helyezésében és konfigurálásában. 

* **Egyéni összekötő**: az általános LDAP-összekötő lehetővé teszi az Azure ad Connect synchronization szolgáltatás integrálását egy LDAP v3-kiszolgálóval. Azure AD Connect.

* **Active Directory**: a Active Directory a legtöbb Windows Server operációs rendszerhez tartozó címtárszolgáltatás. Active Directory címtárszolgáltatást futtató kiszolgálókat tartományvezérlőknek nevezzük, és a Windows-tartomány összes felhasználóját és számítógépét hitelesítik és engedélyezik.

* **LDAP v3-kiszolgáló**: LDAP protokollal kompatibilis könyvtár, amely a címtárszolgáltatások hitelesítéséhez használt vállalati felhasználókat és jelszavakat tárolja.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>LDAP-szinkronizálás megvalósítása az Azure AD-vel

* [Hibrid identitási címtár-integrációs eszközök](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Azure AD Connect telepítési ütemterv](../hybrid/how-to-connect-install-roadmap.md) 

* [LDAP-összekötő áttekintése és létrehozása](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > Az LDAP-összekötők olyan speciális konfiguráció, amely a Forefront Identity Manager és/vagy Microsoft Identity Manager ismeretét igényli. Ha éles környezetben használja, javasoljuk, hogy a konfigurációval kapcsolatos kérdéseit [Premier szintű támogatás](https://support.microsoft.com/premier) vagy Microsoft Partner Network kell átesnie.

