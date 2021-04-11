---
title: Valós felhasználómérés weblapok között – Azure Traffic Manager
description: Ebből a cikkből megtudhatja, hogyan állíthatja be a weblapjait valós felhasználómérés küldéséhez az Azure Traffic Managerba.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 7ec91945c901f46d7036e8c474f9f5f0714ce65e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580383"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>valós felhasználómérés küldése az Azure-Traffic Manager weblapok használatával

A weblapokat úgy is beállíthatja, hogy valós felhasználómérés küldjön Traffic Manager egy valós felhasználómérés (RUM) kulcs beszerzésével és a generált kód weblapra való beágyazásával.

## <a name="obtain-a-real-user-measurements-key"></a>valós felhasználómérés kulcs beszerzése

Az ügyfélalkalmazás által Traffic Manager elküldött méréseket a szolgáltatás a **valós felhasználómérés (rum) kulcsnak** nevezett egyedi karakterlánc használatával azonosítja. A RUM-kulcsot a Azure Portal, a REST API, a PowerShell vagy az Azure parancssori felület használatával kérheti le.

A RUM kulcs beszerzése Azure Portal használatával:
1. Egy böngészőben jelentkezzen be az Azure Portalra. Ha még nincs fiókja, regisztrálhat egy egy hónapos ingyenes próbaverzióra.

1. A portálon keresse meg a módosítani kívánt Traffic Manager profil nevét, majd válassza ki a Traffic Manager profilt a megjelenített eredmények között.

1. A Traffic Manager profil lapon válassza a **valós felhasználómérés** lehetőséget a **Beállítások** területen.

1. Válassza a **kulcs létrehozása** lehetőséget egy új rum-kulcs létrehozásához.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="Képernyőkép a kulcs létrehozásáról."::: 

   **1. ábra: valós felhasználómérés kulcs létrehozása**

1. A lap ekkor megjeleníti a generált RUM-kulcsot, valamint egy JavaScript-kódrészletet, amelyet be kell ágyazni a HTML-lapra.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="A generált JavaScript-kód képernyőképe."::: 

    **2. ábra: valós felhasználómérés kulcs-és mérési JavaScript**
 
1. A JavaScript-kód másolásához kattintson a **Másolás** gombra. 

> [!IMPORTANT]
> A valós felhasználómérés funkció megfelelő működéséhez használja a generált JavaScriptet. A parancsfájl vagy az valós felhasználómérés által használt parancsfájlok módosításai kiszámíthatatlan működéshez vezethetnek.

## <a name="embed-the-code-to-an-html-web-page"></a>A kód beágyazása egy HTML-weblapra

A RUM-kulcs beszerzése után a következő lépés a másolt JavaScript beágyazása egy olyan HTML-oldalra, amelyet a végfelhasználók meglátogatnak. A HTML-szerkesztés többféleképpen is elvégezhető, és különböző eszközöket és munkafolyamatokat használhat. Ebből a példából megtudhatja, hogyan frissítheti a HTML-lapokat a szkript hozzáadásához. Ezt az útmutatót használhatja a HTML-forrás felügyeleti munkafolyamathoz való igazításhoz.

1. Nyissa meg a HTML-lapot egy szövegszerkesztőben.

1. Illessze be az utolsó szakaszban másolt JavaScript-kódot a HTML törzs szakaszába. A másolt kód a 8 & 9. sorban található, lásd: 3. ábra.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="Képernyőfelvétel a weboldalra ágyazott generált JavaScriptről."::: 

    **3. ábra: egyszerű HTML beágyazott valós felhasználómérés JavaScripttel**

1. Mentse a HTML-fájlt, és az internethez csatlakozó webkiszolgálón tárolja.

1. Amikor legközelebb megjeleníti a lapot egy webböngészőben, a rendszer letölti a JavaScriptet, és a parancsfájl végrehajtja a mérési és jelentéskészítési műveleteket.

## <a name="next-steps"></a>Következő lépések
- További információ a [valós felhasználómérés](traffic-manager-rum-overview.md)
- Tudnivalók a [Traffic Manager működéséről](traffic-manager-overview.md)
- További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)
- Megtudhatja, hogyan [hozhat létre Traffic Manager-profilt](./quickstart-create-traffic-manager-profile.md)
