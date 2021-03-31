---
title: Azure Internet Analyzer | Microsoft Docs
description: További tudnivalók az Azure Internet Analyzerről
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 2512092c69390ce604c26ab6c5dd000afafc7c05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91360888"
---
# <a name="what-is-internet-analyzer-preview"></a>Mi az Internet Analyzer? (Előzetes verzió)

Az Internet Analyzer egy ügyféloldali mérési platform, amellyel tesztelheti, hogy a hálózati infrastruktúra milyen hatással van az ügyfelek teljesítményére. Akár a helyszínről migrál az Azure-ba, akár egy új Azure-szolgáltatást értékel, az Internet Analyzer lehetővé teszi, hogy a migrálás előtt a felhasználók adatai és a Microsoft részletes elemzései alapján jobban megérthesse és optimalizálhassa a hálózati architektúrát az Azure-ral.

Az Internet Analyzer egy kisméretű JavaScript-ügyfelet használ a webalkalmazásba, amely a végfelhasználók által a kiválasztott hálózati célhelyek késését méri, a _végpontokat_ hívjuk. Az Internet Analyzer lehetővé teszi több párhuzamos tesztelés beállítását, amely lehetővé teszi különböző forgatókönyvek kiértékelését az infrastruktúra és az ügyfél igényei szerint. Az Internet Analyzer egyéni és előre konfigurált végpontokat biztosít, így biztosítva a kényelmet és a rugalmasságot, hogy megbízható teljesítménybeli döntéseket hozhasson a végfelhasználók számára. 


> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Gyors & testreszabható tesztek

Az Internet Analyzer a Felhőbeli áttelepítés teljesítményével kapcsolatos kérdéseit, új vagy további Azure-régiókban való üzembe helyezését, illetve az Azure-beli új alkalmazás-és tartalomszolgáltató platformok tesztelését, például az [Azure bejárati ajtót](https://azure.microsoft.com/services/frontdoor/) és [Microsoft Azure CDN](https://azure.microsoft.com/services/cdn/)-t teszteli. 

Az Internet Analyzerben létrehozott minden teszt két végpontból áll: az A végpont és a B végpont. A B végpont teljesítményének elemzése az A végponthoz képest történik. 

Konfigurálhatja saját egyéni végpontját, vagy választhat számos előre konfigurált Azure-végpontból is. Egyéni végpontokat kell használni a helyszíni számítási feladatok, a más felhőalapú szolgáltatók példányai vagy az egyéni Azure-konfigurációk kiértékeléséhez. A tesztek két egyéni végpontból állhatnak; legalább egy egyéni végpontot azonban az Azure-ban kell üzemeltetni. Az előre konfigurált Azure-végpontok gyorsan és egyszerűen kiértékelik a népszerű Azure hálózati platformok, például az Azure bejárati ajtó, az Azure Traffic Manager és a Azure CDN teljesítményét. 

Az előzetes verzió ideje alatt a következő előre konfigurált végpontok érhetők el: 

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

## <a name="suggested-test-scenarios"></a>Javasolt tesztelési forgatókönyvek 

Az Internet Analyzer lehetővé teszi, hogy az ügyfelek számára a legjobb teljesítményű döntéseket hozza, hogy kiértékelje a végfelhasználók adott populációjának két végpontját. 

Míg az Internet Analyzer számos kérdést tud válaszolni, a leggyakoribbak a következők: 
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

## <a name="how-it-works"></a>Működés

Az Internet Analyzer használatához állítson be egy Internet Analyzer-erőforrást a Microsoft Azure Portal, és telepítse a kis JavaScript-ügyfelet az alkalmazásban. Az ügyfél a végfelhasználók késését a kiválasztott végpontokra méri a HTTPS-en keresztül egy képpontos rendszerkép letöltésével. A késési mérések összegyűjtése után az ügyfél elküldi a mérési adatokat az Internet Analyzernek.

Amikor egy felhasználó meglátogatja a webalkalmazást, a JavaScript-ügyfél két végpontot választ ki az összes konfigurált teszt alapján. Az ügyfél minden végpont esetében _hideg_ és _meleg_ mérést végez. A _hideg_ mérés további késleltetést okoz a felhasználó és a végpont közötti, például a DNS-feloldás, a TCP-kapcsolati kézfogás és az SSL/TLS egyeztetése közötti tiszta hálózati késés mellett. A _meleg_ mérés közvetlenül a _hideg_ mérés befejeződése után következik be, és kihasználja a modern böngészők állandó TCP-kapcsolatok felügyeletének előnyeit a teljes körű késés érdekében. Ha a felhasználó böngészője támogatja, a W3C-erőforrás időzítési API-ját a pontos mérési időzítéshez használja a rendszer. Jelenleg csak a meleg késési mérések használatosak az elemzéshez.

![A diagram egy olyan végfelhasználót mutat be, amely egy, a-ügyféllel és az interneten lévő két végponttal csatlakozik egy alkalmazás-kiszolgálóhoz, több lehetőség közül. A felhasználó a méréseket az Internet Analyzerbe tölti fel.](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Sarokszámokat 

A teszt elindítását követően a telemetria adatai megjelennek az Internet Analyzer erőforrásában a scorecard lapon. Az adatokat a rendszer mindig összesíti. A következő szűrők használatával módosíthatja a megjelenő adatnézeteket: 

* **Teszt:** Válassza ki azt a tesztet, amelyre vonatkozóan meg szeretné tekinteni az eredményeket. Az adattesztek akkor jelennek meg, ha elegendő mennyiségű adattal kell elvégezniük az elemzést – a legtöbb esetben 24 órán belül. 
* Időtartam **& befejezési dátum:** Az Internet Analyzer naponta három scorecardot hoz létre – az egyes scorecardok egy másik összesítési időszakot, a 24 órát (nap), a hét előtti napot (hetet) és a 30 nappal korábbi (hónap) időpontot tükrözik. A "befejezési dátum" szűrő használatával válassza ki a megtekinteni kívánt időtartamot. 
* **Ország:** Ezzel a szűrővel megtekintheti a végfelhasználók által az országban tartózkodó felhasználókra vonatkozó adatértékeket. A globális szűrő az összes földrajzi régión belül jeleníti meg az összes adathalmazt.  

A scorecardokkal kapcsolatos további információkért tekintse meg a [Scorecardok értelmezését](internet-analyzer-scorecard.md) ismertető oldalt. 


## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhatja létre az első Internet Analyzer-erőforrását](internet-analyzer-create-test-portal.md).
* Olvassa el az [Internet Analyzer – gyakori kérdések](internet-analyzer-faq.md)című részt. 
