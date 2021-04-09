---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 5bc00f4de95d22eec71f9b1b2504b00f506232dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99214546"
---
### <a name="to-sign-up-for-a-sendgrid-account"></a>SendGrid-fiók regisztrálása
1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A Azure Portal menüben vagy a kezdőlapon válassza az **erőforrás létrehozása** lehetőséget.

    ![Képernyőkép a Azure Portal menüjéről az erőforrás létrehozása lehetőség kiválasztásával.][command-bar-new]
3. Keresse meg és válassza ki a **SendGrid**.

    ![Képernyőkép a Azure Portal Marketplace képernyőről, amely a keresési eredmények között kiválasztott SendGr és SendGrid jeleníti meg a "" elemet.][sendgrid-store]
4. Töltse ki a regisztrációs űrlapot, majd válassza a **Létrehozás** lehetőséget.

    ![Képernyőkép az új SendGrid-fiók létrehozása párbeszédpanelről, a név, a jelszó, az előfizetés és az erőforráscsoport mezők kitöltésével.][sendgrid-create]
5. Adjon meg egy **Nevet** a SendGrid-szolgáltatás azonosításához az Azure beállításaiban. A neveknek 1–100 karakter hosszúságúnak kell lenniük, és kizárólag alfanumerikus karaktereket, kötőjeleket, pontokat és aláhúzásjeleket tartalmazhatnak. A névnek egyedinek kell lennie az előfizetett Azure Store-elemek listájában.
6. Adja meg és erősítse meg a **Jelszót**.
7. Válassza ki az **Előfizetést**.
8. Hozzon létre egy új **Erőforráscsoportot** vagy használjon egy meglévőt.
9. A **Tarifacsomag** szakaszban válassza ki azt a SendGrid-csomagot, amelyre regisztrálni kíván.

    ![Képernyőkép az új SendGrid-fiók létrehozása párbeszédpanel megnyitásáról a díjszabási csomag kiválasztása szakaszban][sendgrid-pricing]
10. Adjon meg egy **Promóciókódot**, ha rendelkezik ilyennel.
11. Adja meg a **kapcsolattartási adatait**.
12. Tekintse át és fogadja el a **Jogi feltételeket**.
13. A vásárlás megerősítését követően egy **központi telepítés sikeres** előugró ablak jelenik meg, és megjelenik a fiókja.

    ![Képernyőkép a SendGrid-fiókok oldalról, amely a felsorolt új fiók ContosoSendGrid mutatja.][all-resources]

    Miután befejezte a vásárlást, valamint a **Kezelés** gombra kattintva elindította az e-mail ellenőrzésének folyamatát, a SendGrid küld egy e-mailt, amelyben a fiókja megerősítésére kéri. Ha nem kapja meg ezt az e-mailt, vagy nem sikerül ellenőrizni a fiókját, tekintse meg a gyakori kérdéseket.

    ![Képernyőkép a ContosoSendGrid-fiók oldaláról, a kezelés gombbal kiemelve.][manage]

    **Fiókja megerősítéséig naponta legfeljebb 100 e-mailt tud elküldeni.**

    Az előfizetési csomag módosításához vagy a SendGrid kapcsolattartási beállításainak megtekintéséhez kattintson a SendGrid-szolgáltatás nevére a SendGrid Marketplace irányítópultjának megnyitásához.

    ![Képernyőfelvétel: a ContosoSendGrid-fiók beállítások lapja a ContosoSendGrid-fiók lapon a minden beállítás lehetőség kiválasztásával nyitható meg.][settings]

    Ha a SendGrid használatával szeretne e-mailt küldeni, meg kell adnia API-kulcsát.

### <a name="to-find-your-sendgrid-api-key"></a>A SendGrid API-kulcsának megkeresése
1. Kattintson a **Kezelés** gombra.

    ![Képernyőkép a ContosoSendGrid-fiók oldaláról, a kezelés gombbal kiemelve.][manage]
2. A SendGrid irányítópultján válassza a **Beállítások**, majd az **API-kulcsok** lehetőséget a bal oldali menüben.

    ![Képernyőfelvétel a SendGrid-irányítópultról, a beállítások legördülő menüből kiválasztva és az API-kulcsok kiválasztásával.][api-keys]

3. Kattintson az **API létrehozása kulcsra**.

    ![Képernyőkép az API-kulcsok képernyőről, az API-kulcs létrehozása gomb kiválasztásával.][general-api-key]
4. Adja meg legalább a **Kulcs nevét**, biztosítson teljes hozzáférést a **Levélküldés** számára, majd válassza a **Mentés** lehetőséget.

    ![Képernyőkép az új általános API-kulcs hozzáadása képernyőről a levelezés küldési készlete teljes hozzáféréshez beállításnál az ütemezett küldés nem fér hozzá, és a Mentés gomb ki van emelve.][access]
5. Ekkor az API egyetlen alkalommal megjelenik. Ügyeljen arra, hogy biztonságosan tárolja.

### <a name="to-find-your-sendgrid-credentials"></a>A SendGrid hitelesítő adatainak megkeresése
1. Kattintson a kulcs ikonra a **Felhasználónév** megkereséséhez.

    ![Képernyőfelvétel a ContosoSendGrid-fiók oldaláról, a kulcs ikon kiemelve.][key]
2. A jelszó megegyezik azzal, amelyet a beállításkor megadott. Módosításokat a **Jelszó módosítása** vagy a **Jelszó alaphelyzetbe állítása** lehetőségek kiválasztásával eszközölhet.

Az e-mailek kézbesítési eredményessége beállításainak kezeléséhez kattintson a **Kezelés gombra**. A rendszer átirányítja a SendGrid-irányítópultra.

![Képernyőkép a ContosoSendGrid-fiók oldaláról, a kezelés gombbal kiemelve.][manage]

Az e-mailek SendGrid-n keresztül történő elküldésével kapcsolatos további információkért tekintse meg az [E-mail API áttekintését][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
