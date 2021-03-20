---
title: Vezető felügyelet a Dynamics 365 Customer engagement-Microsoft kereskedelmi piactéren
description: Ismerje meg, hogyan állíthatja be a Dynamics 365-ügyfelek részvételét a Microsoft AppSource és az Azure Marketplace-en található érdeklődők kezeléséhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: d996a2e0732ad9b4da104265710970e04cdbd368
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98199774"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Az érdeklődők felügyeletének konfigurálása a Dynamics 365 Customer engagement szolgáltatáshoz

Ez a cikk bemutatja, hogyan állíthatja be a Dynamics 365 Customer Engagementt (korábban Dynamics CRM Online néven). További információ a [kiszolgáló alapú hitelesítés konfigurálása a Customer engagement és a SharePoint Online szolgáltatásban](/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) című cikkből a kereskedelmi Piactéri ajánlatból származó értékesítési érdeklődők feldolgozásához.

>[!NOTE]
>Ezek az utasítások a Microsoft által üzemeltetett felhőalapú környezetre vonatkoznak a Dynamics 365 Customer engagement esetében. A közvetlenül a Dynamics helyszíni környezethez való csatlakozás jelenleg nem támogatott. Más lehetőségek is rendelkezésre állnak az érdeklődők fogadására, például egy [https-végpont](./commercial-marketplace-lead-management-instructions-https.md) vagy egy [Azure-tábla](./commercial-marketplace-lead-management-instructions-azure-table.md)konfigurálására.

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek végrehajtásához a következő felhasználói engedélyek szükségesek:

* A Dynamics 365 Customer engagement-példány rendszergazdai jogosultságai a megoldás telepítéséhez.
* Bérlői rendszergazdai jogosultságok új szolgáltatásfiók létrehozásához az érdeklődők számára a kereskedelmi Piactéri ajánlatok által küldött érdeklődők számára.
* Hozzáférés a felügyeleti portálhoz.
* Hozzáférés a Azure Portalhoz.

## <a name="install-the-solution"></a>A megoldás telepítése

1. Töltse le a [Microsoft Marketplace vezető író megoldását](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip), és mentse helyileg a számítógépre.

1. Nyissa meg a Dynamics 365 Customer Engagementt a Dynamics-példány URL-címére, például: `https://tenant.crm.dynamics.com` .

