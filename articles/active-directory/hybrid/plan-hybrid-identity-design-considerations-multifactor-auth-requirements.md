---
title: Hybrid Identity design – multi-Factor Authentication-követelmények az Azure-ban | Microsoft Docs
description: A feltételes hozzáférés-vezérléssel az Azure AD ellenőrzi a felhasználó hitelesítése és az alkalmazáshoz való hozzáférés engedélyezése előtt kiválasztott konkrét feltételeket.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d8ddf372e234bab242e4b28ba53dce7dd68cc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90976054"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>A többtényezős hitelesítési követelmények meghatározása a hibrid identitás megoldásához
A mobilitás ezen világában a felhasználók a felhőben és bármely eszközön hozzáférő adatokhoz és alkalmazásokhoz férnek hozzá.  Minden nap van egy új, biztonsági szabálysértéssel kapcsolatos főcím.  Bár az ilyen jellegű szabálysértések esetében nincs garancia, a többtényezős hitelesítés egy további biztonsági réteget biztosít, amely segít megelőzni ezeket a problémákat.
Először értékelje a többtényezős hitelesítéshez szükséges szervezetekre vonatkozó követelményeket. Ez azt, hogy mi a szervezet számára a biztonság.  Ez az értékelés fontos a szervezetek felhasználói számára a többtényezős hitelesítéshez való beállításához és engedélyezéséhez szükséges technikai követelmények meghatározásához.

Ügyeljen rá, hogy válaszoljon a következőkre:

* A vállalata a Microsoft-alkalmazások védelmét kísérli meg? 
* Hogyan jelennek meg ezek az alkalmazások?
* A vállalata olyan távelérést biztosít, amely lehetővé teszi az alkalmazottak számára a helyszíni alkalmazások elérését?

Ha igen, milyen típusú távelérésre van lehetőség? Azt is ki kell értékelnie, hogy az alkalmazásokat elérő felhasználók hol találhatók. Ez a kiértékelés egy másik fontos lépés a megfelelő multi-Factor Authentication-stratégia definiálásához. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Hol találhatók a felhasználók?
* Bárhol lehetnek?
* A vállalata a felhasználó tartózkodási helye alapján kívánja létrehozni a korlátozásokat?

Ha megértette ezeket a követelményeket, fontos, hogy kiértékelje a felhasználó a többtényezős hitelesítésre vonatkozó követelményeit is. Ez az értékelés azért fontos, mert meghatározza a többtényezős hitelesítés működésének követelményeit. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Ismerik a felhasználók a többtényezős hitelesítést?
* Szükség van bizonyos felhasználásokra további hitelesítés biztosításához?  
  * Ha igen, minden alkalommal, amikor külső hálózatokról érkezik, vagy bizonyos alkalmazásokhoz vagy más feltételekhez fér hozzá?
* A felhasználóknak a többtényezős hitelesítés beállításához és megvalósításához kell betanítást alkalmazniuk?
* Mik a legfontosabb forgatókönyvek, amelyekkel a vállalat engedélyezni szeretné a többtényezős hitelesítést a felhasználók számára?

Az előző kérdések megválaszolása után megtudhatja, hogy van-e már implementálva a többtényezős hitelesítés a helyszínen. Ez az értékelés fontos a szervezetek felhasználói számára a többtényezős hitelesítéshez való beállításához és engedélyezéséhez szükséges technikai követelmények meghatározásához. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* A vállalatának szüksége van az MFA-val rendelkező Kiemelt fiókok biztonságára?
* A vállalatának engedélyezni kell az MFA használatát bizonyos alkalmazásoknak a megfelelőségi okokból?
* A vállalatának engedélyeznie kell az MFA-t az alkalmazás összes jogosult felhasználója vagy csak a rendszergazdák számára?
* Szükség van-e az MFA mindig engedélyezve vagy csak akkor, ha a felhasználók a vállalati hálózaton kívül vannak naplózva?

## <a name="next-steps"></a>Következő lépések
[Hibrid identitás-bevezetési stratégia definiálása](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Lásd még
[Tervezési szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

