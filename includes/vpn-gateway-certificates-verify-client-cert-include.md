---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67178978"
---
Ha nem sikerül a csatlakozás, ellenőrizze a következő elemeket:

- Ha exportált egy ügyféltanúsítványt a **Tanúsítvány exportálása varázslóval**, akkor győződjön meg róla, hogy. pfx-fájlként exportálta, és **Ha lehetséges, az összes tanúsítvány belefoglalása a tanúsítvány elérési útjába** jelölőnégyzetet. Ha ezzel az értékkel exportálja, a rendszer a főtanúsítvány információit is exportálja. Miután telepítette a tanúsítványt az ügyfélszámítógépen, a. pfx-fájl főtanúsítványa is telepítve lesz. A főtanúsítvány telepítésének ellenőrzéséhez nyissa meg a **felhasználói tanúsítványok kezelése** elemet, és válassza a **megbízható legfelső szintű hitelesítésszolgáltató \ tanúsítványok részhez** lehetőséget. Ellenőrizze, hogy a főtanúsítvány szerepel-e a listáján, amelynek a hitelesítés működéséhez jelen kell lennie.

- Ha olyan tanúsítványt használt, amelyet vállalati HITELESÍTÉSSZOLGÁLTATÓI megoldás bocsátott ki, és nem tud hitelesíteni, ellenőrizze az ügyféltanúsítvány hitelesítési sorrendjét. Ellenőrizze a hitelesítési lista sorrendjét, ha duplán kattint az ügyféltanúsítvány elemre, majd válassza a **részletek** lapot, majd a **Kibővített kulcshasználat** elemet. Győződjön meg arról, hogy az *ügyfél-hitelesítés* a lista első eleme. Ha nem, állítson ki egy ügyféltanúsítványt az *ügyfél-hitelesítéssel* rendelkező felhasználói sablon alapján a lista első elemeként.

- A pont–hely (P2S) hibaelhárítással kapcsolatos további információkért lásd: [Troubleshoot P2S connections](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Pont–hely kapcsolatok hibaelhárítása).