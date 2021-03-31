---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "68728815"
---
1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.
2. A projekt létrehozása után válassza az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) lehetőséget. 

    ![Firebase hozzáadása Android-alkalmazáshoz](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. A **Firebase hozzáadása az Android-alkalmazáshoz** oldalon hajtsa végre a következő lépéseket: 
    1. Az **android-csomag neve** mezőben adja meg a csomag nevét. Példa: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Adja meg a csomag nevét](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Válassza az **alkalmazás regisztrálása** lehetőséget.  
    1. Válassza **a letöltés google-services.jsa** következőn:. Ezután mentse a fájlt a projekt mappájába, és válassza a **tovább** lehetőséget. Ha még nem hozta létre a Visual Studio-projektet, ezt a lépést a projekt létrehozása után is elvégezheti. 

        ![google-services.jsletöltése](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Kattintson a **Tovább** gombra. 
    7. Válassza **a lépés kihagyása** lehetőséget. 

        ![Az utolsó lépés kihagyása](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. A Firebase konzolján kattintson a projektjéhez tartozó fogaskerékre. Ezután kattintson a **Project Settings** (Projektbeállítások) elemre.

    ![A projektbeállítások kiválasztása](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Ha nem töltötte le a **google-services.js** fájlt, letöltheti azt ezen az oldalon. 

    ![google-services.jsletöltése az Általános lapon](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Váltson a felül található **Cloud Messaging (Felhőbeli üzenetkezelés** ) lapra. Másolja és mentse a **kiszolgálói kulcsot** későbbi használatra. Ezt az értéket használja az értesítési központ konfigurálásához.

    ![Kiszolgáló kulcsának másolása](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
