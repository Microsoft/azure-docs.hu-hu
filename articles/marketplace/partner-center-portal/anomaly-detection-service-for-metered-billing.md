---
title: Anomália-észlelési szolgáltatás mért számlázáshoz – Microsoft Azure Marketplace
description: Leírja, hogyan működik a anomáliák észlelése, hogyan történik az értesítések küldése, és mi a teendő, valamint a támogatási lehetőségek.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: 909b818c60932f5b1d6071ea1ccf0ee4c76173b4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280529"
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

Heti rendszerességgel e-mailben elküldjük a anomáliák észlelését. Ez magában foglalja az összes olyan rendellenességet, amelyet a héten az összes fogyasztásmérő és ügyfél számára észlelt. Ezt az e-mailt a rendszer az ajánlat létrehozásakor megadott **mérnöki** és **támogatási** partnereknek küldi el.

Meg kell vizsgálnia, hogy az észlelt rendellenességek valós problémák-e, és ha igen, forduljon a Microsofthoz, és jelentse a helytelen használatot (lásd az alábbi támogatási szakaszt).

Ha megerősíti, hogy az észlelt rendellenességek normál használatban vannak, nincs szükség további műveletre. Ha azonban egy rendellenesség potenciálisan magas pénzügyi kockázatot jelent, felvesszük Önnel a kapcsolatot, hogy erősítse a használatot.  

## <a name="when-and-how-to-get-support"></a>Mikor és hogyan kérhet támogatást

Ha a helytelen használatot küldte a Microsoft számára, és ez az ügyfél számára is fennáll, akkor a Microsoft nem küld számlát az ügyfélnek a jelentett használat miatt, vagy a használatért kell fizetnie. A kevesebb mennyiség jelentése miatti bevételkiesést Önnek kell állnia.

A következő esetekben támogatási jegyet nyithat, hogy visszatérítést kérjen, vagy helyesbíttesse az ügyfelek számláját:

- Megerősítette, hogy az észlelt rendellenességek egyike valódi probléma, és a helytelen használat az ügyfél **túlterhelését** eredményezi.
- Felderíti, hogy helytelen használatot kapott nekünk, és a helytelen használat az ügyfél **túlterhelését** eredményezi.
- Az ügyfél forgalmi díjas számlázási felhasználásának használatáért visszatérítést szeretne kérni.

Jegy elküldése:

1. Nyissa meg a támogatási lapot. A **mondja el a probléma mezőjében**írja be a "helytelen használat" kifejezést.
2. A támogatási témakörökben a keresési eredmények legördülő menüben válassza ki a következők egyikét:
    - **Kereskedelmi piactér**  >  **Mért számlázás**  >  **Helytelen használat érkezik az Azure-alkalmazások ajánlatához**, vagy
    - **Kereskedelmi piactér**  >  **Mért számlázás**  >  **Helytelen használat lett elküldve SaaS-ajánlathoz**
3. A **következő lépés**alatt jelölje be a **megoldások véleményezése** gombot a partner központba való bejelentkezéshez a támogatási jegy elküldéséhez.

További közzétevői támogatási lehetőségekért lásd: [támogatás a kereskedelmi piactér programhoz a partner Centerben](../support.md).

## <a name="next-step"></a>Következő lépés

- Ismerje meg a [Marketplace-mérési szolgáltatás API](marketplace-metering-service-apis.md)-ját.
