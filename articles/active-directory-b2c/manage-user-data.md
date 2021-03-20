---
title: Felhasználói adatkezelés Azure Active Directory B2Cban | Microsoft Docs
description: Megtudhatja, hogyan törölhet vagy exportálhatunk felhasználói információkat Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d9bdf7258296b82e65e03f6b8af8021b9a7be0f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94952471"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Felhasználói adatkezelés Azure Active Directory B2C

 Ez a cikk azt ismerteti, hogyan kezelheti a felhasználói adatok Azure Active Directory B2Cban (Azure AD B2C) a [Microsoft Graph API](/graph/use-the-api)által biztosított műveletek használatával. A felhasználói adatok kezelése magában foglalja az adatok törlését és a naplókból való exportálását.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Felhasználói adatértékek törlése

A felhasználói adatszolgáltatások tárolása a Azure AD B2C könyvtárban és a naplókban történik. Az összes felhasználói naplózási adat megmarad a Azure AD B2C 7 napig. Ha az adott 7 napos időszakon belül szeretné törölni a felhasználói adatbázisokat, használhatja a [felhasználó törlése](/graph/api/user-delete) műveletet. TÖRLÉSi műveletre van szükség az egyes Azure AD B2C bérlők esetében, ahol az adattárolók találhatók.

Azure AD B2C minden felhasználója hozzá van rendelve egy objektum-AZONOSÍTÓhoz. Az objektumazonosító egyértelmű azonosítót biztosít, amellyel törölheti Azure AD B2C felhasználói adatait. Az architektúrától függően az objektumazonosító hasznos korrelációs azonosító lehet más szolgáltatások, például pénzügyi, marketing és ügyfélkapcsolat-kezelési adatbázisok között.

A felhasználó objektum-AZONOSÍTÓjának lekéréséhez a legpontosabb módszer, ha a hitelesítési út részeként beszerezze Azure AD B2C. Ha más módszerekkel is érvényes adatkérést kap a felhasználótól, az offline folyamat, például az ügyfélszolgálati támogatási ügynök általi keresés szükséges lehet a felhasználó megtalálásához, és jegyezze fel a társított objektumazonosítót.

Az alábbi példa az adattörlés lehetséges folyamatát mutatja be:

1. A felhasználó bejelentkezik, és kiválasztja az **adattörlés** lehetőséget.
2. Az alkalmazás lehetőséget kínál az alkalmazások törlésére az alkalmazás adminisztrációs részén belül.
3. Az alkalmazás Azure AD B2C hitelesítését kényszeríti. Azure AD B2C biztosít egy jogkivonatot, amely a felhasználó objektumazonosítóát adja vissza az alkalmazásnak.
4. Az alkalmazás megkapja a tokent, és az objektumazonosító használatával törli a felhasználói adatelemet a Microsoft Graph API hívásával. A Microsoft Graph API törli a felhasználói adatok számát, és visszaadja az 200-es állapotkódot.
5. Az alkalmazás az objektumazonosító vagy más azonosítók használatával összehangolja a felhasználói adat más szervezeti rendszerekben való törlését.
6. Az alkalmazás megerősíti az adattörlést, és a következő lépéseket biztosítja a felhasználó számára.

## <a name="export-customer-data"></a>Ügyféladatok exportálása

A vásárlói adatok Azure AD B2Cból való exportálásának folyamata hasonló a törlési folyamathoz.

Azure AD B2C felhasználói adatmennyiség a következőre korlátozódik:

- **A Azure Active Directory tárolt adatai**: az adatAzure ad B2C-hitelesítés felhasználói útvonalon az objektum azonosítója vagy bármely bejelentkezési név, például e-mail-cím vagy Felhasználónév használatával kérhető le.
- **Felhasználó-specifikus naplózási események jelentés**: a rendszer az objektum azonosítójának használatával indexeli az adatindexet.

Az exportálási folyamat következő példájában az alkalmazás által elvégezhető lépéseket egy háttérrendszer vagy egy rendszergazdai szerepkörrel rendelkező felhasználó is végrehajthatja a címtárban:

1. A felhasználó bejelentkezik az alkalmazásba. Ha szükséges, Azure AD B2C érvényesíti az Azure AD-Multi-Factor Authentication való hitelesítést.
2. Az alkalmazás a felhasználói hitelesítő adatok használatával hívja meg az Microsoft Graph API-műveletet a felhasználói attribútumok lekéréséhez. A Microsoft Graph API JSON formátumú attribútumot biztosít. A sémától függően beállíthatja az azonosító token tartalmát úgy, hogy az tartalmazza a felhasználóra vonatkozó összes személyes információt.
3. Az alkalmazás lekéri a felhasználói naplózási tevékenységet. A Microsoft Graph API biztosítja az eseményre vonatkozó adatértékeket az alkalmazásnak.
4. Az alkalmazás összesíti az adatokat, és elérhetővé teszi a felhasználó számára.

## <a name="next-steps"></a>Következő lépések

A felhasználók alkalmazáshoz való hozzáférésének kezelésével kapcsolatos információkért lásd: [felhasználói hozzáférés kezelése](manage-user-access.md).