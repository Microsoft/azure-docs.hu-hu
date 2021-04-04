---
title: LDAP-hitelesítés Azure Active Directory
description: Építészeti útmutató az LDAP-hitelesítés Azure Active Directory használatával történő eléréséhez.
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
ms.openlocfilehash: f209d394e1a0c2c4ddde9cbf8df2704647e2822a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168712"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>LDAP-hitelesítés Azure Active Directory

A Lightweight Directory Access Protocol (LDAP) egy olyan alkalmazási protokoll, amellyel különböző címtárszolgáltatás működik. A címtárszolgáltatások, például a Active Directory, a [felhasználói és a fiókadatok](https://www.dnsstuff.com/active-directory-service-accounts), valamint a biztonsági információk (például jelszavak) tárolására szolgálnak. A szolgáltatás ezután lehetővé teszi az adatok megosztását a hálózaton lévő más eszközökkel. A vállalati alkalmazások, például az e-mailek, az Ügyfélkapcsolat-kezelők (CRM-EK) és az emberi erőforrások (HR) szoftverei az LDAP használatával hitelesítik, érhetik el és kereshetik meg az információkat. 

Azure Active Directory (Azure AD) támogatja ezt a mintát Azure AD Domain Services (AD DS) használatával. Lehetővé teszi az olyan szervezetek számára, amelyek egy Felhőbeli első stratégiát vezetnek be a környezet korszerűsítéséhez azáltal, hogy a helyszíni LDAP-erőforrásokat a felhőbe helyezik. Az azonnali előnyök a következőket jelentik: 

* Integrálva van az Azure AD-vel. A felhasználók és csoportok hozzáadását, illetve az objektumokra vonatkozó attribútumok módosításait a rendszer automatikusan szinkronizálja az Azure AD-bérlőből a AD DSba. A helyszíni Active Directoryban lévő objektumok módosításai szinkronizálva lesznek az Azure AD-vel, majd AD DS.

* Egyszerűsítse a műveleteket. Csökkenti a helyszíni infrastruktúrák manuális megőrzésének és javításának szükségességét. 

* Megbízható. Felügyelt, magasan elérhető szolgáltatásokat kap 

## <a name="use-when"></a>A következő esetekben használja

Egy alkalmazásnak vagy szolgáltatásnak LDAP-hitelesítést kell használnia.

![Architektúra ábrája](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>A System összetevői

* **Felhasználó**: az LDAP-függő alkalmazások elérése böngészőn keresztül.

* **Webböngésző**: az alkalmazás külső URL-címének eléréséhez a felhasználó által kommunikáló felület.

* **Virtual Network**: egy Azure-beli magánhálózati hálózat, amelyen keresztül az örökölt alkalmazás képes az LDAP-szolgáltatások használatára. 

* **Örökölt alkalmazások**: olyan alkalmazások vagy kiszolgálói munkaterhelések, amelyek az Azure-ban egy virtuális hálózaton üzembe helyezett LDAP-t igényelnek, vagy amelyek hálózati útvonalakon keresztül láthatók a AD DS példányok IP-címeire. 

* **Azure ad**: a szervezet helyi könyvtárából származó azonosító adatok szinkronizálása Azure ad Connect használatával.

* **Azure ad Domain Services (AD DS)**: egyirányú szinkronizálást végez az Azure ad-ből, hogy hozzáférést biztosítson a felhasználók, csoportok és hitelesítő adatok központi készletéhez. A AD DS példány egy virtuális hálózathoz van rendelve. Az Azure-ban a AD DShoz rendelt virtuális hálózathoz csatlakozó alkalmazások, szolgáltatások és virtuális gépek olyan általános AD DS funkciókat használhatnak, mint az LDAP, a tartományhoz való csatlakozás, a csoportházirend, a Kerberos és az NTLM-hitelesítés.
   > [!NOTE]
   >  Olyan környezetekben, ahol a szervezet nem tudja szinkronizálni a jelszó-kivonatokat, vagy ha a felhasználók intelligens kártyával jelentkeznek be, javasoljuk, hogy használjon AD DS erőforrás-erdőt. 

* **Azure ad Connect**: egy eszköz, amely a helyszíni identitás információit Microsoft Azure Adre szinkronizálja. A központi telepítési varázsló és az interaktív funkciók segítségével konfigurálhatja a kapcsolódáshoz szükséges előfeltételeket és összetevőket, beleértve a szinkronizálást és a bejelentkezést Active Directory az Azure AD-be. 

* **Active Directory**: [a helyszíni identitási adatokat tároló címtárszolgáltatás, például felhasználói és fiókadatok](https://www.dnsstuff.com/active-directory-service-accounts), valamint biztonsági információk, például jelszavak.

## <a name="implement-ldap-authentication-with-azure-ad"></a>LDAP-hitelesítés implementálása az Azure AD-vel

* [Azure AD DS-példány létrehozása és konfigurálása](../../active-directory-domain-services/tutorial-create-instance.md) 

* [Virtuális hálózatkezelés konfigurálása Azure AD DS-példányhoz](../../active-directory-domain-services/tutorial-configure-networking.md) 

* [Secure LDAP konfigurálása Azure AD DS felügyelt tartományhoz](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

* [Kimenő erdőszintű megbízhatósági kapcsolat létrehozása helyszíni tartományhoz az Azure-ban AD DS](../../active-directory-domain-services/tutorial-create-forest-trust.md)

