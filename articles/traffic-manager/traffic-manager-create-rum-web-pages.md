---
title: Valós felhasználói mérések az Azure Traffic Manager weblapokat |} A Microsoft Docs
description: Valós felhasználói mérések küldése a Traffic Manager a weblapok beállítása
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 2d044457df80f16a6e8073e7f3253a611f74d8a8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071214"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Valós felhasználói mérések küldése az Azure Traffic Manager weblapok használata

Konfigurálhatja a weblapok a Traffic Manager valós felhasználói mérések küldése a valós felhasználói mérések (RUM) kulcs beszerzése és beágyazása a weblapra a generált kódot.

## <a name="obtain-a-real-user-measurements-key"></a>A valós felhasználói mérések kulcs beszerzése

A mérések igénybe vehet, és a Traffic Manager küldött az ügyfélalkalmazás egy egyedi karakterlánccá, nevű szolgáltatáspéldányt azonosítja a **valós felhasználói mérések (RUM) kulcs**. Megtekintheti az Azure portal, a REST API használatával az kulcs vagy a PowerShell vagy az Azure CLI használatával.

Az Azure portal használatával RUM kulcs beszerzése:
1. Egy böngészőben jelentkezzen be az Azure Portalon. Ha még nincs fiókja, akkor regisztráljon egy ingyenes egy hónapos próbaidőszak.
2. A portál keresősávjában keressen a Traffic Manager-profil neve, amelyet módosítani szeretne, majd kattintson az eredményeket a Traffic Manager-profilt, amely jelenik meg.
3. A Traffic Manager-profil panelen kattintson a **valós felhasználói mérések** alatt **beállítások**.
4. Kattintson a **kulcs létrehozása** RUM új kulcs létrehozásához.
 
   ![Valós felhasználói mérések kulcsának létrehozása](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **1. ábra: Valós felhasználói mérések kulcs létrehozása**

5. A panel most hozza létre a RUM kulcsot és a egy JavaScript kódrészletet, amelyet a HTML-oldalt beilleszthető jeleníti meg.
 
    ![Valós felhasználói mérések kulcsát a JavaScript-kódot](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **2. ábra: Valós felhasználói mérések kulcsának és a mérési JavaScript**
 
6. Kattintson a **másolási** gombra, hogy a JavaScript-kódot. 

>[!IMPORTANT]
> Használja a létrehozott JavaScript valós felhasználói mérések funkció megfelelő működéséhez. Ez a szkript és a valós felhasználói mérések által használt parancsfájlok módosítása előre nem látható viselkedéshez vezethet.

## <a name="embed-the-code-to-an-html-web-page"></a>Ágyazhat be a kódot, és a egy HTML-weblap

Ha beszerezte a az kulcsot, a következő lépés az a másolt JavaScript beágyazása egy HTML-oldalt, amely a végfelhasználók számára, keresse fel. HTML-szerkesztés számos módon elvégezhető, és különböző eszközökkel és munkafolyamatokkal. Ez a példa bemutatja, hogyan frissíthető egy HTML-oldalt hozzáadni ezt a szkriptet. Ez az útmutató segítségével a HTML-forrás-felügyeleti munkafolyamat igazodjon.

1.  Egy szövegszerkesztőben nyissa meg a HTML-oldal
2.  Illessze be a JavaScript-kódot a HTML TÖRZSÉBEN szakaszában a korábbi lépésben másolt volt (a másolt kód 8 & 9. sor, tekintse meg a 3. ábra).
 
    ![Javascript-kód beágyazza a weblap a valós felhasználói mérések](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **3. ábra: Valós felhasználói mérések beágyazott JavaScript egyszerű HTML**

3.  Mentse a HTML-fájl és a gazdagép, a webkiszolgáló csatlakozik az internethez. 
4. Ezen a lapon jelenik meg a böngészőben, amikor legközelebb letölti a hivatkozott JavaScript, és a szkriptet fogja végrehajtani, a mérés és jelentéskészítési műveletek.


## <a name="next-steps"></a>További lépések
- Tudjon meg többet [valós felhasználói mérések](traffic-manager-rum-overview.md)
- Ismerje meg, [Traffic Manager működése](traffic-manager-overview.md)
- Tudjon meg többet a [forgalom-útválasztási módszerek](traffic-manager-routing-methods.md) a Traffic Manager által támogatott
- Ismerje meg, hogyan [Traffic Manager-profil létrehozása](traffic-manager-create-profile.md)

