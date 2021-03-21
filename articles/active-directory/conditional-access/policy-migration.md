---
title: Feltételes hozzáférési szabályzatok áttelepíteni – Azure Active Directory
description: Ismerje meg, hogy mit kell tudnia a klasszikus szabályzatok áttelepítéséhez a Azure Portalban.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1811d5b9ae4d3e34b48e1cdc156438f2bad98d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90601654"
---
# <a name="conditional-access-classic-policy-migration"></a>A feltételes hozzáférés klasszikus szabályzatának áttelepítése

A feltételes hozzáférés az Azure Active Directory által használt eszköz, amely lehetővé teszi a jelek összekapcsolását, a döntések meghozatalát és a szervezeti házirendek betartatását. A feltételes hozzáférés az új Identity vezérelt vezérlő síkja szívében található. Habár a cél még mindig ugyanaz, az új Azure Portal kiadása jelentős újdonságokat vezetett be a feltételes hozzáférés működéséhez.

Érdemes áttelepíteni azokat a házirendeket, amelyeket nem a Azure Portal hozott létre, mert:

- Mostantól olyan forgatókönyveket is megadhat, amelyeket nem tudott kezelni.
- Csökkentheti a felügyelni kívánt szabályzatok számát.
- A feltételes hozzáférési szabályzatokat egyetlen központi helyen kezelheti.
- A klasszikus Azure portál kivonásra kerül.

Ez a cikk azt ismerteti, hogy mit kell tudnia a meglévő feltételes hozzáférési szabályzatok új keretrendszerre való átállításához.

## <a name="classic-policies"></a>Klasszikus szabályzatok

