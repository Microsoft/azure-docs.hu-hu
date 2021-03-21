---
title: Probléma az alkalmazásproxy-alkalmazás létrehozásakor | Microsoft Docs
description: Alkalmazásproxy-alkalmazások létrehozásával kapcsolatos problémák elhárítása az Azure AD felügyeleti portálon
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
ms.openlocfilehash: c0c88b90d0caa2f723921d76b5f9dc036895f14e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258014"
---
# <a name="problem-creating-an-application-proxy-application"></a>Probléma az alkalmazásproxy-alkalmazás létrehozásakor 

Az alábbiakban felsorolunk néhányat az új alkalmazásproxy-alkalmazás létrehozásakor előforduló gyakori problémákról.

## <a name="recommended-documents"></a>Ajánlott dokumentumok 

Az alkalmazásproxy-alkalmazások felügyeleti portálon keresztüli létrehozásával kapcsolatos további tudnivalókért tekintse meg az [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md)című témakört.

Ha a dokumentumban leírt lépéseket követve hibaüzenetet kap az alkalmazás létrehozásakor, tekintse meg a hiba részleteit, és javaslatokat az alkalmazás kijavításához. A legtöbb hibaüzenet tartalmaz egy javasolt javítást. 

## <a name="specific-things-to-check"></a>Konkrétan ellenőrizhető dolgok

A gyakori hibák elkerüléséhez ellenőrizze az alábbiakat:

-   Rendszergazdai jogosultsággal rendelkezik alkalmazásproxy-alkalmazás létrehozásához

-   A belső URL-cím egyedi

-   A külső URL-cím egyedi

-   Az URL-címek http vagy HTTPS protokollal kezdődnek, és a végén egy "/"

-   Az URL-címnek tartománynévnek és nem IP-címnek kell lennie

A hibaüzenetnek a jobb felső sarokban kell megjelennie az alkalmazás létrehozásakor. Az értesítés ikont is kiválaszthatja a hibaüzenetek megtekintéséhez.

   ![Értesítési kérés](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Következő lépések
[Alkalmazásproxy engedélyezése az Azure Portalon](application-proxy-add-on-premises-application.md)
