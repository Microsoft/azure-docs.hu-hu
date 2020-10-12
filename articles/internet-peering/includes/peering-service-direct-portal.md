---
title: fájlbefoglalás
titleSuffix: Azure
description: fájlbefoglalás
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81687048"
---
1. Válassza ki az Azure-partneri szolgáltatás számára engedélyezni kívánt társítási kapcsolatot. Ezután válassza a **...**  >  lehetőséget. A **Kapcsolatok szerkesztése**.
    > [!div class="mx-imgBorder"]
    > ![Társ-létrehozási kapcsolatok szerkesztése](../media/setup-direct-modify-editconnection.png)
1. A **társítási** **szolgáltatás használata**területen válassza az engedélyezve lehetőséget, majd kattintson a **Mentés**gombra.
    > [!div class="mx-imgBorder"]
    > ![Társ-létesítési szolgáltatás engedélyezése](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Az **Áttekintés** képernyőn megtekintheti a központi telepítés részleteit. Az üzembe helyezés befejezése után válassza az **Ugrás erőforráshoz**lehetőséget.
    > [!div class="mx-imgBorder"]
    > ![Az üzembe helyezés befejeződött](../media/setup-direct-modify-overview-deployment-complete.png)

1. A **regisztrált előtagok** panelen válassza a **regisztrált előtag hozzáadása**elemet.
    > [!div class="mx-imgBorder"]
    > ![Regisztrált előtag hozzáadása](../media/setup-direct-modify-add-registered-prefix.png)
1. Regisztráljon egy előtagot egy **név** és egy **előtag** kiválasztásával, majd válassza a **Mentés**lehetőséget.
    > [!div class="mx-imgBorder"]
    >  ![Előtag regisztrálása](../media/setup-direct-modify-register-a-prefix.png) 

1. Egy előtag létrehozása után megjelenik a **regisztrált előtagok**listájában. A további részletek megtekintéséhez válassza ki az előtag **nevét** .
    > [!div class="mx-imgBorder"]
    > ![Regisztrált előtagok és kapcsolatok](../media/setup-direct-modify-registered-prefixes.png)
1. A regisztrált előtag lapon megtekintheti a részletes adatokat, amelyek tartalmazzák az egyes előtagok **előtag-kulcsát** . Ezt a kulcsot meg kell adni az ügyfélnek az előfizetést szolgáltató INTERNETSZOLGÁLTATÓtól kiosztott számára. Az ügyfél ezután regisztrálhatja az előfizetését az előfizetésen belül a kulcs használatával.
    > [!div class="mx-imgBorder"]
    > ![Előtag előtag-kulccsal](../media/setup-direct-modify-registered-prefix-detail.png)