A [Azure Portal](https://portal.azure.com)a feltételes hozzáférési szabályzatok a **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférés** területen találhatók. Előfordulhat, hogy a szervezet régebbi feltételes hozzáférési szabályzatokat nem hozott létre ezen a lapon. Ezeket a szabályzatokat *klasszikus szabályzatoknak* nevezzük. A klasszikus szabályzatok feltételes hozzáférési szabályzatok, amelyeket a ben hozott létre:

- A klasszikus Azure portál
- A klasszikus Intune-portál
- A Intune App Protection portál

A **feltételes hozzáférés** lapon a [**klasszikus házirendek lehetőségre kattintva érheti**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) el a klasszikus házirendeket a **kezelés** szakaszban. 

![Feltételes hozzáférés az Azure AD-ben klasszikus házirendek nézet](./media/policy-migration/71.png)

A **klasszikus szabályzatok** nézet a következő lehetőségeket kínálja:

- A klasszikus szabályzatok szűrése.
- Klasszikus szabályzatok letiltása.
- Tekintse át a klasszikus szabályzat beállításait, és tiltsa le.

   ![Klasszikus szabályzatok részletei, beleértve a meglévő házirend-konfigurációt](./media/policy-migration/74.png)

> [!WARNING]
> A klasszikus szabályzat letiltását követően nem engedélyezhető újra.

A klasszikus szabályzat részletes nézete lehetővé teszi a beállítások dokumentálására, a befoglalt vagy kizárt csoportok módosítására, valamint a szabályzat letiltására.

![Szabályzat részletei – belefoglalni vagy kizárni kívánt csoportok](./media/policy-migration/75.png)

A kiválasztott csoportok módosításával vagy adott csoportok kizárásával tesztelheti a letiltott klasszikus házirendek hatását néhány tesztelési felhasználóra, mielőtt letiltja a házirendet az összes befoglalt felhasználó és csoport számára.
 
## <a name="migration-considerations"></a>A migrálás szempontjai

Ebben a cikkben az Azure AD feltételes hozzáférési szabályzatait *új szabályzatoknak* is nevezzük.
A klasszikus szabályzatok továbbra is az új szabályzatokkal együtt működnek, amíg le nem tiltja vagy nem törli őket. 

A házirend-Összevonás kontextusában a következő szempontok fontosak:

- Habár a klasszikus szabályzatok egy adott felhőalapú alkalmazáshoz vannak kötve, az új szabályzatokban tetszőleges számú felhőalapú alkalmazást választhat.
- A klasszikus szabályzatok és a felhőalapú alkalmazások új szabályzatának szabályozása megköveteli az összes vezérlő (*és*) teljesítését. 
- Egy új szabályzatban a következőket teheti:
   - Ha a forgatókönyve megköveteli, több feltételt is egyesíteni kell. 
   - Válasszon több engedélyezési követelményt hozzáférés-vezérlésként, és kombinálja őket logikai *vagy* (a kijelölt vezérlők valamelyikének megkövetelése) vagy logikai *és* (az összes kijelölt vezérlő megkövetelése) beállítással.

### <a name="exchange-online"></a>Exchange Online

Ha olyan klasszikus házirendeket szeretne áttelepíteni az **Exchange Online** -hoz, amelyek **Exchange Active Sync** ügyfél-alkalmazási feltételként szerepelnek, előfordulhat, hogy nem tudja összevonni őket egyetlen új szabályzatba. 

Ez például az az eset, ha az összes ügyfélalkalmazás-típust támogatni szeretné. Egy olyan új szabályzatban, amely **Exchange Active Sync** ügyfél-alkalmazási feltételként, nem választhat más ügyfélalkalmazások közül.

![Ügyfélalkalmazások kiválasztása feltételes hozzáféréssel](./media/policy-migration/64.png)

Ha a klasszikus szabályzat több feltételt is tartalmaz, akkor nem lehet konszolidálni egy új szabályzatot. Egy olyan új szabályzat, amely **Exchange Active Sync** ügyfél-alkalmazási feltétel konfigurálva, nem támogatja a többi feltételt:   

![Az Exchange ActiveSync nem támogatja a kiválasztott feltételeket](./media/policy-migration/08.png)

Ha van olyan új szabályzata, amely **Exchange Active Sync** ügyfél-alkalmazási feltétel konfigurálva van, akkor győződjön meg arról, hogy az összes többi feltétel sincs konfigurálva. 

![Feltételes hozzáférési feltételek](./media/policy-migration/16.png)
 
Az olyan, az Exchange Online-hoz készült alkalmazás-alapú klasszikus házirendek, amelyek **Exchange Active Sync** ügyfél-alkalmazási feltételnek teszik lehetővé a **támogatott** **és a nem támogatott eszközök** platformját. Habár a kapcsolódó új házirendekben nem konfigurálhat egyedi eszközöket, csak a [támogatott eszközökre](concept-conditional-access-conditions.md#device-platforms) korlátozhatja a támogatást. 

![Feltételes hozzáférés az Exchange ActiveSync kiválasztása](./media/policy-migration/65.png)

Több klasszikus szabályzatot is összevonhat, amelyek a **Exchange Active Sync** ügyfél-alkalmazási feltételnek minősülnek, ha rendelkeznek a következőkkel:

- Csak **Exchange Active Sync** feltételként 
- A hozzáférés konfigurálásának számos követelménye

Az egyik gyakori forgatókönyv az alábbiak összevonása:

- A klasszikus Azure portál eszközön alapuló klasszikus szabályzata 
- Egy alkalmazás-alapú klasszikus szabályzat az Intune app Protection portálon 
 
Ebben az esetben a klasszikus szabályzatokat egy új házirendbe összevonhatja, amely mindkét követelménynek megfelel.

![Feltételes hozzáférés engedélyezési vezérlői](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Eszközplatformok

Az alkalmazáson alapuló vezérlőkkel rendelkező klasszikus szabályzatok előre konfigurálva vannak az iOS és az Android platformon az eszköz platformjának feltétele. 

Egy új szabályzatban ki kell választania a támogatni kívánt [eszközök platformját](concept-conditional-access-conditions.md#device-platforms) .

![Feltételes hozzáférésű eszköz platformok kiválasztása](./media/policy-migration/41.png)

## <a name="next-steps"></a>Következő lépések

- [A feltételes hozzáféréshez csak jelentési módot használhat az új házirend-döntések hatásának megállapításához.](concept-conditional-access-report-only.md)
- Ha tudni szeretné, hogyan konfigurálhat feltételes hozzáférési szabályzatot, tekintse meg a [feltételes hozzáférés általános házirendjeit](concept-conditional-access-policy-common.md).
- Ha készen áll a környezethez tartozó feltételes hozzáférési szabályzatok konfigurálására, tekintse meg a következő cikket [: Hogyan tervezze meg a feltételes hozzáférés telepítését a Azure Active Directory](plan-conditional-access.md). 
