---
title: Hozzáférési csomag erőforrás-szerepköreinek módosítása az Azure AD-jogosultságok kezelésében – Azure Active Directory
description: Megtudhatja, hogyan módosíthatja egy meglévő hozzáférési csomag erőforrás-szerepköreit Azure Active Directory jogosultságok felügyelete alatt.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f69cf492ec3e28d7f4aa86971dc6c91b34bdf5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644177"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag erőforrás-szerepköreinek módosítása az Azure AD-jogosultságok kezelésében

Hozzáférés-csomagkezelőként bármikor módosíthatja egy hozzáférési csomag erőforrásait anélkül, hogy aggódnia kellene a felhasználó új erőforrásaihoz való hozzáférésének kiosztásával vagy az előző erőforrásokból való hozzáférés eltávolításával. Ez a cikk azt ismerteti, hogyan lehet módosítani egy meglévő hozzáférési csomag erőforrás-szerepköreit.

Ez a videó áttekintést nyújt a hozzáférési csomagok módosításáról.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Erőforrások katalógusának keresése

Ha erőforrásokat kell hozzáadnia egy hozzáférési csomaghoz, ellenőrizze, hogy a szükséges erőforrások elérhetők-e a katalógusban. Ha Ön egy Access Package Manager, nem adhat hozzá erőforrásokat a katalógushoz, még akkor sem, ha Ön a tulajdonosa. A katalógusban elérhető erőforrások használatára korlátozódik.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **katalógus** elemre, majd nyissa meg a katalógust.

