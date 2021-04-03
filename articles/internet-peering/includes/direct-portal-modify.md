---
title: fájl belefoglalása
titleSuffix: Azure
description: fájl belefoglalása
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c3212acd5015edbb639b8904b885c718d654e5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356261"
---
Ez a szakasz azt ismerteti, hogyan hajthatja végre a következő módosítási műveleteket a közvetlen társításhoz.

### <a name="add-direct-peering-connections"></a>Közvetlen társas kapcsolatok hozzáadása
1. Válassza a **+ kapcsolatok hozzáadása** gombot, és konfigurálja az új társ-létesítési kapcsolatot.
    > [!div class="mx-imgBorder"]
    > ![A AshburnPeering-Connections lap felsorolja a kapcsolatokat. A + kapcsolatok hozzáadása gomb ki van emelve.](../media/setup-direct-modify-addconnection.png)

1. Töltse ki a **közvetlen társas kapcsolatok** űrlapot, és kattintson a **Mentés** gombra. A társ-létesítési kapcsolatok konfigurálásával kapcsolatos segítségért tekintse át a "közvetlen társítás létrehozása és kiépítése" című szakasz lépéseit.
    > [!div class="mx-imgBorder"]
    > ![Közvetlen társas kapcsolatok űrlapja](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Közvetlen társas kapcsolatok eltávolítása

A Azure Portal jelenleg nem támogatja a kapcsolatok eltávolítását. További információért forduljon a [Microsoft-partnerekhez](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>A sávszélesség frissítése vagy visszalépése aktív kapcsolatokon
1. Válassza ki a módosítani kívánt társi kapcsolatot, majd válassza a **...**  >  lehetőséget. A **Kapcsolatok szerkesztése**.
    > [!div class="mx-imgBorder"]
    > ![Kapcsolatok szerkesztése](../media/setup-direct-modify-editconnection.png)

1. Módosítsa a sávszélességet a csúszka mozgatásával, majd válassza a **Mentés** lehetőséget.
    > [!div class="mx-imgBorder"]
    > ![Sávszélesség módosítása](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>IPv4-vagy IPv6-munkamenet adatainak hozzáadása az aktív kapcsolatokhoz
1. Válassza ki a módosítani kívánt társi kapcsolatot, majd válassza a **...**  >  lehetőséget. A **Kapcsolatok szerkesztése** az 1. lépésben látható módon.
1. Adja meg a **munkamenet IPv4-előtagját** vagy a **munkamenet IPv6-előtagjának** adatait, majd kattintson a **Mentés** gombra.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>IPv4-vagy IPv6-munkamenet adatainak eltávolítása az aktív kapcsolatokon
A **munkamenet IPv4-előtagjának** vagy a **munkamenet IPv6-előtagja** adatainak eltávolítása jelenleg nem támogatott a portálon. További információért forduljon a [Microsoft-partnerekhez](mailto:peeringexperience@microsoft.com).
