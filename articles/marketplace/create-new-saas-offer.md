---
title: SaaS-ajánlat létrehozása a Microsoft kereskedelmi piactéren
description: Megtudhatja, hogyan hozhat létre az Microsoft AppSource, az Azure Marketplace vagy a Cloud Solution Provider (CSP) program keretében a Microsoft partner Center kereskedelmi piactér portáljának használatával új, szolgáltatásként nyújtott szoftvert (SaaS).
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 74d30b7c42002c8f134520e0198774eba1519bcd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553838"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>SaaS-ajánlat létrehozása a kereskedelmi piactéren

Kereskedelmi Piactéri közzétevőként létrehozhat egy szolgáltatott szoftvert (SaaS), így a potenciális ügyfelek megvásárolhatják SaaS-alapú technikai megoldásait. Ez a cikk bemutatja, hogyan hozhat létre SaaS-ajánlatot a Microsoft kereskedelmi Piactérről.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem tette meg, olvassa el [a következőt: Saas-ajánlat megtervezése a kereskedelmi piactéren](plan-saas-offer.md). Ismerteti a SaaS-alkalmazás technikai követelményeit, valamint az ajánlat létrehozásakor szükséges információkat és eszközöket. Hacsak nem tervezi egy egyszerű lista közzétételét (**Kapcsolatfelvétel** a listában) a kereskedelmi piactéren, az SaaS-alkalmazásnak meg kell felelnie a hitelesítés technikai követelményeinek.

> [!IMPORTANT]
> Javasoljuk, hogy hozzon létre egy külön fejlesztési/tesztelési (DEV) ajánlatot és egy külön éles üzemi (Production) ajánlatot. Ez a cikk azt ismerteti, hogyan hozható létre GYÁRTÁSIRENDELÉS-ajánlat. A fejlesztői ajánlat létrehozásával kapcsolatos részletekért tekintse meg [a fejlesztési és tesztelési ajánlat létrehozása](create-saas-dev-test-offer.md)című témakört.

## <a name="create-a-new-saas-offer"></a>Új SaaS-ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
1. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés** lehetőséget.
1. Az **Áttekintés** lapon válassza az **+ új ajánlat**  >  **szoftver szolgáltatásként** lehetőséget.

   :::image type="content" source="media/new-offer-saas.png" alt-text="A bal oldali navigációs menü és az új ajánlatok listájának ábrázolása.":::

1. Az **új ajánlat** párbeszédpanelen adja meg az **ajánlat azonosítóját**. Ez az azonosító a kereskedelmi piactér-lista és a Azure Resource Manager-sablonok URL-címében látható, ha van ilyen. Ha például beírja a **test-Offer-1** értéket ebben a mezőben, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
   + A fiók minden ajánlatának egyedi ajánlat-AZONOSÍTÓval kell rendelkeznie.
   + Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet.
   + Az ajánlat azonosítója a **Létrehozás** gombra kattintva nem módosítható.

1. Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

   + Ez a név nem látható a kereskedelmi piactéren, és nem egyezik meg az ajánlat nevével és más, az ügyfelek számára megjelenített értékekkel.
   + Az ajánlat aliasa nem módosítható a **Létrehozás** gombra kattintva.
1. Az ajánlat létrehozásához és a folytatáshoz válassza a **Létrehozás** lehetőséget.

## <a name="configure-your-saas-offer-setup-details"></a>SaaS-ajánlat telepítési részleteinek konfigurálása

