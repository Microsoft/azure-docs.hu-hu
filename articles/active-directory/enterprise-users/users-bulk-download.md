---
title: A felhasználók listájának letöltése a Azure Active Directory portálon | Microsoft Docs
description: A felhasználói rekordok tömeges letöltése a Azure Active Directory Azure felügyeleti központban.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/04/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57e3a059a5dd846250ba162513ef118e084c4b87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861600"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Felhasználók listájának letöltése Azure Active Directory portálon

Azure Active Directory (Azure AD) támogatja a tömeges felhasználói importálás (létrehozás) műveleteit.

## <a name="required-permissions"></a>Szükséges engedélyek

Ha le szeretné tölteni a felhasználók listáját az Azure Active Directory felügyeleti központjából, olyan felhasználói fiókkal kell bejelentkeznie, amely legalább egy szervezeti szintű adminisztrátori szerepkörrel rendelkezik az Azure Active Directoryban (legalább Felhasználói adminisztrátor szintű jogosultságra van szükség). A vendégmeghívói és az alkalmazásfejlesztői szerepkör nem minősül adminisztrátori szerepkörnek.

## <a name="to-download-a-list-of-users"></a>A felhasználók listájának letöltése

1. [Jelentkezzen be az Azure ad-szervezetbe](https://aad.portal.azure.com) egy felhasználói rendszergazdai fiókkal a szervezeten belül.
2. Válassza az Azure Active Directory > Felhasználók lehetőséget. Jelölje ki a letöltendő listába belefoglalni kívánt felhasználókat úgy, hogy bejelöli a nevüktől balra található oszlopban lévő jelölőnégyzetet. Megjegyzés: Jelenleg nincs lehetőség arra, hogy az összes felhasználót kijelölje exportálás céljából. Mindegyiket külön-külön kell kijelölnie.
3. Az Azure ad-ben válassza a **felhasználók**  >  **Letöltés felhasználók** lehetőséget.
4. A **felhasználók letöltése** lapon kattintson a **Start** gombra a felhasználói profil tulajdonságait tartalmazó CSV-fájl fogadásához. Ha hibák lépnek fel, a Tömeges művelet eredményei oldalon letöltheti és megnézheti az eredményeket tartalmazó fájlt. A fájl az egyes hibák okát is tartalmazza.

   ![Válassza ki, hova szeretné listázni a letölteni kívánt felhasználókat](./media/users-bulk-download/bulk-download.png)

   A letöltött fájlban a felhasználók szűrt listáját találja.

   Ezeket a felhasználói attribútumokat tartalmazza a fájl:

   - userPrincipalName
   - displayName
   - surname
   - Levelezés
   - givenName
   - objectId
   - userType
   - jobTitle
   - Részleg
   - accountEnabled
   - usageLocation
   - streetAddress
   - állapot
   - ország
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobil
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - korcsoport
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Állapot ellenőrzése

A függőben lévő tömeges kérelmek állapotát a **tömeges művelet eredményei** lapon tekintheti meg.

[![A tömeges műveletek eredményei lapon található állapot keresése.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Szolgáltatási korlátok tömeges letöltése

A felhasználók listájának létrehozásához minden tömeges tevékenység akár egy óráig is futhat. Ez lehetővé teszi, hogy legalább 500 000 felhasználóból álló listát hozzanak létre és töltsenek le.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók tömeges hozzáadása](users-bulk-add.md)
- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Felhasználók tömeges visszaállítása](users-bulk-restore.md)
