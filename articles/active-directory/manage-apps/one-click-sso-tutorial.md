---
title: Azure Marketplace-alkalmazás egykattintásos, egyszeri bejelentkezéses (SSO) konfigurációja | Microsoft Docs
description: Az Azure piactéren az alkalmazás egyszeri bejelentkezéses konfigurációjának lépései.
services: active-directory
documentationCenter: na
author: kenwith
manager: daveba
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50f705e587010d956c29a71127e97dd85cb517ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258252"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Egyszeri bejelentkezéses alkalmazás egykattintásos konfigurálása

 Ebből az oktatóanyagból megtudhatja, hogyan hajthat végre egykattintásos, egyszeri bejelentkezéses (SSO) konfigurációt az SAML-támogatású, Azure Active Directory (Azure AD) alkalmazások számára az Azure piactéren.

## <a name="introduction-to-one-click-sso"></a>Az egykattintásos egyszeri bejelentkezés bemutatása

Az egykattintásos egyszeri bejelentkezés funkció az SAML protokollt támogató Azure Marketplace-alkalmazások egyszeri bejelentkezésének konfigurálására szolgál. Az Azure AD SSO konfiguráció lapján ez a beállítás lehetővé teszi az Azure AD-metaadatok automatikus konfigurálását az alkalmazás oldalán. Így gyorsan állíthatja be az egyszeri bejelentkezést minimális manuális erőfeszítéssel.

## <a name="advantages-of-one-click-sso"></a>Egy kattintásos egyszeri bejelentkezés előnyei

- Az Azure Marketplace-alkalmazások gyors egyszeri bejelentkezéses konfigurációja, amely megköveteli az alkalmazás oldalán a manuális telepítést.
- Hatékonyabb és pontos SSO-konfiguráció.
- Nincs szükség partneri kommunikációra vagy támogatásra a telepítéshez. Az alkalmazás az SAML-konfiguráció felhasználói felületét biztosítja.

## <a name="prerequisites"></a>Előfeltételek

- Az alkalmazás aktív előfizetése az SSO-val való konfiguráláshoz. Rendszergazdai hitelesítő adatokra is szükség van.
- A **saját alkalmazások biztonságos bejelentkezési bővítményét** a Microsoft a böngészőben telepítette. További információ: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="one-click-sso-configuration-steps"></a>Egyszeri bejelentkezéses SSO-konfigurációs lépések

1. Adja hozzá az alkalmazást az Azure Marketplace-ről.

2. Válassza az **egyszeri bejelentkezés** lehetőséget.

3. Válassza az **egyszeri bejelentkezés engedélyezése** lehetőséget.

4. Töltse fel a kötelező konfigurációs értékeket az **alapszintű SAML-konfiguráció** szakaszban.

    > [!NOTE]
    > Ha az alkalmazáshoz egyéni jogcímek szükségesek, akkor az egykattintásos egyszeri bejelentkezés végrehajtása előtt kezelje őket.

5. Ha az egykattintásos egyszeri bejelentkezés funkció elérhető az Azure Marketplace-alkalmazáshoz, a következő képernyő jelenik meg. Előfordulhat, hogy a **bővítmény telepítése** lehetőségre kattintva telepítenie kell a **saját alkalmazások biztonságos bejelentkezési böngésző bővítményét** .

   ![Saját alkalmazások telepítése biztonságos bejelentkezési böngésző bővítmény](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Miután hozzáadta a bővítményt a böngészőhöz, válassza a **telepítés \<Application Name\>** lehetőséget. Az alkalmazás-felügyeleti portálra való átirányítást követően jelentkezzen be rendszergazdaként.

   ![Alkalmazás nevének beállítása](./media/one-click-sso-tutorial/setup-sso.png)

7. A böngésző bővítménye automatikusan konfigurálja az egyszeri bejelentkezést az alkalmazásban. Erősítse meg az **Igen** lehetőséget.

   ![Az automatikusan feltöltött adatok mentése](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Ha az alkalmazás egyszeri bejelentkezéses konfigurációja további lépéseket igényel, kövesse az utasításokat a lépések végrehajtásához.

8. A konfiguráció befejezése után kattintson az **OK gombra** a módosítások mentéséhez.

   ![Az automatikusan feltöltött adatok mentése](./media/one-click-sso-tutorial/save-data.png)

9. Egy megerősítő ablak jelenik meg, amely tudatja Önnel, hogy az SSO-beállítások konfigurálása sikeres volt.

   ![Egyszeri bejelentkezés konfigurálva](./media/one-click-sso-tutorial/sso-configured.png)

10. A konfiguráció sikeres végrehajtása után kijelentkezett az alkalmazásból, és visszatért a Azure Portal.

11. Kiválaszthatja **az egyszeri bejelentkezés tesztelését** .

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md)
* [Mi a My apps biztonságos bejelentkezési böngésző bővítmény?](../user-help/my-apps-portal-end-user-access.md)
