---
title: Az Azure Application Gateway átirányítási áttekintése
description: Ismerje meg az Azure Application Gateway átirányítási képességét, hogy átirányítsa az egyik figyelőre érkező forgalmat egy másik figyelőre vagy egy külső webhelyre.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: fb01d5a4923410f693b682d66be8d5d09f9019d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90561578"
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway átirányítási áttekintés

Az Application Gateway segítségével átirányíthatja a forgalmat.  Általános átirányítási mechanizmusa lehetővé teszi, hogy egy figyelő által fogadott forgalmat egy másik figyelőre vagy egy külső webhelyre lehessen átirányítani. Ez egyszerűsíti az alkalmazások konfigurációját, optimalizálja az erőforrás-használatot, és támogatja az új átirányítási forgatókönyveket, beleértve a globális és az elérésiút-alapú átirányítást.

Számos webalkalmazás esetében a gyakori átirányítási forgatókönyvek támogatják az automatikus HTTP-t a HTTPS-átirányítás számára, így biztosítva, hogy az alkalmazás és a felhasználók közötti kommunikáció egy titkosított útvonalon történjen. A múltban az ügyfelek olyan technikákat használták, mint például egy dedikált háttér-készlet létrehozása, amelynek a célja, hogy átirányítsa a HTTP-n keresztül fogadott kérelmeket a HTTPS-re. A Application Gateway átirányítási támogatásával egyszerűen elvégezhető, ha új átirányítási konfigurációt ad hozzá egy útválasztási szabályhoz, és egy másik figyelőt ad meg egy HTTPS protokollal, célként figyelőként.

A következő típusú átirányítási típusok támogatottak:

- 301 végleges átirányítás
- 302 található
- 303 további információ
- 307 ideiglenes átirányítás

Az Application Gateway átirányítási támogatása a következő funkciókat nyújtja:

-  **Globális átirányítás**

   Átirányítja az egyik figyelőről egy másik figyelőre az átjárón. Ez lehetővé teszi a HTTP–HTTPS átirányítást egy webhelyen.
- **Elérésiút-alapú átirányítás**

   Ez a típusú átirányítás lehetővé teszi a HTTP-t a HTTPS-átirányítás csak egy adott hely területén, például egy bevásárlókosár terület, amelyet a/cart/* jelöl.
- **Átirányítás külső helyre**

![A diagramon a felhasználók és az alkalmazás-átjárók, valamint a kettő közötti kapcsolatok láthatók, beleértve a nem engedélyezett H T P piros nyilat, a nem megengedett 301 közvetlen piros nyilat, valamint a zárolt H T P-t egy zöld nyílra.](./media/redirect-overview/redirect.png)

Ezzel a módosítással az ügyfeleknek létre kell hozniuk egy új átirányítási konfigurációs objektumot, amely meghatározza azt a célként figyelőt vagy külső helyet, amelyre az átirányítás szükség van. A konfigurációs elem olyan beállításokat is támogat, amelyek lehetővé teszik az URI elérési út és a lekérdezési karakterlánc hozzáfűzését az átirányított URL-címhez. Kiválaszthatja az átirányítás típusát is. A létrehozást követően ez az átirányítási konfiguráció egy új szabály segítségével csatlakozik a forrás-figyelőhöz. Alapszintű szabály használatakor az átirányítási konfiguráció a forrás-figyelőhöz van társítva, és globális átirányítás. Elérésiút-alapú szabály használatakor az átirányítási konfiguráció az URL-cím elérési útja alapján van meghatározva. Ezért csak a helyek adott elérési útjára vonatkozik.

### <a name="next-steps"></a>Következő lépések

[URL-átirányítás konfigurálása Application gatewayen](tutorial-url-redirect-powershell.md)
