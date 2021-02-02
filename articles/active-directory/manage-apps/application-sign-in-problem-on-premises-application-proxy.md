---
title: Hiba történt a helyszíni alkalmazásba való bejelentkezéskor az Azure AD App proxy használatával | Microsoft Docs
description: Gyakori hibák elhárítása, amikor nem tud bejelentkezni az Azure AD-vel integrált helyszíni alkalmazásba az Azure AD Application Proxy használatával
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
ms.openlocfilehash: 9a92ce03b4a8ad241a21b29bbff4e7367d656fab
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259994"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Problémák a helyszíni alkalmazásba való bejelentkezéskor az Azure AD alkalmazásproxy használatával

Ha problémába ütközik egy helyszíni alkalmazásba való bejelentkezés során, a probléma megoldásához kövesse az alábbi lépéseket.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>Az alkalmazásom betöltődik, de az oldal hibásnak tűnik

Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.

  * [Hozzáférek az alkalmazáshoz, de az alkalmazás oldala nem megfelelően jelenik meg](application-proxy-page-appearance-broken-problem.md)
  * [Hozzáférek az alkalmazáshoz, de az alkalmazás betöltése túl sokáig tart](application-proxy-page-load-speed-problem.md)
  * [Hozzáférek az alkalmazáshoz, de az alkalmazás oldalán található hivatkozások nem működnek](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>Kapcsolati probléma van az alkalmazásommal
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Nem tudom, milyen portokat nyissak meg az alkalmazáshoz](application-proxy-add-on-premises-application.md)
  * [Problémát tapasztalok, mert az alkalmazáshoz tartozó összekötőcsoportban nem volt működő összekötő](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Probléma lépett fel az Azure AD-alkalmazásproxy konfigurálásakor a rendszergazdai portálon
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Problémát tapasztalok egy alkalmazásproxy-alkalmazás konfigurálásánál](application-proxy-config-how-to.md)
  * [Nem tudom, hogy kell beállítani az egyszeri bejelentkezést az alkalmazásproxy-alkalmazáshoz](application-proxy-config-sso-how-to.md)
  * [Problémát tapasztalok, amikor alkalmazást hozok létre a felügyeleti portálon](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Probléma lépett fel az alkalmazásomhoz való háttér-hitelesítés beállításakor
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Nem tudom, hogy kell konfigurálni a Kerberos általi korlátozott delegálást](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [Nem tudom, hogy kell konfigurálni az alkalmazást a PingAccess segítségével ](./application-proxy-ping-access-publishing-guide.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Probléma lépett fel az alkalmazásomba történő bejelentkezés során
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * ["Can't Access this Corporate Application" (Nem lehet hozzáférni ehhez a céges alkalmazáshoz) hibaüzenet jelenik meg](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Problémát tapasztalok az alkalmazásproxy-ügynök összekötőjével
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Problémát tapasztalok az alkalmazásproxy-ügynök összekötője telepítésekor](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>Következő lépések
[Helyszíni alkalmazások biztonságos távoli elérése](application-proxy.md)