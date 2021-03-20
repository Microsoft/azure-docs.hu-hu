---
title: Azure Marketplace-előfizetés beállítása üzemeltetett tesztelési meghajtókhoz
description: Ismerteti, hogyan állíthat be egy Azure Marketplace-előfizetést a Dynamics 365-hez a Customer engagement és a Dynamics 365 for Operations test Drives szolgáltatáshoz
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 03/16/2020
ms.openlocfilehash: a7f12891bf394e54ee46c60598536faed1731202
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600883"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>Azure Marketplace-előfizetés beállítása üzemeltetett tesztelési meghajtókhoz

Ez a cikk azt ismerteti, hogyan állítható be az Azure Marketplace-előfizetés és a **dynamics 365 a Customer engagement** vagy a **Dynamics 365 for Operations** -környezethez a tesztelési meghajtókhoz.

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>A Dynamics 365 beállítása a Customer engagement szolgáltatáshoz

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy rendszergazdai fiókkal.
2. Ellenőrizze, hogy a Dynamics 365 Test Drive-példányhoz társított bérlőn található-e a jobb felső sarokban lévő fiók ikonja fölé. Ha nem a megfelelő bérlőben van, válassza a fiók ikont a megfelelő bérlőre való váltáshoz.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="A megfelelő bérlő kiválasztása.":::

