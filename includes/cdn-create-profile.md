---
title: fájl belefoglalása
description: fájl belefoglalása
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 9a003b5c42a6ef4c699a3768d15ae08f86d56e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367290"
---
## <a name="create-a-new-cdn-profile"></a>Új CDN-profil létrehozása

A CDN-profil a CDN-végpontok egy olyan tárolója, amely meghatároz egy tarifacsomagot.

1. A Azure Portal válassza az **erőforrás létrehozása** (a bal felső sarokban) lehetőséget. Ekkor megnyílik az **Új** panel.
   
1. Keresse meg és válassza a **CDN** elemet, majd kattintson a **Létrehozás** elemre:
   
    ![CDN erőforrás kiválasztása](./media/cdn-create-profile/cdn-new-resource.png)

    Megjelenik a **CDN-profil** panel.

1. Írja be a következő értékeket:
   
    | Beállítás  | Érték |
    | -------- | ----- |
    | **Név** | Írja be a *CDN-Profile-123* nevet a profil neveként. |
    | **Előfizetés** | Válasszon egy Azure-előfizetést a legördülő listából. |
    | **Erőforráscsoport** | Válassza az **új létrehozása** elemet, és írja be a *CDNQuickstart-RG* nevet az erőforráscsoport neveként, vagy válassza a **meglévő használata** lehetőséget, és válassza a *CDNQuickstart – RG* lehetőséget, ha már rendelkezik a csoporttal. | 
    | **Erőforráscsoport helye** | Válasszon egy helyet a legördülő listából. |
    | **Tarifacsomag** | Válassza ki a **szabványos Akamai** lehetőséget a legördülő listából. (A Akamai réteg üzembe helyezési ideje körülbelül egy percet tartalmaz. A Microsoft szintje körülbelül 10 percet vesz igénybe, és a Verizon szintjei körülbelül 30 percet vesznek igénybe.) |
    | **Új CDN-végpont létrehozása** | Hagyja kiválasztatlanul. |  
   
    ![Új CDN-profil](./media/cdn-create-profile/cdn-new-profile.png)

1. Válassza a **Létrehozás** gombot a profil létrehozásához.

