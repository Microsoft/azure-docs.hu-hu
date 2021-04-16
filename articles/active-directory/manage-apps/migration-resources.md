---
title: Erőforrások alkalmazások áttelepítéséhez Azure Active Directory | Microsoft Docs
description: Erőforrások az alkalmazás-hozzáférés és -hitelesítés áttelepítéséhez Azure Active Directory (Azure AD) szolgáltatásba.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: 2d01c174bbfa522700773b87737b1e3da2de422e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376647"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Forrásanyagok az alkalmazások Azure Active Directoryba való migrálásához

Erőforrások az alkalmazás-hozzáférés és -hitelesítés áttelepítéséhez Azure Active Directory (Azure AD) szolgáltatásba.

| Erőforrás  | Leírás  |
|:-----------|:-------------|
|[Alkalmazások áttelepítése az Azure AD-be](https://aka.ms/migrateapps/whitepaper) | Ez a tanulmány bemutatja a migrálás előnyeit, és ismerteti, hogyan tervezhető meg a migrálás négy, egyértelműen felvázolt fázisban: felderítés, besorolás, migrálás és folyamatos felügyelet. A rendszer végigvezeti a folyamaton, és a projekt könnyen feldolgozható részletekre való lebontásán. A dokumentum olyan fontos forrásokra mutató hivatkozásokat tartalmaz, amelyek végig segítségére lesznek. |
|[Megoldási útmutató: Alkalmazások áttelepítése Active Directory összevonási szolgáltatások (AD FS) (AD FS) az Azure AD-be](./migrate-adfs-apps-to-azure.md) | Ez a megoldási útmutató végigvezeti a migrálási tanulmány magasabb szintjén ismertetett alkalmazás-migrálási projekt tervezésének és végrehajtásának négy fázisán. Ebből az útmutatóból megtudhatja, hogyan alkalmazhatja ezeket a fázisokat arra a konkrét célra, hogy egy alkalmazást az Azure Directory összevont szolgáltatásokból (AD FS) az Azure AD-be.|
|[Fejlesztői oktatóanyag: AD FS Azure AD-alkalmazások migrálási forgatókönyvének áttelepítése fejlesztőknek](https://aka.ms/adfsplaybook) | Az ASP.NET-kódminták és a kapcsolódó oktatóanyagok segítségével megtudhatja, hogyan mily módon minálhatja biztonságosan és biztonságosan az Active Directory összevonási szolgáltatások (AD FS) (AD FS) integrált alkalmazásait az Azure Active Directory (Azure AD) szolgáltatásba. Ez az oktatóanyag olyan fejlesztőknek szól, akiknek nem csupán az AD FS-ban és az Azure AD-ben is meg kell tanulniuk az alkalmazások konfigurálását, hanem tisztában vannak azzal is, hogy a kódbázisuk változásaira is szükség lesz ebben a folyamatban.|
| [Eszköz: Active Directory összevonási szolgáltatások (AD FS) migrálási készenlét parancsfájlja](https://aka.ms/migrateapps/adfstools) | Ez egy olyan szkript, amely a helyi Active Directory összevonási szolgáltatások (AD FS) kiszolgálón futtatható annak meghatározásához, hogy az alkalmazások az Azure AD-be való migrálásra készek-e.|
| [Üzembe helyezési terv: Áttelepítés AD FS jelszó-kivonat szinkronizálására](https://aka.ms/ADFSTOPHSDPDownload) | A jelszó-kivonat szinkronizálásával a felhasználói jelszavak kivonatai szinkronizálva lesznek helyi Active Directory Azure AD-be. Ez lehetővé teszi, hogy az Azure AD anélkül hitelesítse a felhasználókat, hogy interakcióba helyi Active Directory.| 
| [Üzembe helyezési terv: Áttelepítés AD FS átmenő hitelesítésre](https://aka.ms/ADFSTOPTADPDownload)|Az Azure AD átmenő hitelesítése segít a felhasználóknak bejelentkezni a helyszíni és a felhőalapú alkalmazásokba ugyanazokkal a jelszóval. Ez a funkció jobb élményt nyújt a felhasználóknak, mivel kevesebb jelszót kell megjegyezniük. Emellett csökkenti az it-segítők költségeit is, mivel a felhasználók kisebb valószínűséggel felejtik el a bejelentkezést, ha csak egyetlen jelszót kell megjegyezniük. Az Azure AD-vel való bejelentkezéskor a szolgáltatás közvetlenül a helyszíni Active Directoryban tárolt adatok alapján érvényesíti a felhasználói jelszavakat.|
| [Üzembe helyezési terv: Egyszeri bejelentkezés engedélyezése SaaS-alkalmazásokhoz az Azure AD-val](https://aka.ms/SSODPDownload) | Az egyszeri bejelentkezés (SSO) segítségével egyetlen felhasználói fiókkal hozzáférhet az üzlethez szükséges összes alkalmazáshoz és erőforráshoz, miközben csak egyszer jelentkezik be. Ha például egy felhasználó bejelentkezett, a felhasználó a Microsoft Office-ről a SalesForce-hoz anélkül is átléphet a Boxra, hogy másodszor is hitelesítené (például beírna egy jelszót). 
| [Üzembe helyezési terv: Alkalmazások kiterjesztése az Azure AD-be alkalmazásproxy](https://aka.ms/AppProxyDPDownload)| Az alkalmazotti laptopok és más eszközök helyszíni alkalmazásokhoz való hozzáférésének biztosítása hagyományosan virtuális magánhálózatokat (VPN-eket) vagy demilitarizált zónákat (DMZ-ket) is érintett. Ezeket a megoldásokat azonban nem csupán nagyon nehéz és komplex dolog védelemmel ellátni, hanem a beállításuk és az üzemeltetésük is nagyon költséges. Az Azure AD alkalmazásproxy megkönnyíti a helyszíni alkalmazásokhoz való hozzáférést. |
| [Üzembehelyezési tervek](../fundamentals/active-directory-deployment-plans.md) | További üzembehelyezés-terveket találhat olyan funkciók üzembe helyezéséhez, mint például a többtényezős hitelesítés, a feltételes hozzáférés, a felhasználók üzembe helyezése, a zökkenőmentes egyszeri bejelentkezés, az új jelszó önkiszolgáló visszaállítása és még sok más! |