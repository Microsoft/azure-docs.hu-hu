---
title: Az Azure-beli eszközök ajánlott biztonsági gyakorlatai
titleSuffix: Azure security
description: Ez a cikk üzemeltetési ajánlott eljárásokat tartalmaz az adatok, alkalmazások és egyéb Azure-beli eszközök védelméhez.
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
ms.openlocfilehash: 6634a536828b3c19d771d135fdb3a1224d3dfdf3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717422"
---
# <a name="azure-operational-security-best-practices"></a>Azure Operational Security – ajánlott eljárások
Ez a cikk üzemeltetési ajánlott eljárásokat tartalmaz az adatok, alkalmazások és egyéb Azure-beli eszközök védelméhez.

Az ajánlott eljárások a vélemények megegyezésére épülnek, és az Azure platform jelenlegi képességeivel és funkciókészletekkel működnek együtt. A vélemények és technológiák idővel változnak, és a cikk rendszeresen frissül, hogy tükrözze ezeket a változásokat.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Erős működési biztonsági eljárások meghatározása és üzembe helyezése
Az Azure működési biztonsága azokat a szolgáltatásokat, vezérlőket és funkciókat jelenti, amelyek a felhasználók számára az adatok, alkalmazások és egyéb Azure-beli eszközök védelmére szolgálnak. Az Azure működési biztonsága egy olyan keretrendszerre épül, amely magában foglalja a Microsoft egyedi képességeivel szerzett ismereteket, beleértve a biztonsági fejlesztési [életciklust (SDL),](https://www.microsoft.com/sdl)az [Microsoft Biztonsági reagálási központ-programot](https://www.microsoft.com/msrc?rtc=1) és a kiberbiztonsági fenyegetések környezetének mélyebb ismeretét.

## <a name="manage-and-monitor-user-passwords"></a>Felhasználói jelszavak kezelése és figyelése
Az alábbi táblázat a felhasználói jelszavak kezelésével kapcsolatos ajánlott eljárásokat sorolja fel:

**Ajánlott eljárás:** Győződjön meg arról, hogy a megfelelő szintű jelszóvédelem van a felhőben.   
**Részletek:** Kövesse a [Microsoft](https://www.microsoft.com/research/publication/password-guidance/)jelszó-útmutató útmutatását, amely a Microsoft identitásplatformjainak felhasználóira terjed ki (Azure Active Directory, Active Directory és Microsoft-fiók).

**Ajánlott eljárás:** Figyelje a felhasználói fiókokhoz kapcsolódó gyanús műveleteket.   
**Részletek:** A veszélyeztetett [felhasználók](../../active-directory/identity-protection/overview-identity-protection.md) és a kockázatos bejelentkezések figyelése az Azure AD biztonsági jelentésekkel. [](../../active-directory/identity-protection/overview-identity-protection.md)

**Ajánlott eljárás:** A magas kockázatú jelszavak automatikus észlelése és szervize.   
**Részletek:** [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) A prémium szintű Azure AD P2 kiadás egyik funkciója, amely a következő funkciókat teszi lehetővé:

- A szervezet identitását érintő lehetséges biztonsági rések észlelése
- Automatikus válaszok konfigurálása a szervezet identitásával kapcsolatos gyanús műveletek észlelésére
- Gyanús incidensek kivizsgálása és a megoldásukhoz szükséges lépések meghozása

## <a name="receive-incident-notifications-from-microsoft"></a>Incidensértesítések fogadása a Microsofttól
Győződjön meg arról, hogy a biztonsági üzemeltetési csapat megkapja a Microsoft Azure-incidensekkel kapcsolatos értesítését. Az incidensértesítések tudatják a biztonsági csapattal, hogy feltörte az Azure-erőforrásokat, így gyorsan reagálhat a lehetséges biztonsági kockázatokra, és orvosolhatja őket.

Az Azure regisztrációs portálon biztosíthatja, hogy a rendszergazdai kapcsolattartási adatok tartalmazzák a biztonsági műveleteket értesítő adatokat. A kapcsolattartási adatok egy e-mail-cím és egy telefonszám.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Azure-előfizetések rendszerezése felügyeleti csoportokba
Ha a cég több előfizetéssel rendelkezik, szüksége lehet egy hatékony módszerre az előfizetések hozzáférésének, szabályzatainak és megfelelőségének kezelésére. [Az Azure-beli](../../governance/management-groups/create-management-group-portal.md) felügyeleti csoportok az előfizetések fölötti hatókörszintet biztosítanak. Az előfizetéseket felügyeleti csoportnak nevezett tárolókban rendezi, és ezekre a csoportokra alkalmazza a szabályozási feltételeket. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott feltételeket.

A felügyeleti csoportok és előfizetések rugalmas szerkezetét egy címtárba építheti be. Minden címtárhoz tartozik egy legfelső szintű felügyeleti csoport, a gyökérszintű felügyeleti csoport. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. A gyökérszintű felügyeleti csoport lehetővé teszi a globális szabályzatok és az Azure-beli szerepkör-hozzárendelések címtárszinten való alkalmazását.

Íme néhány ajánlott eljárás a felügyeleti csoportok használatával kapcsolatban:

**Ajánlott eljárás:** Győződjön meg arról, hogy az új előfizetések olyan irányítási elemeket alkalmaznak, mint a szabályzatok és engedélyek a hozzáadásukkor.   
**Részletek:** A gyökér felügyeleti csoporttal az összes Azure-eszközre érvényes vállalati szintű biztonsági elemeket rendelhet hozzá. A szabályzatok és engedélyek példák elemekre.

**Ajánlott eljárás:** A felügyeleti csoportok legfelső szintjeinek igazítása a szegmentációs stratégiához, hogy az egyes szegmensek vezérlési és szabályzatkonzisztenciáját biztosítsa.   
**Részletek:** Hozzon létre egy felügyeleti csoportot minden szegmenshez a gyökér felügyeleti csoport alatt. Ne hozzon létre más felügyeleti csoportokat a gyökér alatt.

**Ajánlott eljárás:** Korlátozza a felügyeleti csoportok mélységét a műveleteket és a biztonságot egyaránt akadályozó félreértések elkerülése érdekében.   
**Részletek:** Korlátozza a hierarchiát három szintre, beleértve a gyökérszintet is.

**Ajánlott eljárás:** Gondosan válassza ki a gyökér felügyeleti csoporttal együtt a teljes vállalatra alkalmazni kívánt elemeket.   
**Részletek:** Győződjön meg arról, hogy a gyökér felügyeleti csoport elemeit egyértelműen alkalmazni kell minden erőforrásra, és hogy azok alacsony hatással vannak.

A megfelelő jelöltek közé tartoznak a következők:

- Egyértelmű üzleti hatást gyakoroló szabályozási követelmények (például az adatok önállóságával kapcsolatos korlátozások)
- Olyan követelmények, amelyek nulla vagy akár negatív hatással lehetnek a műveletekre, például naplózással rendelkező szabályzat vagy az Azure RBAC engedély-hozzárendelései, amelyek alaposan át vannak nézve

**Ajánlott eljárás:** Az összes vállalati szintű módosítást alaposan meg kell tervezni és tesztelni kell a gyökér felügyeleti csoporton azok alkalmazása előtt (szabályzat, Azure RBAC-modell stb.).   
**Részletek:** A gyökér felügyeleti csoport módosításai hatással lehetnek az Azure összes erőforrásra. Bár hatékony lehetőséget biztosítanak a vállalaton belül a konzisztencia biztosítására, a hibák vagy a helytelen használat negatív hatással lehet az éles műveletekre. Tesztelje a gyökér felügyeleti csoport módosításait egy tesztlaborban vagy éles tesztkörnyezetben.

## <a name="streamline-environment-creation-with-blueprints"></a>Környezetek létrehozásának egyszerűsítése tervekkel
[Az Azure Blueprints](../../governance/blueprints/overview.md) szolgáltatás lehetővé teszi a felhőszolgáltatás-tervezők és központi információs technológiai csoportok számára, hogy megismételhető Azure-erőforrásokat határozzanak meg, amelyek megvalósítják és betartják a szervezet szabványait, mintáit és követelményeit. Azure Blueprints lehetővé teszi a fejlesztői csapatok számára, hogy gyorsan építsenek ki és állítsanak fel új környezeteket beépített összetevőkkel, és biztos abban, hogy ezeket a környezeteket a szervezeti megfelelőségen belül létrehozására használják.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>A tárolási szolgáltatások figyelése a viselkedés váratlan változásainak ellenőrzésére
A felhőalapú környezetben üzemeltetett elosztott alkalmazások problémáinak diagnosztizálása és hibaelhárítása összetettebb lehet, mint a hagyományos környezetekben. Az alkalmazások üzembe helyezhetők PaaS- vagy IaaS-infrastruktúrában, helyszínen, mobileszközön vagy ezek valamilyen kombinációjában. Az alkalmazás hálózati forgalma nyilvános és privát hálózatokon is áthaladhat, és az alkalmazás több tárolási technológiát is használhat.

Folyamatosan figyelnie kell az alkalmazás által használt tárolási szolgáltatásokat a viselkedés váratlan változásaihoz (például lassabb válaszidőkhez). Naplózással részletesebb adatokat gyűjthet, és részletesen elemezheti a problémákat. A monitorozásból és naplózásból származó diagnosztikai információk segítenek meghatározni az alkalmazás által észlelt probléma kiváltó okát. Ezután elháríthatja a problémát, és meghatározhatja a megfelelő lépéseket a megoldásához.

[Azure Storage Analytics](../../storage/common/storage-analytics.md) naplózást végez, és metrikák adatait szolgáltatja egy Azure-tárfiókhoz. Javasoljuk, hogy ezeket az adatokat használja a kérelmek nyomkövetéséhez, a használati trendek elemzéséhez és a tárfiókkal kapcsolatos problémák diagnosztizálásához.

## <a name="prevent-detect-and-respond-to-threats"></a>Fenyegetések megelőzése, észlelése és válaszadás a fenyegetésekre
[Azure Security Center](../../security-center/security-center-introduction.md) segít megelőzni, észlelni és reagálni a fenyegetésekre azáltal, hogy nagyobb betekintést (és irányítást) biztosít az Azure-erőforrások biztonságába. Integrált biztonsági monitorozást és szabályzatkezelést biztosít az Összes Azure-előfizetésben, segít észlelni a fenyegetéseket, amelyek egyébként észrevétlenül maradnak, és különböző biztonsági megoldásokkal is együttműködik.

Az ingyenes Security Center csak az Azure-erőforrások számára nyújt korlátozott biztonságot. A Standard szint ezeket a képességeket kiterjeszti a helyszíni és más felhőkre. Security Center Standard segítségével megkeresheti és kijavíthatja a biztonsági réseket, hozzáférés- és alkalmazásvezérlőket alkalmazhat a kártékony tevékenységek blokkolására, észlelheti a fenyegetéseket elemzéssel és intelligenciával, és gyorsan reagálhat, ha támadás alatt áll. Az első 60 napban díjmentesen próbálhatja ki a Security Center Standard verzióját. Javasoljuk, hogy frissítse [Azure-előfizetését a Security Center standard verzióra.](../../security-center/security-center-get-started.md)

A Security Center megtekintheti az összes Azure-erőforrás biztonsági állapotát. Első pillantásra ellenőrizze, hogy a megfelelő biztonsági vezérlők működnek-e és megfelelően vannak-e konfigurálva, és gyorsan azonosítson minden figyelmet igényelő erőforrást.

Security Center a Microsoft Defender [Advanced Threat Protection (ATP)](../../security-center/security-center-wdatp.md)alkalmazással is integrálható, amely átfogó végpontészlelési és -válaszképességet biztosít. A Microsoft Defender ATP integrációjával észlelheti a rendellenességeket. Emellett észlelheti a monitorozási szolgáltatás által figyelt kiszolgálóvégpontokat, és reagálhat Security Center.

Szinte minden nagyvállalatnak van biztonsági információs és eseménykezelési (SIEM) rendszere, amely segít azonosítani a felmerülő fenyegetéseket azáltal, hogy összevonja a különböző jelgyűjtő eszközök naplóinformációit. A naplókat ezután egy adatelemzési rendszer elemzi, hogy azonosítsa, mi az "érdekes" a zajból, amely minden naplógyűjtési és elemzési megoldásban elkerülhetetlen.

[Azure Sentinel](../../sentinel/overview.md) egy skálázható, natív felhőbeli, biztonsági információk és események kezelése (SIEM) és biztonsági vezénylési automatizált válaszadási (SOAR) megoldás. Azure Sentinel intelligens biztonsági elemzést és fenyegetésészlelést biztosít a riasztások észlelése, a fenyegetések láthatósága, a proaktív veszélyforrás-felderítés és az automatizált fenyegetés-reagálás révén.

Íme néhány ajánlott eljárás a fenyegetések megelőzéséhez, észleléshez és a fenyegetésekre való reagáláshoz:

**Ajánlott eljárás:** A SIEM-megoldás sebességének és méretezhetőságának növelése felhőalapú SIEM használatával.   
**Részletek:** Megvizsgálhatja a [](../../sentinel/overview.md) Azure Sentinel funkcióit és képességeit, és összehasonlíthatja őket a jelenleg helyszíni használatban lévő funkciókkal. Fontolja meg a Azure Sentinel, ha az megfelel a szervezet SIEM-követelményeinek.

**Ajánlott eljárás:** A súlyos biztonsági rések megkeresve rangsorolhatja a vizsgálatot.   
**Részletek:** Tekintse át [az Azure biztonsági pontszámát,](../../security-center/secure-score-security-controls.md) és tekintse át az Azure-szabályzatok és -kezdeményezések beépített Azure Security Center. Ezek a javaslatok segítenek az olyan legfontosabb kockázatok, mint a biztonsági frissítések, a végpontvédelem, a titkosítás, a biztonsági konfigurációk, a hiányzó WAF, az internethez csatlakozó virtuális gépek stb. kezelése.

A Center for Internet Security (CIS) vezérlőin alapuló biztonsági pontszám lehetővé teszi a szervezet Azure-beli biztonságának külső forrásokkal való összehasonlítását. A külső ellenőrzés segít a csapat biztonsági stratégiájának ellenőrzésében és gazdagításában.

**Ajánlott eljárás:** A gépek, hálózatok, tárolási és adatszolgáltatások, valamint alkalmazások biztonsági biztonságának figyelése a potenciális biztonsági problémák felderítése és rangsorolása érdekében.  
**Részletek:** Kövesse [a](../../security-center/security-center-recommendations.md) Security Center, a legmagasabb prioritású elemekkel kezdve.

**Ajánlott eljárás:** Security Center riasztásokat integrálhatja a biztonsági információk és események kezelési (SIEM) megoldásába.   
**Részletek:** A legtöbb SIEM-et igénylő szervezet központi elszámolóházként használja az elemzői választ igénylő biztonsági riasztások esetében. A naplók által Security Center eseményeket a rendszer közzéteszi az Azure-tevékenységnaplóban, amely a Azure Monitor. Azure Monitor egy összevont folyamatot kínál a monitorozási adatok SIEM-eszközbe való átirányításához. Útmutatásért [lásd: Stream-riasztások SIEM-, SOAR- vagy IT-szolgáltatáskezelési](../../security-center/export-to-siem.md) megoldásba. Ha az alkalmazásokat használja, Azure Sentinel [Connect Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Ajánlott eljárás:** Azure-naplók integrálása a SIEM-sel.   
**Részletek:** Az [Azure Monitor adatokat gyűjthet és exportálhatja.](../../azure-monitor/overview.md#integrate-and-export-data) Ez a gyakorlat kritikus fontosságú a biztonsági incidensek vizsgálatának lehetővé tételében, és az online naplómegőrzés korlátozott. Ha az adatforrásokat használja Azure Sentinel: [Adatforrások csatlakoztatása.](../../sentinel/connect-data-sources.md)

**Ajánlott eljárás:** Felgyorsíthatja a vizsgálati és a támadáskeresési folyamatokat, és csökkentheti a téves riasztásokat azáltal, hogy az Endpoint Detection and Response (EDR) képességeket integrálja a támadási vizsgálatba.   
**Részletek:** [Engedélyezze a Microsoft Defender végpontintegrációját](../../security-center/security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration) a Security Center szabályzattal. Fontolja meg a Azure Sentinel veszélyforrás-veszélyforrás-veszélyforrás-kereséshez és incidensválaszhoz való használatának.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Végpontok között forgatókönyv-alapú hálózatmonitorozás figyelése
Az ügyfelek végpontok közötti hálózatot építenek ki az Azure-ban olyan hálózati erőforrások kombinálásával, mint a virtuális hálózat, az ExpressRoute, a Application Gateway és a terheléseltöltők. A figyelés minden hálózati erőforráson elérhető.

[Az Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy regionális szolgáltatás. Diagnosztikai és vizualizációs eszközeivel monitorhatja és diagnosztizálhatja a feltételeket az Azure-ban, illetve az Azure-ban a hálózati forgatókönyvek szintjén.

Az alábbiakban a hálózatfigyelés és az elérhető eszközök ajánlott eljárásait íme.

**Ajánlott eljárás:** Távoli hálózatfigyelés automatizálása csomagrögzítéssel.  
**Részletek:** Hálózati problémák monitorozása és diagnosztizálása anélkül, hogy bejelentkezik a virtuális gépekre a Network Watcher. Aktiválja [a csomagrögzítést](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) a riasztások beállításával, és a csomag szintjén valós idejű teljesítményinformációkhoz férhet hozzá. Ha problémát lát, részletesen megvizsgálhatja a jobb diagnosztizálásokat.

**Ajánlott eljárás:** Forgalmi naplók használatával betekintést nyerhet a hálózati forgalomba.  
**Részletek:** A hálózati biztonsági csoport forgalmi naplói segítségével részletesebben is megérthet hálózati forgalmi [mintákat.](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) A folyamatnaplókban található információk segítségével adatokat gyűjthet a hálózati biztonsági profil megfelelősége, naplózása és figyelése érdekében.

**Ajánlott eljárás:** VPN-kapcsolati problémák diagnosztizálása.  
**Részletek:** A Network Watcher segítségével diagnosztizálhatja a leggyakoribb VPN Gateway [és csatlakozási problémákat.](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md) Nem csupán azonosíthatja a problémát, de részletes naplókat is használhat a további vizsgálathoz.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Biztonságos üzembe helyezés bevált DevOps-eszközökkel
Az alábbi Ajánlott DevOps-eljárások segítségével biztosíthatja a vállalat és a csapatok hatékonyságát és hatékonyságát.

**Ajánlott eljárás:** A szolgáltatások felépítésének és üzembe helyezésének automatizálása.  
**Részletek:** [Az infrastruktúra mint kód](/azure/devops/learn/what-is-infrastructure-as-code) olyan technikák és eljárások készlete, amelyek segítenek az it-szakembereknek eltávolítani a moduláris infrastruktúra mindennapos felépítésének és felügyeletének terhét. Lehetővé teszi az it-szakemberek számára, hogy a szoftverfejlesztőkhez hasonló módon építsenek ki és tartsnak karban modern kiszolgálókörnyezetet.

Az [alkalmazásokat Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) deklaratív sablonnal is kiépítheti. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont használja az alkalmazás ismételt üzembe helyezéséhez az alkalmazás életciklusának minden szakaszában.

**Ajánlott eljárás:** Automatikus build és üzembe helyezés Azure-webalkalmazásokban vagy felhőszolgáltatásokban.  
**Részletek:** Konfigurálhatja úgy a  [Azure DevOps Projects,](/azure/devops/pipelines/index) hogy automatikusan azure-webalkalmazásokat vagy felhőszolgáltatásokat építsen ki és telepítsen. Az Azure DevOps automatikusan üzembe helyezheti a bináris fájlokat, miután minden kódbe bejelentkezés után buildel az Azure-ba. A csomag buildfolyamata megegyezik az Visual Studio Csomag parancsának, a közzétételi lépések pedig a közzétételi parancs közzétételi Visual Studio.

**Ajánlott eljárás:** A kiadáskezelés automatizálása.  
**Részletek:** [Az Azure Pipelines](/azure/devops/pipelines/index) megoldást kínál a többszakaszos üzembe helyezés automatizálására és a kiadási folyamat kezelésére. Felügyelt folyamatos üzembe helyezési folyamatok létrehozása a gyors, egyszerű és gyakori kiadáshoz. Az Azure Pipelines segítségével automatizálhatja a kiadási folyamatot, és előre meghatározott jóváhagyási munkafolyamatokat is használhat. A helyszínen és a felhőben is üzembe helyezheti, igény szerint kibővítheti és testre szabhatja.

**Ajánlott eljárás:** Mielőtt elindítja az alkalmazást, vagy frissítéseket telepít éles környezetbe, ellenőrizze az alkalmazás teljesítményét.  
**Részletek:** Felhőalapú terheléses [tesztek futtatása a](/azure/devops/test/load-test/overview#alternatives) következőre:

- Teljesítményproblémák megkeresása az alkalmazásban.
- Az üzembe helyezés minőségének javítása.
- Győződjön meg arról, hogy az alkalmazás mindig elérhető.
- Győződjön meg arról, hogy az alkalmazás képes kezelni a forgalmat a következő indítási vagy marketingkampány során.

[Az Apache JMeter](https://jmeter.apache.org/) egy ingyenes, népszerű, nyílt forráskódú eszköz, erős közösségi támogatással.

**Ajánlott eljárás:** Az alkalmazás teljesítményének figyelése.  
**Részletek:** [Az Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) egy többplatformos webfejlesztők számára készült, extensible application performance management (APM) szolgáltatás. Az Application Insights az élő webalkalmazás figyelése. Automatikusan észleli a teljesítményanomáliákat. Elemzési eszközöket tartalmaz, amelyek segítenek diagnosztizálni a problémákat, és megérteni, hogy a felhasználók valójában mit tegyenek az alkalmazással. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

## <a name="mitigate-and-protect-against-ddos"></a>A DDoS mérséklése és a DDoS elleni védelem
Az elosztott szolgáltatásmegtagadás (DDoS) egy olyan típusú támadás, amely megpróbálja kimerítani az alkalmazás erőforrásait. A cél az alkalmazás rendelkezésre állásának és a megbízható kérések kezelésének képessége. Ezek a támadások egyre kifinomultabbá válnak, és méretük és hatásuk is nagyobb. Bármely olyan végpontot megcélzhat, amely nyilvánosan elérhető az interneten keresztül.

A DDoS rugalmasságának tervezéséhez és kialakításához különböző hibamódok tervezésére és tervezésére van szükség. Az alábbiakban a DDoS-rugalmas szolgáltatások Azure-ban való építésével kapcsolatos ajánlott eljárások találhatók.

**Ajánlott eljárás:** Győződjön meg arról, hogy a biztonság az alkalmazás teljes életciklusában prioritást élvez, a tervezéstől és implementációtól az üzembe helyezésig és a műveletekig. Az alkalmazások olyan hibákat okozhatnak, amelyek viszonylag kevés erőforrás használatát teszik lehetővé, ami szolgáltatáskimaradást eredményez.  
**Részletek:** A Microsoft Azure-on futó szolgáltatások védelme érdekében jól kell értenie az alkalmazásarchitektúrát, és a szoftverminőség öt [alappillérére kell összpontosítania.](/azure/architecture/guide/pillars) Tudnia kell a tipikus adatforgalmat, az alkalmazás és más alkalmazások közötti kapcsolati modellt, valamint a nyilvános interneten elérhető szolgáltatásvégpontokat.

A legfontosabb annak biztosítása, hogy egy alkalmazás elég rugalmas legyen ahhoz, hogy kezelni tudja az alkalmazást célzó szolgáltatásmegtagadást. A biztonság és az adatvédelem az Azure platformba van beépítve, a Security [Development Lifecycle (SDL) (Biztonsági fejlesztési életciklussal (SDL) kezdve).](https://www.microsoft.com/sdl) Az SDL minden fejlesztési fázisban foglalkozik a biztonsággal, és gondoskodik arról, hogy az Azure folyamatosan frissül, hogy még biztonságosabb legyen.

**Ajánlott eljárás:** Az [](/azure/architecture/guide/design-principles/scale-out) alkalmazásokat úgy tervezz meg, hogy horizontálisan skálázva, az amplified terhelési igényeknek megfelelően skálázva, különösen DDoS-támadás esetén. Ha az alkalmazás egy szolgáltatás egyetlen példányától függ, egyetlen meghibásodási pontot hoz létre. Több példány kiépítése rugalmasbbá és skálázhatóbbá teszi a rendszert.  
**Részletek:** A [Azure App Service](../../app-service/overview.md)válasszon ki egy [App Service több](../../app-service/overview-hosting-plans.md) példányt is kínál.

A Azure Cloud Services konfigurálja az egyes szerepköröket úgy, hogy több [példányt használjanak.](../../cloud-services/cloud-services-choose-me.md)

Az Azure Virtual Machines győződjön meg [arról,](../../virtual-machines/windows/overview.md)hogy a virtuálisgép-architektúra egynél több virtuális gépet tartalmaz, és hogy mindegyik virtuális gép szerepel egy [rendelkezésre állási készletben.](../../virtual-machines/windows/tutorial-availability-sets.md) Az automatikus skálázáshoz a virtuálisgép-méretezési készletek használatát javasoljuk.

**Ajánlott eljárás:** A biztonsági védelem alkalmazáson való rétegezése csökkenti a sikeres támadások esélyét. Biztonságos kialakításokat valósíthat meg alkalmazásai számára az Azure platform beépített képességeinek használatával.  
**Részletek:** A támadás kockázata az alkalmazás méretével (felületével) együtt növekszik. A felület csökkentéséhez egy jóváhagyási lista segítségével bezárhatja a felfedett IP-címteret, és figyelheti azokat a portokat, amelyekre nincs szükség a terheléseltöltőkben[(Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) és [Azure Application Gateway).](../../application-gateway/application-gateway-create-probe-portal.md)

[A hálózati biztonsági csoportok](../../virtual-network/network-security-groups-overview.md) egy másik módja a támadási felület csökkentésének. A szolgáltatáscímkék [](../../virtual-network/network-security-groups-overview.md#application-security-groups) [és](../../virtual-network/network-security-groups-overview.md#service-tags) alkalmazásbiztonsági csoportok használatával minimalizálhatja a biztonsági szabályok létrehozásának és a hálózati biztonság konfigurálásának összetettségét az alkalmazás struktúrájának természetes kiterjesztéseként.

Amikor csak lehetséges, üzembe kell helyeznie az [Azure-szolgáltatásokat](../../virtual-network/virtual-networks-overview.md) egy virtuális hálózatban. Ez a gyakorlat lehetővé teszi, hogy a szolgáltatási erőforrások magánhálózati IP-címeken keresztül kommunikáljanak. A virtuális hálózatról származó Azure-szolgáltatások forgalma alapértelmezés szerint nyilvános IP-címeket használ forrás IP-címként.

A [szolgáltatásvégpontokkal](../../virtual-network/virtual-network-service-endpoints-overview.md) a szolgáltatásforgalom átváltható a virtuális hálózat magánhálózati címeinek forrás IP-címként való használatára, amikor egy virtuális hálózatról férnek hozzá az Azure-szolgáltatáshoz.

Gyakran előfordul, hogy az azure-beli erőforrásokkal együtt az ügyfelek helyszíni erőforrásait is megtámadják. Ha helyszíni környezetet csatlakoztat az Azure-hoz, minimalizálja a helyszíni erőforrások nyilvános internettel való kitettségét.

Az Azure két [](../../ddos-protection/ddos-protection-overview.md) DDoS-szolgáltatásajánlattal rendelkezik, amelyek védelmet nyújtanak a hálózati támadások ellen:

- Az alapszintű védelem alapértelmezés szerint további költségek nélkül integrálva van az Azure-ba. A globálisan üzembe helyezett Azure-hálózat skálája és kapacitása védelmet nyújt a gyakori hálózati rétegbeli támadások ellen a folyamatos forgalomfigyelés és a valós idejű kockázatcsökkentés révén. Az Alapszintű szolgáltatás nem igényel felhasználói konfigurációt vagy alkalmazásváltozásokat, és segít megvédeni az összes Azure-szolgáltatást, beleértve az olyan PaaS-szolgáltatásokat is, mint Azure DNS.
- A standard szintű védelem fejlett DDoS-kockázatcsökkentési képességeket biztosít a hálózati támadások ellen. A rendszer automatikusan behangolja az adott Azure-erőforrások védelme érdekében. A virtuális hálózatok létrehozása során a védelem egyszerűen engedélyezhető. A létrehozás után is használhatja, és nem igényel alkalmazás- vagy erőforrás-módosításokat.

## <a name="enable-azure-policy"></a>A Azure Policy
[Azure Policy](../../governance/policy/overview.md) azure-beli szolgáltatás, amely szabályzatok létrehozására, hozzárendelésére és kezelésére használható. Ezek a szabályzatok szabályokat és hatásokat kényszerítik ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek. Az Azure Policy úgy tesz eleget ezeknek az elvárásoknak, hogy kiértékeli, megfelelnek-e az erőforrások a hozzájuk rendelt szabályzatoknak.

Engedélyezze Azure Policy a szervezet írásos szabályzatának figyelése és betartatás érdekében. Ez biztosítja a vállalat vagy a jogszabályi biztonsági követelmények betartását a hibrid felhőalapú számítási feladatok biztonsági szabályzatai központi felügyeletével. Megtudhatja, hogyan [hozhat létre és kezelhet szabályzatokat a megfelelőség kikényszerítenie.](../../governance/policy/tutorials/create-and-manage.md) A [Azure Policy elemeinek](../../governance/policy/concepts/definition-structure.md) áttekintéséhez tekintse meg a Azure Policy definíciószerkezetét ismertető témakört.

Az alábbi ajánlott biztonsági eljárásokat kell követnie a Azure Policy:

**Ajánlott eljárás:** A szabályzat számos hatástípust támogat. A definíciók szerkezetében olvashat [Azure Policy őket.](../../governance/policy/concepts/definition-structure.md#policy-rule) Az üzleti műveleteket hátrányosan érintheti a  megtagadási és a javítási  hatás, ezért kezdje a naplózási hatással, hogy korlátozza a szabályzat negatív hatásainak kockázatát.    
**Részletek:** [Indítsa el a házirendek központi telepítését naplózási módban,](../../governance/policy/concepts/definition-structure.md#policy-rule) majd később tiltsa le vagy **orvosolja a következőt:**.  Tesztelje és tekintse át a naplózási  hatás eredményeit, mielőtt továbblép a megtagadási vagy **szervizeléssel kapcsolatosra.**

További információ: Szabályzatok [létrehozása és kezelése a megfelelőség kényszerítéséhez.](../../governance/policy/tutorials/create-and-manage.md)

**Ajánlott eljárás:** Azonosítsa a szabályzatsértések monitorozásáért felelős szerepköröket, és biztosítsa a megfelelő szervizelési művelet gyors követését.   
**Részletek:** A hozzárendelt szerepkör-figyelő megfelelőségét a Azure Portal [vagy](../../governance/policy/how-to/get-compliance-data.md#portal) a [parancssor használatával kell ellenőrizni.](../../governance/policy/how-to/get-compliance-data.md#command-line)

**Ajánlott eljárás:** Azure Policy a szervezet írásos szabályzatának technikai reprezentációja. Az összes Azure Policy leképezheti a szervezeti szabályzatokat a félreértések csökkentése és a konzisztencia növelése érdekében.   
**Részletek:** Dokumentumleképezés a szervezet dokumentációjában vagy magában a Azure Policy-definícióban úgy, [](../../governance/policy/concepts/definition-structure.md#display-name-and-description) hogy hozzáad egy hivatkozást a szervezeti szabályzathoz a szabályzatdefinícióban vagy a kezdeményezési definíció [leírásában.](../../governance/policy/concepts/initiative-definition-structure.md#metadata)

## <a name="monitor-azure-ad-risk-reports"></a>Az Azure AD kockázati jelentésének figyelése
A biztonsági incidensek túlnyomó többsége akkor történik, amikor a támadók egy felhasználó identitásának ellopása által hozzáférést szereznek a környezethez. A feltört identitások felderítése nem egyszerű feladat. Az Azure AD adaptív gépi tanulási algoritmusokat és heurisztikát használ a felhasználói fiókokhoz kapcsolódó gyanús műveletek észlelésére. Minden észlelt gyanús művelet egy kockázatészlelési nevű [rekordban van tárolva.](../../active-directory/identity-protection/overview-identity-protection.md) A kockázatészleléseket az Azure AD biztonsági jelentései rögzítik. További információért olvassa [](../../active-directory/identity-protection/overview-identity-protection.md) el a veszélyeztetett felhasználókról szóló biztonsági jelentést és a kockázatos bejelentkezések [biztonsági jelentését.](../../active-directory/identity-protection/overview-identity-protection.md)

## <a name="next-steps"></a>Következő lépések
A [felhőalapú](best-practices-and-patterns.md) megoldások Azure-ral való tervezésekor, üzembe helyezésekor és kezelésekor használható további ajánlott biztonsági eljárásokért tekintse meg az Azure biztonsági ajánlott eljárásait és mintáit.

Az Alábbi források általánosabb információkat nyújtanak az Azure biztonságával és a kapcsolódó Microsoft-szolgáltatások:
* [Az Azure Security csapat blogja](/archive/blogs/azuresecurity/) – naprakész információk az Azure Security legújabb verziójáról
* [Microsoft Biztonsági reagálási központ](https://technet.microsoft.com/library/dn440717.aspx) – a Microsoft biztonsági réseit, beleértve az Azure-ral kapcsolatos problémákat, jelenteni lehet, vagy e-mailben a következő címre: secure@microsoft.com