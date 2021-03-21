---
title: Inaktív felhasználói fiókok kezelése az Azure AD-ben | Microsoft Docs
description: További információ az Azure AD-beli felhasználói fiókok észleléséről és kezeléséről, amely elavulttá vált
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
ms.openlocfilehash: 8fb517f8c50ad2c32f23542e60069a0e0a496a2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660664"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Útmutató: inaktív felhasználói fiókok kezelése az Azure AD-ben

Nagyméretű környezetekben a felhasználói fiókok nem törlődnek mindig, amikor az alkalmazottak elhagynak egy szervezetet. Rendszergazdaként szeretné felderíteni és kezelni ezeket az elavult felhasználói fiókokat, mivel azok biztonsági kockázatot jelentenek.

Ez a cikk az elavult felhasználói fiókok Azure AD-ben való kezelésére szolgáló metódust ismerteti. 

## <a name="what-are-inactive-user-accounts"></a>Mik azok az inaktív felhasználói fiókok?

Az inaktív fiókok olyan felhasználói fiókok, amelyek nem szükségesek többé a szervezet tagjai számára, hogy hozzáférjenek az erőforrásaihoz. Az inaktív fiókok egyik kulcsa, hogy *egy ideig* nem használták fel a bejelentkezést a környezetbe. Mivel az inaktív fiókok a bejelentkezési tevékenységhez vannak kötve, a legutóbbi bejelentkezés időbélyegét használhatja, amely sikeresen észlelte őket. 

Ennek a módszernek a feladata annak meghatározása, hogy a környezet esetében milyen *egy ideig* . Előfordulhat például, hogy a felhasználók *egy ideig* nem jelentkezhetnek be egy környezetbe, mert a vakáción vannak. Az inaktív felhasználói fiókok különbözetének meghatározásakor a környezetbe való bejelentkezéshez szükséges összes jogos okot figyelembe kell vennie. Számos szervezetben az inaktív felhasználói fiókok különbözete 90 és 180 nap közé esik. 

A legutóbbi sikeres bejelentkezés lehetővé teszi, hogy a felhasználók továbbra is hozzáférjenek az erőforrásokhoz.  Segítséget nyújt annak meghatározásában, hogy a csoporttagság vagy az alkalmazás-hozzáférés továbbra is szükséges-e, vagy el lehet-e távolítani. A külső felhasználók felügyeletéhez megtudhatja, hogy egy külső felhasználó még aktív-e a bérlőn belül, vagy törölni kell. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Inaktív felhasználói fiókok észlelése

Az inaktív fiókok észleléséhez ki kell értékelni a **Microsoft Graph** API **signInActivity** -erőforrástípus által megjelenített **lastSignInDateTime** tulajdonságot. Ennek a tulajdonságnak a használatával a következő helyzetekben hozhat létre megoldást:

- **Felhasználók név szerint**: ebben az esetben név alapján keres egy adott felhasználót, amely lehetővé teszi a lastSignInDateTime kiértékelését: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Felhasználók dátum szerint**: ebben a forgatókönyvben a megadott dátum előtt egy lastSignInDateTime rendelkező felhasználók listáját kéri le: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>Amit még tudnia kell

Ez a szakasz felsorolja, hogy mit kell tudnia a lastSignInDateTime tulajdonságról.

### <a name="how-can-i-access-this-property"></a>Hogyan lehet hozzáférni ehhez a tulajdonsághoz?

A **lastSignInDateTime** tulajdonságot a [Microsoft Graph REST API](/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph) [signInActivity erőforrástípus](/graph/api/resources/signinactivity?view=graph-rest-beta) teszi elérhetővé.   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Elérhető a lastSignInDateTime tulajdonság a Get-AzureAdUser parancsmagon keresztül?

Nem.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Milyen Azure AD-kiadásra van szükségem a tulajdonság eléréséhez?

Ezt a tulajdonságot az Azure AD összes kiadásában elérheti.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Milyen engedélyekre van szükségem a tulajdonság beolvasásához?

A tulajdonság olvasásához a következő jogosultságokat kell megadnia: 

- AuditLogs. Read. All
- Szervezet. Read. All  


### <a name="when-does-azure-ad-update-the-property"></a>Mikor frissíti az Azure AD a tulajdonságot?

Minden olyan interaktív bejelentkezés sikeres volt, amely a mögöttes adattár frissítését eredményezte. A sikeres bejelentkezések általában 10 percen belül megjelennek a kapcsolódó bejelentkezési jelentésben.
 

### <a name="what-does-a-blank-property-value-mean"></a>Mit jelent az üres tulajdonság értéke?

LastSignInDateTime timestamp létrehozásához sikeres bejelentkezés szükséges. Mivel a lastSignInDateTime tulajdonság egy új szolgáltatás, a lastSignInDateTime tulajdonság értéke üres is lehet, ha:

- A felhasználó utolsó sikeres bejelentkezését április 2020. előtt végezték el.
- Az érintett felhasználói fiókot soha nem használták sikeres bejelentkezéshez.

## <a name="next-steps"></a>Következő lépések

* [Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
* [Naplózási API-referenciák](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [A bejelentkezési tevékenység jelentésének API-referenciája](/graph/api/resources/signin?view=graph-rest-beta)