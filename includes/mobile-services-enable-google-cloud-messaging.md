---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b32ff0926aa2b797bdb8dfcbb01cf845080a0ce9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93376025"
---
1. Lépjen be a [Google Cloud Console](https://console.developers.google.com/cloud-resource-manager) (Google felhőkonzol) felületére, és jelentkezzen be Google-fiókja hitelesítő adataival. 
2. Kattintson az eszköztár **Create Project** (Projekt létrehozása) gombjára. 
   
    ![Új projekt létrehozása](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. A **Project name** (Projekt neve) mezőben adja meg a projektje nevét, majd kattintson a **Create** (Létrehozás) gombra.
4. Válassza az eszköztáron az **Alerts** (Értesítések) gombot, majd válassza ki a projektjét a listából. Ekkor megjelenik a projektje irányítópultja. Az irányítópultot közvetlenül is elérheti a következő URL használatával: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

    ![Projekt kiválasztása az értesítéseknél](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. Jegyezze az irányítópult **Project info** (Projektadatok) csempéjén a **Project info** (Projektinformáció) tulajdonság értékét. 

    ![Projektazonosító](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. Az irányítópult **APIs** (API-k) csempéjén kattintson a **Go to APIs overview** (Ugrás az API-k áttekintéséhez) hivatkozásra. 

    ![API-k áttekintésének hivatkozása](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. Az **API** lapon válassza az **ENABLE APIS AND SERVICES** (API-k és szolgáltatások engedélyezése) lehetőséget. 

    ![API-k és szolgáltatások engedélyezésének gombja](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. Keresse meg és válassza a **Google Cloud Messaging** lehetőséget. 

    ![Google Cloud Messaging megkeresése és kiválasztása](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. A Google Cloud Messaging projektben való engedélyezéséhez válassza az **ENABLE** (Engedélyezés) lehetőséget.

    ![A Google Cloud Messaging engedélyezése](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. Kattintson az eszköztár **Create credentials** (Hitelesítő adatok létrehozása) gombjára. 

    ![Hitelesítő adatok létrehozásának gombja](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. Az **Add credentials to your project** (Hitelesítő adatok hozzáadása a projekthez) lapon válassza az **API key** (API-kulcs) hivatkozást. 

    ![Hitelesítő adatok hozzáadása](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. Az **API key** lapon válassza a **Create/Save** (Létrehozás/mentés) lehetőséget. Az alábbi példában az **IP addresses** (IP-címek) lehetőséget választottuk, és a **0.0.0.0/0** értéket adtuk meg az engedélyezett IP-címeknek. Önnek a saját környezete követelményeinek megfelelően kell beállítania az API-kulcsok korlátozását. 

    ![API-kulcs létrehozására szolgáló gomb](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. Másolja az **API key** (API-kulcs) értéket a vágólapra, és mentse valahova. 

    ![API-kulcs másolása](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    Ezt az API-kulcs-értéket fogja használni, hogy engedélyezze az Azure-nak a GCM-mel való hitelesítést, és hogy leküldéses értesítéseket küldjön az alkalmazása nevében. A projekt irányítópultjára való visszatéréshez használja ezt az URL-t: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

