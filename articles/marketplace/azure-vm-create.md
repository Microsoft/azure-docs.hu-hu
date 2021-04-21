---
title: Hozzon létre egy virtuálisgép-ajánlatot a Azure Marketplace.
description: Ismerje meg, hogyan hozhat létre virtuálisgép-ajánlatot a Microsoft kereskedelmi piacterén.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 04/08/2021
ms.openlocfilehash: f0c1d9d528ed4fbf61786042fb6fb34f05fec5d5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812592"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Virtuálisgép-ajánlat létrehozása a Azure Marketplace

Ez a cikk bemutatja, hogyan hozhat létre Azure-beli [virtuálisgép-Azure Marketplace.](https://azuremarketplace.microsoft.com/) Windows- és Linux-alapú virtuális gépeket is kezel, amelyek operációs rendszert, virtuális merevlemezt (VHD) és legfeljebb 16 adatlemezt tartalmaznak.

Mielőtt elkezdené, [hozzon létre egy kereskedelmi piactéri fiókot a Partnerközpont.](create-account.md) Győződjön meg arról, hogy a fiókja regisztrálva van a kereskedelmi piactéri programban.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem tette meg, tekintse át [a virtuálisgép-ajánlat megtervezését ismertetőt.](marketplace-virtual-machines.md) Ez ismerteti a virtuális gép műszaki követelményeit, és felsorolja azokat az információkat és eszközöket, amelyekre szüksége lesz az ajánlat létrehozásakor.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [Partnerközpont.](https://partner.microsoft.com/dashboard/home)
2. A bal oldali panelen válassza a **Kereskedelmi piactér – áttekintés**  >  **lehetőséget.**
3. Az Áttekintés **lapon** válassza az + Új ajánlat   >  **Azure-beli virtuális gép lehetőséget.**

    ![Képernyőkép a bal oldali panel menü lehetőségeiről és az "Új ajánlat" gombról.](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Az ajánlat közzététele után az abban a Partnerközpont szerkesztett módosítások Azure Marketplace az ajánlat újbóli közzététele után jelennek meg. A módosításokat követően mindig tegye közzé újra az ajánlatot.

Adjon meg egy **ajánlatazonosítót.** Ez a fiók minden ajánlatának egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a Azure Marketplace ajánlat webcímében, illetve Azure PowerShell Azure CLI-ben, ha van ilyen.
- Csak kisbetűket és számokat használjon. Az azonosító kötőjeleket és aláhúzásjeleket tartalmazhat, szóközök nélkül, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-offer-1** címet adja meg, az ajánlat webcíme `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` lesz.
- A Létrehozás lehetőséget választva az ajánlatazonosító nem **módosítható.**

Adjon meg egy **Ajánlat aliast.** Az ajánlat aliasa az ajánlathoz használt név a Partnerközpont.

- Ez a név nem használatos a Azure Marketplace. Ez eltér az ajánlat nevétől és az ügyfelek számára megjelenő egyéb értékektől.

Válassza **a Létrehozás lehetőséget** az ajánlat létrehozásához és a folytatáshoz. Partnerközpont Megnyílik az **Ajánlat beállítása** lap.

## <a name="enable-a-test-drive-optional"></a>Teszt meghajtó engedélyezése (nem kötelező)

A teszt meghajtó remek módja annak, hogy bemutassa ajánlatát a lehetséges ügyfeleknek azáltal, hogy adott számú órán keresztül hozzáférést biztosít számukra egy előre konfigurált környezethez. A tesztverziós ajánlat magasabb konverziós rátát eredményez, és magas minősített érdeklődőket eredményez. További információ a tesztmeghajtókról: [Mi az a tesztmeghajtó?](./what-is-test-drive.md).

> [!TIP]
> A teszt meghajtó eltér az ingyenes próbaverziótól. Kínálhat teszt meghajtót, ingyenes próbaverziót vagy mindkettőt. Mindkettő adott időtartamra biztosítja az Ön megoldását az ügyfelek számára. A tesztmeghajtó azonban magában foglalja a termék fő funkcióinak és előnyeinek gyakorlati, önvezető bemutatóját is, amely egy valós megvalósítási forgatókönyvben van bemutatva.

A teszt meghajtó engedélyezéséhez jelölje be **a Teszt** meghajtó engedélyezése jelölőnégyzetet. A teszt meghajtót később fogja konfigurálni. A teszt meghajtóval konfigurálni kell egy CRM-et (lásd a következő szakaszt).

## <a name="configure-customer-leads-management"></a>Ügyfél érdeklődők kezelésének konfigurálása

[!INCLUDE [Customer leads](includes/customer-leads.md)] 

Válassza **a Piszkozat** mentése lehetőséget, mielőtt továbblép a bal oldali navigációs menü Tulajdonságok lapján a **következő lapra.**

## <a name="next-steps"></a>Következő lépések

- [Virtuálisgép-ajánlat tulajdonságainak konfigurálása](azure-vm-create-properties.md)
- [Ajánlatlistákra vonatkozó ajánlott eljárások](gtm-offer-listing-best-practices.md)