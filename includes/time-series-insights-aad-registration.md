---
title: fájl belefoglalása
description: fájl belefoglalása
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 0ce9575f078058c821ffffe1b9fe45eed5a4ad94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724173"
---
* Miután kiválasztotta a megfelelő platformot a [platform beállításainak konfigurálása](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#configure-platform-settings) szakasz 4. lépésében, konfigurálja az **átirányítási URI-ket** és a **hozzáférési jogkivonatokat** az oldalsó panelen a felhasználói felület jobb oldalán.

    * Az **átirányítási URI-azonosítóknak** meg kell egyezniük a hitelesítési kérelem által megadott címtől:

        * Helyi fejlesztési környezetben üzemeltetett alkalmazásokhoz válassza a **nyilvános ügyfél (mobil & asztali)** lehetőséget. Ügyeljen arra, hogy a **nyilvános ügyfelet** állítsa **Igen** értékre.
        * A Azure App Serviceban üzemeltetett Single-Page alkalmazások esetében válassza a **web** lehetőséget.

    * Döntse el, hogy megfelelő-e a **kijelentkezési URL-cím** .

    * Engedélyezze az implicit engedélyezési folyamatot a **hozzáférési jogkivonatok** vagy **azonosító tokenek** ellenőrzésével.

    [![Átirányítási URI-k létrehozása](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Kattintson a **Konfigurálás**, majd a **Mentés** elemre.

* A Azure Active Directory alkalmazás Azure Time Series Insightsének hozzárendelése. Válassza az **API-engedélyek** lehetőséget a  >    >  **szervezetem által használt API**-k hozzáadása lehetőségre.

    [![API hozzárendelése a Azure Active Directory-alkalmazáshoz](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Írja be `Azure Time Series Insights` a keresősáv kifejezést, majd válassza a elemet `Azure Time Series Insights` .

* Ezután adja meg az alkalmazás által igényelt API-engedélyt. Alapértelmezés szerint a **delegált engedélyek** ki lesznek emelve. Válassza ki az engedély típusát, majd válassza az **engedélyek hozzáadása** lehetőséget.

    [![Adja meg az alkalmazás által igényelt API-engedély típusát](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* [Adja meg a hitelesítő adatokat](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#add-credentials) , ha az alkalmazás saját maga fogja hívni a környezet API-jait. A hitelesítő adatok lehetővé teszik az alkalmazás számára, hogy önmagát hitelesítse magát, ami nem igényel interakciót a felhasználótól futásidőben.