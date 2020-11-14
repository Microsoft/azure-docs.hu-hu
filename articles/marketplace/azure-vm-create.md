---
title: Hozzon létre egy virtuálisgép-ajánlatot az Azure Marketplace-en.
description: Ismerje meg, hogyan hozhat létre virtuálisgép-ajánlatokat a Microsoft kereskedelmi piactéren.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/20/2020
ms.openlocfilehash: 2ef80d26336ddbe3c015dfcde0c5ed29b762f39b
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629716"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Virtuálisgép-ajánlat létrehozása az Azure Marketplace-en

Ez a cikk bemutatja, hogyan hozhat létre Azure-beli virtuális gépeket az [Azure Marketplace](https://azuremarketplace.microsoft.com/)-hez. A Windows-alapú és a Linux-alapú virtuális gépeket is tartalmazza, amelyek egy operációs rendszert, egy virtuális merevlemezt (VHD) és legfeljebb 16 adatlemezt tartalmaznak.

Mielőtt elkezdené, [hozzon létre egy kereskedelmi piactér-fiókot a partner Centerben](partner-center-portal/create-account.md). Győződjön meg arról, hogy a fiók regisztrálva van a kereskedelmi piactér programban.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem tette meg, tekintse át [a virtuális gép ajánlatának megtervezése](marketplace-virtual-machines.md)című oktatóanyagot. Ismerteti a virtuális gép technikai követelményeit, és felsorolja az ajánlat létrehozásakor szükséges információkat és eszközöket.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali panelen válassza a **kereskedelmi piactér**  >  **áttekintése** elemet.
3. Az **Áttekintés** lapon válassza az **+ új ajánlat** Azure-beli  >  **virtuális gép** lehetőséget.

    ![Képernyőkép a bal oldali ablaktábla menüpontok és az "új ajánlat" gomb megjelenítéséhez.](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Az ajánlat közzétételét követően a partner Centerben végrehajtott módosítások csak az ajánlat ismételt közzététele után jelennek meg az Azure piactéren. A módosítások végrehajtása után ügyeljen arra, hogy mindig újra tegye közzé az ajánlatokat.

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító az Azure Marketplace-ajánlathoz tartozó webcímek ügyfelei számára látható, Azure PowerShell és az Azure CLI-ben, ha van ilyen.
- Csak kisbetűket és számokat használjon. Az azonosító kötőjeleket és aláhúzást tartalmazhat, de nem tartalmaz szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1** értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Az ajánlat azonosítója a **Létrehozás** gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Az ajánlat alias a partner Centerben az ajánlathoz használt név.

- Ez a név nem használható az Azure Marketplace-en. Eltér az ajánlat nevétől és az ügyfelek számára megjelenített egyéb értéktől.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz. A partner Center megnyitja az **ajánlat beállítása** lapot.

## <a name="enable-a-test-drive-optional"></a>Tesztelési meghajtó engedélyezése (nem kötelező)

A test Drive nagyszerű lehetőséget nyújt arra, hogy az ajánlatot a lehetséges ügyfelek számára is bemutassa, ha egy előre konfigurált környezethez hozzáférést ad nekik egy rögzített számú órán keresztül. A tesztelési meghajtó felajánlása nagyobb konverziós arányt eredményez, és nagy mértékben minősített érdeklődőket generál. További információ a tesztelési meghajtókról: [Mi az a test Drive?](./what-is-test-drive.md).

> [!TIP]
> A tesztelési meghajtó nem azonos az ingyenes próbaverzióval. Akár tesztelési meghajtót, ingyenes próbaverziót vagy mindkettőt is használhat. Mind a megoldás egy meghatározott időszakra biztosítanak ügyfeleinek. A test Drive azonban magában foglalja a termék legfontosabb funkcióit és előnyeit is, amelyek egy valós megvalósítási forgatókönyvben jelennek meg.

A tesztelési meghajtó engedélyezéséhez jelölje be a **teszt-meghajtó engedélyezése** jelölőnégyzetet. A tesztelési meghajtót később kell konfigurálnia. A test Drive használatával a CRM konfigurálása szükséges (lásd a következő szakaszt).

## <a name="configure-customer-leads-management"></a>Az ügyfél-érdeklődők felügyeletének konfigurálása

Ha a partner Center használatával tesz közzé ajánlatot a kereskedelmi piactéren, kapcsolja össze az Ügyfélkapcsolat-kezelési (CRM) rendszerével. Ez lehetővé teszi az ügyfelek kapcsolattartási adatainak megszerzését, amint valaki érdeklődik a termék iránt, vagy használja a terméket. Ha egy tesztelési meghajtót szeretne engedélyezni, a CRM-hez való csatlakozásra van szükség (lásd az előző szakaszt). Ellenkező esetben a CRM-hez való csatlakozás nem kötelező.

1. Az **ügyfél-érdeklődők** területen válassza a **Kapcsolódás** hivatkozást.
1. A **kapcsolat részletei** párbeszédpanelen válasszon egy érdeklődő célhelyet.
1. Fejezze be a megjelenő mezőket. A részletes lépésekért tekintse meg a következő cikkeket:

   - [Az ajánlat konfigurálása az érdeklődők Azure-táblázatba való küldésére](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Az ajánlat beállítása a dynamics 365 Customer engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (korábban Dynamics CRM Online) felé irányuló érdeklődők küldésére
   - [Az ajánlat beállítása az érdeklődők HTTPS-végpontra küldéséhez](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Az ajánlat beállítása, hogy érdeklődőket küldjön a Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Az ajánlat beállítása, hogy érdeklődőket küldjön a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. A megadott konfiguráció ellenőrzéséhez válassza az **Érvényesítés** hivatkozást.
1. Válassza a **Kapcsolódás** lehetőséget.

A bal oldali navigációs menü következő lapján a Tovább gombra kattintva válassza a **Piszkozat mentése** lehetőséget, majd kattintson a **Tulajdonságok** elemre.

## <a name="next-steps"></a>További lépések

- [A virtuális gépek ajánlati tulajdonságainak konfigurálása](azure-vm-create-properties.md)
- [Ajánlatlistákra vonatkozó ajánlott eljárások](gtm-offer-listing-best-practices.md)