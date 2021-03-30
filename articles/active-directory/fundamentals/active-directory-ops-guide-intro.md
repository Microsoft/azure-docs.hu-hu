---
title: Azure Active Directory üzemeltetési útmutató
description: Ez az üzemeltetési útmutató ismerteti azokat az ellenőrzéseket és műveleteket, amelyeket az identitás-és hozzáférés-kezelés, a hitelesítés, az irányítás és a műveletek biztonságossá tételéhez és karbantartásához kell végrehajtania.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74535313"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Azure Active Directory üzemeltetési útmutató

Ez az üzemeltetési útmutató ismerteti azokat az ellenőrzéseket és műveleteket, amelyeket a következő területek biztonságossá tételéhez és karbantartásához kell tennie:

- **[Identitás-és hozzáférés-kezelési](active-directory-ops-guide-iam.md)** képesség az identitások és a hozzájuk tartozó jogosultságok életciklusának kezeléséhez.
- **[Hitelesítés kezelése](active-directory-ops-guide-auth.md)** – a hitelesítő adatok kezelése, a hitelesítési élmény meghatározása, a hozzárendelés delegálása, a használat mérése és a hozzáférési házirendek definiálása a vállalati biztonsági testhelyzet alapján.
- **[Irányítási](active-directory-ops-guide-govern.md)** képesség a hozzáférés értékelésére és igazolására, amely nem Kiemelt jogosultságú és emelt szintű identitásokat, naplózást és a környezet változásainak szabályozását teszi lehetővé.
- **[Műveletek](active-directory-ops-guide-ops.md)** – optimalizálhatja az operatív Azure Active Directory (Azure ad).

Előfordulhat, hogy néhány javaslat nem alkalmazható az összes ügyfél környezetére, például AD FS ajánlott eljárások nem érvényesek, ha a szervezet jelszó-kivonatolási szinkronizálást használ.

> [!NOTE]
> Ezek a javaslatok a közzététel dátumától kezdve érvényesek, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell az identitásuk gyakorlatát, mivel a Microsoft termékei és szolgáltatásai idővel fejlődnek. A javaslatok változhatnak, ha a szervezetek egy másik prémium szintű Azure AD licencre fizetnek elő. Prémium szintű Azure AD P2 például több irányítási javaslatot is tartalmaz.

## <a name="stakeholders"></a>Érintettek

A jelen útmutató minden szakasza azt javasolja, hogy az érdekelt felek kiosztása sikeres legyen a kulcsfontosságú feladatok megtervezéséhez és megvalósításához. A következő táblázat az útmutatóban szereplő összes érintett résztvevő listáját ismerteti:

| Résztvevő | Description |
| :- | :- |
| IAM Operations csapat | Ez a csapat kezeli az identitás-és hozzáférés-kezelési rendszerek napi műveleteinek kezelését. |
| Termelékenységi csapat | A csapat tulajdonosa és felügyeli a hatékonyságnövelő alkalmazásokat, például az e-maileket, a fájlmegosztást és az együttműködést, az azonnali üzenetküldést és a konferenciákat. |
| Alkalmazás tulajdonosa | Ez a csapat az adott alkalmazást a vállalaton belül, általában technikai szempontból a szervezeten belül. |
| InfoSec-architektúra csapata | A csapat megtervezi és megtervezi a szervezet informatikai biztonsági gyakorlatát. |
| InfoSec-műveleti csapat | Ez a csapat futtatja és figyeli az InfoSec-architektúra csapatának megvalósított informatikai biztonsági gyakorlatait. |

## <a name="next-steps"></a>Következő lépések

Ismerkedés az [identitás-és hozzáférés-kezelési ellenőrzésekkel és műveletekkel](active-directory-ops-guide-iam.md).
