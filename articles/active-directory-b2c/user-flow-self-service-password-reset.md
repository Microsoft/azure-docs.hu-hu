---
title: Önkiszolgáló jelszó-visszaállítás a Azure Active Directory B2Cban | Microsoft Docs
description: Bemutatja, hogyan állíthatja be az ügyfelek önkiszolgáló jelszó-visszaállítását Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0c9edaf3356ea4c1a521a89f2ec60a4b6ba1a5ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87481495"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Önkiszolgáló jelszó-visszaállítás beállítása ügyfelei számára

Az önkiszolgáló jelszó-visszaállítási szolgáltatással a helyi fiókokra regisztrált ügyfelei saját jelszavukat is visszaállíthatják. Ez jelentősen csökkenti a támogatási munkatársak terhelését, különösen akkor, ha az alkalmazásnak több millió ügyfele van használatban rendszeresen. Jelenleg ellenőrzött e-mail-cím használata az egyetlen támogatott helyreállítási módszer.

> [!NOTE]
> Ez a cikk a normál **bejelentkezési** felhasználói folyamat kontextusában használt önkiszolgáló jelszó-visszaállításra vonatkozik, amely a **helyi fiók bejelentkezési** használja az identitás-szolgáltatóként. Ha teljes mértékben testre szabható jelszó-visszaállítási felhasználói folyamatokra van szüksége az alkalmazásból, tekintse meg [ezt a cikket](user-flow-overview.md).
>
>

Alapértelmezés szerint a címtárban nincs bekapcsolva az önkiszolgáló jelszó-visszaállítás. A következő lépésekkel kapcsolhatja be:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) előfizetés-rendszergazdaként. Ez ugyanaz a munkahelyi vagy iskolai fiók, vagy ugyanaz a Microsoft-fiók, amelyet a címtár létrehozásához használt.
2. Nyissa meg **Azure Active Directory** (a bal oldali navigációs sávon).
3. Görgessen le a beállítások panelen, és válassza a **jelszó alaphelyzetbe állítása** lehetőséget.
4. Állítsa be az **önkiszolgáló jelszó-visszaállítás**  beállítást az **összes** értékre.
5. Kattintson az oldal tetején lévő **Mentés** elemre. Ennyi az egész!

A teszteléshez használja a "Futtatás most" funkciót minden olyan bejelentkezési felhasználói folyamatnál, amely rendelkezik identitás-szolgáltatóként helyi fiókkal. A helyi fiók bejelentkezési oldalán (ahol meg kell adnia egy e-mail címet és egy jelszót, vagy egy felhasználónevet és egy jelszót), kattintson a **nem fér hozzá a fiókjához?** a felhasználói élmény ellenőrzéséhez.

> [!NOTE]
> Az önkiszolgáló jelszó-visszaállítási lapok testreszabhatók a [vállalati arculati funkció](../active-directory/fundamentals/customize-branding.md)használatával.
>
>

