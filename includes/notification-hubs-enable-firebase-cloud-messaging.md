---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "70935099"
---
1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.
2. A projekt létrehozása után válassza az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) lehetőséget. 

    ![Firebase hozzáadása Android-alkalmazáshoz](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. A **Firebase hozzáadása az Android-alkalmazáshoz** oldalon hajtsa végre a következő lépéseket: 
    1. Az **android-csomag neve mezőben** másolja a **applicationId** értékét az alkalmazás Build. gradle fájljába. Ebben a példában ez a következő: `com.fabrikam.fcmtutorial1app` . 

        ![Adja meg a csomag nevét](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Válassza az **alkalmazás regisztrálása** lehetőséget. 
4. Válassza a **letöltés google-services.jsbe** lehetőséget, mentse a fájlt a projekt **alkalmazás** mappájába, majd válassza a **tovább** lehetőséget. 

    ![google-services.jsletöltése](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Végezze el a következő **konfigurációs módosításokat** a projektben Android Studioban. 
    1.  A projekt szintű Build. gradle fájlban ( &lt; Project &gt; /Build.gradle) adja hozzá a következő utasítást a **függőségek** szakaszhoz. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Az alkalmazás szintű Build. gradle fájlban ( &lt; Project &gt; / &lt; app-Module &gt; /Build.gradle) adja hozzá a következő utasításokat a **függőségek** szakaszhoz. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Adja hozzá a következő sort az alkalmazás szintű Build. gradle fájl végéhez a függőségek szakasz után. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Kattintson a **szinkronizálás most** elemre az eszköztáron. 
 
        ![a Build. gradle konfigurációs módosításai](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Kattintson a **Tovább** gombra. 
7. Válassza **a lépés kihagyása** lehetőséget. 

    ![Az utolsó lépés kihagyása](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. A Firebase konzolján kattintson a projektjéhez tartozó fogaskerékre. Ezután kattintson a **Project Settings** (Projektbeállítások) elemre.

    ![A projektbeállítások kiválasztása](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Ha nem töltötte le a google-services.jsfájlt a Android Studio projekt **alkalmazás** mappájába, ezt megteheti ezen a lapon. 
5. Váltson a felül található **Cloud Messaging (Felhőbeli üzenetkezelés** ) lapra. 
6. Másolja és mentse a **kiszolgálói kulcsot** későbbi használatra. Ezt az értéket használja a hub konfigurálásához.
