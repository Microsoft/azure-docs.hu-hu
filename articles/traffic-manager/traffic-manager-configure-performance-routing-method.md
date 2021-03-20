---
title: A teljesítmény forgalmának útválasztási módszerének konfigurálása az Azure Traffic Manager használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a Traffic Managert, hogy a legalacsonyabb késéssel irányítsa át a forgalmat a végpontra
services: traffic-manager
manager: twooley
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: 46d71a38b79f449084f7353527f2dfb05d5b92a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994910"
---
# <a name="configure-the-performance-traffic-routing-method"></a>A teljesítmény forgalmának útválasztási módszerének konfigurálása

A teljesítmény-forgalom útválasztási módszere lehetővé teszi, hogy a végpontra irányítsa a forgalmat a legalacsonyabb késéssel az ügyfél hálózatáról. Általában a legalacsonyabb késésű adatközpont a legközelebbi földrajzi távolság. Ez a forgalmi útválasztási módszer nem tud a hálózati konfiguráció vagy a betöltés valós idejű változásaihoz.

##  <a name="to-configure-performance-routing-method"></a>A teljesítmény-útválasztási módszer konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). 
2. A portál keresési sávján keresse meg a **Traffic Manager profilokat** , majd kattintson arra a profil nevére, amelyre az útválasztási módszert konfigurálni kívánja.
3. A **Traffic Manager profil** panelen ellenőrizze, hogy a konfigurációban szerepeltetni kívánt Cloud Services és websites is megtalálható-e.
4. A **Beállítások** szakaszban kattintson a **konfiguráció** elemre, majd a **konfiguráció** panelen hajtsa végre a következő lépéseket:
    1. A **forgalom-útválasztási módszer beállításainál** az **útválasztási módszernél** válassza a **teljesítmény** lehetőséget.
    2. A következő módon állítsa be a profil összes végpontján megegyező **Endpoint monitor beállításait** :
        1. Válassza ki a megfelelő **protokollt**, és adja meg a **portszámot** . 
        2. Az **elérési út** mezőbe írja be a perjelet */* . A végpontok figyeléséhez meg kell adnia egy elérési utat és egy fájlnevet. A "/" perjel a relatív elérési útra érvényes bejegyzés, amely azt jelenti, hogy a fájl a gyökérkönyvtárban van (alapértelmezés).
        3. Kattintson a lap tetején található **Mentés** gombra.
5.  Tesztelje a konfiguráció módosításait a következőképpen:
    1.  A portál keresési sávjában keressen rá a Traffic Manager profilnév nevére, és kattintson a Traffic Manager profilra a megjelenített eredmények között.
    2.  A **Traffic Manager** profil panelen kattintson az **Áttekintés** elemre.
    3.  A **Traffic Manager profil** panel az újonnan létrehozott Traffic Manager-profil DNS-nevét jeleníti meg. Ezt bármely ügyfél használhatja (például a böngésző használatával történő navigálással), hogy a jobb oldali végpontra irányítsa át az útválasztási típus alapján. Ebben az esetben az összes kérést a rendszer a végponthoz irányítja, az ügyfél hálózatának legalacsonyabb késésével.
6. Ha a Traffic Manager-profilja működik, szerkessze a DNS-rekordot a mérvadó DNS-kiszolgálón, hogy a vállalat tartománynevét a Traffic Manager tartománynevére mutassa.

![A teljesítmény-forgalmi útválasztási módszer konfigurálása Traffic Manager használatával][1]

## <a name="next-steps"></a>Következő lépések

- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ az [elsődleges útválasztási metódusról](traffic-manager-configure-priority-routing-method.md).
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
- Ismerje meg, hogyan [tesztelheti Traffic Manager beállításait](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png