3. Ellenőrizze, hogy elérhető-e a **Dynamics 365 Customer engagement-csomag** licence.

    [![A csomag licencének ellenőrzése.](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Hozzon létre egy Azure Active Directory (AD) alkalmazást az Azure-ban. A AppSource ezt az alkalmazást fogja használni a tesztelési meghajtó felhasználójának a bérlőben való kiépítéséhez és megszüntetéséhez.
    1. A szűrő ablaktáblán válassza a **Azure Active Directory** lehetőséget.
    2. Válassza az **Alkalmazásregisztrációk** lehetőséget.

        [![Az alkalmazások regisztrációjának kiválasztása.](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. Válassza az **új regisztráció** lehetőséget.
    4. Adja meg a megfelelő alkalmazásnév-nevet.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Alkalmazás regisztrálása.":::

    5. A támogatott fióktípus területen válassza **a fiók lehetőséget a szervezeti címtárban és a személyes Microsoft-fiókokban**.
    6. Válassza a **Létrehozás** lehetőséget, és várja meg az alkalmazás létrehozását.
    7. Az alkalmazás létrehozása után figyelje meg az **alkalmazás azonosítóját** az áttekintési képernyőn. Erre az értékre később szükség lesz a tesztelési meghajtó konfigurálásakor.
    8. Az **alkalmazás kezelése** területen válassza az **API-engedélyek** lehetőséget.
    9. Válassza **az engedély hozzáadása** , majd a **Microsoft Graph API** elemet.
    10. Válassza ki az **alkalmazás** engedély kategóriát, majd a **User. ReadWrite. All**, a **Directory. Read. All** és a **Directory. ReadWrite. All** engedélyeket.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Az alkalmazás engedélyeinek beállítása.":::

    11. Az engedély hozzáadása után válassza a **rendszergazdai jóváhagyás megadása a Microsoftnak** lehetőséget.
    12. Az üzenet riasztása területen válassza az **Igen** lehetőséget.

        [![Az alkalmazáshoz tartozó engedélyek sikeres megadását mutatja.](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    13. Titkos kód létrehozása a Azure AD alkalmazáshoz:
        1. Az **alkalmazás kezelése** lapon válassza a **tanúsítvány és titkos kulcsok** elemet.
        2. Az ügyfél titkos kulcsa területen válassza az **új ügyfél titka** lehetőséget.
        3. Adjon meg egy leírást, például a *Test Drive*-t, és válasszon ki egy megfelelő időtartamot. A teszt meghajtó a kulcs lejárata után megszakad, ekkor új kulcsot kell létrehoznia, és meg kell adnia a AppSource.
        4. Az Azure-alkalmazás titkos kódjának létrehozásához válassza a **Hozzáadás** lehetőséget. Másolja ezt az értéket, mert a panel fürdik után eltűnik. Erre az értékre később szükség lesz a tesztelési meghajtó konfigurálásakor.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Ügyfél titkos kulcsának hozzáadása.":::

5. Adja hozzá az egyszerű szolgáltatásnév szerepkört az alkalmazáshoz, hogy az Azure AD-alkalmazás eltávolítsa a felhasználókat az Azure-bérlőből.
    1. Nyisson meg egy rendszergazdai szintű PowerShell-parancssort.
    2. Install-Module MSOnline (futtassa ezt a parancsot, ha nincs telepítve a MSOnline).
    3. Connect-MsolService (ekkor megjelenik egy előugró ablak, amely az újonnan létrehozott szervezeti Bérlővel jelentkezik be).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5E62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Jelentkezzen be a fiókjába.":::

6. Adja hozzá a fenti létrehozott Azure-alkalmazást alkalmazás-felhasználóként a test Drive CRM-példányhoz.
    1. Új felhasználó hozzáadása **Azure Active Directoryban**. A felhasználó létrehozásához csak a **név** és a **Felhasználónév** érték (ugyanahhoz a bérlőhöz tartozó) szükséges, ezért a többi mezőt hagyja alapértelmezettként. Másolja a username értéket.
    2. Jelentkezzen be a **CRM-példányba** , és válassza a   >  **biztonsági**  >  **felhasználók** beállítása lehetőséget.
    3. Módosítsa a nézetet az **alkalmazás felhasználóira**.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="Felhasználói fiók adatainak beállítása.":::

    4. Új felhasználó hozzáadása (gondoskodjon arról, hogy az űrlap az alkalmazás FELHASZNÁLÓja legyen).
    5. Adja meg ugyanazt a felhasználónevet az **elsődleges e-mail-cím** és a **Felhasználónév** mezőben. Adja hozzá az **Azure-ApplicationId** az alkalmazás- **azonosítóban**.
    6. Adjon meg bármilyen **teljes nevet**.
    7. Kattintson a **Mentés** gombra.
    8. Válassza a **Szerepkörök kezelése** lehetőséget.
    9. Rendeljen olyan egyéni vagy OOB biztonsági szerepkört, amely olvasási, írási és hozzárendelési jogosultságokat, *például rendszergazdát tartalmaz.*

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="Válassza ki a szerepkör jogosultságokat.":::

    10. Emellett engedélyezze az **eljárni egy másik felhasználói jogosultság nevében** .
    11. Rendelje hozzá az alkalmazás felhasználóját a tesztelési meghajtóhoz létrehozott egyéni biztonsági szerepkörhöz.

## <a name="set-up-for-dynamics-365-for-operations"></a>A Dynamics 365 for Operations beállítása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy rendszergazdai fiókkal.
2. Ellenőrizze, hogy a Dynamics 365 Test Drive-példányhoz társított bérlőn található-e a jobb felső sarokban lévő fiók ikonja fölé. Ha nem a megfelelő bérlőben van, válassza a fiók ikont a megfelelő bérlőre való váltáshoz.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="A megfelelő bérlő kiválasztása.":::

3. Hozzon létre egy Azure AD alkalmazás az Azure-ban. A AppSource ezt az alkalmazást fogja használni a tesztelési meghajtó felhasználójának a bérlőben való kiépítéséhez és megszüntetéséhez.
    1. A szűrő ablaktáblán válassza a **Azure Active Directory** lehetőséget.
    2. Válassza az **Alkalmazásregisztrációk** lehetőséget.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="Az alkalmazás regisztrációjának kiválasztása.":::

    3. Válassza az **új regisztráció** lehetőséget.
    4. Adja meg a megfelelő alkalmazásnév-nevet.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Alkalmazás regisztrálása.":::

    5. A támogatott fióktípus területen válassza **a fiók lehetőséget a szervezeti címtárban és a személyes Microsoft-fiókokban**.
    6. Válassza a **Létrehozás** lehetőséget, és várja meg az alkalmazás létrehozását.
    7. Az alkalmazás létrehozása után figyelje meg az **alkalmazás azonosítóját** az áttekintési képernyőn. Erre az értékre később szükség lesz a tesztelési meghajtó konfigurálásakor.
    8. Az **alkalmazás kezelése** területen válassza az **API-engedélyek** lehetőséget.
    9. Válassza **az engedély hozzáadása** , majd a **Microsoft Graph API** elemet.
    10. Válassza ki az **alkalmazás** engedély kategóriát, majd a **könyvtár. Read. All** és **Directory. ReadWrite. All** engedélyeket.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Alkalmazás engedélyeinek beállítása.":::

    11. Válassza az **engedély hozzáadása** lehetőséget.
    12. Az engedély hozzáadása után válassza a **rendszergazdai jóváhagyás megadása a Microsoftnak** lehetőséget.
    13. Az üzenet riasztása területen válassza az **Igen** lehetőséget.

        [![Azt mutatja, hogy az alkalmazás engedélyei sikeresen meg lettek adva.](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Titkos kód létrehozása a Azure AD alkalmazáshoz:
        1. Az **alkalmazás kezelése** lapon válassza a **tanúsítvány és titkos kulcsok** elemet.
        2. Az ügyfél titkos kulcsa területen válassza az **új ügyfél titka** lehetőséget.
        3. Adjon meg egy leírást, például a *Test Drive*-t, és válasszon ki egy megfelelő időtartamot. A teszt meghajtó a kulcs lejárata után megszakad, ekkor új kulcsot kell létrehoznia, és meg kell adnia a AppSource.
        4. Az Azure-alkalmazás titkos kódjának létrehozásához válassza a **Hozzáadás** lehetőséget. Másolja ezt az értéket, mert a panel fürdik után eltűnik. Erre az értékre később szükség lesz a tesztelési meghajtó konfigurálásakor.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Ügyfél titkos kulcsának hozzáadása.":::

4. Adja hozzá az egyszerű szolgáltatásnév szerepkört az alkalmazáshoz, hogy az Azure AD-alkalmazás eltávolítsa a felhasználókat az Azure-bérlőből.
    1. Nyisson meg egy rendszergazdai szintű PowerShell-parancssort.
    2. Install-Module MSOnline (futtassa ezt a parancsot, ha nincs telepítve a MSOnline).
    3. Connect-MsolService (ekkor megjelenik egy előugró ablak, amely az újonnan létrehozott szervezeti Bérlővel jelentkezik be).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5E62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Jelentkezzen be a fiókjába.":::

5. Most adja hozzá a fenti alkalmazást a **Dynamics 365 for Operations szolgáltatáshoz** , amely lehetővé teszi, hogy az alkalmazás kezelje a felhasználókat.
    1. A **Dynamics 365 for Operations** -példány megkeresése.
    2. A bal felső sarokban kattintson a három soros menüre (hamburger).
    3. Válassza a **rendszerfelügyelet** lehetőséget.
    4. Válassza ki **Azure Active Directory alkalmazásokat**.
    5. Válassza a **+ Új** lehetőséget.
    6. Adja meg az **Azure ad-alkalmazás ügyfél-azonosítóját** , amely végre fogja hajtani a műveletet.

    > [!NOTE]
    > Az a felhasználói azonosító, amelynek nevében a műveletek el lesznek hajtva (jellemzően a példány rendszerrendszergazdája vagy olyan felhasználó, aki más felhasználók hozzáadására jogosult).

    [![Az a felhasználói azonosító, amelynek nevében a műveletek el lesznek hajtva (jellemzően a példány rendszerrendszergazdája vagy olyan felhasználó, aki más felhasználók hozzáadására jogosult).](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
