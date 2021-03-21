---
title: Ajánlott biztonsági eljárások az Azure-eszközökhöz
titleSuffix: Azure security
description: Ez a cikk az Azure-ban tárolt adatok, alkalmazások és egyéb eszközök védelmére vonatkozó ajánlott eljárásokat ismerteti.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 86874a60d48ddcbdaca5ae779ad554ee58cc233b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498846"
---
# <a name="azure-operational-security-best-practices"></a>Az Azure működési biztonságának ajánlott eljárásai
Ez a cikk az Azure-ban tárolt adatok, alkalmazások és egyéb eszközök védelmére vonatkozó ajánlott eljárásokat ismerteti.

Az ajánlott eljárások a vélemények konszenzusán alapulnak, és a jelenlegi Azure platform-képességekkel és-szolgáltatásokkal működnek. A vélemények és technológiák idővel változnak, és ez a cikk rendszeresen frissül, hogy tükrözze ezeket a módosításokat.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Erős működési biztonsági eljárások definiálása és üzembe helyezése
Az Azure Operational Security a felhasználók számára elérhető szolgáltatásokat, vezérlőket és szolgáltatásokat jelenti az Azure-ban tárolt adatok, alkalmazások és egyéb erőforrások védelméhez. Az Azure Operational Security olyan keretrendszerre épül, amely magában foglalja a Microsoft számára egyedi képességekkel szerzett ismereteket, beleértve a [biztonsági fejlesztési életciklust (SDL)](https://www.microsoft.com/sdl), a [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) programot, valamint a kiberbiztonsági fenyegetések tájképének alapos ismeretét.

## <a name="manage-and-monitor-user-passwords"></a>Felhasználói jelszavak kezelése és figyelése
A következő táblázat felsorolja a felhasználói jelszavak kezelésével kapcsolatos ajánlott eljárásokat:

**Ajánlott eljárás**: gondoskodjon arról, hogy megfelelő szintű jelszavas védelmet biztosítson a felhőben.   
**Részletek**: kövesse a Microsoft- [jelszóval kapcsolatos útmutató](https://www.microsoft.com/research/publication/password-guidance/)útmutatását, amely a microsoft Identity platform (Azure Active Directory, Active Directory és Microsoft-fiók) felhasználóira terjed ki.

**Ajánlott eljárás**: a felhasználói fiókokkal kapcsolatos gyanús műveletek figyelése.   
**Részletek**: az Azure ad-alapú biztonsági jelentések segítségével figyelje a [veszélyeztetett felhasználókat](../../active-directory/identity-protection/overview-identity-protection.md) és a [kockázatos bejelentkezéseket](../../active-directory/identity-protection/overview-identity-protection.md) .

**Ajánlott eljárás**: a nagy kockázatú jelszavak automatikus észlelése és szervizelése.   
**Részletek**: a [Azure ad Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) a prémium szintű Azure ad P2 kiadás egyik funkciója, amely a következőket teszi lehetővé:

- A szervezet identitásait érintő lehetséges sebezhetőségek észlelése
- A szervezete identitásával kapcsolatos gyanús műveletekre vonatkozó automatizált válaszok konfigurálása
- Gyanús incidensek kivizsgálásával és a megoldáshoz szükséges megfelelő műveletek elvégzésével

## <a name="receive-incident-notifications-from-microsoft"></a>Incidens-értesítések fogadása a Microsofttól
Győződjön meg arról, hogy a Security Operations csapata fogadja az Azure incidensekkel kapcsolatos értesítéseket a Microsofttól. Az incidensek értesítése lehetővé teszi a biztonsági csapatnak, hogy feltörte az Azure-erőforrásokat, így gyorsan reagálhat a lehetséges biztonsági kockázatokra, és javíthatja azokat.

Az Azure beléptetési portálon gondoskodhat arról, hogy a rendszergazdai kapcsolattartási adatok a biztonsági műveleteket értesítő részleteket tartalmazzanak. A kapcsolattartási adatok egy e-mail-cím és egy telefonszám.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Azure-előfizetések rendszerezése felügyeleti csoportokba
Ha a cég több előfizetéssel rendelkezik, szüksége lehet egy hatékony módszerre az előfizetések hozzáférésének, szabályzatainak és megfelelőségének kezelésére. Az [Azure felügyeleti csoportjai](../../governance/management-groups/create-management-group-portal.md) magasabb szintű hatókört biztosítanak az előfizetésekhez. Az előfizetéseket felügyeleti csoportnak nevezett tárolókban rendezi, és ezekre a csoportokra alkalmazza a szabályozási feltételeket. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott feltételeket.

A felügyeleti csoportok és előfizetések rugalmas struktúráját létrehozhatja egy könyvtárba. Minden címtár egyetlen legfelső szintű felügyeleti csoportot kap, amelynek neve a gyökérszintű felügyeleti csoport. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. A felügyeleti csoport lehetővé teszi, hogy a globális házirendek és az Azure-beli szerepkör-hozzárendelések a címtár szintjén legyenek alkalmazva.

Íme néhány ajánlott eljárás a felügyeleti csoportok használatához:

**Ajánlott eljárás**: gondoskodjon arról, hogy az új előfizetések olyan irányítási elemeket alkalmazzanak, mint például a hozzáadott szabályzatok és engedélyek.   
**Részletek**: az összes Azure-eszközre érvényes, nagyvállalati szintű biztonsági elemek hozzárendeléséhez használja a gyökérszintű felügyeleti csoportot. A szabályzatok és az engedélyek például elemek.

**Ajánlott eljárás**: a felügyeleti csoportok legfelső szintjének igazítása a szegmentálási stratégiával, hogy az egyes szegmenseken belül a szabályozás és a házirendek egységességét biztosítsa.   
**Részletek**: hozzon létre egyetlen felügyeleti csoportot a legfelső szintű felügyeleti csoportba tartozó egyes szegmensekhez. Ne hozzon létre más felügyeleti csoportokat a gyökér alatt.

**Ajánlott eljárás**: a felügyeleti csoport mélységének korlátozása a félreértések elkerülése érdekében, amely akadályozza a műveleteket és a biztonságot.   
**Részletek**: a hierarchiát három szintre korlátozza, beleértve a gyökeret is.

**Ajánlott eljárás**: gondosan válassza ki, hogy mely elemeket kell alkalmazni a teljes vállalatra a legfelső szintű felügyeleti csoporttal.   
**Részletek**: gondoskodjon arról, hogy a gyökérszintű felügyeleti csoportok elemeinek minden erőforrásban egyértelműek legyenek, és hogy ne legyenek kevésbé hatással.

A jó jelöltek a következők:

- Egyértelmű üzleti hatású szabályozási követelmények (például az adatszuverenitással kapcsolatos korlátozások)
- Azok a követelmények, amelyekhez közel nulla lehetséges negatív hatással vannak a műveletekre, mint például a szabályzat vagy a naplózási hatás, vagy az Azure RBAC engedélyek hozzárendelései, amelyeket alaposan ellenőriztek

**Ajánlott eljárás**: körültekintően tervezze meg és tesztelje a legfelső szintű felügyeleti csoport összes nagyvállalati módosítását, mielőtt alkalmazná őket (házirend, Azure RBAC modell stb.).   
**Részletek**: a gyökérszintű felügyeleti csoport változásai hatással lehetnek az Azure összes erőforrására. Habár hatékony módszert biztosítanak a vállalaton belüli konzisztencia biztosítására, a hibák és a helytelen használat negatív hatással lehet a termelési műveletekre. Tesztelje a gyökérszintű felügyeleti csoport összes módosítását tesztkörnyezetben vagy éles próbaüzem során.

## <a name="streamline-environment-creation-with-blueprints"></a>Környezetek létrehozásának egyszerűsítése tervrajzokkal
[Az Azure BluePrints](../../governance/blueprints/overview.md) szolgáltatás lehetővé teszi a felhőalapú építészek és a központi informatikai csoportok számára, hogy meghatározhatnak egy olyan Azure-erőforrást, amely megvalósítja és betartja a szervezet szabványait, mintáit és követelményeit. Az Azure-tervezetek lehetővé teszik a fejlesztői csapatoknak, hogy gyorsan építsenek és hozzanak létre új környezeteket a beépített összetevőkkel, valamint azt, hogy ezek a környezetek a szervezeti megfelelőségen belül legyenek létrehozva.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Tárolási szolgáltatások figyelése a működés közbeni váratlan változásokhoz
A felhőalapú környezetben üzemeltetett elosztott alkalmazások hibáinak diagnosztizálása és hibaelhárítása összetettebb lehet, mint a hagyományos környezetekben. Az alkalmazások a IaaS-infrastruktúrában, a helyszínen, a mobileszközön vagy a környezetek valamilyen kombinációjában is üzembe helyezhetők. Előfordulhat, hogy az alkalmazás hálózati forgalma nyilvános és magánhálózati hálózatokon halad át, és az alkalmazás több tárolási technológiát is használhat.

Folyamatosan figyelnie kell az alkalmazás által a működés közben felmerülő váratlan változásokra (például a lassabb válaszidő) vonatkozó tárolási szolgáltatásokat. A naplózás használatával részletesebb adatokat gyűjthet, és részletesen elemezheti a problémát. A monitorozási és naplózási adatokból beszerzett diagnosztikai információk segítségével meghatározható az alkalmazás által észlelt probléma kiváltó oka. Ezután hárítsa el a problémát, és határozza meg a megfelelő lépéseket a megoldásához.

[Azure Storage Analytics](../../storage/common/storage-analytics.md) naplózást végez, és metrikai adatokat biztosít egy Azure Storage-fiókhoz. Javasoljuk, hogy ezeket az adatokat a kérelmek nyomon követéséhez, a használati trendek elemzéséhez és a Storage-fiókkal kapcsolatos problémák diagnosztizálásához használja.

## <a name="prevent-detect-and-respond-to-threats"></a>Fenyegetések megelőzése, észlelése és elhárítása
[Azure Security Center](../../security-center/security-center-introduction.md) segít megakadályozni, észlelni és reagálni a fenyegetésekre azáltal, hogy megnövelte az Azure-erőforrások biztonságának növelését (és felügyeletét). Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetések között, segít felderíteni a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és különböző biztonsági megoldásokkal működnek.

A Security Center ingyenes szintje csak az Azure-erőforrások számára biztosít korlátozott biztonságot. A standard szint kiterjeszti ezeket a képességeket a helyszíni és egyéb felhőkre. A Security Center Standard segítségével megtalálhatja és elháríthatja a biztonsági réseket, a hozzáférési és alkalmazás-vezérlőket a kártékony tevékenységek blokkolására, az elemzések és az intelligencia használatával azonosíthatja a fenyegetéseket, és gyorsan reagálhat a támadásokra. Az első 60 napban díjmentesen próbálhatja ki a Security Center Standard verzióját. Javasoljuk, hogy az [Azure-előfizetését Security Center Standard verzióra frissítse](../../security-center/security-center-get-started.md).

Az Security Center segítségével megtekintheti az Azure-erőforrások biztonsági állapotának központi nézetét. Egy pillantással ellenőrizze, hogy a megfelelő biztonsági vezérlők megfelelően vannak-e konfigurálva, és hogy gyorsan azonosítson-e minden olyan erőforrást, amelyhez figyelmet igényel.

A Security Center a [Microsoft Defender komplex veszélyforrások elleni védelem (ATP)](../../security-center/security-center-wdatp.md)szolgáltatással is integrálható, amely átfogó végpont-észlelési és-reagálási (EDR) képességeket biztosít. A Microsoft Defender ATP-integrációval szokatlanokat lehet kimutatni. A Security Center által figyelt kiszolgálói végpontokon a speciális támadások észlelésére és reagálására is lehetőség van.

Szinte minden vállalati szervezet rendelkezik biztonsági információkkal és eseménykezelővel (SIEM), amely segít azonosítani az újonnan felmerülő fenyegetéseket azáltal, hogy összevonja a különböző Signal Gathering-eszközök naplójának adatait. A naplók elemzését egy adatelemzési rendszer elemzi, amely segít azonosítani, hogy milyen "érdekes" lehet a zaj, amely minden naplózási és elemzési megoldás esetében elkerülhetetlen.

Az [Azure Sentinel](../../sentinel/overview.md) egy skálázható, Felhőbeli natív, biztonsági információ-és rendezvényszervezés (SIEM) és biztonsági előkészítést szolgáló automatizált válasz (SOAR) megoldás. Az Azure Sentinel intelligens biztonsági elemzési és veszélyforrási intelligenciát biztosít a riasztások észlelése, a fenyegetések láthatósága, a proaktív vadászat és az automatizált veszélyforrások miatt.

Íme néhány ajánlott eljárás a fenyegetések megelőzéséhez, észleléséhez és megválaszolásához:

**Ajánlott eljárás**: az Siem-megoldás sebességének és méretezhetőségének növelése felhőalapú Siem használatával.   
**Részletek**: vizsgálja meg az [Azure Sentinel](../../sentinel/overview.md) funkcióit és képességeit, és hasonlítsa össze őket a jelenleg a helyszínen használt funkciókkal. Vegye fontolóra az Azure Sentinel bevezetését, ha az megfelel a szervezet SIEM-követelményeinek.

**Ajánlott eljárás**: keresse meg a legsúlyosabb biztonsági réseket, hogy rangsorolja a vizsgálatot.   
**Részletek**: Tekintse át az Azure-beli [biztonsági pontszámát](../../security-center/secure-score-security-controls.md) , és tekintse meg a Azure Security Center beépített Azure-szabályzatok és-kezdeményezések által létrehozott javaslatokat. Ezek a javaslatok segítenek a legfontosabb kockázatok, például a biztonsági frissítések, az Endpoint Protection, a titkosítás, a biztonsági konfigurációk, a hiányzó WAF, az internetkapcsolattal rendelkező virtuális gépek és sok más megoldás kezelésében.

A biztonságos pontszám, amely a Center for Internet Security (CIS) vezérlőkön alapul, lehetővé teszi, hogy a szervezet Azure-beli biztonsága a külső forrásokkal szemben is mérhető legyen. A külső érvényesítés segíti a csapat biztonsági stratégiájának érvényesítését és bővítését.

**Ajánlott eljárás**: a gépek, hálózatok, tárolási és adatszolgáltatások, valamint alkalmazások biztonsági állapotának monitorozása a lehetséges biztonsági problémák felderítése és rangsorolása érdekében.  
**Részletek**: kövesse a Security Center kezdődő [biztonsági javaslatokat](../../security-center/security-center-recommendations.md) a legmagasabb prioritású elemekkel.

**Ajánlott eljárás**: Security Center riasztások integrálása a biztonsági információk és az Event Management (SIEM) megoldásba.   
**Részletek**: a legtöbb Siem-et használó szervezet központi elszámolóházként használja az elemzői választ igénylő biztonsági riasztásokhoz. A Security Center által készített feldolgozott események közzé lesznek téve az Azure-tevékenység naplójában, amely az Azure Monitoron keresztül elérhető naplók egyike. A Azure Monitor összevont folyamatot biztosít a figyelési adatainak egy SIEM-eszközre történő átirányításához. Útmutatásért lásd: [stream-riasztások egy Siem, SOAR vagy IT Service Management megoldáshoz](../../security-center/export-to-siem.md) . Ha az Azure Sentinelt használja, tekintse meg a [Azure Security Center összekapcsolását](../../sentinel/connect-azure-security-center.md)ismertető témakört.

**Ajánlott eljárás**: az Azure-naplók integrálása a Siem-szel.   
**Részletek**: [Azure monitor használata adatok gyűjtésére és exportálására](../../azure-monitor/overview.md#integrate-and-export-data). Ez a gyakorlat kritikus fontosságú a biztonsági incidensek vizsgálatának engedélyezéséhez, és az online napló megtartásának korlátozása. Ha Azure Sentinelt használ, tekintse meg [az adatforrások összekapcsolását](../../sentinel/connect-data-sources.md)ismertető témakört.

**Ajánlott** eljárás: a vizsgálat és a vadászat folyamatának felgyorsítása, valamint a téves pozitív érték csökkentése a támadási vizsgálat során a végpont-észlelési és a reagálási (EDR) képességek integrálásával.   
**Részletek**: [a Microsoft Defender for Endpoint Integration engedélyezése](../../security-center/security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration) a Security Center biztonsági házirend segítségével. Vegye fontolóra az Azure Sentinel használatát a fenyegetések vadászatához és az incidensek megválaszolásához.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Végpontok közötti forgatókönyv-alapú Hálózatfigyelő figyelése
Az ügyfelek egy végpontok közötti hálózatot építenek ki az Azure-ban a hálózati erőforrások, például a virtuális hálózat, a ExpressRoute, a Application Gateway és a terheléselosztó kombinálásával. A figyelés minden hálózati erőforráson elérhető.

Az [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy regionális szolgáltatás. A diagnosztikai és vizualizációs eszközeivel figyelheti és diagnosztizálhatja a feltételeket a hálózati forgatókönyvek szintjén, az Azure-ban és az-ban.

A következő ajánlott eljárások a hálózati figyeléshez és a rendelkezésre álló eszközökhöz.

**Ajánlott eljárás**: a távoli hálózat figyelésének automatizálása a csomagok rögzítésével.  
**Részletek**: hálózati problémák figyelése és diagnosztizálása a virtuális gépekre való bejelentkezés nélkül Network Watcher használatával. A [csomagok rögzítése](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) a riasztások beállításával és a valós idejű teljesítményadatokat a csomagok szintjén érheti el. Ha problémát tapasztal, részletesen megvizsgálhatja a jobb diagnosztizálást.

**Ajánlott eljárás**: betekintést nyerhet a hálózati forgalomba a flow-naplók használatával.  
**Részletek**: a hálózati forgalom mintáinak mélyebb megismerése a [hálózati biztonsági csoport folyamatábráinak](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)használatával. A flow-naplókban található információk segítséget biztosítanak a hálózati biztonsági profil megfelelőségének, naplózásának és figyelésének adatainak gyűjtéséhez.

**Ajánlott eljárás**: a VPN-kapcsolati problémák diagnosztizálása.  
**Részletek**: Network Watcher használata a leggyakoribb [VPN Gateway és a kapcsolódási problémák diagnosztizálásához](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Nem csak a probléma azonosítására, hanem a részletes naplók használatára is lehetőség van a további vizsgálathoz.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Biztonságos üzembe helyezés bevált DevOps-eszközök használatával
A következő DevOps ajánlott eljárásokat követve biztosíthatja, hogy a vállalat és a csapatok hatékonyak és hatékonyak legyenek.

**Ajánlott eljárás**: a szolgáltatások kiépítésének és üzembe helyezésének automatizálása.  
**Részletek**: az [infrastruktúra mint kód](/azure/devops/learn/what-is-infrastructure-as-code) olyan technikák és gyakorlatok összessége, amelyek segítségével az informatikai szakemberek el tudnak távolítani a moduláris infrastruktúra napi felépítésének és kezelésének terheit. Lehetővé teszi az informatikai szakemberek számára, hogy modern kiszolgálói környezetet hozzanak létre és tartanak karban úgy, ahogy a szoftverfejlesztők az alkalmazás kódjának kiépítésével és karbantartásával kapcsolatosak.

Az alkalmazások deklaratív sablonnal történő kiépítéséhez [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) használható. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont használja az alkalmazás életciklusának minden fázisában történő ismételt üzembe helyezéséhez.

**Ajánlott eljárás**: az Azure Web Apps vagy a Cloud Services automatikus létrehozása és üzembe helyezése.  
**Részletek**: beállíthatja, hogy a Azure DevOps projects  [automatikusan létrejöjjön és üzembe helyezhető](/azure/devops/pipelines/index) az Azure Web Apps vagy a Cloud Services szolgáltatásban. Az Azure DevOps automatikusan telepíti a bináris fájlokat az Azure-hoz való kiépítést követően, miután minden kód bejelentkezett. A csomag-összeállítási folyamat egyenértékű a Visual Studióban található Package paranccsal, és a közzétételi lépések egyenértékűek a Visual Studióban található publish paranccsal.

**Ajánlott eljárás**: a kiadási felügyelet automatizálása.  
**Részletek**: az [Azure-folyamatok](/azure/devops/pipelines/index) egy megoldás a többfázisú üzembe helyezés automatizálására és a kiadási folyamat kezelésére. Felügyelt folyamatos üzembe helyezési folyamatokat hozhat létre gyorsan, egyszerűen és gyakran. Az Azure-folyamatok segítségével automatizálhatja a kiadási folyamatot, és előre definiált jóváhagyási munkafolyamatokat is használhat. A helyszíni és a felhő üzembe helyezése, bővítése és testreszabása szükség szerint.

**Ajánlott eljárás**: megtekintheti az alkalmazás teljesítményét, mielőtt elindítja vagy telepíti a frissítéseket az éles környezetbe.  
**Részletek**: felhőalapú [terhelési tesztek](/azure/devops/test/load-test/overview#alternatives) futtatása a következőre:

- Teljesítménnyel kapcsolatos problémák keresése az alkalmazásban.
- A központi telepítés minőségének javítása.
- Győződjön meg arról, hogy az alkalmazás mindig elérhető.
- Győződjön meg arról, hogy az alkalmazás képes a következő indítási vagy marketing kampány forgalmát kezelni.

Az [Apache JMeter](https://jmeter.apache.org/) egy ingyenes, népszerű nyílt forráskódú eszköz, amely erős közösségi háttérrel rendelkezik.

**Ajánlott eljárás**: az alkalmazás teljesítményének figyelése.  
**Részletek**: az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) egy bővíthető Application Performance Management-(APM-) szolgáltatás több platformon futó webes fejlesztőknek. Az élő webalkalmazás figyeléséhez használja a Application Insights. Automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket. Olyan elemzési eszközöket tartalmaz, amelyekkel diagnosztizálhatja a problémákat, és megtudhatja, hogy a felhasználók mit tesznek ténylegesen az alkalmazással. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

## <a name="mitigate-and-protect-against-ddos"></a>A DDoS elleni védelem enyhítése
Az elosztott szolgáltatásmegtagadás (DDoS) olyan támadási típus, amely megpróbálja kimeríteni az alkalmazás erőforrásait. A cél az alkalmazás rendelkezésre állásának és a legitim kérelmek kezelésére való képességének a befolyásolása. Ezek a támadások egyre kifinomultabbak és nagyobb méretekben és hatással vannak. Az interneten keresztül nyilvánosan elérhető végpontokat is megcélozhat.

A DDoS rugalmasság megtervezése és kiépítése számos különböző meghibásodási mód megtervezését és kialakítását igényli. A következő ajánlott eljárások az Azure-beli DDoS-rugalmasságú szolgáltatások létrehozásához.

**Ajánlott eljárás**: gondoskodjon arról, hogy a biztonság az alkalmazások teljes életciklusa alatt, a tervezéstől és a megvalósítástól az üzembe helyezésig és a műveletekig legyen prioritás. Az alkalmazások olyan hibákkal rendelkezhetnek, amelyek lehetővé teszik, hogy viszonylag alacsony mennyiségű kérést használjon sok erőforrás használatára, ami a szolgáltatás leállását eredményezi.  
**Részletek**: a Microsoft Azureon futó szolgáltatások védelmének elősegítése érdekében érdemes megismernie az alkalmazás architektúráját, és a [szoftver minőségének öt pillérére](/azure/architecture/guide/pillars)kell összpontosítania. Ismernie kell a jellemző adatforgalmi köteteket, a kapcsolati modellt az alkalmazás és más alkalmazások között, valamint a nyilvános interneten elérhető szolgáltatási végpontokat.

Fontos, hogy az alkalmazások elég rugalmasak legyenek ahhoz, hogy az alkalmazásra irányuló szolgáltatásmegtagadás ne legyen a legfontosabb. A biztonság és az adatvédelem az Azure platformra épül, a [biztonsági fejlesztési életciklussal (SDL)](https://www.microsoft.com/sdl)kezdve. Az SDL minden fejlesztési fázisban kezeli a biztonságot, és gondoskodik arról, hogy az Azure-t folyamatosan frissítsék, hogy még biztonságosabb legyen.

**Ajánlott eljárás**: Tervezze meg, hogy az alkalmazások [horizontálisan méretezhetők](/azure/architecture/guide/design-principles/scale-out) legyenek, hogy megfeleljenek egy felerősített terhelés igényének, különösen a DDOS-támadások esetén. Ha az alkalmazása egy szolgáltatás egyetlen példányán múlik, akkor az egyetlen meghibásodási pontot hoz létre. A több példány kiépítés révén a rendszerek rugalmasabbak és méretezhetők.  
**Részletek**: [Azure app Service](../../app-service/overview.md)esetében válasszon ki egy olyan [app Service tervet](../../app-service/overview-hosting-plans.md) , amely több példányt is kínál.

Az Azure Cloud Services esetében konfigurálja az egyes szerepköröket [több példány](../../cloud-services/cloud-services-choose-me.md)használatára.

Az [Azure Virtual Machines](../../virtual-machines/windows/overview.md)esetén győződjön meg arról, hogy a virtuálisgép-architektúra több virtuális gépet tartalmaz, és hogy minden virtuális gép egy [rendelkezésre állási csoportba](../../virtual-machines/windows/tutorial-availability-sets.md)tartozik. Javasoljuk, hogy használjon virtuálisgép-méretezési csoportokat az automatikus skálázási képességekhez.

**Ajánlott eljárás**: a biztonsági védelem egy alkalmazásban való rétegződése csökkenti a sikeres támadás esélyét. Hozzon létre biztonságos terveket az alkalmazásaihoz az Azure platform beépített képességeinek használatával.  
**Részletek**: a támadás kockázata az alkalmazás méretével (felszíni területével) növekszik. A felszín területét egy jóváhagyási lista használatával csökkentheti a kihelyezett IP-címtartomány és a terheléselosztó által nem szükséges portok figyelése ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) és az [Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md)).

A [hálózati biztonsági csoportok](../../virtual-network/network-security-groups-overview.md) egy másik módszer a támadási felület csökkentésére. A [szolgáltatási címkék](../../virtual-network/network-security-groups-overview.md#service-tags) és az [alkalmazás-biztonsági csoportok](../../virtual-network/network-security-groups-overview.md#application-security-groups) használatával csökkentheti a biztonsági szabályok létrehozásának összetettségét, és konfigurálhatja a hálózati biztonságot az alkalmazások struktúrájának természetes kiterjesztéseként.

Amikor csak lehetséges, üzembe kell helyeznie az Azure-szolgáltatásokat egy [virtuális hálózaton](../../virtual-network/virtual-networks-overview.md) . Ez a gyakorlat lehetővé teszi a szolgáltatási erőforrások számára, hogy magánhálózati IP-címeken keresztül kommunikáljanak egymással. A virtuális hálózatról származó Azure-szolgáltatási forgalom alapértelmezés szerint a nyilvános IP-címeket használja forrás IP-címként.

A [szolgáltatás-végpontok](../../virtual-network/virtual-network-service-endpoints-overview.md) használatával a szolgáltatás forgalma virtuális hálózati magánhálózati címeket használ forrás IP-címként, amikor egy virtuális hálózatról érik el az Azure-szolgáltatást.

Gyakran tekintjük meg az ügyfelek helyszíni erőforrásait az Azure-beli erőforrásaikkal együtt. Ha helyszíni környezetet csatlakoztat az Azure-hoz, csökkentse a helyszíni erőforrások a nyilvános internethez való hozzáférését.

Az Azure két DDoS [Service-ajánlattal](../../ddos-protection/ddos-protection-overview.md) rendelkezik, amelyek védelmet biztosítanak a hálózati támadásoktól:

- Alapértelmezés szerint az alapszintű védelem az Azure-ba van integrálva, felár nélkül. A globálisan üzembe helyezett Azure-hálózat skálázása és kapacitása védelmet nyújt a közös hálózati rétegbeli támadásokkal szemben a folyamatos forgalom monitorozása és a valós idejű mérséklés révén. Az alapszintű felhasználónak nincs szükség felhasználói konfigurációra vagy alkalmazásra, és megvédheti az összes Azure-szolgáltatást, beleértve a Pásti-szolgáltatásokat, mint a Azure DNS.
- A standard szintű védelem fejlett DDoS-elhárítási képességeket biztosít a hálózati támadásokkal szemben. A rendszer automatikusan hangolja az adott Azure-erőforrások megóvására. A védelem egyszerűen engedélyezhető a virtuális hálózatok létrehozása során. A létrehozás után is elvégezhető, és nem igényel alkalmazás-vagy erőforrás-módosítást.

## <a name="enable-azure-policy"></a>Azure Policy engedélyezése
[Azure Policy](../../governance/policy/overview.md) a szabályzatok létrehozásához, hozzárendeléséhez és kezeléséhez használt Azure-szolgáltatás. Ezek a szabályzatok kikényszerítik az erőforrásokra vonatkozó szabályokat és hatásokat, így ezek az erőforrások megfelelnek a vállalati szabványoknak és a szolgáltatói szerződéseknek. Az Azure Policy úgy tesz eleget ezeknek az elvárásoknak, hogy kiértékeli, megfelelnek-e az erőforrások a hozzájuk rendelt szabályzatoknak.

Engedélyezze Azure Policy a szervezet írásos szabályzatának figyelését és betartatását. Ez biztosítja a vállalat vagy a szabályozás biztonsági követelményeinek való megfelelést azáltal, hogy központilag kezeli a biztonsági szabályzatokat a hibrid felhőalapú számítási feladatokban. Ismerje meg, hogyan [hozhat létre és kezelhet szabályzatokat a megfelelőség érvényesítéséhez](../../governance/policy/tutorials/create-and-manage.md). A szabályzat elemeinek áttekintéséhez tekintse meg [Azure Policy definíciós struktúrát](../../governance/policy/concepts/definition-structure.md) .

Íme néhány ajánlott biztonsági eljárás, amelyet a Azure Policy elfogadása után követhet nyomon:

**Ajánlott eljárás**: a szabályzat többféle típusú effektust is támogat. Ezekről [Azure Policy definíciós struktúrában](../../governance/policy/concepts/definition-structure.md#policy-rule)olvashat. Az üzleti műveleteket negatívan érintheti a **megtagadási** hatás és a **szervizelési** hatás, ezért a **naplózási** hatás megadásával korlátozhatja a házirend negatív hatásának kockázatát.   
**Részletek**: [megkezdi a házirend központi telepítését a naplózási módban](../../governance/policy/concepts/definition-structure.md#policy-rule) , majd később **elutasítja** vagy **szervizelni** a folyamatot. A **Megtagadás** vagy a **szervizelés** előtt tesztelje és tekintse át a naplózási effektus eredményét.

További információ: [házirendek létrehozása és kezelése a megfelelőség kikényszerítása érdekében](../../governance/policy/tutorials/create-and-manage.md).

**Ajánlott eljárás**: a szabályzat megsértésének figyeléséhez felelős szerepkörök azonosítása, valamint a megfelelő szervizelési műveletek gyors elvégzésének biztosítása.   
**Részletek**: a hozzárendelt szerepkör-figyelő megfelelősége a [Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) vagy a [parancssoron](../../governance/policy/how-to/get-compliance-data.md#command-line)keresztül.

**Ajánlott eljárás**: Azure Policy a szervezet írásos szabályzatának technikai ábrázolása. Társítsa az összes Azure Policy definíciót a szervezeti házirendekhez, hogy csökkentse a zavart és növelje a konzisztenciát.   
**Részletek**: dokumentumok leképezése a szervezet dokumentációjában vagy maga a Azure Policy-definícióban a szervezeti házirendre mutató hivatkozás hozzáadásával a [házirend-definícióban](../../governance/policy/concepts/definition-structure.md#display-name-and-description) vagy a [kezdeményezés definíciójának](../../governance/policy/concepts/initiative-definition-structure.md#metadata) leírása.

## <a name="monitor-azure-ad-risk-reports"></a>Azure AD kockázati jelentések figyelése
A biztonsági rések túlnyomó többsége akkor kerül sor, amikor a támadók a felhasználó identitásának ellopásával hozzáférnek a környezetekhez. A feltört identitások feltárása nem egyszerű feladat. Az Azure AD adaptív gépi tanulási algoritmusokat és heurisztikus műveleteket használ a felhasználói fiókokhoz kapcsolódó gyanús műveletek észlelésére. A rendszer minden észlelt gyanús műveletet egy [kockázati észlelésnek](../../active-directory/identity-protection/overview-identity-protection.md)nevezett rekordban tárol. A kockázati észlelések az Azure AD biztonsági jelentéseiben vannak rögzítve. További információért olvassa el a [veszélyeztetett felhasználókról szóló jelentést](../../active-directory/identity-protection/overview-identity-protection.md) , valamint a [kockázatos bejelentkezések biztonsági jelentését](../../active-directory/identity-protection/overview-identity-protection.md).

## <a name="next-steps"></a>Következő lépések
Az Azure-beli felhőalapú megoldások tervezésekor, üzembe helyezése és kezelése során ajánlott biztonsági eljárásokat az [Azure biztonsági eljárásaival és modelljeivel](best-practices-and-patterns.md) foglalkozó témakörben talál.

Az Azure-biztonsággal és a kapcsolódó Microsoft-szolgáltatásokkal kapcsolatos általános információk az alábbi forrásokból érhetők el:
* Az [Azure Security csapatának blogja](/archive/blogs/azuresecurity/) – naprakész információk az Azure Security legújabb frissítéseiről
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – a Microsoft biztonsági rései, például az Azure-nal kapcsolatos problémák, jelentések vagy e-mailen keresztül secure@microsoft.com