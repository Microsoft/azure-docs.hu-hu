---
title: Nem található működő összekötő csoport egy alkalmazásproxy-alkalmazáshoz
description: Problémák merülhetnek fel, ha nincs működő összekötő egy összekötő csoportban az alkalmazáshoz az Azure AD Application Proxy
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cec028dec2bf2e6295f59aae9c2e243da2209827
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257034"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nem található működő összekötő csoport a alkalmazásproxy-alkalmazáshoz

Ez a cikk segítséget nyújt az olyan gyakori problémák megoldásában, amelyek akkor szembesülnek, ha a Azure Active Directory-vel integrált alkalmazásproxy-alkalmazás nem észlelt összekötőt.

## <a name="overview-of-steps"></a>A lépések áttekintése
Ha nincs működő összekötő az alkalmazáshoz tartozó összekötő csoportban, a probléma megoldásának néhány módja van:

-   Ha nincs összekötő a csoportban, a következőket teheti:

    -   Töltsön le egy új összekötőt a jobb oldali helyi kiszolgálón, és rendelje hozzá ehhez a csoporthoz

    -   Aktív összekötő áthelyezése a csoportba

-   Ha nem rendelkezik aktív összekötővel a csoportban, a következőket teheti:

    -   Annak megállapítása, hogy az összekötő inaktív-e, és hogyan oldható fel

    -   Aktív összekötő áthelyezése a csoportba

A probléma megoldásához nyissa meg az alkalmazás "alkalmazásproxy" menüjét, és tekintse meg az összekötő csoport figyelmeztető üzenetét. Ha nincsenek összekötők a csoportban, a figyelmeztető üzenet megadja, hogy a csoportnak legalább egy összekötőt kell tartalmaznia. Ha nem rendelkezik aktív összekötővel, a figyelmeztető üzenet elmagyarázza a következőt:. Gyakori, hogy inaktív összekötők vannak. 

   ![Összekötő csoport kijelölése Azure Portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Az egyes beállításokkal kapcsolatos részletekért tekintse meg az alábbi megfelelő szakaszt. Az utasítások azt feltételezik, hogy az összekötő-felügyeleti lapról indul. Ha a fenti hibaüzenetet keresi, a figyelmeztető üzenetre kattintva megnyithatja ezt a lapot. A lapot a **Azure Active Directory**, a **vállalati alkalmazások**, majd az alkalmazásproxy lehetőségre kattintva is elérheti **.**

   ![Összekötő csoport kezelése Azure Portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Új összekötő letöltése

Új összekötő letöltéséhez használja az oldal tetején található "Connector letöltése" gombot.

Telepítse az összekötőt egy olyan gépre, amelyen közvetlen a háttérbeli alkalmazás található. Az összekötő általában ugyanarra a kiszolgálóra van telepítve, mint az alkalmazás. A letöltés után az összekötőnek ebben a menüben kell megjelennie. kattintson az összekötőre, és az "összekötő csoport" legördülő lista használatával győződjön meg arról, hogy a megfelelő csoporthoz tartozik. Mentse a módosításokat.

   ![Az összekötő letöltése a Azure Portal](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Aktív összekötő áthelyezése

Ha rendelkezik egy olyan aktív összekötővel, amely a csoporthoz tartozik, és a cél háttérbeli alkalmazásra mutat, akkor áthelyezheti az összekötőt a hozzárendelt csoportba. Ehhez kattintson az összekötőre. Az "összekötő csoport" mezőben a legördülő listából válassza ki a megfelelő csoportot, majd kattintson a Mentés gombra.

## <a name="resolve-an-inactive-connector"></a>Inaktív összekötő feloldása

Ha a csoport egyetlen összekötője inaktív, akkor valószínű, hogy olyan gépen van, amelyen nincs minden szükséges port feloldani.

a probléma kivizsgálásával kapcsolatos részletekért tekintse meg a portok – problémamegoldás című dokumentumot.

## <a name="next-steps"></a>Következő lépések
[Az Azure AD Application Proxy-összekötők ismertetése](application-proxy-connectors.md)


