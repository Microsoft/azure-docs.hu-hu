---
title: Azure Operational Security – áttekintés | Microsoft Docs
description: További információ az Azure működési biztonságáról ebben az áttekintésben. A működési biztonság az Asset Protection Services, a Controls és a features szolgáltatásra vonatkozik.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: 4bc30fbf342a9bc85b52c9f88ce7ca1df3c36e23
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595504"
---
# <a name="azure-operational-security-overview"></a>Azure Operational Security – áttekintés

Az [Azure Operational Security](./operational-security.md) a felhasználók számára elérhető szolgáltatásokat, vezérlőket és szolgáltatásokat jelenti a Microsoft Azureban tárolt adatok, alkalmazások és egyéb eszközök védelmére. Ez egy keretrendszer, amely magában foglalja a Microsoft számára egyedülálló különböző funkciókkal szerzett ismereteket. Ezek a képességek közé tartozik a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft Security Response Center program, valamint a kiberbiztonsági fenyegetések tájképének alapos ismerete.

## <a name="azure-management-services"></a>Azure felügyeleti szolgáltatások

Az informatikai operatív csapat felelős az adatközpont-infrastruktúra, az alkalmazások és az adatok kezeléséért, beleértve a rendszerek stabilitását és biztonságát. Ugyanakkor a növekvő összetett informatikai környezetekben egyre több biztonsági elemzésre van szükség, hogy a szervezetek több biztonsági és felügyeleti rendszerből származó adatokkal együtt macskakövesek.

A [Microsoft Azure a monitorozási naplók](../../azure-monitor/overview.md) egy felhőalapú informatikai felügyeleti megoldás, amely segít a helyszíni és a Felhőbeli infrastruktúra felügyeletében és kezelésében. Alapvető funkcióit az Azure-ban futó alábbi szolgáltatások biztosítják. Az Azure több szolgáltatást is tartalmaz, amelyek segítenek a helyszíni és a Felhőbeli infrastruktúra kezelésében és védelmében. Minden szolgáltatás egy adott felügyeleti funkciót biztosít. A szolgáltatások kombinálása különböző felügyeleti forgatókönyvek elérésére használható. 

### <a name="azure-monitor"></a>Azure Monitor

A [Azure monitor](../../azure-monitor/overview.md) a felügyelt forrásokból származó adatokat központi adattárba gyűjti. Ezek az információk lehetnek az API-n keresztül biztosított események, teljesítményadatok vagy egyéni adat. Az adatgyűjtés után a rendszer riasztást, elemzést és exportálást is biztosít.

Több forrásból is összevonhatja az adatait, és az Azure-szolgáltatásokból származó adatok egyesíthetők a meglévő helyszíni környezettel. Azure Monitor naplók is egyértelműen elkülönítik az adatok gyűjtését az adatokon végrehajtott műveletből, hogy minden művelet elérhető legyen minden típusú adatok számára.

### <a name="automation"></a>Automation

A [Azure Automation](../../automation/automation-intro.md) lehetővé teszi a manuális, hosszan futó, hibákra hajlamos és gyakran ismétlődő feladatok automatizálását, amelyeket gyakran hajtanak végre a felhőben és a nagyvállalati környezetben. Időt takaríthat meg, és növeli a felügyeleti feladatok megbízhatóságát. Ez a feladat rendszeres időközönként automatikusan is elvégezhető. A runbookok használatával automatizálhatja a folyamatokat, vagy automatizálhatja a konfigurálást a kívánt állapot konfigurációjának használatával.

### <a name="backup"></a>Backup

[Azure Backup](../../backup/backup-overview.md) az Azure-alapú szolgáltatás, amellyel biztonsági mentést készíthet (vagy védelemmel láthat el), és visszaállíthatja az adatait a Microsoft Cloudban. Azure Backup lecseréli a meglévő helyszíni vagy külső biztonsági mentési megoldását egy megbízható, biztonságos és költséghatékony felhőalapú megoldással.

A Azure Backup a megfelelő számítógépen vagy kiszolgálón, vagy a felhőben letöltheti és telepítheti az összetevőket. A telepítendő összetevő vagy ügynök attól függ, hogy mit szeretne megvédeni. Az összes Azure Backup-összetevő (függetlenül attól, hogy a helyszíni vagy a felhőben tárolt adatok védelmére alkalmas-e) az adatok biztonsági mentését egy Azure Recovery Services-tárolóba az Azure-ban.

