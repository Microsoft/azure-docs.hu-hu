---
title: Anomália-észlelési szolgáltatás mért számlázáshoz – Microsoft Azure Marketplace
description: Leírja, hogyan működik a anomáliák észlelése, hogyan történik az értesítések küldése, és mi a teendő, valamint a támogatási lehetőségek.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: 5ab57bcccb6f681f5c9282ef461181952ed5a679
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653074"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Rendellenesség-észlelő szolgáltatás forgalmi díjas számlázáshoz

A [piactér-mérési szolgáltatás](marketplace-metering-service-apis-faq.md) lehetővé teszi, hogy olyan ajánlatokat hozzon létre a kereskedelmi piactér programban, amelyek díja a nem standard egységek alapján történik. A mért számlázással elküldheti az ügyfél használati eseményeit a Microsoftnak, és a használat alapján előkészítjük a számlázást.

A helytelen használati adatok különböző okokból származhatnak, például a hibákkal, a felhasználás nyomon követésével vagy csalással kapcsolatos hibás konfigurációkkal. A helytelen használati adatok helytelen vásárlói díjakat és számlázási vitákat eredményeznek.

A kockázat enyhítése érdekében a rendszer a gépi tanulási algoritmusok alapján határozza meg a normál mért számlázási viselkedést, elemezni a mért számlázási használatot, és felderíti a rendellenességeket minimális felhasználói beavatkozással.

A rendszer értesítést küld, ha a mért számlázási használatban rendellenesség észlelhető. Ez lehetőséget nyújt arra, hogy megvizsgáljon és értesítsen minket, ha az anomália valós probléma, és az ügyfél számlázási probléma proaktív megoldására irányuló műveletekre is sor kerül.

A hirtelen kiugrások, a dips és a mért számlázási használat tendenciái mellett a modell is az idényjellegű hatásokat is elvégezheti. Mivel a forgalmi díjas számlázás a túlhasználati adaton keresztül történik, a modellünk a hosszú ideig tartó hiányzó adatmennyiséget is képes kecsesen kezelni.

A következő példák a anomáliák észlelési eredményeire mutatnak. A várt tartomány sárga sávban jelenik meg. Az elfogadható mért számlázási használat a sávon belüli zöld csillagként jelenik meg. A sávon kívüli számlázási használat piros pontként jelenik meg.  

Kiszámítható trenden kívül észlelt rendellenességek:

![Szemlélteti a kiszámítható trenden kívül észlelt rendellenességeket.](media/anomaly-1.png)

Ismétlődő ciklikus trendeken kívül észlelt rendellenességek:

![Az ismétlődő ciklikus trendeken kívül észlelt rendellenességeket mutatja be.](media/anomaly-2.png)

Emelkedő tendenciában észlelt rendellenességek:

![A felfelé mutató trendekben észlelt rendellenességeket mutatja be.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>A rendellenesség-észlelési szolgáltatás működése

A anomáliák észlelése automatikusan engedélyezve van az összes mért számlázási használathoz. A használati események a Microsoftnak való elküldésekor a rendellenesség-észlelési szolgáltatás a múltbeli használati adatok alapján létrehoz egy várt értékeket tartalmazó modellt. Ez a modell hetente fut.

A anomáliák észlelési funkciói egy-és felhasználónkénti szinten. Ez azt jelenti, hogy minden egyes ügyfélnél az ügyfél korábbi használati mintája alapján lesz kitanítva a modell.

A modell visszamenőleges megbízhatósági intervallumok generálásával működik. Az idősorozat-előrejelzés egy általánosított adalékanyag-modell, amely egy trend-előrejelzési részből és egy szezonális részből áll. Mivel a modell regressziós feladatként lett kialakítva, képes a hosszú ideig tartó hiányzó adatok kezelésére. Ha a megfigyelés az előre jelzett megbízhatósági intervallumokon kívül esik, az azt jelenti, hogy a megfigyelést nem lehet megmagyarázni a mért számlázás korábbi mintái alapján, ezért ez lehet anomália.

## <a name="anomaly-detection-notification"></a>Rendellenesség-észlelési értesítés

A partner Centerben kiértékelheti, kezelheti és visszaigazolhatja a rendellenességeket. Ebből a cikkből megtudhatja, hogyan tekintheti meg [a forgalmi rendellenesség észlelését a mért számlázáshoz](../anomaly-detection.md).

Annak biztosítása érdekében, hogy az ügyfelek ne legyenek túlterhelve a mért használatnál, meg kell vizsgálnia, hogy az észlelt rendellenességek valós problémák-e. Ha igen, tudomásul veheti a nem megfelelő használatot a partner Centerben.

Javasoljuk, hogy ellenőrizze, hogy az észlelt rendellenességek normál használatban vannak-e. Ennek köszönhetően javulni fog az Ön számára biztosított anomália-információ. Ha egy rendellenesség potenciálisan magas pénzügyi kockázatot jelent, felvesszük Önnel a kapcsolatot, hogy erősítse meg a használatot.

## <a name="when-and-how-to-get-support"></a>Mikor és hogyan kérhet támogatást

Ha olyan helytelen forgalmi díjas használatot küldött nekünk, amely az ügyfél számára is felszámított, nem küldünk számlát az ügyfélnek a jelentett használat miatt, vagy a használatért kell fizetnie. A kevesebb mennyiség jelentése miatti bevételkiesést Önnek kell állnia.

Ha a következő esetek valamelyike érvényes, a partner Centerben módosíthatja a használati összeget, amely visszatérítési vagy számlázási beállításokat eredményez ügyfelei számára:

- Megerősítette, hogy az észlelt rendellenességek egyike valódi probléma, és a helytelen használat az ügyfél túlterhelését eredményezi.
- Felderíti, hogy helytelen használatot kapott nekünk, és a helytelen használat az ügyfél túlterhelését eredményezi.

A mért számlázási rendellenességekkel kapcsolatos támogatási jegy elküldése:

1. Jelentkezzen be a [partner Centerben](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) a munkahelyi fiókjával.
1. A lap jobb felső részén található menüben válassza a **támogatás** ikont. A **Súgó és támogatás** panel a lap jobb oldalán jelenik meg.
1. Ha segítségre van a kereskedelmi piactéren, válassza a **kereskedelmi piactér** lehetőséget.
   ![A támogatási panel illusztrálása.](../media/support/commercial-marketplace-support-pane.png)
1. A **probléma összegzése** mezőben adja meg a **kereskedelmi piactér > mért számlázás** értéket.
1. A **probléma típusa** mezőben válassza a következők egyikét:
    - **Kereskedelmi piactér > mért számlázási > az Azure-alkalmazások ajánlatának helytelen használata**
    - **Kereskedelmi piactér > mért számlázás > az SaaS-ajánlathoz nem megfelelő használat**
1. A **következő lépésben** válassza a **megoldások áttekintése** elemet.
1. A támogatási jegy elküldéséhez tekintse át az ajánlott dokumentumokat, ha vannak ilyenek, vagy válassza a **probléma részleteinek megadása** lehetőséget.

További közzétevői támogatási lehetőségekért lásd: [támogatás a kereskedelmi piactér programhoz a partner Centerben](../support.md).

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Marketplace-mérési szolgáltatás API](marketplace-metering-service-apis.md)-ját.
- [A mért számlázás rendellenességének észlelése](../anomaly-detection.md)
