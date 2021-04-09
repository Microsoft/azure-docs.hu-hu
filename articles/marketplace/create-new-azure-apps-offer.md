---
title: Azure-alkalmazások ajánlatának létrehozása a kereskedelmi piactéren
description: Ismerje meg, hogyan hozhat létre új Azure-alkalmazást az Azure Marketplace-en, illetve a Microsoft partner Center kereskedelmi piactér portálján keresztül a Cloud Solution Provider (CSP) program használatával.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370226"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>Azure-alkalmazások ajánlatának létrehozása a kereskedelmi piactéren

Kereskedelmi Piactéri közzétevőként létrehozhat egy Azure-alkalmazási ajánlatot, így a potenciális ügyfelek megvásárolhatják a megoldást. Ez a cikk bemutatja, hogyan hozhat létre Azure-alkalmazási ajánlatokat a Microsoft kereskedelmi piactéren.

Ha még nem tette meg, olvassa el [a kereskedelmi piactérre vonatkozó Azure-alkalmazás ajánlatának megtervezése](plan-azure-application-offer.md)című cikkét. Megadja az erőforrásokat, és segít összegyűjteni az ajánlat létrehozásakor szükséges információkat és eszközöket.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).

1. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés** lehetőséget.

1. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **Azure-alkalmazás** lehetőséget.

    ![A bal oldali navigációs menü ábrázolása.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. Az **új ajánlat** párbeszédpanelen adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója. Ez az azonosító a kereskedelmi piactér-lista és a Azure Resource Manager-sablonok URL-címében látható, ha van ilyen. Ha például beírja a test-Offer-1 értéket ebben a mezőben, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

     * A fiók minden ajánlatának egyedi ajánlat-AZONOSÍTÓval kell rendelkeznie.
     * Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet.
     * Az ajánlat azonosítója a **Létrehozás** gombra kattintva nem módosítható.

1. Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

     * Ez a név csak a partner Centerben látható, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.
     * Az ajánlat aliasa nem módosítható a **Létrehozás** gombra kattintva.

1. Az ajánlat létrehozásához és a folytatáshoz válassza a  **Létrehozás** lehetőséget.

## <a name="configure-your-azure-application-offer-setup-details"></a>Az Azure-alkalmazás ajánlatának beállítása – részletek

Az **ajánlat beállítása** lap **telepítés részletei** területén válassza ki, hogy szeretné-e tesztelni a tesztelési meghajtót. Az Ügyfélkapcsolat-kezelési (CRM) rendszert a kereskedelmi Marketplace-ajánlatával is összekapcsolhatjuk.

### <a name="enable-a-test-drive-optional"></a>Tesztelési meghajtó engedélyezése (nem kötelező)

A test Drive nagyszerű lehetőséget nyújt arra, hogy az ajánlatot a lehetséges ügyfelek számára is bemutassa, ha egy előre konfigurált környezethez hozzáférést ad nekik egy rögzített számú órán keresztül. A tesztelési meghajtó felajánlása nagyobb konverziós arányt eredményez, és nagy mértékben minősített érdeklődőket generál. További információ a tesztelési meghajtókról: [Test Drive](plan-azure-application-offer.md#test-drive).

#### <a name="to-enable-a-test-drive"></a>Tesztelési meghajtó engedélyezése

- A **tesztelési meghajtó** területen jelölje be a **tesztvezetés engedélyezése** jelölőnégyzetet.

### <a name="customer-lead-management"></a>Ügyfél-érdeklődői felügyelet

Az Ügyfélkapcsolat-kezelési (CRM) rendszer a kereskedelmi piactérsel való összekapcsolásával biztosítható, hogy az ügyfél kapcsolattartási adatait, amikor az ügyfél kifejezi érdeklődését vagy üzembe helyezi a terméket.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>A kapcsolat részleteinek konfigurálása a partner Centerben

1. Az **ügyfél-érdeklődők** területen válassza a **Kapcsolódás** hivatkozást.
1. A **kapcsolat részletei** párbeszédpanelen válasszon ki egy érdeklődő célhelyet a listából.
1. Fejezze be a megjelenő mezőket. A részletes lépésekért tekintse meg a következő cikkeket:

   - [Az ajánlat konfigurálása az érdeklődők Azure-táblázatba való küldésére](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Az ajánlat beállítása a dynamics 365 Customer engagement](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (korábban Dynamics CRM Online) felé irányuló érdeklődők küldésére
   - [Az ajánlat beállítása az érdeklődők HTTPS-végpontra küldéséhez](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Az ajánlat beállítása, hogy érdeklődőket küldjön a Marketo](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Az ajánlat beállítása, hogy érdeklődőket küldjön a Salesforce](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. A megadott konfiguráció ellenőrzéséhez válassza az **Érvényesítés** hivatkozást, ha van ilyen.
1. A párbeszédpanel bezárásához válassza a **Kapcsolódás** lehetőséget.
1. A következő lapra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: tulajdonságok.

> [!NOTE]
> Győződjön meg arról, hogy a vezető célhoz tartozó kapcsolat naprakész marad, hogy ne veszítse el az érdeklődőket. Győződjön meg róla, hogy frissíti ezeket a kapcsolatokat, amikor valamilyen változás történt.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-alkalmazás ajánlati tulajdonságainak konfigurálása](create-new-azure-apps-offer-properties.md)