További információ: [Azure Backup Components tábla](../../backup/backup-overview.md#what-can-i-back-up).

### <a name="site-recovery"></a>Site Recovery

A [Azure site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) üzleti folytonosságot biztosít a helyszíni virtuális és fizikai gépek Azure-ba vagy egy másodlagos helyre történő replikálásával. Ha az elsődleges hely nem érhető el, a rendszer átadja a feladatokat a másodlagos helynek, így a felhasználók továbbra is dolgozhatnak. A rendszer visszaadja a feladatokat, amikor a rendszerek visszatérnek a munkarendbe. Az intelligensebb és hatékony veszélyforrások észlelése Azure Security Center használatával.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure ad)](../../active-directory/manage-apps/what-is-application-management.md) egy átfogó identitás-szolgáltatás, amely a következőket nyújtja:

-   Az identitás-és hozzáférés-kezelés (IAM) engedélyezése felhőalapú szolgáltatásként.
-   Központi hozzáférés-kezelést, egyszeri bejelentkezést (SSO) és jelentéskészítést biztosít.
-   A támogatja az Azure piactéren [futó több ezer alkalmazás](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) integrált hozzáférés-vezérlését, beleértve a Salesforce, a Google Apps, a Box és a egybeesik.

Az Azure AD az [Identitáskezelés teljes funkcionalitását](./identity-management-overview.md#security-monitoring-alerts-and-machine-learning-based-reports)is tartalmazza, beleértve a következőket:

- [Többtényezős hitelesítés](../../active-directory/authentication/concept-mfa-howitworks.md)
- [Önkiszolgáló jelszókezelés](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Önkiszolgáló csoport kezelése](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
- [Kiemelt jogosultságú fiókok felügyelete](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)
- [Alkalmazás-használat figyelése](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Gazdag naplózás](../../active-directory/reports-monitoring/concept-audit-logs.md)
- [Biztonsági monitorozás és riasztások](../../security-center/security-center-managing-and-responding-alerts.md)

Azure Active Directory a partnereknek és ügyfeleknek (üzleti vagy fogyasztói) közzétett összes alkalmazás ugyanazzal az identitás-és hozzáférés-kezelési képességekkel rendelkezik. Ez lehetővé teszi a működési költségek jelentős csökkentését.

## <a name="azure-security-center"></a>Azure Security Center

A [Azure Security Center](../../security-center/security-center-introduction.md) a fenyegetések megelőzését, észlelését és elhárítását segíti az Azure-erőforrások biztonságának jobb láthatóságával (és szabályozásával). Integrált biztonsági monitorozást és házirend-kezelést biztosít az előfizetések között. Segít felderíteni a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és a biztonsági megoldások széles körű ökoszisztémával működnek.

A [virtuális gép (VM) adatai védelme](../../security-center/security-center-introduction.md) az Azure-ban a virtuális gép biztonsági beállításainak és a fenyegetések figyelésének biztosításával. A Security Center a következőket tudja megfigyelés alatt tartani a virtuális gépeken:

- Az operációs rendszer biztonsági beállításai az ajánlott konfigurációs szabályokkal.
- Hiányzó rendszerbiztonság és kritikus frissítések.
- Endpoint Protection-javaslatok.
- Lemez titkosításának ellenőrzése.
- Hálózati támadások.

Security Center [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)használ. Az Azure RBAC olyan [beépített szerepköröket](../../role-based-access-control/built-in-roles.md) biztosít, amelyek a felhasználókhoz, csoportokhoz és szolgáltatásokhoz rendelhetők az Azure-ban.

Security Center megvizsgálja az erőforrások konfigurációját a biztonsági problémák és a biztonsági rések azonosítása érdekében. Security Center az erőforrásokhoz kapcsolódó információkat csak akkor látja, ha a tulajdonos, közreműködő vagy olvasó szerepkörét rendelte hozzá ahhoz az előfizetéshez vagy erőforráscsoporthoz, amelyhez az erőforrás tartozik.

>[!Note]
>Ha többet szeretne megtudni a Security Center szerepköreiről és az engedélyezett műveletekről, tekintse meg a [Azure Security Center engedélyeit](../../security-center/security-center-permissions.md).

Security Center a Microsoft monitoring agentet használja. Ez ugyanaz az ügynök, amelyet a Azure Monitor szolgáltatás használ. Az ettől az ügynöktől gyűjtött adatok tárolása az Azure-előfizetéshez vagy egy új munkaterülethez társított meglévő Log Analytics [munkaterületen](../../azure-monitor/logs/manage-access.md) történik, a virtuális gép földrajzi helyének figyelembevételével.

## <a name="azure-monitor"></a>Azure Monitor

A felhőalapú alkalmazás teljesítményével kapcsolatos problémák befolyásolhatják a vállalatot. Több összekapcsolt összetevővel és gyakori kiadásokkal a romlások bármikor megtörténhetnek. Ha pedig fejleszt egy alkalmazást, a felhasználók általában felderítik a tesztelés során nem talált problémákat. Ezeket a problémákat azonnal ismernie kell, és a problémák diagnosztizálásához és javításához szükséges eszközöket kell használnia.

[Azure monitor](../../azure-monitor/overview.md) az Azure-on futó szolgáltatások figyelésének alapvető eszköze. Infrastruktúra-szintű adatokkal szolgál a szolgáltatás és a környező környezet teljesítményéről. Ha az összes Azure-ban kezeli az alkalmazásait, és eldönti, hogy az erőforrások vertikális fel-vagy leskálázása, Azure Monitor az indulási hely.

A figyelési adatait is használhatja az alkalmazással kapcsolatos részletes elemzések megszerzéséhez. Ezzel az ismerettel javíthatja az alkalmazások teljesítményét vagy karbantartását, vagy automatizálhatja azokat a műveleteket, amelyek egyébként manuális beavatkozást igényelnek.

Azure Monitor a következő összetevőket tartalmazza.

### <a name="azure-activity-log"></a>Azure-tevékenységnapló

Az [Azure-tevékenység naplója](../../azure-monitor/essentials/platform-logs-overview.md) betekintést nyújt az előfizetése erőforrásaiban végrehajtott műveletekre. Korábban "naplónak" vagy "operatív naplónak" nevezték, mert az előfizetések vezérlési sík eseményeit jelenti.

### <a name="azure-diagnostic-logs"></a>Azure diagnosztikai naplók

Az [Azure diagnosztikai naplókat](../../azure-monitor/essentials/platform-logs-overview.md) egy erőforrás bocsátja ki, és az adott erőforrás működésével kapcsolatos gazdag és gyakori információkat biztosít. A naplók tartalma az erőforrástípus alapján változik.

A Windows-események rendszernaplói a virtuális gépek diagnosztikai naplóinak egyik kategóriája. A blob-, tábla-és üzenetsor-naplók a tárolási fiókok diagnosztikai naplófájljainak kategóriái.

A diagnosztikai naplók eltérnek a [tevékenység naplójától](../../azure-monitor/essentials/platform-logs-overview.md). A tevékenység naplója betekintést nyújt az előfizetése erőforrásaiban végrehajtott műveletekre. A diagnosztikai naplók betekintést nyújtanak az erőforrás által végrehajtott műveletekre.

### <a name="metrics"></a>Mérőszámok

A Azure Monitor telemetria biztosít az Azure-beli számítási feladatok teljesítményének és állapotának áttekintéséhez. Az Azure telemetria-adatok legfontosabb típusa a legtöbb Azure-erőforrás által kibocsátott [mérőszámok](../../azure-monitor/data-platform.md) (más néven teljesítményszámlálók). A Azure Monitor számos módszert biztosít ezen mérőszámok konfigurálásához és felhasználásához a figyeléshez és a hibaelhárításhoz.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Azure Diagnostics lehetővé teszi a diagnosztikai adatgyűjtést egy telepített alkalmazáson. A diagnosztikai bővítményt különböző forrásokból is használhatja. Jelenleg támogatott az [Azure Cloud Service-szerepkörök](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), a Microsoft Windowst futtató Azure-beli [virtuális gépek](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) és az [Azure Service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Az ügyfelek egy végpontok közötti hálózatot építenek ki az Azure-ban az egyes hálózati erőforrások, például a virtuális hálózatok, az Azure ExpressRoute, az Azure Application Gateway és a terheléselosztó összehangolása és összeállítása során. A figyelés minden hálózati erőforráson elérhető.

A végpontok közötti hálózat összetett konfigurációkkal és az erőforrások közötti interakcióval is rendelkezhet. Az eredmény olyan összetett forgatókönyvek, amelyeken az [Azure Network Watcheron](../../network-watcher/network-watcher-monitoring-overview.md)keresztüli forgatókönyv-alapú figyelésre van szükség.

Network Watcher leegyszerűsíti az Azure-hálózat figyelését és diagnosztizálását. A Network Watcher diagnosztikai és vizualizációs eszközeivel a következőket végezheti el:

- Távoli csomagok rögzítése egy Azure-beli virtuális gépen.
- Betekintést nyerhet a hálózati forgalomba a flow-naplók használatával.
- Az Azure VPN Gateway és kapcsolatainak diagnosztizálása.

A Network Watcher jelenleg a következő képességekkel rendelkezik:

- [Topológia](../../network-watcher/view-network-topology.md): az erőforráscsoport hálózati erőforrásai közötti különböző összekapcsolások és társítások nézetét jeleníti meg.
- [Változó csomagok rögzítése](../../network-watcher/network-watcher-packet-capture-overview.md): a rendszer a virtuális gépeken belül és kívül rögzíti a csomagok mennyiségét. A fejlett szűrési lehetőségek és finomhangolt vezérlők, mint például az idő- és méretkorlátok megadásának képessége, sokoldalúságot biztosít. A csomagok tárolása blob-tárolóban vagy a helyi lemezen. Cap formátumban történhet.
- [IP-forgalom ellenőrzése](../../network-watcher/network-watcher-ip-flow-verify-overview.md): ellenőrzi, hogy egy csomag engedélyezett vagy letiltott-e a flow adatainak 5 rekordos paraméterei alapján (cél IP-címe, forrás IP-címe, célport, forrásport és protokoll). Ha egy biztonsági csoport megtagadja a csomagot, a rendszer a csomagot megtagadó szabályt és csoportot adja vissza.
- [Következő ugrás](../../network-watcher/network-watcher-next-hop-overview.md): az Azure hálózati hálóban köröztetett csomagok következő ugrását határozza meg, így bármilyen helytelenül konfigurált, felhasználó által megadott útvonalat diagnosztizálhat.
- [Biztonsági csoport nézet](../../network-watcher/network-watcher-security-group-view-overview.md): a virtuális gépen alkalmazott hatályos és alkalmazott biztonsági szabályok beolvasása.
- [Hálózati biztonsági csoportok NSG](../../network-watcher/network-watcher-nsg-flow-logging-overview.md): lehetővé teszi a csoport biztonsági szabályai által engedélyezett vagy megtagadott forgalomhoz kapcsolódó naplók rögzítését. A folyamat 5 rekordos információval van definiálva: forrás IP-címe, cél IP-címe, forrásport, célport és protokoll.
- A [virtuális hálózati átjáró és a kapcsolat hibaelhárítása](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): lehetővé teszi a virtuális hálózati átjárók és kapcsolatok hibaelhárítását.
- [Hálózati előfizetés korlátai](../../network-watcher/network-watcher-monitoring-overview.md): lehetővé teszi a hálózati erőforrás-használat korlátozásait.
- [Diagnosztikai naplók](../../network-watcher/network-watcher-monitoring-overview.md): egyetlen panelen engedélyezheti vagy letilthatja az erőforráscsoport hálózati erőforrásaihoz tartozó diagnosztikai naplókat.

További információ: [Configure Network Watcher](../../network-watcher/network-watcher-create.md).

## <a name="cloud-service-provider-access-transparency"></a>Felhőalapú szolgáltatói hozzáférés átlátszósága

A [Ügyfélszéf for Microsoft Azure](customer-lockbox-overview.md) egy olyan szolgáltatás, amely Azure Portalba integrált, amely explicit módon szabályozza a ritka példányokat, amikor egy Microsoft ügyfélszolgálata mérnöknek szüksége lehet az adataihoz való hozzáférésre a probléma megoldásához.
Nagyon kevés példány létezik, például egy távoli elérési probléma, amelyben egy Microsoft ügyfélszolgálata mérnök emelt szintű engedélyeket igényel a probléma megoldásához. Ilyen esetekben a Microsoft-mérnökök igény szerinti hozzáférési szolgáltatást használnak, amely korlátozott, időkorlátos engedélyezést biztosít a szolgáltatásra korlátozott hozzáféréssel.  
Noha a Microsoft mindig megszerezte az ügyfelek hozzáférését, Ügyfélszéf mostantól lehetővé teszi az Azure Portalról érkező kérések áttekintését és jóváhagyását, illetve elutasítását. A Microsoft támogatási szakemberei addig nem kapnak hozzáférést, amíg jóvá nem hagyja a kérést.

## <a name="standardized-and-compliant-deployments"></a>Szabványosított és megfelelő központi telepítések

Az [Azure-tervezetek](../../governance/blueprints/overview.md) lehetővé teszik a felhő-építészek és a központi informatikai csoportok számára, hogy meghatározhatnak egy olyan Azure-erőforrást, amely megvalósítja és betartja a szervezet szabványait, mintáit és követelményeit.  
Ez lehetővé teszi, hogy a DevOps csapatok gyorsan építsenek és hozzanak létre új környezeteket, és bíznak abban, hogy a szervezeti megfelelőséget fenntartó infrastruktúrával építik ki őket.
A tervrajzok deklaratív módszert biztosítanak különböző erőforrás-sablonok és egyéb összetevők üzembe helyezéséhez, például:

- Szerepkör-hozzárendelések
- Szabályzat-hozzárendelések
- Azure Resource Manager-sablonok
- Erőforráscsoportok

## <a name="devops"></a>DevOps

A [fejlesztői műveletek (DevOps-alkalmazások)](https://azure.microsoft.com/overview/what-is-devops/) fejlesztése előtt a csapatok feladata a szoftverek üzleti követelményeinek összegyűjtése és a kód írása. Ezután egy különálló QA-csapat tesztelte a programot egy elkülönített fejlesztői környezetben. Ha teljesülnek a követelmények, a QA csapat kiadta a programkódot a telepítendő műveletekhez. Az üzembe helyezési csapatokat további csoportokba osztottuk, például hálózatkezelésre és adatbázisra. Minden alkalommal, amikor egy szoftver egy különálló csapatnak lett kiváltva, a rendszer szűk keresztmetszeteket adott hozzá.

A DevOps lehetővé teszi, hogy a csapatok gyorsabban és olcsóbban nyújtsanak biztonságosabb, magasabb színvonalú megoldásokat. Az ügyfelek dinamikus és megbízható élményt várnak a szoftverek és szolgáltatások fogyasztása során. A csapatoknak gyorsan meg kell ismételni a szoftverfrissítéseket, és mérniük kell a frissítések hatását. A problémák megoldásához és a további értékek megadásához gyorsan reagálni kell az új fejlesztési iterációkkal.  

A Felhőbeli platformok, például a Microsoft Azure a hagyományos szűk keresztmetszeteket távolították el, és segítették a commoditize-infrastruktúrát. A szoftver minden üzletágban meguralkodik, mint a kulcsfontosságú differenciálás és az üzleti eredményekben felmerülő tényező. Nincs szervezet, fejlesztő vagy informatikai dolgozó, vagy el kell kerülnie a DevOps mozgását.

Az érett DevOps-szakemberek az alábbi eljárások közül többet is elfogadnak. Ezek a [gyakorlatok a stratégiákat az üzleti](/azure/devops/learn/what-is-devops-culture) forgatókönyvek alapján alkotják. Az eszközök megkönnyítik a különböző eljárások automatizálását.

- Az [agilis tervezési és projektmenedzsment](https://www.visualstudio.com/learn/what-is-agile/) technikák használatával megtervezheti és elkülönítheti a munkafolyamatokat a Sprint szolgáltatásban, kezelheti a csapat kapacitását, és a csoportok gyorsan alkalmazkodnak a változó üzleti igényekhez.
- A [verziókövetés általában a git](/azure/devops/learn/git/what-is-git)használatával lehetővé teszi a világ bármely pontján található csapatok számára, hogy megosszák a forrást, és integrálják a szoftverfejlesztői eszközöket a kiadási folyamat automatizálására.
- A [folyamatos integráció](/azure/devops/learn/what-is-continuous-integration) vezérli a kód folyamatos egyesítését és tesztelését, amely a hibák korai megkeresését eredményezi.  Az egyéb előnyök kevesebb időt vesznek igénybe az egyesítéssel kapcsolatos problémák és a fejlesztési csapatok gyors visszajelzése miatt.
- A szoftverek üzemi és tesztelési környezetekhez való [folyamatos továbbítása](/azure/devops/learn/what-is-continuous-delivery) segít a szervezeteknek a hibák javításában és a folyamatosan változó üzleti követelmények megválaszolásában.
- Futó alkalmazások [figyelése](/azure/devops/learn/what-is-monitoring) – beleértve az alkalmazások állapotának éles környezetét, valamint az ügyfelek használatát – segít a szervezeteknek hipotézist alkotni, és gyorsan érvényesíteni vagy megcáfolni a stratégiákat.  A rendszer a különböző naplózási formátumokban rögzíti és tárolja a gazdag adattárolást.
- Az [infrastruktúra mint kód (IaC)](/azure/devops/learn/what-is-infrastructure-as-code) olyan gyakorlat, amely lehetővé teszi a hálózatok és virtuális gépek létrehozásának és Teardown automatizálását és érvényesítését, így segítve a biztonságos, stabil alkalmazás-üzemeltetési platformok nyújtását.
- A [szolgáltatás](/azure/devops/learn/what-are-microservices) architektúrája az üzleti használati esetek kis újrafelhasználható szolgáltatásokra való elkülönítésére szolgál.  Ez az architektúra lehetővé teszi a méretezhetőséget és a hatékonyságot.

## <a name="next-steps"></a>Következő lépések

A Security and Audit megoldással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Biztonság és megfelelőség](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](../../security-center/security-center-introduction.md)
- [Azure Monitor](../../azure-monitor/overview.md)