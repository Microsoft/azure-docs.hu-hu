---
title: Inaktív felhasználói fiókok kezelése az Azure AD-| Microsoft Docs
description: Ismerje meg, hogyan észlelheti és kezelheti az elavult felhasználói fiókokat az Azure AD-ban
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ea62a8d602cc472269b52c230529aa3f9b86ed4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535106"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Útmutató: Inaktív felhasználói fiókok kezelése az Azure AD-ban

Nagy környezetekben a felhasználói fiókok nem mindig törlődnek, amikor az alkalmazottak elhagyják a szervezetet. Rendszergazdaként ön szeretné észlelni és kezelni ezeket az elavult felhasználói fiókokat, mivel ezek biztonsági kockázatot jelentenek.

Ez a cikk egy módszert ismerteti az elavult felhasználói fiókok kezelésével az Azure AD-ban. 

## <a name="what-are-inactive-user-accounts"></a>Mik azok az inaktív felhasználói fiókok?

Az inaktív fiókok olyan felhasználói fiókok, amelyekre már nincs szükség a szervezet tagjai számára az erőforrásokhoz való hozzáféréshez. Az inaktív fiókok egyik kulcsazonosítója, hogy már egy ideje nem használták őket *a* környezetbe való bejelentkezéshez. Mivel az inaktív fiókok a bejelentkezési tevékenységhez vannak kötve, a legutóbbi sikeres bejelentkezés időbélyegzőjével észlelheti őket. 

Ennek a metódusnak az a kihívása, hogy *meghatározza,* mit jelent egy ideig a környezete esetében. Előfordulhat például, hogy a felhasználók egy ideig nem jelentkeznek be *egy környezetbe,* mert szabadságon vannak. Az inaktív felhasználói fiókok eltérésének meghatározásakor minden megbízható okot figyelembe kell kell fogni a környezetbe való bejelentkezés mellet. Számos szervezetben az inaktív felhasználói fiókok eltérése 90 és 180 nap között van. 

Az utolsó sikeres bejelentkezés potenciális betekintést nyújt a felhasználó erőforrásokhoz való folyamatos hozzáférés-hez való ismeretibe.  Segíthet meghatározni, hogy szükség van-e még csoporttagságra vagy alkalmazás-hozzáférésre, vagy eltávolítható-e. Külső felhasználókezelés esetén tudnia kell, hogy egy külső felhasználó még aktív-e a bérlőn belül, vagy meg kell-e tisztítani. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Inaktív felhasználói fiókok észlelése

Az inaktív fiókok észlelése az API **signInActivity** erőforrástípusa által elérhetővé vált **lastSignInDateTime** **tulajdonság Microsoft Graph** meg. Ezzel a tulajdonságkal a következő forgatókönyvekhez valósíthat meg megoldást:

- **Felhasználók név szerint:** Ebben a forgatókönyvben egy adott felhasználóra keres név alapján, amely lehetővé teszi a lastSignInDateTime kiértékelését: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Felhasználók dátum szerint:** Ebben a forgatókönyvben olyan felhasználók listáját kéri le, akiknél a lastSignInDateTime érték a megadott dátum előtt van: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`

> [!NOTE]
> Szükség lehet az összes felhasználó utolsó bejelentkezési dátumának jelentésére, ha igen, az alábbi forgatókönyvet használhatja.
> **Utolsó bejelentkezési dátum és** időpont minden felhasználóhoz: Ebben a forgatókönyvben le kell kérnie az összes felhasználó listáját, valamint az utolsó lastSignInDateTime azonosítót az egyes felhasználókhoz: `https://graph.microsoft.com/beta/users?$select=displayName,signInActivity` 

## <a name="what-you-need-to-know"></a>Amit még tudnia kell

Ebben a szakaszban a lastSignInDateTime tulajdonságról tudni kell.

### <a name="how-can-i-access-this-property"></a>Hogyan férhetek hozzá ehhez a tulajdonsághoz?

A **lastSignInDateTime** tulajdonságot az erőforrás [signInActivity](/graph/api/resources/signinactivity?view=graph-rest-beta&preserve-view=true) erőforrástípusa [teszi Microsoft Graph REST API.](/graph/overview#whats-in-microsoft-graph)   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Elérhető a lastSignInDateTime tulajdonság a Get-AzureAdUser parancsmagon keresztül?

Nem.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Az Azure AD melyik kiadására van szükségem a tulajdonság eléréséhez?

Ezt a tulajdonságot az Azure AD összes kiadásában elérheti.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Milyen engedélyre van szükségem a tulajdonság olvassa?

A tulajdonság olvass el, ha megadja a következő jogosultságokat: 

- AuditLogs.Read.All
- Szervezet.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Mikor frissíti az Azure AD a tulajdonságot?

Minden sikeres interaktív bejelentkezés a mögöttes adattár frissítését jelenti. A sikeres bejelentkezések általában 10 percen belül jelentkőznek meg a kapcsolódó bejelentkezési jelentésben.
 

### <a name="what-does-a-blank-property-value-mean"></a>Mit jelent az üres tulajdonságérték?

A lastSignInDateTime időbélyeg létrehozásához sikeres bejelentkezésre van szükség. Mivel a lastSignInDateTime tulajdonság egy új funkció, a lastSignInDateTime tulajdonság értéke üres lehet, ha:

- A felhasználó utolsó sikeres bejelentkezése 2020 áprilisa előtt történt.
- Az érintett felhasználói fiókot soha nem használták sikeres bejelentkezéshez.

## <a name="next-steps"></a>Következő lépések

* [Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
* [Api-referencia naplózása](/graph/api/resources/directoryaudit) 
* [Bejelentkezési tevékenység jelentésének API-referenciája](/graph/api/resources/signin)