Az **ajánlat beállítása** lap **telepítés részletei** területén kiválaszthatja, hogy az ajánlatot a Microsofton keresztül szeretné-e eladni, vagy egymástól függetlenül kezelheti a tranzakciókat. A Microsoft által eladott ajánlatokat a rendszer felkínálja, mint a _visszagörgethető ajánlatok_, ami azt jelenti, hogy a Microsoft a kiadó nevében lehetővé teszi a szoftveres licencek cseréjét. További információ ezekről a lehetőségekről: [listázási beállítások](plan-saas-offer.md#listing-options) és [a közzétételi lehetőség meghatározása](determine-your-listing-type.md).

1. A Microsofton keresztüli értékesítéshez és a tranzakciók megkönnyítése érdekében válassza az **Igen** lehetőséget. Folytassa a [tesztelési meghajtó engedélyezésével](#enable-a-test-drive-optional).

1. Ha az ajánlatot a kereskedelmi piactéren szeretné listázni, és a tranzakciókat egymástól függetlenül dolgozza fel, válassza a **nem** lehetőséget, majd tegye a következők egyikét:
   + Ha ingyenes előfizetést szeretne biztosítani az ajánlatához, válassza a **Letöltés most (ingyenes)** lehetőséget. Ezután a megjelenő **ajánlat URL-címe** mezőbe írja be az URL-címet (a *http* vagy a *https* verziótól kezdődően), ahol az ügyfelek a [Azure Active Directory (Azure ad) használatával](azure-ad-saas.md)kérhetnek le egy próbaverziót az egykattintásos hitelesítéshez. Például: `https://contoso.com/saas-app`.
   + 30 napos ingyenes próbaverzió biztosításához válassza az **ingyenes próbaverzió** lehetőséget, majd a megjelenő **próbaverzió URL-címe** mezőbe írja be a megjelenő URL-címet (a *http* vagy a *https* verziótól kezdődően), ahol az ügyfelek hozzáférhetnek az ingyenes próbaidőszakhoz az [Azure Active Directory (Azure ad) használatával egy kattintással](azure-ad-saas.md). Például: `https://contoso.com/trial/saas-app`.
   + Ha szeretné, hogy a potenciális ügyfelek kapcsolatba lépjenek az ajánlat megvásárlásához, válassza a **Kapcsolatfelvétel** lehetőséget.

## <a name="enable-a-test-drive-optional"></a>Tesztelési meghajtó engedélyezése (nem kötelező)

A test Drive nagyszerű lehetőséget nyújt arra, hogy az ajánlatot a lehetséges ügyfelek számára is bemutassa, ha egy előre konfigurált környezethez hozzáférést ad nekik egy rögzített számú órán keresztül. A tesztelési meghajtó felajánlása nagyobb konverziós arányt eredményez, és nagy mértékben minősített érdeklődőket generál. További információ a tesztelési meghajtókról: [Mi az a test Drive?](./what-is-test-drive.md).

> [!TIP]
> A tesztelési meghajtó nem azonos az ingyenes próbaverzióval. Akár tesztelési meghajtót, ingyenes próbaverziót vagy mindkettőt is használhat. Mind a megoldás egy meghatározott időszakra biztosítanak ügyfeleinek. A test Drive azonban magában foglalja a termék legfontosabb funkcióit és előnyeit is, amelyek egy valós megvalósítási forgatókönyvben jelennek meg.

**Tesztelési meghajtó engedélyezése**
1.  A **tesztelési meghajtó** területen jelölje be a **tesztvezetés engedélyezése** jelölőnégyzetet.
1.  Válassza ki a tesztoldal típusát a megjelenő listából.

## <a name="configure-lead-management"></a>Érdeklődők kezelésének konfigurálása

Az Ügyfélkapcsolat-kezelési (CRM) rendszer a kereskedelmi piactérsel való összekapcsolásával biztosítható, hogy az ügyfél kapcsolattartási adatait, amikor az ügyfél kifejezi érdeklődését vagy üzembe helyezi a terméket. Ezt a csatlakozást bármikor módosíthatja az ajánlat létrehozásakor vagy után.

> [!NOTE]
> Az érdeklődők felügyeletét akkor kell konfigurálnia, ha az ajánlatot a Microsofton keresztül értékesíti, vagy kiválasztotta a **kapcsolatfelvételi** lista lehetőséget. Részletes útmutatásért tekintse [meg a kereskedelmi Marketplace-ajánlat ügyfeleinek vezetőit](partner-center-portal/commercial-marketplace-get-customer-leads.md).

### <a name="configure-the-connection-details-in-partner-center"></a>A kapcsolat részleteinek konfigurálása a partner Centerben

1.  Az **ügyfél-érdeklődők** területen válassza a **Kapcsolódás** hivatkozást.
1. A **kapcsolat részletei** párbeszédpanelen válasszon ki egy érdeklődő célhelyet a listából.
1. Fejezze be a megjelenő mezőket. A részletes lépésekért tekintse meg a következő cikkeket:

   - [Az ajánlat konfigurálása az érdeklődők Azure-táblázatba való küldésére](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Az ajánlat beállítása a dynamics 365 Customer engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (korábban Dynamics CRM Online) felé irányuló érdeklődők küldésére
   - [Az ajánlat beállítása az érdeklődők HTTPS-végpontra küldéséhez](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Az ajánlat beállítása, hogy érdeklődőket küldjön a Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Az ajánlat beállítása, hogy érdeklődőket küldjön a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. A megadott konfiguráció ellenőrzéséhez válassza az **Érvényesítés** hivatkozást.
1. A párbeszédpanel bezárásához kattintson **az OK gombra**.

## <a name="configure-microsoft-365-app-integration"></a>Microsoft 365 alkalmazás-integráció konfigurálása

Összekapcsolhatja az SaaS-ajánlat és a kapcsolódó Microsoft 365 alkalmazások felhasználásának [egységes felderítését és továbbítását](./plan-SaaS-offer.md) .

### <a name="integrate-with-microsoft-api"></a>Integráció a Microsoft API-val

1. Ha az SaaS-ajánlat nem integrálható a Microsoft Graph API-val, válassza a **nem** lehetőséget. Folytassa a közzétett Microsoft 365 alkalmazás-felhasználási ügyfelekkel való csatolást.  

1. Ha az SaaS-ajánlat integrálva van Microsoft Graph API-val, válassza az **Igen** lehetőséget, majd adja meg az Microsoft Graph API-val való integrációhoz létrehozott és regisztrált Azure Active Directory-alkalmazás azonosítóját. 

### <a name="link-published-microsoft-365-app-consumption-clients"></a>Microsoft 365 alkalmazás-felhasználási ügyfelek közzétett hivatkozása

1. Ha nem rendelkezik olyan közzétett Office-bővítményekkel, Teams alkalmazással vagy SharePoint-keretrendszer megoldásokkal, amelyek az SaaS-ajánlattal működnek, válassza a **nem** lehetőséget.

1. Ha olyan Office-bővítményt, Teams alkalmazást vagy SharePoint-keretrendszerbeli megoldásokat adott közzé, amelyek az SaaS-ajánlattal működnek, válassza az **Igen**, majd a **+ további AppSource-hivatkozás hozzáadása** lehetőséget az új hivatkozások hozzáadásához.  

1. Érvényes AppSource-hivatkozást adjon meg.

1. Az összes hivatkozás hozzáadásának folytatásához válassza a **+ további AppSource-hivatkozás hozzáadása** lehetőséget, és adjon meg érvényes AppSource-hivatkozásokat.  

1. A társított termékek megrendelése az SaaS-ajánlat listázási lapján a rangsor értéke jelzi, a lista kiválasztásával, megtartásával és mozgatásával módosíthatja a = ikont. 

1. A termék sorában a **Törlés** lehetőség kiválasztásával törölhet egy kapcsolódó terméket.  


> [!IMPORTANT]
> Ha leállítja egy kapcsolt termék értékesítését, nem lesz automatikusan leválasztva az SaaS-ajánlathoz, törölnie kell azt a társított termékek listájáról, majd újra be kell küldenie az SaaS-ajánlatot.  

 

## <a name="next-steps"></a>Következő lépések

- [SaaS-ajánlat tulajdonságainak konfigurálása](create-new-saas-offer-properties.md)