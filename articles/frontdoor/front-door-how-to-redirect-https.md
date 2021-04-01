---
title: A Azure Portal használatával HTTP-vel HTTPS-átirányítással hozhat létre egy bejárati ajtót
description: Megtudhatja, hogyan hozhat létre átirányított forgalmat a HTTP-ről HTTPS-re a Azure Portal használatával.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 19908b3cba63bc76a205097ef8d16e612d58503b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91626642"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>A Azure Portal használatával HTTP-vel HTTPS-átirányítással hozhat létre egy bejárati ajtót

A Azure Portal használatával [létrehozhat egy bejárati ajtót](quickstart-create-front-door.md) a TLS-lezáráshoz szükséges tanúsítvánnyal. Egy útválasztási szabály a HTTP-forgalom HTTPS-re való átirányítására szolgál.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Bejárati ajtó létrehozása meglévő Web App-erőforrással

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

1. Válassza a Azure Portal bal felső sarkában található **erőforrás létrehozása** elemet.

1. Keresse meg a **bejárati ajtót** a keresősáv használatával, és ha megtalálta az erőforrás típusát, válassza a **Létrehozás** lehetőséget.

1. Válasszon egy *előfizetést* , majd használjon egy meglévő erőforráscsoportot, vagy hozzon létre újat. Kattintson a **tovább** gombra a konfiguráció lap megadásához.

    > [!NOTE]
    > A felhasználói felületen megkérdezett hely csak az erőforráscsoport esetében érhető el. A bejárati ajtó konfigurációját a rendszer az összes [Azure-beli elülső ajtó pop-helyein](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)üzembe helyezi.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="Új bejárati ajtó alapalapjainak konfigurálása":::

1. A bejárati ajtó konfigurálása három lépésben történik – egy alapértelmezett előtér-gazdagép hozzáadása, a háttérrendszer-készletben lévő hátterek hozzáadása, majd az útválasztási szabályok létrehozása a frontend-gazdagép útválasztási viselkedésének leképezéséhez. Válassza a " **+** " ikont a előtér- _gazdagépeken_ egy előtér-állomás létrehozásához.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Bejárati ajtó konfigurációs tervezője":::

1. Adja meg a bejárati ajtó alapértelmezett előtér-gazdagépének globálisan egyedi nevét. Kattintson a **Hozzáadás** gombra a következő lépéshez való továbblépéshez.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="Előtér-gazdagép hozzáadása":::

### <a name="create-backend-pool"></a>Háttér-készlet létrehozása

1. A háttér **+** - _készletek_ létrehozásához válassza a "" ikont a háttér-készleteken. Adja meg a háttér-készlet nevét, majd válassza a **háttér hozzáadása** elemet.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="A bejárati ajtó Configuration Designer backend-készlete":::

1. Válassza ki a háttér-gazdagép típusát az _app Service_-ben. Válassza ki azt az előfizetést, amelyre a webalkalmazást üzemelteti, majd válassza ki az adott webalkalmazást a legördülő menüből a **háttérbeli gazdagép neve** elemnél.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="Háttér hozzáadása a háttérbeli készlethez":::

1. Válassza a **Hozzáadás** lehetőséget a háttér mentéséhez, majd a háttérbeli készlet konfigurációjának mentéséhez kattintson ismét a **Hozzáadás** gombra. 

## <a name="create-http-to-https-redirect-rule"></a>HTTP-alapú HTTPS-átirányítási szabály létrehozása

1. **+** Útvonal létrehozásához válassza az *útválasztási szabályok* ikonját. Adja meg az útvonal nevét (például "HttpToHttpsRedirect"), majd állítsa az *elfogadott protokoll* mezőt a **"http only"** értékre. Győződjön meg arról, hogy a megfelelő előtér */tartomány* van kiválasztva.  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="A bejárati ajtó konfigurációs tervezője útválasztási szabálya":::

1. Az *útvonal részletei* szakaszban adja meg az **átirányítási** *útvonal típusát* . Győződjön meg arról, hogy az *átirányítási típus* a következőre van beállítva: **Found (302)** és *átirányítási protokoll* – a Get beállítása **csak HTTPS** értékre. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="HTTP-HTTPS-átirányítási útvonal hozzáadása":::

1. Válassza a **Hozzáadás** lehetőséget a http-re irányuló útválasztási szabály mentéséhez a https-átirányítás érdekében.

## <a name="create-forwarding-rule"></a>Továbbítási szabály létrehozása

1. Adjon hozzá egy másik útválasztási szabályt a HTTPS-forgalom kezeléséhez. Válassza ki az **+** *útválasztási szabályok* "" aláírását, és adja meg az útvonal nevét, például: "DefaultForwardingRoute". Ezután állítsa az *elfogadott protokollok* mezőt **csak HTTPS** értékre. Győződjön meg arról, hogy a megfelelő előtér */tartomány* van kiválasztva.

1. Az útvonal részletei szakaszban állítsa az *útvonal típusát* **továbbítandó** értékre. Győződjön meg arról, hogy a jobb oldali háttér-készlet kiválasztásra kerül, és a *továbbítási protokoll* **csak HTTPS**-re van beállítva. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="Továbbítási útvonal hozzáadása a HTTPS-forgalomhoz" border="false":::

1. Válassza a **Hozzáadás** lehetőséget az útválasztási szabály mentéséhez a kérelem továbbításához.

1. Válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás** lehetőséget az előtérben lévő profil létrehozásához. A létrehozás után nyissa meg az erőforrást.

## <a name="next-steps"></a>Következő lépések

- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
- További információ az [URL-átirányítás a bejárati ajtón](front-door-url-redirect.md).
