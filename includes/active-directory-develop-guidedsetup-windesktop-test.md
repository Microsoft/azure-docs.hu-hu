---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: f121be4ec8c3d3ab618e2955d9dbd8ab5eea461d
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129530"
---
## <a name="test-your-code"></a>A kód tesztelése

A projekt futtatásához a Visual Studióban válassza az **F5**lehetőséget. Az alkalmazás **MainWindow** megjelenik, ahogy az itt látható:

![Az alkalmazás tesztelése](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Amikor első alkalommal futtatja az alkalmazást, és kiválasztja a **Microsoft Graph API meghívása** gombot, a rendszer felszólítja, hogy jelentkezzen be. A teszteléshez használjon Azure Active Directory fiókot (munkahelyi vagy iskolai fiókot) vagy egy Microsoft-fiók (live.com, outlook.com).

![Bejelentkezés az alkalmazásba](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Adja meg az alkalmazás-hozzáférés beleegyezikét

Amikor először jelentkezik be az alkalmazásba, a rendszer arra kéri, hogy engedélyezze az alkalmazás számára a profil elérését, és jelentkezzen be, ahogy az itt látható:

![Adja meg az alkalmazás-hozzáféréshez szükséges engedélyt](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Alkalmazás eredményeinek megtekintése

A bejelentkezést követően meg kell jelennie a Microsoft Graph API-nak a hívás által visszaadott felhasználói profil adatainak. Az eredmények az **API-hívás eredményei** mezőben jelennek meg. Az `AcquireTokenInteractive` vagy `AcquireTokenSilent` hívásakor beszerzett jogkivonatra vonatkozó alapszintű információk a **jogkivonat-információ** mezőben láthatók. Az eredmények a következő tulajdonságokat tartalmazzák:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|**Felhasználónév** |<span>user@domain.com</span> |A felhasználó azonosítására szolgáló Felhasználónév.|
|**A jogkivonat lejár** |DateTime |A jogkivonat lejárati ideje. A MSAL a jogkivonatot szükség szerint megújítva kiterjeszti a lejárati dátumot.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörökről és a delegált engedélyekről

A Microsoft Graph API-nak a felhasználónak *. Read* hatókörrel kell rendelkeznie a felhasználói profil olvasásához. A rendszer alapértelmezés szerint automatikusan hozzáadja ezt a hatókört az alkalmazás regisztrációs portálján regisztrált összes alkalmazáshoz. A Microsoft Graph egyéb API-jai, valamint a háttér-kiszolgálóhoz tartozó egyéni API-k további hatóköröket is igényelhetnek. A Microsoft Graph API-nak szüksége van a *naptárak. Read* hatókörre a felhasználó naptárának listázásához.

Ha egy alkalmazás kontextusában szeretné elérni a felhasználó naptárait, adja hozzá a *naptárakat. olvassa el* a delegált jogosultságokat az alkalmazás regisztrációs adataihoz. Ezután adja hozzá a *naptárak. Read* hatókört a `acquireTokenSilent` híváshoz.

>[!NOTE]
>A rendszer a hatókörök számának növelésével további hozzájárulásokat is kérhet a felhasználótól.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
