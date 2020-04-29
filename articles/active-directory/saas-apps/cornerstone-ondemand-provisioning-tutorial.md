---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés sarokköve OnDemand konfigurálása a Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a sarokköve OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77058424"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés sarokköve OnDemand konfigurálása

Ez az oktatóanyag bemutatja, hogyan kell végrehajtani a lépéseket a sarokköve OnDemand és Azure Active Directory (Azure AD) az Azure AD konfigurálásához, hogy a felhasználók vagy csoportok automatikusan kiépítsék és felépítsék a OnDemand.

> [!NOTE]
> Ez az oktatóanyag egy, az Azure AD-felhasználó kiépítési szolgáltatására épülő összekötőt ismertet. További információ a szolgáltatás működéséről, működéséről és gyakori kérdéseiről: a felhasználók kiépítésének [automatizálása és a szolgáltatott szoftveres (SaaS) alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy rendelkezik a következővel:

* Egy Azure AD-bérlő.
* Egy sarokköve OnDemand-bérlő.
* A OnDemand sarokköve felhasználói fiók rendszergazdai jogosultságokkal rendelkezik.

> [!NOTE]
> Az Azure AD kiépítési integrációja a [OnDemand Web Service](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)-re támaszkodik. Ez a szolgáltatás a sarokköve OnDemand csapatok számára érhető el.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Sarokköve OnDemand hozzáadása az Azure Marketplace-ről

Mielőtt konfigurálja az Azure AD-vel való automatikus felhasználói üzembe helyezéshez szükséges sarokköve OnDemand, adjon hozzá sarokkövei OnDemand a piactéren a felügyelt SaaS-alkalmazások listájához.

Az alábbi lépéseket követve adhat hozzá sarokkövei OnDemand a piactéren.

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs ablaktáblán válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory ikon](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a párbeszédpanel tetején található **új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **sarokköve OnDemand** elemet, és válassza ki a **sarokköve OnDemand** elemet az eredmények panelen. Az alkalmazás hozzáadásához válassza a **Hozzáadás**lehetőséget.

    ![Sarokköve OnDemand az eredmények listájában](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Felhasználók kiosztása a sarokköve OnDemand

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak vagy csoportjai számára szükséges a sarokköve a OnDemand. Ha ezeket a felhasználókat vagy csoportokat a sarokköve OnDemand szeretné hozzárendelni, kövesse a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)című témakör utasításait.

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Fontos Tippek a felhasználók fő OnDemand való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználót rendeljen a sarokköve OnDemand az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat vagy csoportokat is hozzárendelhet.

* Ha egy felhasználót a sarokköve OnDemand rendel hozzá, akkor a hozzárendelés párbeszédpanelen válasszon egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Az automatikus felhasználó-kiépítés konfigurálása a sarokköve OnDemand

Ez a szakasz végigvezeti az Azure AD kiépítési szolgáltatás konfigurálásának lépésein. A használatával felhasználók vagy csoportok létrehozása, frissítése és letiltása az Azure AD-ben felhasználói vagy OnDemand alapján.

Az Azure AD-ben az alábbi lépések végrehajtásával konfigurálhatja az automatikus felhasználói üzembe helyezést a sarokkövei OnDemand.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások** > **minden alkalmazás** > **sarokköve OnDemand**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza ki a **sarokköve OnDemand**elemet.

    ![A OnDemand sarokköve hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![OnDemand kiépítés sarokköve](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. A **kiépítési mód** beállítása **automatikusra**.

    ![Sarokköve OnDemand kiépítési módja](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a OnDemand fiókjának rendszergazdai felhasználónevét, rendszergazdai jelszavát és tartományát:

    * A **rendszergazda felhasználóneve** mezőben adja meg a rendszergazdai fiók tartományát vagy felhasználónevét a sarokköve OnDemand-bérlőn. Példa contoso\admin.

    * A **rendszergazdai jelszó** mezőbe írja be a rendszergazdai felhasználónévnek megfelelő jelszót.

    * A **tartomány** mezőben adja meg a OnDemand-bérlő webszolgáltatásának URL-címét. Például a szolgáltatás a (z) helyen `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`található, a contoso esetében pedig a `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`tartomány. A webszolgáltatás URL-címének beolvasásával kapcsolatos további információkért tekintse meg [ezt a PDF-fájlt](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Miután kitöltötte az 5. lépésben látható mezőket, válassza a **kapcsolat tesztelése** lehetőséget, és győződjön meg róla, hogy az Azure ad képes csatlakozni a sarokköve OnDemand. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a sarokköve OnDemand-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![OnDemand-tesztelési kapcsolatok sarokköve](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, aki a kiépítési hibákra vonatkozó értesítéseket szeretné kapni. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![A sarokköve OnDemand értesítő e-mail-címe](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Kattintson a **Mentés** gombra.

9. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a sarokköve OnDemand**lehetőséget.

    ![OnDemand-szinkronizálás sarokköve](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelések** szakaszának sarokköve OnDemand. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a OnDemand sarokköve a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés**lehetőséget.

    ![Sarokköve OnDemand attribútum-hozzárendelések](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. A hatóköri szűrők konfigurálásához kövesse a hatókör- [szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

12. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a sarokköve OnDemand, a beállítások szakaszban módosítsa a **kiépítési állapot** **beállítást** **a**következőre:.

    ![A OnDemand kiépítési állapotának sarokköve](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Adja meg azokat a felhasználókat és csoportokat, amelyeket a sarokköve OnDemand kíván kiépíteni. A **Beállítások** szakaszban válassza ki a **hatókörben**használni kívánt értékeket.

    ![Sarokköve OnDemand hatóköre](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Ha készen áll a létesítésre, válassza a **Mentés**lehetőséget.

    ![Alapköve OnDemand mentése](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Körülbelül 40 percenként történnek, amíg az Azure AD-kiépítési szolgáltatás fut. 

A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésének hivatkozásait. A jelentés ismerteti az Azure AD-kiépítési szolgáltatás által a sarokkövek OnDemand végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

A sarokköve OnDemand **position** attribútum olyan értéket vár, amely megfelel a sarokkövei OnDemand-portál szerepköreinek. Az érvényes **pozicionálási** értékek listájának lekéréséhez lépjen a **felhasználói rekord szerkesztése > szervezet szerkezete > pozíció** a sarokköve OnDemand portálon.

![OnDemand-kiépítés – felhasználói rekord szerkesztése](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![A OnDemand kiépítési pozíciójának sarokköve](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![A OnDemand-létesítési pozíciók sarokköve](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
