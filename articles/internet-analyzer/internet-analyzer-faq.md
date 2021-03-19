---
title: Internet Analyzer – gyakori kérdések | Microsoft Docs
description: Az Azure Internet Analyzer szolgáltatással kapcsolatos gyakori kérdések.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74184259"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure Internet Analyzer – gyakori kérdések (előzetes verzió)

Ez az Azure Internet Analyzer gyakori kérdései – Ha további kérdése van, látogasson el a [visszajelzési fórumra](https://aka.ms/internetAnalyzerFeedbackForum) , és tegye fel a kérdését. Ha egy kérdést gyakran megkérdeznek, azt felvesszük a cikkbe, így gyorsan és egyszerűen megtalálhatók.

## <a name="how-do-i-participate-in-the-preview"></a>Hogyan részt vesz az előzetes verzióban?

Az előnézet elérhető az ügyfelek kiválasztásához. Ha szeretne csatlakozni az előzetes verzióhoz, tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com).
2. Navigáljon az **előfizetések** lapra.
3. Kattintson arra az Azure-előfizetésre, amelyre az Internet Analyzert szeretné használni.
4. Nyissa meg az előfizetés **erőforrás-szolgáltatói** beállításait.
5. Keressen rá a **Microsoft. Network** kifejezésre, és kattintson a **regisztráció** (vagy a regisztráció **újbóli regisztrálása**) gombra.
![hozzáférési kérelem](./media/ia-faq/request-preview-access.png)

6. A [kérelem jóváhagyásához](https://aka.ms/internetAnalyzerContact) adja meg nekünk az e-mail-címét és a hozzáférési kérelem elvégzéséhez használt Azure-előfizetés azonosítóját.
7. Ha jóváhagyta kérelmét, e-mailben megerősítő értesítést kap, és az újonnan engedélyezett Azure-előfizetésből létrehozhat/frissíthet/módosíthat Internet Analyzer-erőforrásokat.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Be kell ágyazni az ügyfelet egy teszt futtatásához?

Igen, az Internet Analyzer-ügyfelet telepíteni kell az alkalmazásba, hogy a felhasználókra vonatkozó mérőszámokat gyűjtsön. [Ismerje meg, hogyan telepítheti az ügyfelet.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Fizetnem kell az előzetes verzióban való részvételért?
Nem, az Azure Internet Analyzer ingyenesen használható előzetes verzióban. Az előzetes verzióban nem érhető el szolgáltatói szerződés.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Milyen forgatókönyvek vannak kialakítva az Internet Analyzer számára?

Az Internet Analyzer úgy lett kialakítva, hogy a felhasználói populáción alapuló hálózati teljesítményt nyújtson. A felhasználók számára legmegfelelőbb teljesítménybeli döntések elősegítése érdekében az Internet Analyzer összehasonlítja a két internetes végpont teljesítményét a különböző felhasználói populációk használatával. Míg az Internet Analyzer számos kérdést tud válaszolni, a leggyakoribbak a következők:

* Milyen hatással van a felhőbe való Migrálás teljesítményére? 
    * *Javasolt teszt: egyéni (a jelenlegi helyszíni infrastruktúra) és az Azure (bármely előre konfigurált végpont)*
* Mi az az érték, hogy az adatközpontban hogyan helyezik üzembe az adataimat? 
    *  *Javasolt teszt: Azure-beli és Azure-beli bejárati ajtó, Azure vagy Azure CDN a Microsofttól*
* Mi az Azure bejárati ajtó teljesítménybeli előnye?
    *  *Javasolt teszt: Custom/Azure/CDN vagy Azure bejárati ajtó*
* Milyen teljesítménybeli előnyökkel jár a Microsoft Azure CDN? 
    *  *Javasolt teszt: Custom/Azure/AFD vs. Azure CDN a Microsofttól*
* Hogyan működik Azure CDN a Microsoft stackből? 
    *  *Javasolt teszt: Custom (egyéb CDN-végpont) és a Microsoft Azure CDN*
* Mi a legjobb felhő a végfelhasználói populációban az egyes régiókban? 
    *  *Javasolt teszt: Custom (egyéb Cloud Service) vs. Azure (bármely előre konfigurált végpont)*

## <a name="which-tests-can-i-run-in-preview"></a>Milyen tesztek futtathatók az előzetes verzióban?

Az Internet Analyzerben létrehozott egyes tesztek két végpontból állnak – A végpont és A B végpont. A következő kombinációk bármelyike használható tesztként:  
* Két előre konfigurált végpont,
* Egy egyéni és egy előre konfigurált végpont, vagy
* Két [Egyéni végpont](internet-analyzer-custom-endpoint.md) (egy egyéni végpontnak az Azure-ban kell lennie).

Az előzetes verzióban az alábbi előre konfigurált végpontok érhetők el:
* **Azure-régiók**
    * Dél-Brazília
    * Közép-India
    * Az USA középső régiója
    * Kelet-Ázsia
    * USA keleti régiója
    * Nyugat-Japán
    * Észak-Európa
    * Dél-Afrika északi régiója
    * Délkelet-Ázsia
    * Észak-Egyesült Arab
    * Az Egyesült Királyság nyugati régiója  
    * Nyugat-Európa
    * USA nyugati régiója
    * USA 2. nyugati régiója
* **Több Azure-régió kombinációja**
    * USA keleti régiója, Dél-Brazília
    * USA keleti régiója, Kelet-Ázsia
    * Nyugat-Európa, Dél-Brazília
    * Nyugat-Európa, Délkelet-Ázsia
    * Nyugat-Európa, Észak-Egyesült Arab
    * USA nyugati régiója, USA keleti régiója
    * USA nyugati régiója, Nyugat-Európa
    * USA nyugati régiója, Észak-Egyesült Arab
    * Nyugat-Európa, Észak-Ausztrália, Délkelet-Ázsia
    * USA nyugati régiója, Nyugat-Európa, Kelet-Ázsia
    * USA nyugati régiója, Észak-Európa, Délkelet-Ázsia, Észak-Ausztrália, Dél-Afrika északi régiója 
* **Azure + Azure bejárati ajtó** – üzembe helyezése a fent felsorolt egyetlen vagy több Azure-régió kombinációjában
* **Az Azure + Azure CDN a Microsoft** által üzembe helyezett bármely, a fent felsorolt egyetlen Azure-régió kombinációján
* **Azure + azure Traffic Manager** – üzembe helyezés a fent felsorolt több Azure-régió kombinációján

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Miben különbözik az Internet Analyzer az Azure által biztosított egyéb monitorozási szolgáltatásoktól?

Az Internet Analyzer segítségével megismerheti a végfelhasználók teljesítményét, és segítheti a döntések meghozatalát a teljesítményük javítása érdekében. Míg más Azure monitoring-eszközök betekintést nyújtanak az Azure-szolgáltatásba, az Internet Analyzer a felhasználók teljes körű internetes teljesítményének mérésére koncentrál.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Hogyan történik az Internet Analyzer által kezelt mérési adatfeldolgozás?

Az Azure [erős biztonsági folyamatokkal rendelkezik, és megfelel a megfelelőségi szabványok széles skálájának](https://azure.microsoft.com/support/trust-center/). Csak Ön és a kijelölt csapat férhet hozzá az adataihoz. A Microsoft munkatársai csak bizonyos korlátozott körülmények között érhetik el a hozzáférést az Ön számára. Az átvitel és a nyugalmi állapotban is titkosítva van.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse meg az [Internet Analyzer áttekintése](internet-analyzer-overview.md)című témakört.
