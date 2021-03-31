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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680935"
---
Ez a szakasz azt ismerteti, hogyan hajthatja végre a következő módosítási műveleteket a közvetlen társításhoz.

### <a name="add-exchange-peering-connections"></a>Exchange-partneri kapcsolatok hozzáadása

1. Válassza a **+ kapcsolatok hozzáadása** gombot, és konfigurálja az új társ-létesítési kapcsolatot.
    > [!div class="mx-imgBorder"]
    > ![Egyenrangú erőforrás nézet](../media/setup-exchange-modify-addconnection.png)
1. Töltse ki az **Exchange-társi kapcsolatok** űrlapot, és kattintson a **Mentés** gombra. A társ-létesítési kapcsolatok konfigurálásával kapcsolatos segítségért tekintse át a "közvetlen társítás létrehozása és kiépítése" című szakasz lépéseit.
    > [!div class="mx-imgBorder"]
    > ![Exchange-partneri kapcsolatok űrlapja](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange-társi kapcsolatok eltávolítása

1. Válassza ki a törölni kívánt társi kapcsolatot, majd válassza a **...**  >  lehetőséget. A **kapcsolatok törlése**.
    > [!div class="mx-imgBorder"]
    > ![Kapcsolatok törlése gomb](../media/setup-exchange-modify-deleteconnection.png)
1. A **Törlés megerősítése** mezőbe írja be az erőforrás-azonosítót, és válassza a **Törlés** lehetőséget.
    > [!div class="mx-imgBorder"]
    > ![Törlés megerősítése](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>IPv4-vagy IPv6-munkamenet hozzáadása az aktív kapcsolatokhoz

1. Válassza ki a módosítani kívánt társi kapcsolatot, majd válassza a **...**  >  lehetőséget. A **Kapcsolatok szerkesztése**.
    > [!div class="mx-imgBorder"]
    > ![A kapcsolatok szerkesztése gomb](../media/setup-exchange-modify-editconnection.png)
1. Adja meg az **IPv4-cím** vagy az **IPv6-cím** adatait, majd kattintson a **Mentés** gombra.
    > [!div class="mx-imgBorder"]
    > ![Társ-létrehozási kapcsolatok módosításai](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>IPv4-vagy IPv6-munkamenet eltávolítása az aktív kapcsolatokon

Egy IPv4-vagy IPv6-munkamenet meglévő kapcsolatból való eltávolítása jelenleg nem támogatott a portálon. További információért forduljon a [Microsoft-partnerekhez](mailto:peeringexperience@microsoft.com).