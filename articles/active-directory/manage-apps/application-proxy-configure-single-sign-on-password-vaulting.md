---
title: Egyszeri bejelentkezés az alkalmazásokba az Azure AD Application Proxy használatával | Microsoft Docs
description: Kapcsolja be az egyszeri bejelentkezést a közzétett helyszíni alkalmazásokhoz az Azure AD Application Proxy a Azure Portalban.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0cb2830c019635e9020a4b586bdc370450fddb0
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254002"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Jelszó-tároló egyszeri bejelentkezéshez alkalmazásproxy

Azure Active Directory Application Proxy segít a hatékonyság növelésében a helyszíni alkalmazások közzétételével, így a távoli alkalmazottak is biztonságosan férhetnek hozzájuk. A Azure Portal az egyszeri bejelentkezést (SSO) is beállíthatja ezekhez az alkalmazásokhoz. A felhasználóknak csak az Azure AD-ben kell hitelesíteniük magukat, és nem kell újból bejelentkezniük a vállalati alkalmazásba.

Az alkalmazásproxy számos [egyszeri bejelentkezési módot](sso-options.md#choosing-a-single-sign-on-method)támogat. A jelszó alapú bejelentkezés olyan alkalmazások számára készült, amelyek Felhasználónév/jelszó kombinációt használnak a hitelesítéshez. Ha a jelszó-alapú bejelentkezést az alkalmazáshoz konfigurálja, a felhasználóknak egyszer be kell jelentkezniük a helyszíni alkalmazásba. Ezután a Azure Active Directory tárolja a bejelentkezési adatokat, és automatikusan megadja azt az alkalmazásnak, amikor a felhasználók távolról érik el.

Az alkalmazást már közzétette és tesztelte az Application proxyval. Ha nem, kövesse az [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md) című témakör lépéseit, majd térjen vissza ide.

## <a name="set-up-password-vaulting-for-your-application"></a>Jelszó-tároló beállítása az alkalmazáshoz

1. Jelentkezzen be rendszergazdaként a [Azure Portalba](https://portal.azure.com) .
1. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. A listából válassza ki az SSO-val beállítani kívánt alkalmazást.  
1. **Alkalmazásproxy** kiválasztása 
1. Módosítsa az **előhitelesítés típusát** **áteresztő** értékre, majd válassza a **Mentés** lehetőséget. Később visszaválthat **Azure Active Directory** típusra. 
1. Válassza az **egyszeri bejelentkezés** lehetőséget.

   ![Az alkalmazás áttekintés lapjáról válassza az egyszeri bejelentkezés lehetőséget.](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Az egyszeri bejelentkezés mód esetében válassza a **jelszó alapú bejelentkezés** lehetőséget.
1. A bejelentkezési URL-cím mezőbe írja be annak az oldalnak az URL-címét, amelyben a felhasználók megadják a felhasználónevét és jelszavát, hogy bejelentkezzenek az alkalmazásba a vállalati hálózaton kívül. Ez lehet a külső URL-cím, amelyet az alkalmazás alkalmazásproxy használatával történő közzétételekor hozott létre.

   ![Válassza a jelszó alapú bejelentkezés lehetőséget, és adja meg az URL-címét](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Kattintson a **Mentés** gombra.
1. **Alkalmazásproxy** kiválasztása 
1. Módosítsa az **előhitelesítés típusát** **Azure Active Directoryre** , majd válassza a **Mentés** lehetőséget. 
1. Válassza **a felhasználók és csoportok** lehetőséget.
1. Rendeljen felhasználókat az alkalmazáshoz a **felhasználó hozzáadása** lehetőség kiválasztásával. 
1. Ha előre definiálni szeretné a felhasználó hitelesítő adatait, jelölje be a Felhasználónév előtt található jelölőnégyzetet, majd válassza a **hitelesítő adatok frissítése** lehetőséget.
1. Válassza ki **Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  **minden alkalmazás** lehetőséget.
1. A listából válassza ki azt az alkalmazást, amelyet a jelszó egyszeri bejelentkezésével konfigurált.
1. Válassza a **branding** elemet. 
1. Frissítse a **Kezdőlap URL-címét** a **Bejelentkezés URL-címére** a jelszó-SSO lapon, majd válassza a **Mentés** lehetőséget.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Az alkalmazás tesztelése

Nyissa meg a saját alkalmazások portált. Jelentkezzen be a hitelesítő adataival (vagy a hozzáféréshez beállított tesztelési fiók hitelesítő adataival). Ha sikeresen bejelentkezett, kattintson az alkalmazás ikonjára. Ez elindíthatja a saját alkalmazások biztonságos bejelentkezési böngésző bővítményének telepítését. Ha a felhasználó előre megadott hitelesítő adatokkal rendelkezik, a hitelesítés automatikusan megtörténik, ellenkező esetben a felhasználónevet vagy a jelszót kell megadnia az első alkalommal. 

## <a name="next-steps"></a>Következő lépések

- További információk az [egyszeri bejelentkezés](what-is-single-sign-on.md) megvalósításának egyéb módjairól
- Tudnivalók [az alkalmazások távoli Azure-ad Application proxy való elérésének biztonsági szempontjairól](application-proxy-security.md)
