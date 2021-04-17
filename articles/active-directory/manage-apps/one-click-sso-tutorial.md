---
title: Az alkalmazás egykattintásos, egyszeri bejelentkezési (SSO) konfigurációja Azure Marketplace alkalmazás | Microsoft Docs
description: Az SSO egykattintásos konfigurálási lépései az alkalmazáshoz a Azure Marketplace.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: e14944bc92b0d728a917402a1bd2f01b8b9012e4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375644"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Egyszeri bejelentkezés egykattintásos alkalmazáskonfigurációja

 Ez az oktatóanyag bemutatja, hogyan konfigurálható egykattintásos egyszeri bejelentkezés (SSO) az SAML-támogatással és Azure Active Directory (Azure AD) alkalmazásokkal a Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Az egykattintásos SSO bemutatása

Az egykattintásos egyszeri bejelentkezés funkció az SAML protokollt támogató Azure Marketplace konfigurálható. Az Azure AD SSO konfigurációs oldalán ez a beállítás lehetővé teszi az Azure AD-metaadatok automatikus konfigurálást az alkalmazás oldalán. Így gyorsan, minimális manuális erőfeszítéssel állíthatja be az SSO-t.

## <a name="advantages-of-one-click-sso"></a>Az egykattintásos SSO előnyei

- Az alkalmazásoldalon manuális Azure Marketplace igénylő alkalmazások gyors SSO-konfigurációja.
- Hatékonyabb és pontosabb SSO-konfiguráció.
- Nincs szükség partneri kommunikációra vagy támogatásra a beállításhoz. Az alkalmazás biztosítja az SAML-konfiguráció felhasználói felületét.

## <a name="prerequisites"></a>Előfeltételek

- Az alkalmazás aktív előfizetése az SSO-val való konfiguráláshoz. Rendszergazdai hitelesítő adatokra is szüksége lesz.
- A **Saját alkalmazások microsoftos Biztonságos** bejelentkezés bővítmény telepítve van a böngészőben. További információ: Alkalmazások elérése és [használata a Saját alkalmazások portálon.](../user-help/my-apps-portal-end-user-access.md)

## <a name="one-click-sso-configuration-steps"></a>Egykattintásos SSO konfigurációs lépések

1. Adja hozzá az alkalmazást a Azure Marketplace.

2. Válassza **az Egyszeri bejelentkezés lehetőséget.**

3. Válassza **az Egyszeri bejelentkezés engedélyezése lehetőséget.**

4. Töltse ki a kötelező konfigurációs értékeket az **SamL-alapkonfiguráció szakaszban.**

    > [!NOTE]
    > Ha az alkalmazás egyéni jogcímeket konfigurál, kezelje őket, mielőtt végrehajtja az egykattintásos SSO-t.

5. Ha az egykattintásos SSO szolgáltatás elérhető a Azure Marketplace számára, az alábbi képernyő jelenik meg. Előfordulhat, hogy telepítenie kell **a Saját alkalmazások Biztonságos** bejelentkezés böngészőbővítményt a **Bővítmény telepítése lehetőség kiválasztásával.**

   ![Biztonságos Saját alkalmazások böngészőbővítmény telepítése](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Miután hozzáadta a bővítményt a böngészőhöz, válassza a Beállítás **lehetőséget. \<Application Name\>** Miután átirányította az alkalmazás felügyeleti portálra, jelentkezzen be rendszergazdaként.

   ![Alkalmazásnév beállítása](./media/one-click-sso-tutorial/setup-sso.png)

7. A böngészőbővítmény automatikusan konfigurálja az SSO-t az alkalmazáson. A megerősítéshez válassza az **Igen választ.**

   ![Az automatikusan kitöltve adatok mentése](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Ha az alkalmazás SSO-konfigurációja további lépéseket igényel, kövesse az utasításokat a lépések végrehajtásához.

8. A konfiguráció befejezése után kattintson az **OK gombra** a módosítások mentéshez.

   ![Az automatikusan kitöltve adatok mentése](./media/one-click-sso-tutorial/save-data.png)

9. Megjelenik egy megerősítő ablak, amely tudatja, hogy az SSO-beállítások konfigurálása sikeres volt.

   ![SSO konfigurálva](./media/one-click-sso-tutorial/sso-configured.png)

10. A konfiguráció sikeres beállítása után kijelentkezik az alkalmazásból, és visszatér a Azure Portal.

11. Az egyszeri bejelentkezés **teszteléshez** válassza a Tesztelés lehetőséget.

## <a name="additional-resources"></a>További források

* [Oktatóanyagok listája az SaaS-alkalmazások és -Azure Active Directory](../saas-apps/tutorial-list.md)
* [Mi a Saját alkalmazások biztonságos bejelentkezési böngészőbővítmény?](../user-help/my-apps-portal-end-user-access.md)
