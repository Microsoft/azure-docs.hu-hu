---
title: Az alkalmazás lap nem jelenik meg megfelelően az alkalmazásproxy-alkalmazáshoz | Microsoft Docs
description: Útmutatás arra az esetre, ha a lap nem jelenik meg megfelelően az Azure AD-vel integrált alkalmazásproxy-alkalmazásban
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
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
ms.openlocfilehash: 99b09e7b15427eb33e1e85edd89f4c8a37c4a3eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254654"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Az alkalmazás lap nem jelenik meg megfelelően egy alkalmazásproxy-alkalmazáshoz

Ez a cikk segítséget nyújt a Azure Active Directory Application Proxy-alkalmazásokkal kapcsolatos hibák elhárításához, amikor megnyitja a lapot, de az oldalon található valami nem megfelelő.

## <a name="overview"></a>Áttekintés
Ha egy alkalmazásproxy-alkalmazást tesz közzé, az alkalmazáshoz való hozzáféréskor csak a gyökér alatti lapok érhetők el. Ha a lap nem megfelelően jelenik meg, előfordulhat, hogy az alkalmazáshoz használt legfelső szintű belső URL-cím nem tartalmaz néhány oldal erőforrást. A megoldáshoz győződjön meg arról, hogy az alkalmazás részeként közzétette az oldal *összes* erőforrását.

A probléma megoldásához ellenőrizze, hogy a hiányzó erőforrások problémát a Network Tracker (például a Hegedűs vagy az F12-eszközök Internet Explorerben vagy a Microsoft Edge-ben) megnyitásával, a lap betöltésével és az 404-es hibák keresésével ellenőrizheti. Ez azt jelzi, hogy jelenleg nem találhatók a lapok, és közzé kell tenni őket.

Ebben az esetben tegyük fel, hogy a belső URL-cím használatával közzétett egy kiadási alkalmazást `http://myapps/expenses` , de az alkalmazás a stíluslapot használja `http://myapps/style.css` . Ebben az esetben a stíluslapot a rendszer nem teszi közzé az alkalmazásban, így a költségek alkalmazás betöltésével a Style. css betöltésére tett kísérlet során 404-as hibát jelez. Ebben a példában a problémát úgy oldja meg, hogy közzéteszi az alkalmazást egy belső URL-címmel `http://myapp/` .

## <a name="problems-with-publishing-as-one-application"></a>Egyetlen alkalmazásként való közzététel problémái

Ha nem lehetséges az összes erőforrás közzététele ugyanazon az alkalmazáson belül, közzé kell tennie több alkalmazást, és engedélyeznie kell a közöttük lévő kapcsolatokat.

Ehhez az [Egyéni tartományok](application-proxy-configure-custom-domain.md) megoldás használatát javasoljuk. Ez a megoldás azonban megköveteli, hogy a tartományhoz tartozó tanúsítvány tulajdonosa legyen, és az alkalmazásai teljes tartománynevet (FQDN) használnak. További lehetőségekért tekintse meg a [hibás csatolások dokumentációját](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Következő lépések
[Alkalmazások közzététele az Azure AD-alkalmazásproxyval](application-proxy-add-on-premises-application.md)
