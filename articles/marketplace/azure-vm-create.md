---
title: Hozzon létre egy virtuálisgép-ajánlatot az Azure Marketplace-en.
description: Ismerje meg, hogyan hozhat létre virtuálisgép-ajánlatokat az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 9395b607f235ab606c9013f9273c4162b8c953d7
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284297"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Virtuálisgép-ajánlat létrehozása az Azure Marketplace-en

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé Azure-beli virtuális gépeket az [Azure Marketplace](https://azuremarketplace.microsoft.com/)-en. A Windows-alapú és a Linux-alapú virtuális gépeket is tartalmazza, amelyek egy operációs rendszert, egy virtuális merevlemezt (VHD) és legfeljebb 16 adatlemezt tartalmaznak.

Mielőtt elkezdené, [hozzon létre egy kereskedelmi piactér-fiókot a partner Centerben](partner-center-portal/create-account.md). Győződjön meg arról, hogy a fiók regisztrálva van a kereskedelmi piactér programban.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem tette meg, tekintse át [a virtuális gép ajánlatának megtervezése](marketplace-virtual-machines.md)című oktatóanyagot. Ismerteti a virtuális gép technikai követelményeit, és felsorolja az ajánlat létrehozásakor szükséges információkat és eszközöket. 

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali panelen válassza a **kereskedelmi piactér**  >  **áttekintése**elemet.
3. Az **Áttekintés** lapon válassza az **új ajánlat**Azure-beli  >  **virtuális gép**lehetőséget.

    ![Képernyőkép a bal oldali ablaktábla menüpontok és az "új ajánlat" gomb megjelenítéséhez.](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Az ajánlat közzétételét követően a partner Centerben végrehajtott módosítások csak az ajánlat ismételt közzététele után jelennek meg az Azure piactéren. A módosítások végrehajtása után ügyeljen arra, hogy mindig újra tegye közzé az ajánlatokat.

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító az Azure Marketplace-ajánlathoz tartozó webcímek ügyfelei számára látható, Azure PowerShell és az Azure CLI-ben, ha van ilyen.
- Csak kisbetűket és számokat használjon. Az azonosító kötőjeleket és aláhúzást tartalmazhat, de nem tartalmaz szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1**értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Az ajánlat azonosítója a **Létrehozás**gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Az ajánlat alias a partner Centerben az ajánlathoz használt név.

- Ez a név nem használható az Azure Marketplace-en. Eltér az ajánlat nevétől és az ügyfelek számára megjelenített egyéb értéktől.

## <a name="enable-a-test-drive-optional"></a>Tesztelési meghajtó engedélyezése (nem kötelező)

A test Drive nagyszerű lehetőséget nyújt arra, hogy az ajánlatot a lehetséges ügyfelek számára is bemutassa, ha egy előre konfigurált környezethez hozzáférést ad nekik egy rögzített számú órán keresztül. A tesztelési meghajtó felajánlása nagyobb konverziós arányt eredményez, és nagy mértékben minősített érdeklődőket generál. További információ a tesztelési meghajtókról: [Mi az a test Drive?](partner-center-portal/test-drive.md).

> [!TIP]
> A tesztelési meghajtó nem azonos az ingyenes próbaverzióval. Akár tesztelési meghajtót, ingyenes próbaverziót vagy mindkettőt is használhat. Mind a megoldás egy meghatározott időszakra biztosítanak ügyfeleinek. A test Drive azonban magában foglalja a termék legfontosabb funkcióit és előnyeit is, amelyek egy valós megvalósítási forgatókönyvben jelennek meg.

**Tesztelési meghajtó engedélyezése**
1.  A **tesztelési meghajtó**területen jelölje be a **tesztvezetés engedélyezése** jelölőnégyzetet.
1.  Válassza ki a tesztoldal típusát a megjelenő listából.

## <a name="configure-lead-management"></a>Érdeklődők kezelésének konfigurálása

Ha közzéteszi az ajánlatot a kereskedelmi piactéren a partner centerrel, kapcsolja össze azt az Ügyfélkapcsolat-kezelési (CRM) rendszerhez. Ez lehetővé teszi az ügyfelek kapcsolattartási adatainak megszerzését, amint valaki érdeklődik a termék iránt, vagy használja a terméket. Ha egy tesztelési meghajtót szeretne engedélyezni, a CRM-hez való csatlakozásra van szükség (lásd az előző szakaszt). Ellenkező esetben a CRM-hez való csatlakozás nem kötelező.

1. az **ügyfél-érdeklődők**területen válassza a **Kapcsolódás** hivatkozást.
1. A **kapcsolat részletei** párbeszédpanelen válasszon ki egy érdeklődő célhelyet a listából.
1. Fejezze be a megjelenő mezőket. A részletes lépésekért tekintse meg a következő cikkeket:

   - [Az ajánlat konfigurálása az érdeklődők Azure-táblázatba való küldésére](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Az ajánlat beállítása a dynamics 365 Customer engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (korábban Dynamics CRM Online) felé irányuló érdeklődők küldésére
   - [Az ajánlat beállítása az érdeklődők HTTPS-végpontra küldéséhez](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Az ajánlat beállítása, hogy érdeklődőket küldjön a Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Az ajánlat beállítása, hogy érdeklődőket küldjön a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. A megadott konfiguráció ellenőrzéséhez válassza az **Érvényesítés** hivatkozást.
1. A párbeszédpanel bezárásához kattintson **az OK gombra**.

## <a name="resell-through-csps"></a>Viszonteladás a CSP-n keresztül

A [Cloud Solution Provider (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/) program partnerei számára elérhetővé teszi az ajánlat elérhetőségét. Az összes saját Licences (BYOL) csomag automatikusan bekerül a programba. Dönthet úgy is, hogy nem BYOL terveket is választ.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="next-steps"></a>Következő lépések

- [Virtuálisgép-ajánlat tulajdonságainak konfigurálása](azure-vm-create-properties.md)
- [Ajánlatlistákra vonatkozó ajánlott eljárások](gtm-offer-listing-best-practices.md)