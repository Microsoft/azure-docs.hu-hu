---
title: Az Azure piactéren üzemeltetett tesztvezetés részletes konfigurációja
description: A kereskedelmi piactéren üzemeltetett tesztelési meghajtóval kapcsolatos magyarázat konfigurációs lépései
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/06/2020
ms.openlocfilehash: 88779f67a2fa9b18f0177a1459b32c672343bb57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462944"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Az üzemeltetett tesztelési meghajtók részletes konfigurációja

Ez a cikk azt ismerteti, hogyan konfigurálható a Dynamics 365-hez az ügyfelek engagement vagy a Dynamics 365 for Operations szolgáltatásban üzemeltetett tesztelési meghajtó.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Konfigurálás a Dynamics 365 Customer engagement szolgáltatáshoz

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/).
2. Ha nem fér hozzá a fenti hivatkozáshoz, [el kell küldenie egy kérelmet az](https://appsource.microsoft.com/partners/list-an-app) alkalmazás közzétételéhez. Ha áttekintjük a kérést, a rendszer hozzáférést kap a közzétételi folyamat elindításához.
3. Keressen egy meglévő **dynamics 365-et az ügyfél-engagement** ajánlathoz, vagy hozzon létre egy új Dynamics 365-t a **Customer engagement** ajánlathoz.
4. Jelölje be a **tesztvezetés engedélyezése** jelölőnégyzetet, és válassza ki a **tesztvezetés típusát** (lásd az alábbi felsorolást), majd kattintson a **Mentés** gombra.

    [![Jelölje be a "Test Drive engedélyezése" jelölőnégyzetet.](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **A tesztvezetés típusa** – válassza **a Microsoft által üzemeltetett (Dynamics 365 for Customer engagement & PowerApps)** lehetőséget. Ez azt jelzi, hogy a Microsoft fogja üzemeltetni és karbantartani a szolgáltatást, amely végrehajtja a tesztelési meghajtó felhasználó általi üzembe helyezését és megszüntetését.

5. Adja meg Microsoft AppSource engedélyt a tesztoldal-felhasználók kiépítéséhez és kiépítéséhez a bérlőn [ezen utasítások](./test-drive-azure-subscription-setup.md)használatával. Ebben a lépésben a **Azure ad alkalmazás azonosítóját** és a **Azure ad alkalmazás legfontosabb** értékeket fogja előállítani.
6. Hajtsa végre ezeket a mezőket a **Test Drive technikai konfiguráció** lapján.

    [![A test Drive technikai konfigurációjának lapja.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - Egyidejű **tesztelési meghajtók maximális** száma – azon egyidejű felhasználók száma, akik egyszerre futnak aktív tesztelési meghajtóval. Minden felhasználó felhasznál egy Dynamics-licencet, amíg a tesztvezetés aktív, ezért győződjön meg arról, hogy legalább ennyi Dynamics-licenc áll rendelkezésre a tesztvezetés felhasználói számára. Ajánlott 3 – 5.
    - **Tesztvezetés időtartama** – az a szám, ameddig a felhasználó tesztelési meghajtója aktív lesz. Az idő lejárta után a felhasználó kikerül a bérlőtől. Az alkalmazás összetettsége alapján 2-24 óra ajánlott. A felhasználó mindig kérhet egy másik tesztvezetés, ha elfogynak az idő, és újra el szeretné érni a teszt meghajtót.
    - **Példány URL** -címe – azt az URL-címet, amelyet a rendszer a test Drive-felhasználó számára küld a tesztvezetés indításakor. Ez általában annak a Dynamics 365-példánynak az URL-címe, amelyen az alkalmazás és a mintaadatok telepítve vannak. Példa értéke: `https://testdrive.crm.dynamics.com` .
    - **Példány webes API URL-címe** – a Dynamics 365-példány webes API URL-címe. Ha bejelentkezik a Microsoft Dynamics 365-példányba, jelentkezzen be, és navigáljon a  >  **Testreszabás**  >  **fejlesztői erőforrások**  >  **példányának webes API** beállításához, és másolja a címet (URL). Példaérték: 

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Példa a példány webes API-ra.":::

    - **Szerepkör neve** – a tesztelési meghajtóhoz létrehozott egyéni Dynamics 365 biztonsági szerepkör neve, vagy használhat meglévő szerepkört. Egy új szerepkörhöz a szerepkörhöz hozzá kell adni a minimálisan szükséges jogosultságokat, hogy bejelentkezzenek egy ügyfél engagement-példányba. Tekintse meg a [Microsoft Dynamics 365-be való bejelentkezéshez szükséges minimális jogosultságokat](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). Ez a szerepkör lesz hozzárendelve a felhasználókhoz a tesztelési meghajtón. Példa értéke: `testdriverole` .
    
        > [!IMPORTANT]
        > Győződjön meg arról, hogy a biztonsági csoport ellenőrzése nincs hozzáadva. Ez lehetővé teszi, hogy a felhasználó szinkronizálva legyen a Customer engagement-példánnyal.

    - **Azure Active Directory bérlő azonosítója** – a Dynamics 365-példány Azure-bérlőjának azonosítója. Az érték beolvasásához jelentkezzen be Azure Portal, és navigáljon a **Azure Active Directory**  >  **Tulajdonságok** elemre, és másolja a címtár-azonosítót. Példa értéke: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory bérlő neve** – a Dynamics 365-példány Azure-bérlőjának neve. Használja a következő formátumot: `<tenantname>.onmicrosoft.com`. Példa értéke: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory alkalmazás azonosítója** – az 5. lépésben létrehozott Azure Active Directory (ad) alkalmazás azonosítója. Példa értéke: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory alkalmazás-ügyfél titkos kulcsa** – az 5. lépésben létrehozott Azure ad-alkalmazás titka. Példa értéke: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Adja meg a Marketplace-lista részleteit. Válassza a **nyelv** lehetőséget a további kötelező mezők megjelenítéséhez.

    [![A piactér listázásának részletei lap.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Leírás** – a tesztelési meghajtó áttekintése. Ez a szöveg jelenik meg a felhasználó számára a tesztelési meghajtó üzembe helyezésekor. Ez a mező támogatja a HTML-t, ha formázott tartalmat szeretne megadni.
    - **Felhasználói kézikönyv** – egy PDF felhasználói kézikönyv, amely segít az alkalmazás használatának megértésében.
    - A **Test Drive bemutató videója** – az alkalmazást bemutató videó (opcionális).

## <a name="configure-for-dynamics-365-operations"></a>Konfigurálás Dynamics 365-műveletekhez

2. Ha nem fér hozzá a fenti hivatkozáshoz, [el kell küldenie egy kérelmet az](https://appsource.microsoft.com/partners/list-an-app) alkalmazás közzétételéhez. Ha áttekintjük a kérést, a rendszer hozzáférést kap a közzétételi folyamat elindításához.
3. Keressen egy meglévő **dynamics 365 for Operations** ajánlatot, vagy hozzon létre egy új **Dynamics 365 for Operations** ajánlatot.
4. Jelölje be a **tesztvezetés engedélyezése** jelölőnégyzetet, és válassza ki a **tesztvezetés típusát** (lásd az alábbi felsorolást), majd kattintson a **Mentés** gombra.

    [![Jelölje be a "teszt meghajtó engedélyezése" jelölőnégyzetet.](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **A test Drive típusa** – válassza **a Dynamics 365 for Operations** lehetőséget. Ez azt jelenti, hogy a Microsoft fogja üzemeltetni és karbantartani a szolgáltatást, amely végrehajtja a tesztelési meghajtó felhasználó általi üzembe helyezését és megszüntetését.

5. Adja meg Microsoft AppSource engedélyt a tesztoldal-felhasználók kiépítéséhez és kiépítéséhez a bérlőn [ezen utasítások](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)használatával. Ebben a lépésben a **Azure ad alkalmazás azonosítóját** és a **Azure ad alkalmazás legfontosabb** értékeket fogja előállítani.
6. Hajtsa végre ezeket a mezőket a **Test Drive technikai konfiguráció** lapján.

    [![A piactér technikai konfigurációjának lapja.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - Egyidejű **tesztelési meghajtók maximális** száma – azon egyidejű felhasználók száma, akik egyszerre futnak aktív tesztelési meghajtóval. Minden felhasználó felhasznál egy Dynamics-licencet, amíg a tesztvezetés aktív, ezért győződjön meg arról, hogy legalább ennyi Dynamics-licenc áll rendelkezésre a tesztvezetés felhasználói számára. Ajánlott 3 – 5.
    - **Tesztvezetés időtartama** – az a szám, ameddig a felhasználó tesztelési meghajtója aktív lesz. Az idő lejárta után a felhasználó kikerül a bérlőtől. Az alkalmazás összetettsége alapján 2-24 óra ajánlott. A felhasználó mindig kérhet egy másik tesztvezetés, ha elfogynak az idő, és újra el szeretné érni a teszt meghajtót.
    - **Példány URL** -címe – azt az URL-címet, amelyet a rendszer a test Drive-felhasználó számára küld a tesztvezetés indításakor. Ez általában annak a Dynamics 365-példánynak az URL-címe, amelyen az alkalmazás és a mintaadatok telepítve vannak. Példa értéke: `https://testdrive.crm.dynamics.com` .
    - **Azure Active Directory bérlő azonosítója** – a Dynamics 365-példány Azure-bérlőjának azonosítója. Az érték beolvasásához jelentkezzen be Azure Portal, és navigáljon a **Azure Active Directory**  >  **Tulajdonságok** elemre, és másolja a címtár-azonosítót. Példa értéke: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory bérlő neve** – a Dynamics 365-példány Azure-bérlőjának neve. Használja a következő formátumot: `<tenantname>.onmicrosoft.com`. Példa értéke: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory alkalmazás azonosítója** – az 5. lépésben létrehozott Azure Active Directory (ad) alkalmazás azonosítója. Példa értéke: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory alkalmazás-ügyfél titkos kulcsa** – az 5. lépésben létrehozott Azure ad-alkalmazás titka. Példa értéke: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Próbaverziós jogi személy** – adjon meg egy jogi személyt a próbaverziós felhasználó hozzárendeléséhez. Létrehozhat egy újat egy [jogi entitás létrehozásakor vagy módosításakor](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity).
    - **Szerepkör neve** – a tesztelési meghajtóhoz létrehozott egyéni Dynamics 365 biztonsági szerepkör AOT-beli neve (Application Object Tree). Ez a szerepkör lesz hozzárendelve a felhasználókhoz a tesztelési meghajtón.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="A biztonsági konfiguráció lap.":::

7. Adja meg a Marketplace-lista részleteit. Válassza a **nyelv** lehetőséget a további kötelező mezők megjelenítéséhez.

    [![A piactér listázásának részletei lap.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Leírás** – a tesztelési meghajtó áttekintése. Ez a szöveg jelenik meg a felhasználó számára a tesztelési meghajtó üzembe helyezésekor. Ez a mező támogatja a HTML-t, ha formázott tartalmat szeretne megadni.
    - **Felhasználói kézikönyv** – egy PDF felhasználói kézikönyv, amely segít az alkalmazás használatának megértésében.
    - A **Test Drive bemutató videója** – az alkalmazást bemutató videó (opcionális).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->