1. Válassza a fogaskerék ikont a felső sávon, majd válassza a **Speciális beállítások** lehetőséget.

    ![Dynamics 365 speciális beállítások menüelem](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. A **Beállítások** lapon nyissa meg a felső sávon található **Beállítások** menüt, és válassza a **megoldások** elemet.

    >[!NOTE]
    >Ha nem látja a beállításokat a következő képernyőn, akkor nem rendelkezik a folytatáshoz szükséges engedélyekkel. Forduljon a Dynamics 365 Customer engagement-példány rendszergazdájához.

    ![Dynamics 365-megoldások lehetőség](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. A **megoldások** lapon válassza az **Importálás** lehetőséget, és lépjen arra a helyre, ahová az 1. lépésben letöltött **Microsoft Marketplace vezető író** megoldást mentette.

    ![Importálás gomb](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Fejezze be a megoldás importálását a megoldás importálása varázsló segítségével.

## <a name="configure-user-permissions"></a>Felhasználói engedélyek konfigurálása

Ha érdeklődőket szeretne írni a Dynamics 365 Customer engagement-példányba, meg kell osztania egy szolgáltatásfiókot a Microsofttal, és konfigurálnia kell a fiókhoz tartozó engedélyeket.

A szolgáltatásfiók létrehozásához és az engedélyek hozzárendeléséhez kövesse az alábbi lépéseket. Azure Active Directory vagy Office 365-et is használhat.

>[!NOTE]
>Ugorjon a megfelelő utasításokra a kiválasztott hitelesítési lehetőség alapján. Lásd: [Azure Active Directory](#azure-active-directory) vagy [Office 365](#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Ezt a beállítást javasoljuk, mert soha nem kell frissítenie a felhasználónevet vagy a jelszót az érdeklődők beszerzéséhez. A Azure Active Directory beállítás használatához meg kell adnia az alkalmazás AZONOSÍTÓját, az alkalmazás kulcsát és a könyvtár AZONOSÍTÓját a Active Directory alkalmazásból.

Azure Active Directory konfigurálása a Dynamics 365 Customer engagement szolgáltatáshoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

1. Válassza a **Tulajdonságok** lehetőséget, és másolja a **címtár-azonosító** értékét a **könyvtár tulajdonságok** lapján. Mentse ezt az értéket, mert a közzétételi portálon meg kell adnia a Piactéri ajánlathoz tartozó érdeklődőket.

    ![Azure Active Directory tulajdonságok menüelem](media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Válassza a **Alkalmazásregisztrációk** lehetőséget a Azure Active Directory bal oldali ablaktáblán, majd válassza az **új regisztráció** lehetőséget az oldalon.
1. Adjon meg egy értelmes nevet az alkalmazás neveként.
1. A **támogatott fiókok típusai** területen válassza **a fiókok lehetőséget bármely szervezeti címtárban**.
1. Az **átirányítási URI (nem kötelező)** területen válassza a **web** lehetőséget, és adjon meg egy URI-t, például: `https://contosoapp1/auth` .
1. Válassza a **Regisztráció** lehetőséget.

    ![Alkalmazás-oldal regisztrálása](media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Most, hogy regisztrálta az alkalmazást, nyissa meg az alkalmazás áttekintés lapját. Másolja az **alkalmazás (ügyfél) azonosító** értékét az adott oldalon. Mentse ezt az értéket, mert a közzétételi Portálon és a Dynamics 365-ben meg kell adnia a Piactéri ajánlathoz tartozó érdeklődőket.

    ![Alkalmazás-(ügyfél-) azonosító mező](media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Válassza ki a **tanúsítványok & Secrets** elemet az alkalmazás bal oldali paneljén, és válassza az **új ügyfél titka** gombot. Adjon meg egy értelmes leírást az ügyfél titkos kódjához, és válassza a **lejárat** alatt lévő **soha** lehetőséget. A **Hozzáadás** gombra kattintva hozza létre az ügyfél titkos kulcsát.

    ![Tanúsítványok & Secrets menüpont](media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Az ügyfél titkos kulcsának sikeres létrehozása után másolja ki az **ügyfél titkos** értékét. Az értéket nem fogja tudni lekérni az oldal elhagyását követően. Mentse ezt az értéket, mert a közzétételi portálon meg kell adnia a Piactéri ajánlathoz tartozó érdeklődőket. 
1. Válassza az **API-engedélyek** lehetőséget az alkalmazás bal oldali paneljén, majd válassza az **+ engedély hozzáadása** lehetőséget.
1. Válassza a **Microsoft API**-k lehetőséget, majd válassza a **Dynamics CRM** lehetőséget API-ként.
1. **Milyen típusú engedélyek szükségesek az alkalmazáshoz?**, győződjön meg arról, hogy a **delegált engedélyek** van kiválasztva.
1. Az **engedély** területen jelölje be a **user_impersonation** jelölőnégyzetet a **hozzáférés Common Data Service szervezeti felhasználóként**. Ezután válassza az **engedélyek hozzáadása** lehetőséget.

    ![Engedélyek hozzáadása gomb](media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Miután elvégezte az 1 – 14. lépést a Azure Portalban, lépjen a Dynamics 365 Customer engagement-példányra az URL-címre, például: `https://tenant.crm.dynamics.com` .
1. Válassza a fogaskerék ikont a felső sávon, majd válassza a **Speciális beállítások** lehetőséget.
1. A **Beállítások** lapon nyissa meg a felső sávon található **Beállítások** menüt, és válassza a **Biztonság** elemet.
1. A **Biztonság** lapon válassza a **felhasználók** lehetőséget. A **felhasználók** lapon jelölje be az **engedélyezett felhasználók** legördülő listát, majd válassza az **alkalmazás felhasználói** lehetőséget.
1. Új felhasználó létrehozásához válassza az **új** lehetőséget.

    ![Új felhasználó létrehozása](media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. Az **új felhasználó** ablaktáblán győződjön meg arról, hogy a **felhasználó: alkalmazás felhasználó** van kiválasztva. Adja meg annak a felhasználónak a felhasználónevét, teljes nevét és e-mail-címét, amelyet ezzel a kapcsolatban szeretne használni. Illessze be az **alkalmazás azonosítóját** is a 8. lépésben a Azure Portalban létrehozott alkalmazáshoz. A felhasználó hozzáadásának befejezéséhez válassza a **mentés & Bezárás** gombra.

    ![Új felhasználó ablaktábla](media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. A jelen cikkben a "biztonsági beállítások" című szakaszban találhatja meg a felhasználóhoz való kapcsolódás konfigurálásának befejezését.

### <a name="office-365"></a>Office 365

Ha nem kívánja használni a Azure Active Directoryt, regisztrálhat egy új felhasználót a Microsoft 365 felügyeleti központban. Az érdeklődők folytatásához 90 naponta frissítenie kell a felhasználónevét és a jelszavát.

Az Office 365 for Dynamics 365 ügyfél-engagement konfigurálása:

1. Jelentkezzen be a [Microsoft 365 Felügyeleti központba](https://admin.microsoft.com).

1. Válassza **a felhasználó hozzáadása** elemet.

    ![Microsoft 365 felügyeleti központ felhasználói lehetőség hozzáadása](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Hozzon létre egy új felhasználót az ólom-író szolgáltatáshoz. Adja meg az alábbi beállításokat:

    * Adjon meg egy felhasználónevet.
    * Adja meg a jelszót, és törölje a jelet az **első bejelentkezéskor a felhasználó módosítása a jelszavuk módosítására** jelölőnégyzetből.
    * Válassza a **felhasználó (nincs rendszergazdai hozzáférés)** elemet a felhasználó szerepköre.
    * Az alábbi képernyőn látható módon válassza ki a **Dynamics 365 Customer engagement-tervet** , amely a termék licence. A választott licencért díjat számítunk fel. 

Mentse ezeket az értékeket, mert a közzétételi portálon meg kell adnia a **felhasználónevet** és a **jelszót** , hogy fogadja az érdeklődőket a Piactéri ajánlathoz.

![Microsoft 365 felügyeleti központ új felhasználójának panelje](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Biztonsági beállítások

Az utolsó lépés az, hogy lehetővé teszi a létrehozott felhasználó számára az érdeklődők írását.

1. Nyissa meg a Dynamics 365 Customer Engagementt a Dynamics-példány URL-címére, például: `https://tenant.crm.dynamics.com` .
1. Válassza a fogaskerék ikont a felső sávon, majd válassza a **Speciális beállítások** lehetőséget.
1. A **Beállítások** lapon nyissa meg a felső sávon található **Beállítások** menüt, és válassza a **Biztonság** elemet.
1. A **Biztonság** lapon válassza a **felhasználók** lehetőséget, majd válassza ki a jelen dokumentum "felhasználói engedélyek konfigurálása" szakaszában létrehozott felhasználót. Ezután válassza a **Szerepkörök kezelése** lehetőséget.

    ![Szerepkörök kezelése lap](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Keresse meg a szerepkör nevét **Microsoft Marketplace érdeklődő író** elemnél, és válassza ki a szerepkört a felhasználó hozzárendeléséhez.

    ![Felhasználói szerepkörök panel kezelése](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Ezt a szerepkört az importált megoldás hozza létre, és csak a megfelelő jogosultsággal rendelkezik az érdeklődők írásához és a megoldás verziójának nyomon követéséhez, hogy biztosítsa a kompatibilitást.

1. Lépjen vissza a **Biztonság** lapra, és válassza a **biztonsági szerepkörök** elemet. Keresse meg a szerepkör **Microsoft Marketplace érdeklődő-író** elemet, majd válassza ki.

    ![Biztonsági szerepkörök panel](media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. A biztonsági szerepkörben válassza az **alapvető rekordok** lapot. Keresse meg a **felhasználói entitás felhasználói felületének beállításai** elemet. A megfelelő választógombok kiválasztásával engedélyezheti az entitás létrehozási, olvasási és írási engedélyeit a felhasználónak (1/4 sárga kör).

    ![Microsoft Marketplace vezető Writer Core Records lap](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. A **Testreszabás** lapon keressen rá a **rendszerfeladat** elemre. A megfelelő választógombok kiválasztásával engedélyezheti az olvasási, írási és AppendTo engedélyeket a szervezet (Solid Green Radio Buttons) számára az entitás számára.

    ![Microsoft Marketplace vezető író testreszabása lap](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Válassza a **Mentés és bezárás** lehetőséget.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Az ajánlat beállítása a Dynamics 365 Customer engagement felé vezető érdeklődők küldéséhez

Az ajánlathoz tartozó érdeklődő-felügyeleti információk konfigurálása a közzétételi portálon:

1. Nyissa meg az ajánlat **telepítési** lapját.
1. Az **ügyfél-érdeklődők** szakaszban válassza a **kapcsolat** lehetőséget.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="Ügyfél-érdeklődők":::

1. A kapcsolat részletei előugró ablakban válassza ki a **Dynamics 365 Customer engagement** elemet a vezető célhoz.

    ![Érdeklődői cél mező](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Adja meg a Dynamics 365 **-példány URL-címét** , például: `https://contoso.crm4.dynamics.com` .

1. Válassza ki a **hitelesítési** módszert (Azure Active Directory vagy Office 365).
1. Ha a **Azure Active Directory** lehetőséget választotta, adja meg az **alkalmazás (ügyfél) azonosítóját** (például `23456052-aaaa-bbbb-8662-1234df56788f` ), a **könyvtár azonosítóját** (például) `12345678-8af1-4asf-1234-12234d01db47` , és az **ügyfél titkos kulcsát** (például: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=` ).

    ![Hitelesítés Azure Active Directory kiválasztva](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. Ha az **Office 365** lehetőséget választotta, adja meg a **felhasználónevet** (például `contoso@contoso.onmicrosoft.com` ) és a **jelszót** (például `P@ssw0rd` ).

    ![Office 365 Felhasználónév mező](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. A **kapcsolattartási e-mail** cím mezőbe írja be az e-mail-címeket a vállalat azon tagjai számára, akik új érdeklődő fogadásakor e-mail-értesítéseket kapnak. A pontosvesszővel elválasztva több e-mail-címet is megadhat.
1. Válassza az **OK** lehetőséget.

Az **Érvényesítés** gombra kattintva győződjön meg arról, hogy sikeresen csatlakozott egy érdeklődői célhoz. Ha a művelet sikeres, a vezető célhelyen egy teszt vezet.

![Kapcsolattartási e-mail mező](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt.