1. A bal oldali menüben kattintson az **erőforrások** elemre a katalógusban található erőforrások listájának megtekintéséhez.

    ![Katalógusban található erőforrások listája](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Ha Ön egy Access Package Manager, és erőforrásokat kell hozzáadnia a katalógushoz, megkérheti a katalógus tulajdonosát, hogy vegye fel őket.

## <a name="add-resource-roles"></a>Erőforrás-Szerepkörök hozzáadása

Az erőforrás-szerepkörök az erőforrásokhoz társított engedélyek gyűjteményei. Ahhoz, hogy a felhasználók számára elérhetővé tegye az erőforrásokat, erőforrás-szerepköröket adhat hozzá a hozzáférési csomaghoz. Hozzáadhat erőforrás-szerepköröket a csoportokhoz, a csoportokhoz, az alkalmazásokhoz és a SharePoint-webhelyekhez.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson az **erőforrás-szerepkörök** elemre.

1. Kattintson az **erőforrás-Szerepkörök hozzáadása** elemre, hogy megnyissa az erőforrás-Szerepkörök hozzáadása a csomaghoz lapot.

    ![Hozzáférési csomag – erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Attól függően, hogy szeretne-e csoportot, csapatot, alkalmazást vagy SharePoint-webhelyet felvenni, hajtsa végre a következő erőforrás-szerepköri szakaszban található lépéseket.

## <a name="add-a-group-or-team-resource-role"></a>Csoport vagy csoport erőforrás-szerepkör hozzáadása

A jogosultságok kezelésével automatikusan hozzáadhat felhasználókat egy csoporthoz vagy csapathoz a Microsoft Teams szolgáltatásban, ha hozzáférési csomagot rendelnek hozzájuk. 

- Ha egy csoport vagy csapat egy hozzáférési csomag része, és egy felhasználó hozzá van rendelve az adott hozzáférési csomaghoz, akkor a felhasználó hozzá lesz adva az adott csoporthoz vagy csapathoz, ha még nem létezik.
- Ha egy felhasználó hozzáférési csomagjának hozzárendelése lejár, a rendszer eltávolítja azokat a csoportból vagy csapatból, kivéve, ha jelenleg egy másik hozzáférési csomagra van rendelve, amely ugyanazt a csoportot vagy csapatot tartalmazza.

Bármelyik [Azure ad biztonsági csoportot vagy Microsoft 365 csoportot](../fundamentals/active-directory-groups-create-azure-portal.md)is kiválaszthatja. A rendszergazdák hozzáadhatnak bármely csoportot egy katalógushoz; a katalógus tulajdonosai bármelyik csoportot hozzáadhatják a katalógushoz, ha a csoport tulajdonosai. A csoportok kiválasztásakor tartsa szem előtt az alábbi Azure AD-korlátozásokat:

- Ha egy felhasználó, a vendéget is beleértve, tagként egy csoport vagy csapat tagjaként van hozzáadva, akkor az adott csoport vagy csapat többi tagját is láthatják.
- Az Azure AD nem tudja módosítani a Windows Server Active Directoryról szinkronizált csoport tagságát Azure AD Connect használatával, vagy az Exchange Online-ban, terjesztési csoportban létrehozottként.  
- A dinamikus csoportok tagsága nem frissíthető egy tag hozzáadásával vagy eltávolításával, így a dinamikus csoporttagságok nem alkalmasak a jogosultsági felügyelettel való használatra.

További információ: csoportok és [Microsoft 365 csoportok és Microsoft Teams](/microsoftteams/office-365-groups) [összevetése](/office365/admin/create-groups/compare-groups) .

1. Az **erőforrás-Szerepkörök hozzáadása a csomaghoz** lapon kattintson a **csoportok és** csoportok elemre a csoportok kiválasztása panel megnyitásához.

1. Válassza ki azokat a csoportokat és csapatokat, amelyeket bele szeretne foglalni a hozzáférési csomagba.

    ![Hozzáférési csomag – erőforrás-Szerepkörök hozzáadása – csoportok kiválasztása](./media/entitlement-management-access-package-resources/group-select.png)

1. Kattintson a **Kiválasztás** elemre.

    Miután kiválasztotta a csoportot vagy a csapatot, az **altípus** oszlopban a következő altípusok egyike jelenik meg:

    | Altípus | Description |
    | --- | --- |
    | Biztonság | Erőforrásokhoz való hozzáférés biztosítására szolgál. |
    | Disztribúció | Az értesítések egy csoportba való küldésére szolgál. |
    | Microsoft 365 | Microsoft 365 csoport, amely nem engedélyezett a csapat számára. A felhasználók közötti, a vállalaton belüli és kívüli együttműködéshez használatos. |
    | Csoport | Microsoft 365 csoportok, amelyeken engedélyezve van a csoport. A felhasználók közötti, a vállalaton belüli és kívüli együttműködéshez használatos. |

1. A **szerepkör** listából válassza a **tulajdonos** vagy a **tag** elemet.

    Általában kiválasztja a tag szerepkört. Ha a tulajdonos szerepkört választja, a felhasználók más tagokat vagy tulajdonosokat is hozzáadhatnak vagy eltávolíthatnak.

    ![Hozzáférési csomag – erőforrás-szerepkör hozzáadása csoporthoz vagy csapathoz](./media/entitlement-management-access-package-resources/group-role.png)

1. Kattintson a **Hozzáadás** parancsra.

    A hozzáférési csomaghoz meglévő hozzárendelésekkel rendelkező felhasználók automatikusan ennek a csoportnak vagy csapatnak a tagjai lesznek, amikor hozzáadják őket.

## <a name="add-an-application-resource-role"></a>Alkalmazás-erőforrás szerepkör hozzáadása

Az Azure AD automatikusan hozzárendelheti a felhasználókat egy Azure AD vállalati alkalmazáshoz, beleértve az SaaS-alkalmazásokat és a szervezet Azure AD-ba összevont alkalmazásait is, amikor egy felhasználó hozzáférési csomagot kap. Az összevont egyszeri bejelentkezésen keresztül az Azure AD-vel integrált alkalmazások esetében az Azure AD összevonási jogkivonatokat állít ki az alkalmazáshoz hozzárendelt felhasználók számára.

Az alkalmazások több szerepkörrel rendelkezhetnek. Ha egy alkalmazás egy hozzáférési csomaghoz van hozzáadva, ha az alkalmazásnak egynél több szerepköre van, meg kell adnia a megfelelő szerepkört a felhasználók számára. Ha alkalmazásokat fejleszt, többet is megtudhat arról, hogyan adja hozzá ezeket a szerepköröket az alkalmazásaihoz a [következő témakörben: a SAML-tokenben kiadott szerepkör-jogcím konfigurálása a vállalati alkalmazásokhoz](../develop/active-directory-enterprise-app-role-management.md).

Ha egy alkalmazás-szerepkör egy hozzáférési csomag része:

- Ha egy felhasználó hozzá van rendelve a hozzáférési csomaghoz, a felhasználó hozzá lesz adva az adott alkalmazási szerepkörhöz, ha még nem létezik.
- Ha a felhasználó hozzáférési csomagjának hozzárendelése lejár, a hozzáférésük el lesz távolítva az alkalmazásból, kivéve, ha az adott alkalmazási szerepkört tartalmazó másik hozzáférési csomaghoz vannak hozzárendelve.

Az alkalmazások kiválasztásakor a következő szempontokat érdemes figyelembe venni:

- Az alkalmazások is rendelkezhetnek a szerepkörökhöz hozzárendelt csoportokkal is.  Lehetőség van arra is, hogy egy hozzáférési csomagban vegyen fel egy csoportot egy alkalmazási szerepkör helyett, de az alkalmazás nem lesz látható a felhasználó számára a hozzáférési portál hozzáférési csomagjának részeként.

1. Az **erőforrás-Szerepkörök hozzáadása a csomaghoz** lapon kattintson az **alkalmazások** elemre az alkalmazások kiválasztása panel megnyitásához.

1. Válassza ki a hozzáférési csomagban szerepeltetni kívánt alkalmazásokat.

    ![Hozzáférési csomag – erőforrás-Szerepkörök hozzáadása – alkalmazások kiválasztása](./media/entitlement-management-access-package-resources/application-select.png)

1. Kattintson a **Kiválasztás** elemre.

1. A **szerepkör** listából válassza ki az alkalmazás szerepkört.

    ![Hozzáférési csomag – erőforrás-szerepkör hozzáadása egy alkalmazáshoz](./media/entitlement-management-access-package-resources/application-role.png)

1. Kattintson a **Hozzáadás** parancsra.

    A hozzáférési csomaghoz meglévő hozzárendelésekkel rendelkező felhasználók automatikusan hozzáférhetnek ehhez az alkalmazáshoz, amikor az hozzá van adva.

## <a name="add-a-sharepoint-site-resource-role"></a>SharePoint-webhely erőforrás-szerepkörének hozzáadása

Az Azure AD automatikusan hozzá tud rendelni felhasználókat a SharePoint Online-webhelyekhez vagy a SharePoint Online-webhelycsoportok eléréséhez, ha hozzáférési csomagot rendelnek hozzájuk.

1. Az **erőforrás-szerepkörök felvétele a csomagba** lapon kattintson a **SharePoint-helyek** elemre a SharePoint Online-webhelyek kiválasztása panel megnyitásához.

    :::image type="content" source="media/entitlement-management-access-package-resources/resource-sharepoint-add.png" alt-text="Hozzáférési csomag – erőforrás-Szerepkörök hozzáadása – SharePoint-helyek kiválasztása – portál nézet":::

1. Válassza ki azokat a SharePoint Online-webhelyeket, amelyeket fel szeretne venni a hozzáférési csomagba.

    ![Hozzáférési csomag – erőforrás-Szerepkörök hozzáadása – SharePoint Online-webhelyek kiválasztása](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Kattintson a **Kiválasztás** elemre.

1. A **szerepkör** listából válassza ki a SharePoint Online-webhely szerepkört.

    ![Hozzáférési csomag – erőforrás-szerepkör hozzáadása egy SharePoint Online-webhelyhez](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Kattintson a **Hozzáadás** parancsra.

    A hozzáférési csomaghoz meglévő hozzárendelésekkel rendelkező felhasználók automatikusan hozzáférhetnek ehhez a SharePoint Online-webhelyhez, amikor az hozzá van adva.

## <a name="remove-resource-roles"></a>Erőforrás-szerepkörök eltávolítása

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson az **erőforrás-szerepkörök** elemre.

1. Az erőforrás-szerepkörök listájában keresse meg az eltávolítani kívánt erőforrás-szerepkört.

1. Kattintson a három pontra (**..**.), majd az **erőforrás-szerepkör eltávolítása** elemre.

    A hozzáférési csomaghoz meglévő hozzárendelésekkel rendelkező felhasználók számára a rendszer automatikusan visszavonja a hozzáférését erre az erőforrás-szerepkörre, ha az eltávolításra kerül.

## <a name="when-changes-are-applied"></a>Módosítások alkalmazása esetén

A jogosultságok kezelése során az Azure AD naponta többször dolgozza fel a hozzáférési csomagok hozzárendeléseinek és erőforrásainak tömeges módosításait. Így ha hozzárendelést végez, vagy megváltoztatja a hozzáférési csomag erőforrás-szerepköreit, akár 24 órát is igénybe vehet az Azure AD-ben, valamint azt, hogy mennyi időt vesz igénybe a módosítások más Microsoft Online Services vagy csatlakoztatott SaaS-alkalmazásokhoz való propagálása. Ha a változás csupán néhány objektumot érint, a változás valószínűleg csak néhány percet vesz igénybe az Azure AD-ben, amely után a többi Azure AD-összetevő ezt követően észleli az SaaS-alkalmazások módosítását és frissítését. Ha a változás több ezer objektumot érint, akkor a módosítás hosszabb ideig tart. Ha például egy 2 alkalmazásból és egy 100 felhasználói hozzárendelésből álló hozzáférési csomaggal rendelkezik, és úgy dönt, hogy egy SharePoint-webhely szerepkört ad hozzá a hozzáférési csomaghoz, akkor előfordulhat, hogy az összes felhasználó a SharePoint-webhely szerepkörbe való belefoglalása után késésben van. Nyomon követheti az előrehaladást az Azure AD-naplóban, az Azure AD-kiépítési naplóban és a SharePoint-webhely naplófájljaiban.

Egy csoport tagjainak eltávolításakor a rendszer eltávolítja azokat a Microsoft 365 csoportból is. Előfordulhat, hogy a csapat csevegési funkciójából való eltávolítás késleltetve történik meg. További [információ: csoporttagság](/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Következő lépések

- [Hozzon létre egy alapszintű csoportot, és vegyen fel tagokat Azure Active Directory használatával](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Útmutató: az SAML-jogkivonatban kiadott szerepkör-jogcím konfigurálása nagyvállalati alkalmazásokhoz](../develop/active-directory-enterprise-app-role-management.md)
- [A SharePoint Online bemutatása](/sharepoint/introduction)