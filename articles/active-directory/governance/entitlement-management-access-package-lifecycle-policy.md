---
title: Hozzáférési csomag életciklus-beállításainak módosítása az Azure AD-jogosultságok kezelésében – Azure Active Directory
description: Megtudhatja, hogyan módosíthatja a kérelmező adatait & életciklus-beállításait egy hozzáférési csomaghoz Azure Active Directory jogosultságok kezelésében.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd119238476b842a4dba1efeec86544ca263920
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980123"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag életciklus-beállításainak módosítása az Azure AD-jogosultságok kezelésében

Hozzáférés-csomagkezelőként bármikor módosíthatja egy hozzáférési csomag életciklus-beállításait egy meglévő szabályzat szerkesztésével. Ha módosítja egy házirend lejárati dátumát, a függőben lévő jóváhagyás vagy jóváhagyott állapotú kérelmek lejárati dátuma nem változik.

Ez a cikk egy meglévő hozzáférési csomag életciklus-beállításainak módosítását ismerteti.

## <a name="open-requestor-information"></a>A kérelmező adatainak megnyitása
Annak biztosítása érdekében, hogy a felhasználók hozzáférjenek a hozzáférési csomagokhoz, az egyéni kérdéseket úgy is konfigurálhatja, hogy kérjenek bizonyos hozzáférési csomagokhoz hozzáférést kérő felhasználókat. A konfigurációs beállítások többek között a következők: honosítás, kötelező/nem kötelező, és szöveg/feleletválasztós válaszok formátuma. A kérelmező a csomag és a jóváhagyók kérése során megjelenő kérdéseket láthatja a kérdésekre adott válaszokban, amelyek segítenek a döntés meghozatalában. A következő lépésekkel konfigurálhatja a kérdéseket egy hozzáférési csomagban:

## <a name="open-lifecycle-settings"></a>Életciklus-beállítások megnyitása

Egy hozzáférési csomag életciklus-beállításainak módosításához meg kell nyitnia a megfelelő szabályzatot. Az alábbi lépéseket követve megnyithatja egy hozzáférési csomag életciklusának beállításait.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **házirendek** elemre, majd kattintson a szerkeszteni kívánt életciklus-beállításokat tartalmazó házirendre.

    Megnyílik a szabályzat részletei ablaktábla az oldal alján.

    ![Hozzáférési csomag – szabályzat részletei ablaktábla](./media/entitlement-management-shared/policy-details.png)

1. A szabályzat szerkesztéséhez kattintson a **Szerkesztés** gombra.

    ![Hozzáférési csomag – szabályzat szerkesztése](./media/entitlement-management-shared/policy-edit.png)

1. Az életciklus-beállítások megnyitásához kattintson az **életciklus** fülre.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomag kérelem-és jóváhagyási beállításainak módosítása](entitlement-management-access-package-request-policy.md)