---
title: Felhasználó alkalmazás-hozzáférésének eltávolítása a Azure Active Directory
description: A felhasználók alkalmazásokhoz való hozzáférésének eltávolítása a Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: iangithinji
ms.openlocfilehash: 958abc5f9be443d66037a6d9fe8d8779e6e37e0e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379587"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Felhasználó alkalmazás-hozzáférésének eltávolítása

Ez a cikk segít megérteni, hogyan távolítható el egy felhasználó hozzáférése egy alkalmazáshoz.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>El szeretném távolítani egy adott felhasználó vagy csoport alkalmazáshoz való hozzárendelését

Ha el szeretne távolítani egy felhasználó- vagy csoport-hozzárendelést egy alkalmazásból, kövesse [a](./assign-user-or-group-access-portal.md) felhasználó- vagy csoport-hozzárendelés vállalati alkalmazásból való eltávolításáról Azure Active Directory cikkben szereplő lépéseket.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Minden felhasználó számára szeretném letiltani az alkalmazáshoz való hozzáférést

Ha le szeretné tiltani az alkalmazásba való összes felhasználói bejelentkezést, kövesse a Felhasználói bejelentkezések letiltása vállalati alkalmazásokhoz a Azure Active Directory [cikkben](./disable-user-sign-in-portal.md) felsorolt lépéseket.

## <a name="i-want-to-delete-an-application-entirely"></a>Teljes egészében törölni szeretnék egy alkalmazást

Az [alkalmazáskezelés gyorsútmutató-sorozata](delete-application-portal.md) útmutatást nyújt az alkalmazásoknak a saját bérlőből Azure Active Directory törléséhez.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Le szeretném tiltani az összes jövőbeli felhasználói hozzájárulási műveletet bármely alkalmazáshoz

Ha letiltja a felhasználói jóváhagyást a teljes címtárra, a végfelhasználók nem járulnak hozzá az alkalmazásokhoz. A rendszergazdák továbbra is járulnak hozzá a felhasználó nevében. Az alkalmazás-jóváhagyással kapcsolatos további információkért, valamint annak okával kapcsolatban, hogy miért kívánja ezt megtenni, olvassa el A felhasználói és rendszergazdai [hozzájárulás ismertetése című témakört.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) Lásd még: [Engedélyek és hozzájárulás.](../develop/v2-permissions-and-consent.md)

Ha le szeretné tiltani az összes jövőbeli felhasználói hozzájárulási műveletet **a teljes címtárban,** kövesse az alábbi utasításokat:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **a Azure Active Directory bővítményt** 

3.  A **navigációs menüben kattintson** a Vállalati alkalmazások elemre.

5.  Kattintson **a Felhasználói beállítások elemre.**

6.  Állítsa a **Felhasználók engedélyezhetik, hogy az** alkalmazások hozzáférjenek  a vállalati adatokhoz a nevükben kapcsolót Nem, majd kattintson a Mentés gombra.


## <a name="next-steps"></a>Következő lépések

[Alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)