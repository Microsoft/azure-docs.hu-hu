---
title: Alkalmazásproxy-alkalmazás konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és konfigurálhat alkalmazásproxy-alkalmazásokat néhány egyszerű lépésben
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
ms.date: 05/18/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f1488986dc38bba89a5b9a109a444b5f0d81f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255203"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Alkalmazásproxy-alkalmazás konfigurálása

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy alkalmazásproxy-alkalmazást az Azure AD-ben, hogy a helyszíni alkalmazásokat a felhőbe tegye elérhetővé.

## <a name="recommended-documents"></a>Ajánlott dokumentumok

Ha szeretne többet megtudni az alkalmazásproxy-alkalmazások kezdeti konfigurációjáról és létrehozásáról a felügyeleti portálon, kövesse az [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md)című témakört.

További információ az összekötők konfigurálásáról: [alkalmazásproxy engedélyezése a Azure Portalban](application-proxy-add-on-premises-application.md).

További információ a tanúsítványok feltöltéséről és az egyéni tartományokról: [Egyéni tartományok használata az Azure ad Application proxyban](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Az alkalmazás létrehozása/az URL-címek beállítása

Ha követi az [alkalmazások közzététele az Azure ad Application proxy](application-proxy-add-on-premises-application.md) dokumentációban leírt lépéseket, és az alkalmazás létrehozása során hiba lép fel, tekintse meg a hiba részleteit, és javaslatokat az alkalmazás kijavításához. A legtöbb hibaüzenet tartalmaz egy javasolt javítást. A gyakori hibák elkerüléséhez ellenőrizze az alábbiakat:

- Rendszergazdai jogosultsággal rendelkezik alkalmazásproxy-alkalmazás létrehozásához
- A belső URL-cím egyedi
- A külső URL-cím egyedi
- Az URL-címek http vagy HTTPS protokollal kezdődnek, és a végén egy "/"
- Az URL-címnek tartománynévnek, nem pedig IP-címnek kell lennie

A hibaüzenetnek az alkalmazás létrehozásakor a jobb felső sarokban kell megjelennie. Az értesítés ikont is kiválaszthatja a hibaüzenetek megtekintéséhez.

![Itt látható, hogy hol található az értesítési kérdés a Azure Portal](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Összekötők/összekötő csoportok konfigurálása

Ha az összekötők és összekötő-csoportok figyelmeztetése miatt nem sikerül konfigurálni az alkalmazást, az összekötők letöltésével kapcsolatos részletekért lásd: az alkalmazásproxy engedélyezésének lépései. Ha többet szeretne megtudni az összekötők szolgáltatásról, tekintse meg az [Összekötők dokumentációját](application-proxy-connectors.md).

Ha az összekötők inaktívak, ez azt jelenti, hogy nem tudják elérni a szolgáltatást. Ez gyakran azért van, mert az összes szükséges port nincs megnyitva. A szükséges portok listájának megtekintéséhez tekintse meg az alkalmazásproxy dokumentációjának engedélyezése című témakör előfeltételek című szakaszát.

## <a name="upload-certificates-for-custom-domains"></a>Tanúsítványok feltöltése egyéni tartományokhoz

Az egyéni tartományok lehetővé teszik a külső URL-címek tartományának megadását. Egyéni tartományok használatához fel kell töltenie az adott tartományhoz tartozó tanúsítványt. További információ az egyéni tartományok és tanúsítványok használatáról: [Egyéni tartományok használata az Azure ad Application proxyban](application-proxy-configure-custom-domain.md).

Ha problémákat tapasztal a tanúsítvány feltöltésekor, keresse meg a hibaüzeneteket a portálon, ahol további információkat talál a tanúsítvánnyal kapcsolatos problémáról. Gyakori tanúsítvány-problémák a következők:

- Lejárt tanúsítvány
- A tanúsítvány önaláírt
- A tanúsítványból hiányzik a titkos kulcs

A tanúsítvány feltöltésekor a jobb felső sarokban látható hibaüzenet jelenik meg. Az értesítés ikont is kiválaszthatja a hibaüzenetek megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

[Alkalmazások közzététele az Azure AD-alkalmazásproxyval](application-proxy-add-on-premises-application.md)
