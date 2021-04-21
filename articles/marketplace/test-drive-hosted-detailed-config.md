---
title: Az üzemeltetett teszt meghajtó részletes konfigurációja a Azure Marketplace
description: Magyarázat konfigurációs lépései üzemeltetett teszt meghajtóhoz a kereskedelmi piactéren
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 04/20/2021
ms.openlocfilehash: f11f1d5601a61bbd9b8729b478c278db8d3e73dc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812414"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Az üzemeltetett tesztmeghajtók részletes konfigurációja

Ez a cikk bemutatja, hogyan konfigurálható egy üzemeltetett teszt meghajtó a Dynamics 365 for Customer Engagementhez vagy a Dynamics 365 for Operationshez.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Konfigurálás a Dynamics 365 Customer Engagementhez

1. Jelentkezzen be a [Partnerközpont.](https://partner.microsoft.com/)
2. Ha nem fér hozzá a fenti hivatkozáshoz, [](https://appsource.microsoft.com/partners/list-an-app) itt kell kérelmet benyújtania az alkalmazás közzétételéhez. A kérelem áttekintése után hozzáférést kap a közzétételi folyamat elkezdését.
3. Megkeres egy meglévő **Dynamics 365 for Customer** Engagement-ajánlatot, vagy létrehoz egy új **Dynamics 365 for Customer Engagement-ajánlatot.**
4. Jelölje be **a Teszt meghajtó engedélyezése** jelölőnégyzetet, és válassza ki a **Teszt** meghajtó típusát (lásd alább a felsorolásjelet), majd válassza a **Mentés lehetőséget.**

    [![Jelölje be a "Teszt meghajtó engedélyezése" jelölőnégyzetet.](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **A teszt meghajtó típusa** – Válassza a **Microsoft Hosted (Dynamics 365 for Customer Engagement & PowerApps) lehetőséget.** Ez azt jelzi, hogy a Teszt meghajtót használó felhasználóátépítést és -megszüntetéseket végző szolgáltatást a Microsoft fogja fenntartani és fogja karbantartani.

5. Adjon Microsoft AppSource engedélyt a teszt meghajtó felhasználóinak a bérlőben való kiépítéséhez és megszüntetéséhez a [következő utasítások alapján:](./test-drive-azure-subscription-setup.md). Ebben a lépésben létrehozza az alább Azure AD alkalmazás **azonosítót és Azure AD alkalmazás** kulcsértékeket. 
6. Töltse ki ezeket a mezőket a **Teszt meghajtó műszaki konfigurációja lapon.**

    [![A teszt meghajtó technikai konfigurációs oldala.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Egyidejű tesztmeghajtók** maximális száma – Azon egyidejű felhasználók száma, akik egyidejűleg futnak aktív tesztmeghajtóval. Minden felhasználó dynamics-licencet használ, miközben a teszt meghajtója aktív, ezért győződjön meg arról, hogy legalább ennyi Dynamics-licenc áll rendelkezésre a teszt meghajtó felhasználói számára. 3–5-öt ajánlottunk.
    - **Teszt meghajtó időtartama** – A felhasználó teszt meghajtója által aktív órák száma. Az idő lejárta után a felhasználó megszüntetődik a bérlőből. Az alkalmazás összetettségétől függően 2–24 órát ajánlottunk. A felhasználó bármikor kérhet egy másik teszt meghajtót, ha elfogy az ideje, és újra hozzá szeretne férni a teszt meghajtóhoz.
    - **Példány URL-címe** – A teszt meghajtó felhasználója a teszt meghajtó elindítani kívánt URL-címe. Ez általában annak a Dynamics 365-példánynak az URL-címe, amelyre az alkalmazás és a mintaadatok telepítve vannak. Példaérték: `https://testdrive.crm.dynamics.com` .
    - **Példány webes API URL-címe** – A Dynamics 365-példány webes API-jának URL-címe. Ezt az értéket a Microsoft Dynamics 365-példányba való bejelentkezéssel és a **Testreszabási** fejlesztői erőforrások példányának webes API-ja beállításával, majd a cím (URL) másolásával  >    >    >  **lehet** lekérni. Példaérték: 

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Példa az Instance Web API-ba.":::

    - **Szerepkör neve** – A teszt meghajtóhoz létrehozott egyéni Dynamics 365 biztonsági szerepkör neve, vagy használhat egy meglévő szerepkört. Az új szerepkörhöz hozzá kell adni a minimálisan szükséges jogosultságokat a Customer Engagement-példányba való bejelentkezéshez. Tekintse meg [a Microsoft Dynamics 365-be](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365)való bejelentkezéshez szükséges minimális jogosultságokat. Ez a szerepkör lesz hozzárendelve a felhasználókhoz a teszt meghajtójaik során. Példaérték: `testdriverole` .
    
        > [!IMPORTANT]
        > Győződjön meg arról, hogy a biztonsági csoport ellenőrzése nincs hozzáadva. Ez lehetővé teszi, hogy a felhasználó szinkronizálva legyen a Customer Engagement-példánytal.

    - **Azure Active Directory-bérlő azonosítója** – A Dynamics 365-példány Azure-bérlője azonosítója. Az érték lekéréshez jelentkezzen be a Azure Portal, lépjen a Azure Active Directory   >  **elemre,** és másolja ki a címtár-azonosítót. Példaérték: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory bérlő neve** – A Dynamics 365-példány Azure-bérlője neve. Használja a következő formátumot: `<tenantname>.onmicrosoft.com`. Példaérték: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory application ID** (Alkalmazásazonosító) – Az 5. lépésben Azure Active Directory (AD) alkalmazás azonosítója. Példaérték: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory 5.** lépésben létrehozott Azure AD-alkalmazás titkos ügyféltitkja. Példaérték: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Adja meg a marketplace termékoldalának részleteit. Válassza **a Nyelv** lehetőséget a további kötelező mezőkhöz.

    [![A Marketplace termékoldalának részleteit tartalmazó oldal.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Leírás** – A tesztmeghajtó áttekintése. Ez a szöveg jelenik meg a felhasználó számára a teszt meghajtó kiépítése közben. Ez a mező támogatja a HTML-t, ha formázott tartalmat szeretne biztosítani.
    - **Felhasználói kézikönyv** – A PDF-fájl felhasználói kézikönyve, amely segít a tesztfelhasználóknak megérteni az alkalmazás használatát.
    - **Tesztmeghajtó bemutató videója** – Az alkalmazást bemutató videó (nem kötelező).

## <a name="configure-for-dynamics-365-operations"></a>Konfigurálás a Dynamics 365 Operations szolgáltatáshoz

2. Ha nem fér hozzá a fenti hivatkozáshoz, [](https://appsource.microsoft.com/partners/list-an-app) itt kell kérelmet benyújtania az alkalmazás közzétételéhez. A kérelem áttekintése után hozzáférést kap a közzétételi folyamat elkezdését.
3. Megkeres egy meglévő **Dynamics 365 for Operations-ajánlatot,** vagy létrehoz egy új **Dynamics 365 for Operations-ajánlatot.**
4. Jelölje be **a Teszt meghajtó engedélyezése** jelölőnégyzetet, és válassza ki a **Teszt** meghajtó típusát (lásd alább a felsorolásjelet), majd válassza a **Mentés lehetőséget.**

    [![Jelölje be a "Teszt meghajtó engedélyezése" jelölőnégyzetet.](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Teszt meghajtó típusa** – Válassza a **Dynamics 365 for Operations** lehetőséget. Ez azt jelenti, hogy a Microsoft fogja azt a szolgáltatást üzembe tartani és karbantartani, amely elvégzi a teszt meghajtón található felhasználóátépítést és -megszüntetéseket.

5. Adjon Microsoft AppSource engedélyt a teszt meghajtó felhasználóinak a bérlőben való kiépítéséhez és megszüntetéséhez ezekkel [az utasításokkal.](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md) Ebben a lépésben létre fogja hozni a Azure AD alkalmazás **azonosítót** és Azure AD alkalmazás **alább** említett kulcsértékeket.
6. Töltse ki ezeket a mezőket a **Teszt meghajtó műszaki konfigurációja lapon.**

    [![A Marketplace műszaki konfigurációs oldala.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Egyidejű tesztmeghajtók** maximális száma – Azon egyidejű felhasználók száma, akik aktív tesztmeghajtóval futnak egyidejűleg. Minden felhasználó dynamics-licencet használ, miközben a teszt meghajtója aktív, ezért győződjön meg arról, hogy legalább ennyi Dynamics-licenc áll rendelkezésre a teszt meghajtó felhasználói számára. 3–5-öt ajánlottunk.
    - **Teszt meghajtó időtartama** – A felhasználó teszt meghajtója által aktív órák száma. Az idő lejárta után a felhasználó megszüntetődik a bérlőből. Az alkalmazás összetettségétől függően 2–24 órát ajánlottunk. A felhasználó bármikor kérhet egy másik teszt meghajtót, ha elfogy az ideje, és újra hozzá szeretne férni a teszt meghajtóhoz.
    - **Példány URL-címe** – A teszt meghajtó felhasználója a teszt meghajtó elindítani kívánt URL-címe. Ez általában annak a Dynamics 365-példánynak az URL-címe, amelyre az alkalmazás és a mintaadatok telepítve vannak. Példaérték: `https://testdrive.crm.dynamics.com` .
    - **Azure Active Directory-bérlő azonosítója** – A Dynamics 365-példány Azure-bérlője azonosítója. Az érték lekéréshez jelentkezzen be a Azure Portal, lépjen a Azure Active Directory   >  **elemre,** és másolja ki a címtár-azonosítót. Példaérték: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory neve** – A Dynamics 365-példány Azure-bérlője neve. Használja a következő formátumot: `<tenantname>.onmicrosoft.com`. Példaérték: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory application ID** (Alkalmazásazonosító) – Az 5. lépésben Azure Active Directory (AD) alkalmazás azonosítója. Példaérték: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory 5.** lépésben létrehozott Azure AD-alkalmazás titkos ügyféltitkja. Példaérték: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Próbaverziós jogi személy** – Adjon meg egy jogi személyt a próbafelhasználó hozzárendelése érdekében. Újat a Jogi személy létrehozása vagy módosítása [között hozhat létre.](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)
    - **Szerepkör neve** – A teszt meghajtóhoz létrehozott egyéni Dynamics 365 biztonsági szerepkör AOT-neve (alkalmazásobjektum-fa). Ez a szerepkör lesz hozzárendelve a felhasználókhoz a teszt meghajtójukon.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="A biztonsági konfigurációs oldal.":::

7. Adja meg a marketplace termékoldalának részleteit. Válassza **a Nyelv** lehetőséget a további kötelező mezőkhöz.

    [![A Marketplace termékoldalának részleteit tartalmazó oldal.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Leírás** – A tesztmeghajtó áttekintése. Ez a szöveg jelenik meg a felhasználó számára a teszt meghajtó kiépítése közben. Ez a mező támogatja a HTML-t, ha formázott tartalmat szeretne biztosítani.
    - **Felhasználói kézikönyv** – A PDF-fájl felhasználói kézikönyve, amely segít a tesztfelhasználóknak megérteni az alkalmazás használatát.
    - **Tesztmeghajtó bemutató videója** – Az alkalmazást bemutató videó (nem kötelező).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->