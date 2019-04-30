---
title: Az Azure fejlett fenyegetésészlelés |} A Microsoft Docs
description: További tudnivalók az Azure AD Identity Protection és annak képességeit.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 60a0d7c2da25f63d0728dbb86f9a6c2328def811
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611026"
---
# <a name="azure-advanced-threat-detection"></a>Az Azure fejlett fenyegetésészlelés

Azure-szolgáltatások, például az Azure Active Directory (Azure AD), az Azure Monitor-naplók és az Azure Security Center észlelési funkciót a komplex veszélyforrások elleni beépített ajánlatok. Ez a témakörgyűjtemény biztonsági szolgáltatások és funkciók segítségével gyorsan és egyszerűen, mi történik, az Azure-környezetek belül.

Az Azure konfigurálására és testreszabására az alkalmazások telepítésének követelményeinek megfelelő biztonsági lehetőségek széles tárházát biztosítja. Ez a cikk leírja, hogyan mindezen követelmények teljesítése érdekében.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Az Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) van egy [Azure Active Directory Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) edition szolgáltatás, amely áttekintést ad a kockázati eseményekről és a szervezet identitásait érintő lehetséges biztonsági résekről. Identity Protection használja az Azure AD-anomáliadetektálás meglévő képességeit keresztül elérhető [az Azure AD rendellenes tevékenységekre vonatkozó jelentések](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports), és valós idejű rendellenességeket új kockázati események típusai vezet be.

![Az Azure AD Identity Protection diagramja](./media/azure-threat-detection/azure-threat-detection-fig1.png)

Identity Protection adaptív gépi tanulási algoritmusok és heurisztika használja a rendellenességek észlelését, és a kockázati eseményeket, amelyek esetleg jelzik, hogy az identitás biztonsága sérült. Ezen adatok alapján Identity Protection állít elő, jelentéseket és riasztásokat, hogy megvizsgálhatja a kockázati eseményeket és a megfelelő korrekciós vagy kockázatcsökkentési művelet.

Az Azure Active Directory Identity Protection több mint egy olyan figyelési és jelentéskészítési eszköz. Kockázati események alapján, Identity Protection számítja ki egy felhasználói kockázati szint minden felhasználó számára, hogy a szervezet identitásainak automatikus védelméhez kockázatalapú szabályzatok konfigurálhatók az.

Ezek kockázatalapú szabályzatok mellett egyéb [feltételes hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) , amely az Azure Active Directory által biztosított és [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), automatikusan blokkolhatják vagy adaptív szervizelési műveletek ajánlat, amely például a jelszó alaphelyzetbe állítása és a többtényezős hitelesítés kényszerítése.

### <a name="identity-protection-capabilities"></a>Identitásvédelmi képességeket

Az Azure Active Directory Identity Protection több mint egy olyan figyelési és jelentéskészítési eszköz. Védelme érdekében a szervezet identitásait, konfigurálhatja a kockázatalapú szabályzatok, amelyek az automatikusan észlelt problémák reagálnak adott kockázati szint elérésekor. Ezek a szabályzatok mellett más feltételes hozzáférés-vezérlés az Azure Active Directory és az EMS által biztosított automatikusan letiltása, vagy kezdeményezzen adaptív szervizelési műveletek, például jelszó alaphelyzetbe állítása és a többtényezős hitelesítés kényszerítése.

Példák az Azure Identity Protection által lehetőségeket a fiókok védelmét, és identitások a következők:

[Kockázati események és a kockázatos fiókok észlelése](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Hat kockázati események típusai gépi tanulási és heurisztikus szabályok használatával észleli.
-   Felhasználói kockázati szintek kiszámítása.
-   Egyéni javaslatokkal átfogó biztonsági rendszer kialakításához kiemelése a biztonsági rések javítása érdekében.

[Kockázati események vizsgálata](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Kockázati események értesítést küldeni.
-   Megfelelő és környezetfüggő adatok felhasználásával kockázati események vizsgálata.
-   Adja meg a vizsgálatok nyomon követéséhez alapvető munkafolyamatok.
-   Szervizelési műveletek egyszerű hozzáférést biztosítanak, mint például a jelszó alaphelyzetbe állítása.

[Kockázatalapú, a feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
-   Bejelentkezés blokkolása vagy a multi-factor authentication kihívások igénylő csökkenti a kockázatos bejelentkezések.
-   Kockázatos felhasználói fiókok biztonságban vagy letiltása.
-   Felhasználók regisztráljanak a multi-factor Authentication hitelesítés szükséges.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

A [Azure Active Directory Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), kezelheti, a szervezeten belüli hozzáférési szabályozhatja és figyelheti. Ez a funkció más Microsoft online szolgáltatásaihoz, például az Office 365 vagy a Microsoft Intune és az Azure AD erőforrásaihoz való hozzáférés tartalmazza.

![Az Azure AD Privileged Identity Management diagram](./media/azure-threat-detection/azure-threat-detection-fig2.png)

A PIM segítségével:

-   Riasztások és az Azure AD-rendszergazdák és a Microsoft online szolgáltatásaihoz, például az Office 365 és Intune-igény (szerinti JIT) rendszergazdai hozzáféréssel kapcsolatos jelentések lekérése.

-   Rendszergazdák hozzáférési műveleteiről és a változások jelentéseket rendszergazdája hozzárendeléseinek beolvasása.

-   A kiemelt szerepkörű való hozzáféréssel kapcsolatos riasztásokat kaphat.

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

[Az Azure Monitor naplóira](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) van a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúrára. Az Azure Monitor naplóira implementálva van a felhő alapú szolgáltatás, mert rendelkezhet, és gyorsan az infrastrukturális szolgáltatásokra fordítandó minimális kiadások mellett a. Új biztonsági funkciók bevezetése automatikus, mentése folyamatban van a folyamatos karbantartás és frissítés költségei.

Értékes szolgáltatásokat biztosít a saját, az Azure Monitor mellett naplók integrálható a System Center-összetevőket, például [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), kiterjesztése a meglévő biztonsági felügyeleti beruházások kiterjeszthetők a felhő. A System Center és az Azure Monitor naplókat is együttműködve a teljes hibrid felügyeleti környezetet biztosít.

### <a name="holistic-security-and-compliance-posture"></a>Átfogó biztonsági és megfelelőségi állapotáról

A [Log Analytics biztonsági és auditálási irányítópultja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) nyújt átfogó képet kaphat a szervezet informatikai biztonsági helyzetét, figyelmet igénylő jelentős problémákat beépített keresési lekérdezések révén. A biztonsági és auditálási irányítópultja a kezdőképernyőn a biztonság az Azure Monitor naplóira kapcsolódó összes elemet. Magas szintű betekintést tesz lehetővé a számítógépek biztonsági állapotába. Minden események az elmúlt 24 óra, 7 nap vagy bármely más egyéni időkeret is megtekintheti.

Az Azure Monitor-naplók segítenek gyorsan és egyszerűen megérteni, hogy bármilyen környezet, IT-üzemeltetési, beleértve a szoftver rendszerfrissítési felmérés, kártevőirtók felmérése és alapkonfigurációk keretein belül az összes általános biztonsági állapotát. Biztonsági naplózási adatok érhető el azonnal a biztonsági és megfelelőségi ellenőrzési eljárások egyszerűsítésére.

![A Log Analytics biztonsági és auditálási irányítópultja](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

A Log Analytics biztonsági és auditálási irányítópultja négy fő kategóriába sorolhatók:

-   **Biztonsági tartományok**: Lehetővé teszi a részletesebb megismerése a biztonsági rekordok időbeli; hozzáférés kártevő értékelések; a frissítési felmérések; hálózati biztonság, identitás és hozzáférés információt; megtekintése biztonsági események; rendelkező számítógépek megtekintése és gyorsan hozzáférhet az Azure Security Center irányítópultján.

-   **Jelentős problémák**: Lehetővé teszi, hogy gyorsan azonosíthatja az aktív problémák számát és a problémák súlyosságát.

-   **Észlelések (előnézet)**: Lehetővé teszi a támadási minták azonosítását az erőforrásokkal bekövetkezésük biztonsági riasztások megjelenítésével.

-   **Fenyegetések felderítése**: Lehetővé teszi a támadási mintákat azonosíthat a kimenő rosszindulatú IP-forgalom kiszolgálók száma, a rosszindulatú fenyegetések típusa és az IP-címek helyek térképe.

-   **Gyakori biztonsági lekérdezések**: Felsorolja a leggyakoribb biztonsági lekérdezésekről, amelyek segítségével figyelni a környezetet. Minden lekérdezés kiválasztásakor a keresési ablak megnyílik, és ez a lekérdezés eredményeit jeleníti meg.

### <a name="insight-and-analytics"></a>Az Insight and analytics
Középső [naplózza az Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) van a tárházhoz, amely az Azure-ban.

![Az Insight and analytics diagramja](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Ön forrásból gyűjthet adatokat a tárházba, csatlakoztatott adatforrások konfigurálása és megoldások hozzáadása az előfizetéséhez.

![Az Azure Monitor irányítópultja](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Adatforrások és megoldások minden egyes külön erőforrásrekord-típusok létrehozása a saját tulajdonságai készletét, de továbbra is elemezheti őket össze a tárházban lekérdezésekben. Együttműködést a különböző források által gyűjtött adatok különböző használhatja ugyanazokat az eszközöket és módszereket.


A legtöbb Azure Monitor naplóira-szal, amely bármilyen böngészőben fut, és biztosít hozzáférést a konfigurációs beállításokhoz és több eszközhöz, elemezheti és kezelhetők az összegyűjtött adatokat az Azure Portalon keresztül történik. A portálról használhat:
* [Naplókeresések](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) ahol elemezheti az összegyűjtött adatokat lekérdezéseket hozhat létre.
* [Az irányítópultok](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards), amelyeket a legértékesebb kereséseinek grafikus nézeteivel testre is szabhatja.
* [Megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), további funkciókat és elemzőeszközöket biztosító.

![Elemzési eszközök](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Megoldások funkciókkal bővítik az Azure Monitor naplóira. Elsősorban futtatása a felhőben és a log analytics-adattárban gyűjtött adatok elemzését végzik. Megoldások is meghatározhatnak új erőforrásrekord-típusok összegyűjtése, amelyek naplókeresésekkel, vagy egy további felhasználói felület, amely a megoldást biztosít a log analytics-Irányítópult segítségével elemezhetők.

A biztonsági és auditálási irányítópultja, amelyek az ilyen típusú megoldások.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatizálás és vezérlés: Biztonsági konfiguráció drifts riasztás

Az Azure Automation-runbookok futtatása a felhőben és a PowerShell-alapú felügyeleti folyamatok automatizálja. A runbookok futtathatók a helyi adatközpontban is, és kezelhetők velük a helyi erőforrások. Az Azure Automation használatával biztosít konfigurációkezelést a PowerShell Desired State Configuration (DSC).

![Az Azure Automation-diagram](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Hozzon létre, és az Azure-ban üzemelnek, és azokat alkalmazni a felhőbeli és helyszíni rendszerek DSC-erőforrások kezeléséhez. Konfigurációk végrehajtásával, így határozza meg, automatikusan érvényesíti a konfigurációt vagy jelentések készítése eltéréseket a biztonság érdekében a szabályzaton belüli továbbra is.

## <a name="azure-security-center"></a>Azure Security Center

Az Azure Security Center segít az Azure-erőforrások védelme. Integrált biztonsági monitorozást és felügyeletet biztosít az Azure-előfizetések között. A szolgáltatáson belül meghatározhatja szabályzatok ellen mind az Azure-előfizetésekre és [erőforráscsoportok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) részletesebb.

![Az Azure Security Center diagramja](./media/azure-threat-detection/azure-threat-detection-fig8.png)

A Microsoft biztonsági kutatói folyamatosan figyelik a megjelenő fenyegetéseket. Rendelkezésükre áll a telemetriai adatok kiterjedt halmaza, amelyet a Microsoft a globális felhőbeli és helyszíni jelenléte során gyűjtött össze. Ezen adatkészletek széles körű és változatos gyűjteményére alapozva a Microsoft fel tudja fedezni az új támadási mintákat és trendeket a helyszíni fogyasztói és a vállalati termékeiben, valamint az online szolgáltatásaiban.

Így a Security Center gyorsan tudja frissíteni az észlelési algoritmusait, a támadók újabb és egyre összetettebb biztonsági rések kiadás. Ez a módszer segít lépést tartani a fenyegetések gyors ütemben környezet.

![A Security Center fenyegetésészlelési](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

A Security Center fenyegetésészlelése úgy működik, hogy automatikusan összegyűjti a biztonsági információkat az Azure-erőforrásokból, a hálózatból és a csatlakoztatott partneri megoldásokból. Elemzi ezeket az információkat, a fenyegetések, több forrásból származó információk használatával történik.

A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására.

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A big data vívmányok és [gépi tanulás](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák segítségével ki az események között a teljes felhőalapú hálóban. Bővített analitika olyan lehetetlen volna azonosítani, manuális módszer és a támadások fejlődésének előrejelzésére fenyegetések észlelését. A következő szakaszokban ismertetett biztonsági elemzések típusaival.

### <a name="threat-intelligence"></a>Fenyegetésészlelési intelligencia

A Microsoft globális fenyegetésfelderítési környezetéről mennyi hozzáféréssel rendelkezik.

Telemetriai adatok elkezdenek beérkezni a több forrásból, például Azure, Office 365-höz, a Microsoft CRM online, a Microsoft Dynamics AX, outlook.com, MSN.com, a Microsoft Digital Crimes Unit (DCU) és a Microsoft Security Response Center (MSRC).

![Threat intelligence megállapításokat](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Kutatói is kapnak a fő felhőszolgáltatók által közösen használt adatokat, és a harmadik felektől származó előfizetnek fenyegetésészlelési intelligenciával foglalkozó hírcsatornákra. Az Azure Security Center mindezeket az adatokat fel tudja használni arra, hogy értesítse Önt az ismert kártékony elemektől eredő fenyegetésekről. Néhány példa:

-   **A gépi tanulás nyilvántartások**: Az Azure Security Center hozzáfér egy hatalmas mennyiségű adattal felhőbeli hálózati tevékenységekbe, amely használható az Azure-környezetek fenyegető veszélyforrások észlelésére.

-   **Találgatásos típusú észlelés**: Machine learning távoli hozzáférési kísérleteket, egy olyan korábbi mintát, amely lehetővé teszi, hogy a Secure Shell (SSH), a távoli asztal protokoll (RDP) és az SQL-portokhoz találgatásos támadások észlelésére szolgál.

-   **Kimenő DDoS és botnet észlelési**: Egy közös, amelyek felhőbeli erőforrásokat céloznak célja, hogy a számítási teljesítmény, a következő erőforrások használata egyéb támadások végrehajtására.

-   **Új viselkedéselemzéssel kiszolgálók és virtuális gépek**: Miután egy kiszolgálón vagy a virtuális gép biztonsága sérül, a támadók foglalkoztat számos különböző technikák észlelési elkerülése, biztosítva az adatmegőrzés és biztonsági vezérlők borítóban során a rendszer rosszindulatú kódot hajthat végre.

-   **Az Azure SQL Database Threat Detection**: A fenyegetésészlelés, az Azure SQL Database, amely azonosítja a rendellenes adatbázis-tevékenységek, amelyek jelzik, hogy szokatlan és vélhetően kárt okozó az adatbázisokat elérni vagy kiaknázni próbál.

### <a name="behavioral-analytics"></a>Működés elemzése

A működés elemzése olyan módszer, amely megvizsgálja és összehasonlítja az adatokat az ismert minták gyűjteményével. Ezek a minták azonban nem csak egyszerű aláírások. Meghatározásuk hatalmas adatkészletekre alkalmazott összetett gépi tanulási algoritmusokkal történt.

![Viselkedéselemzés megállapításokat](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)

A minták elemezték a kártékony működést keresztül is meghatározni a szakértő elemzők által. Az Azure Security Center a működéselemzéssel azonosíthatja a feltört erőforrásokat a virtuális gépek naplóinak, a virtuális hálózati eszközök naplóinak, a háló naplóinak, a összeomlási memóriaképek és a más forrásokból elemzése alapján.

Emellett minták nagy lépétkű kampányok bizonyítékait ellenőrzéséhez más jelekkel közötti kapcsolatot. Az összefüggések felderítése segít azonosítani a feltörés meghatározott tüneteit mutató eseményeket.

Néhány példa:
-   **Gyanús folyamat-végrehajtás**: A támadók különböző módszereket vetnek be hajtsa végre a kártékony szoftverek fordítani. Például egy támadó előfordulhat, hogy megbízható rendszerfájlok nevével megegyező nevet ad, de ezeket a fájlokat helyezze egy másik helyen, egy nevet, amely hasonlít egy ártalmatlan fájl nevére vagy maszkolja a fájl valódi kiterjesztését. A Security Center modellek feldolgozni viselkedése és a figyelő folyamat-végrehajtást az ezekhez hasonló kakukktojások felismeréséhez.

-   **Rejtett és veszélyeztető kísérletek**: Kifinomult kártevők is játszani a hagyományos kártevőirtó termékeket azzal vagy soha nem írnak a lemezre és nem titkosítják a lemezen tárolt szoftverösszetevőket. Azonban az ilyen kártevők észlelik memóriaelemzéssel, mert a kártevő szoftver nyomot kell hagyniuk a memóriában függvénynek. Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában. Az összeomlási memóriaképben található memóriarész elemzésével az Azure Security Center észlelni tudja az technikákat, amelyekkel a szoftver biztonsági réseit, hozzáférnek a bizalmas adatokhoz és maradnak elrejtőzve egy feltört gépet anélkül, hogy befolyásolná a teljesítményt, a gép.

-   **Oldalirányú mozgás és belső felderítés**: Továbbra is fennáll, egy feltört hálózatban, és keresse meg és értékes adatokat gyűjtsön a támadók gyakran oldalirányban próbálnak továbbhaladni a feltört gépről mások számára ugyanazon a hálózaton belül. A Security Center figyeli a folyamat és a bejelentkezési tevékenységek kísérleteit bontsa ki a támadó foothold belül a hálózathoz, például a távoli parancs végrehajtása, a megkísérelt hálózatszondázást és a fiókok számbavételét.

-   **Rosszindulatú PowerShell-parancsfájlok**: PowerShell segítségével a támadók rosszindulatú kódot végrehajtani a cél virtuális gépek különböző célokra. A Security Center megvizsgálja a PowerShell tevékenységeit, hogy megtalálja a gyanús tevékenységek nyomait.

-   **Kimenő támadások**: A támadók gyakran felhőbeli erőforrásokat a cél az erőforrások használata további támadások elvégzésére célozhat meg. A feltört virtuális gépek például felhasználhatók más virtuális gépek elleni találgatásos támadásokra, levélszemét küldése vagy nyitott portok és más eszközök az interneten. A hálózati forgalomra irányított gépi tanulás alkalmazásával a Security Center észlelni tudja, ha a szokásosnál nagyobb mértékű a kimenő hálózati kommunikáció. Levélszemét észlelésekor Security Center ezenkívül összehasonlítja a szokatlan e-mail-forgalmat a meghatározásához, valószínű, hogy az e-mailt Office 365-ből intelligenciával adatgyűjteményével vagy egy normál e-mail-kampányhoz eredményét.

### <a name="anomaly-detection"></a>Rendellenességek észlelése

Az Azure Security Center további módszere a fenyegetések felderítésére a rendellenességek észlelése. A működés elemzésével (amely a nagy adatkészletekből kinyert ismert mintákon alapul) ellentétben a rendellenességek észlelése „testre szabottabb”, és az üzemelő példányokhoz tartozó alapkonfigurációkra összpontosít. Gépi tanulás alkalmazásával az üzemelő példányok normál tevékenységeit, és ezután szabályok jönnek létre rendkívüli körülmények meghatározásához, amelyek biztonsági esemény utalhatnak. Például:

-   **Bejövő RDP/SSH találgatásos támadások**: Előfordulhat, hogy a központi telepítések számos bejelentkezések minden nap a nagy forgalmú virtuális gépek és egyéb virtuális gépeket, amelyek néhány, az esetleges bejelentkezések. Az Azure Security Center határozza meg a bejelentkezési alaptevékenységet ezek a virtuális gépek és a gépi tanulás alkalmazásával meghatározza a normál bejelentkezési tevékenységek körül használja. Ha az alapkonfiguráció definiálva a licencfeltételekre bejelentkezési kapcsolatos jellemzők, előfordulhat, hogy riasztást állít elő. Ebben az esetben is gépi tanulás alapján határozza meg, hogy mi számít szignifikáns eltérésnek.

### <a name="continuous-threat-intelligence-monitoring"></a>A fenyegetésekre vonatkozó intelligencia folyamatos figyelése

Az Azure Security Center biztonsági kutatással és adattudománnyal foglalkozó csapatai világszerte folyamatosan figyelik a fenyegetések területén bekövetkező változásokat, működik. Ide tartoznak a következők:

-   **Fenyegetésekre vonatkozó intelligencia figyelése**: Fenyegetésészlelési intelligencia mechanizmusok, a mutatók, a következmények és a meglévő és felmerülő fenyegetésekkel kapcsolatos gyakorlati tanácsokat tartalmaz. Ez az információ a biztonsági Közösséggel megosztott, és a Microsoft folyamatosan figyeli a fenyegetésészlelési intelligenciával foglalkozó hírcsatornákra belső és külső forrásból.

-   **Jelmegosztás**: A security insights csapatok között a Microsoft tartalmazó felhőalapú és helyszíni szolgáltatásokat, kiszolgálókat és végponti eszközök vannak osztva, és elemzi.

-   **Microsoft biztonsági szakértői**: Folyamatban lévő együttműködés a Microsoft csapataival, amelyek a biztonsági szakterületekkel, például az eseményadatokhoz és a webes támadások észlelésével.

-   **Észlelés finomhangolása**: Algoritmusok elleni valós futnak, és biztonsági kutatói használata az ügyfelek ellenőrzik az eredményeket. Az igazi és a téves találatok megjelölésével pontosítják a gépi algoritmusokat.

Ezek egyesített erőfeszítések új és hatékonyabb észleléseket eredményeznek, amely, hasznát. Nem tartoznak, hogy a műveletek.

## <a name="advanced-threat-detection-features-other-azure-services"></a>A komplex veszélyforrások elleni észlelési funkciók: Más Azure-szolgáltatások

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuális gépek: A Microsoft kártevőirtó

[A Microsoft antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) for Azure egy ügynök megoldás az alkalmazások és a bérlői környezetekben is, amely fut a háttérben, emberi beavatkozás nélkül. Az alkalmazás-munkaterhelések, vagy alapszintű biztonságos alapértelmezés szerint az igényeinek megfelelően, vagy egyéni konfigurálására, beleértve a kártevőirtó-figyelés speciális védelem is telepítheti. Az Azure kártevőirtó beállítás biztonsági Azure-beli virtuális gépek, amelyek automatikusan telepítve van az összes Azure PaaS-virtuális gép.

#### <a name="microsoft-antimalware-core-features"></a>A Microsoft kártevőirtó alapvető funkciók

Az alábbiakban az Azure, amelyek központi telepítése és engedélyezése a Microsoft kártevőirtó szolgáltatásával védi meg az alkalmazások funkcióit:

-   **Valós idejű védelem**: Figyeli a tevékenységet, a cloud services és észleli és blokkolja a kártevő szoftver futtatása virtuális gépeken.

-   **Keresés ütemezett**: A Microsoft rendszeresen végez célzott vizsgálatának például aktívan futó programok észlelését.

-   **Kártevő szoftver eltávolításának**: Észlelt kártevő szoftvereket, például törlése vagy rosszindulatú fájlok karanténba és rosszindulatú beállításjegyzék-bejegyzések ürítése automatikusan működik.

-   **Aláírás-frissítések**: Automatikusan telepíti a legújabb aláírások (vírus-definíciók), hogy alkalmazásvédelmi értesülhet az előre meghatározott gyakoriságát.

-   **Frissíti a kártevőirtó motor**: A Microsoft Antimalware Engine automatikusan frissíti.

-   **A kártevőirtó platform frissítések**: A Microsoft kártevőirtó platform automatikusan frissíti.

-   **Aktív védelem**: Jelentések telemetriai metaadatokat észlelt fenyegetések és a Microsoft Azure-bA gyanús erőforrásokat annak biztosítása érdekében a fenyegető veszélyforrások kérdését, és engedélyezi a valós idejű szinkron aláírás kézbesítési a Microsoft active protection rendszeren keresztül való gyors reagálásról.

-   **Jelentéskészítés – minták**: Biztosít, és minták a jelentéseket a Microsoft kártevőirtó szolgáltatás, pontosítsa a szolgáltatást, és hibaelhárítást.

-   **A kizárások**: Alkalmazás és szolgáltatás-rendszergazdák konfigurálása bizonyos fájlokat, a folyamatok és meghajtók kizárása a védelemből és a teljesítmény és más okok miatt vizsgálatát teszi lehetővé.

-   **Kártevőirtó eseménygyűjtés**: A kártevőirtó szolgáltatás állapota, a gyanús tevékenységek és az operációs rendszer-eseménynaplóban végzett szervizelési műveleteket rögzíti, és gyűjti azokat az ügyfél Azure storage-fiókra.

### <a name="azure-sql-database-threat-detection"></a>Az Azure SQL Database Threat Detection

[Az Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) egy új biztonsági intelligencia funkció az Azure SQL Database szolgáltatásban beépítetten. Éjjel-nappal dolgozik, ismerje meg, a profil és a észlelni a rendellenes adatbázis-tevékenységek, Azure SQL Database Threat Detection azonosítja az adatbázis elleni lehetséges fenyegetések elhárítását.

Biztonsági tisztviselők, vagy más kijelölt rendszergazdák a bekövetkezésük lekérheti a gyanús adatbázis-tevékenységekről az azonnali értesítések. Minden értesítés biztosít a gyanús tevékenység részleteit, és hogyan további vizsgálata és a fenyegetés javasolja.

Jelenleg az Azure SQL Database Threat Detection észleli a potenciális biztonsági réseket és az SQL-injektálásos támadásokról, és a rendellenes adatbázis-hozzáférési mintákról.

Fenyegetésészlelési e-mail értesítést kap, felhasználók meg tudják keresse meg és a kapcsolódó naplórekordok mélyhivatkozás keresztül tekintse meg az e-mailt. A hivatkozás megnyílik egy naplózási viewer vagy egy előre konfigurált naplózási Excel-sablont, amely megjeleníti a a gyanús esemény időpontja környékén rögzített kapcsolódó naplórekordok az alábbiak szerint:

-   Tárolás az adatbázis-kiszolgáló a rendellenes adatbázis-tevékenységek naplózása.

-   Az esemény időpontjában az auditálási naplóba írás használt kapcsolódó naplózási tárolótáblában.

-   A naplórekordok közvetlenül követő esemény óra.

-   Naplórekordok egy hasonló esemény-azonosítót (néhány derítik fel nem kötelező) az esemény időpontjában.

Az SQL Database threat derítik fel a következő észlelési módszerek valamelyikével:

-   **Determinisztikus észlelést**: Gyanús mintákat (szabályok alapján) észleli az SQL-ügyfél lekérdezések, amelyek megfelelnek az ismert támadásokat. Ez a módszer több magas észlelési és alacsony hamis pozitív, de korlátozott lefedettség, mert a "atomi kizárását." kategórián belül esik

-   **Viselkedési észlelési**: Észleli a rendellenes tevékenység, amely az adatbázis által a legutóbbi 30 nap során nem jelent rendellenes viselkedés. Példák az SQL-ügyfél rendellenes tevékenység sikertelen bejelentkezés vagy lekérdezéseket, nagy mennyiségű adatok kibontása közben, szokatlan kanonikus lekérdezések vagy nem használta az adatbázis eléréséhez használt IP-címek ugrásszerű is lehet.

### <a name="application-gateway-web-application-firewall"></a>Application Gateway webalkalmazási tűzfal

[Webalkalmazási tűzfal (WAF) webes](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) szolgáltatása [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) webes alkalmazásokhoz, amelyek egy application gateway a standard szintű védelmet biztosít, amely [kézbesítésiAlkalmazásvezérlés](https://kemptechnologies.com/in/application-delivery-controllers) funkciók. Webalkalmazási tűzfal ezt úgy éri résének többségével szemben a [Open Web Application Security Project (OWASP) 10 leggyakoribb webes biztonsági rések leggyakoribb](https://www.owasp.org/index.php/Top_10_2010-Main).

![Application Gateway webalkalmazásokhoz használható tűzfal diagramja](./media/azure-threat-detection/azure-threat-detection-fig13.png)

Védelmi módszerek a következők:

-   SQL-injektálás elleni védelem.

-   Adatbázisközi hely webhelyközi védelme.

-   Gyakori webes támadások védelme, például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás.

-   HTTP protokoll megsértése elleni védelem.

-   Elleni HTTP protokollanomáliák, például hiányzó gazdagép-felhasználói ügynök, és fogadja el a fejlécek.

-   Robotprogramok, webbejárók és képolvasók elleni megelőzése.

-   Gyakori alkalmazások konfigurációs hibáinak észlelése (vagyis Apache, IIS és így tovább).

A WAF konfigurálása az application gatewayben a következő előnyökkel jár:

-   A webalkalmazás védi a webes biztonsági résektől és támadásoktól a háttér-kód módosítása nélkül.

-   Több webalkalmazás védi egy application gateway mögött egy időben. Egy application gateway akár 20 webhely üzemeltetését is támogatja.

-   Figyelők webes támadások ellen, alkalmazások valós idejű application gateway WAF-naplók által generált jelentések segítségével.

-   Segít a megfelelőségi követelmények teljesítése érdekében. Egyes megfelelőség-ellenőrzési funkciókhoz szükséges összes internetkapcsolattal rendelkező végpontot WAF-megoldás által védendő.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Anomáliadetektálás API: Az Azure Machine Learninggel készült

Az Anomáliadetektáló API-t egy API-t, amely hasznos rendellenes minták számos felismeri az idősoros adatokat a rendszer. Az API-anomáliadetektálás pontszámot rendel az idősorozat, amely használható a riasztások generálásához, az egyes adatpontok irányítópultokról, illetve a hibajegykezelő rendszereihez való kapcsolódás.

A [Anomáliadetektálás API](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) képes észlelni a rendellenességek észlelését, az idősorozat-adatok a következők:

-   **Csúcsok és süllyedések**: Ha használja a megfigyeléshez egy sikertelen bejelentkezések száma, vagy egy e-kereskedelmi webhelyen, a szokatlan csúcsok kivételek számát, vagy DIP biztonsági támadások vagy a szolgáltatás megszakadását utalhat.

-   **Pozitív és negatív trendek**: Használja a megfigyeléshez memóriahasználatát, amikor potenciális memóriavesztést zsugorítását a szabad memória méretét jelzi. A szolgáltatás a figyelési várólistájának hossza a egy tartós növekedési trend mögöttes szoftverproblémára utalhat utalhat.

-   **Szintváltozások és az értékek dinamikus tartományának változásai**: A késések után egy szolgáltatás frissítése vagy az alacsonyabb szintű kivételek figyelése hasznos lehet után megváltozik.

A machine learning-alapú API lehetővé teszi, hogy:

-   **Rugalmas és hatékony észlelési**: A rendellenességek észlelése modellek engedélyezése a felhasználóknak a érzékenységi beállítások konfigurálására, és észlelje a rendellenességeket között évszaknak megfelelő és nem szezonális. A felhasználók módosíthatják a rendellenességek észlelése modellt, hogy az észlelési API több vagy kevesebb bizalmas, saját igényeiknek megfelelően. Ez azt jelentené, hogy az adatokban, és anélkül szezonális minták a több vagy kevesebb látható rendellenességek észlelése.

-   **Méretezhető és időben történő észlelése**: A hagyományos módon e küszöbértékek a figyelési szakértői adatait állította be a költséges és nem méretezhető több millió dinamikusan módosítása az adatkészletek. Az anomáliadetektálási észlelési modellek az API-t a küszöbértéket, és a modellek automatikusan hangolt előzményadatok és a valós idejű adatok alapján.

-   **Gyakorlatban is használható, és proaktív észlelés**: Lassú trend és szint módosításának észlelési korai közüli alkalmazhatók. A korai rendellenes azt jelzi, hogy a rendszer észleli az emberek vizsgálja meg, és reagálhat rájuk a problémás területek közvetlen használható. Ezenkívül legfelső szintű elemzési modellek és a riasztási eszközök felett az anomáliadetektáló API szolgáltatás fejleszthetők.

Az anomáliadetektáló API-forgatókönyvek, például a szolgáltatás állapotát és a figyelés, IoT, a alkalmazásteljesítmény-figyelés és a hálózati forgalom figyelése KPI számos leghatékonyabb megoldás. Az alábbiakban néhány gyakori felhasználási helyzetek, ahol az API-t akkor lehet hasznos:

- IT-részlegek számára szükséges eszközöket, nyomon követheti az eseményeket, a hibakód, a használati napló és a teljesítmény (Processzor, memória, és így tovább) időben.

-   Online kereskedelmi helyek szeretné nyomon követni a felhasználói tevékenységek, oldalmegtekintések, kattintással és így tovább.

-   Segédprogram vállalatok szeretné nyomon követni az eltérésmásoláshoz, gáz, áram- és egyéb erőforrások felhasználását.

-   Létesítmény vagy épületen belül szolgáltatások szeretné figyelni a hőmérséklet, eltüntetéséhez, forgalom és így tovább.

-   IoT/gyártók használandó érzékelőktől kapott adatok idősor figyelő munkahelyi flow, minőségi és így tovább.

-   Szolgáltatók, például a telefonos ügyfélszolgálatok, kell szolgáltatás igény szerint trendek, incidens kötet figyelése várakozási sor hossza, és így tovább.

-   Business analytics csoportok üzleti KPI-k (például értékesítési mennyiség, ügyfél hangulati vagy díjszabás) figyelni szeretné a valós idejű rendellenes történő áthelyezését.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) a Microsoft Cloud biztonsági verem kritikus összetevője. Egy olyan átfogó megoldás, amely segít a szervezete teljes mértékben kihasználhatja a felhőalkalmazások ígérete váltáskor. Hogy a vezérlési tevékenységek jobb átláthatóságával tartja. Emellett a felhőalkalmazások kritikus adatainak védelmét is növeli.

A Cloud App Security eszközeivel könnyebb az informatikai árnyék-infrastruktúra feltárása, a kockázatfelmérés, a szabályzatok kényszerítése, a tevékenységek vizsgálata, valamint a veszélyek megakadályozása. A szervezet így biztonságosan helyezheti át az adatait a felhőbe a kritikus adatok felügyelete mellett.

| | |
|---|---|
| Ismertetők | Fedje fel a Cloud App Securityvel informatikai árnyék-infrastruktúra. Alkalmazások, tevékenységek, felhasználók, adatok és fájlok a felhőalapú környezetben felderítésével nagyobb a láthatóságot. Ismerje meg, amely a felhőhöz csatlakoztatott külső alkalmazásoknak.|
|Vizsgálat | Vizsgálja meg a felhőalkalmazások által felhőalapú nyomkereső eszközökkel, így részletesen elemezheti kockázatos alkalmazásokat, egyes felhasználókat és a hálózati fájlokat. Minták keresése a felhőből gyűjtött adatokban. A felhőt figyelő jelentéseket hozhat létre. |
| Vezérlés | Mérsékelheti a kockázatot szabályzatok és riasztások maximális szabályozhatja a hálózati eléréséhez. A Cloud App Security segítségével biztonságos, engedélyezett felhőalkalmazás-alternatívákra telepítheti át a felhasználóit. |
| Védelem | A Cloud App Security használatával engedélyezését vagy alkalmazások az engedélyeiket, adatveszteség-megelőzés, szabályozhatja az engedélyeket és megosztása, valamint testreszabott jelentéseket és riasztásokat hozhat létre. |
| Vezérlés | Mérsékelheti a kockázatot szabályzatok és riasztások maximális szabályozhatja a hálózati eléréséhez. A Cloud App Security segítségével biztonságos, engedélyezett felhőalkalmazás-alternatívákra telepítheti át a felhasználóit. |
| | |


![Cloud App Security diagramja](./media/azure-threat-detection/azure-threat-detection-fig14.png)

A cloud App Security integrálja a láthatóságot a felhővel:

-   a Cloud Discoveryvel – leképezése és azonosítása a felhőalapú környezet és a felhőalapú alkalmazások a szervezet használja.

-   Alkalmazások engedélyezése és a nem a felhőben lévő alkalmazásokhoz.

-   könnyen telepíthető alkalmazás-összekötők, amelyek kihasználják a szolgáltatói API-k, a láthatósága és irányítása érdekében alkalmazások használatával.

-   útmutatás nyújtása a folyamatos ellenőrzés a beállítást, és rendszeres finomhangolásával való elősegítésével, házirendek szerint.

Ezekből a forrásokból származó adatok gyűjtése, a Cloud App Security kifinomult elemzést futtat rajta. Azonnal riasztást küld a rendellenes tevékenységeket, és a felhőkörnyezet mélyebb betekintést nyújt. A Cloud App Security szolgáltatásban konfigurálhat egy szabályzatot, és, amellyel megvédheti a felhőalapú környezetben.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Külső speciális Fenyegetésészlelési képességeket az Azure Marketplace-en

### <a name="web-application-firewall"></a>Web Application Firewall (Webalkalmazási tűzfal)

Webalkalmazási tűzfal ellenőrzi a bejövő webes forgalmat, és letiltja a SQL injektálások, többhelyes parancsfájl-kezelési, a rosszindulatú feltöltéseket, alkalmazás DDoS-támadások és egyéb támadásokat a webalkalmazások célozza. Szintén átvizsgálja a válaszokat a háttér-kiszolgálókról az adatveszteség-megelőzési (DLP). Az integrált hozzáférés ellenőrzési motor lehetővé teszi, hogy a rendszergazdák létrehozhatnak részletes hozzáférés-vezérlés házirendjét hitelesítési, engedélyezési, és nyilvántartási (AAA), amely révén a szervezetek erős hitelesítés és felhasználói vezérlő.

Webalkalmazási tűzfal az alábbi előnyöket nyújtja:

-   Észleli és blokkolja az SQL injektálások, webhelyek közötti parancsfájl-kezelési, a rosszindulatú feltöltéseket, alkalmazásra irányuló DDoS vagy az alkalmazás elleni esetleges támadások.

-   Hitelesítés és hozzáférés-vezérlés.

-   Figyeli az érzékeny adatok észlelése és maszkolandó vagy blokkolni lehessen a információkat szivárogjanak a kimenő forgalmat.

-   A gyorsabb ütemben webes alkalmazás tartalmát, például gyorsítótárazását, tömörítését és egyéb forgalom optimalizálása képességeivel.

Az Azure Marketplace-en rendelkezésre álló webalkalmazás-tűzfalak példákért lásd [Barracuda WAF, Brocade virtuális webalkalmazási tűzfal (vWAF), Imperva SecureSphere és a ThreatSTOP IP-tűzfal](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>További lépések

- [Az Azure Security Center észlelési funkciói](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities): Segíti az azonosítását, amelyek az Azure-erőforrások célként, és biztosítja a gyors reagálás szükséges aktív fenyegetéseket.

- [Az Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): Segít a cím az az adatbázis elleni lehetséges fenyegetések elhárítását kapcsolatos elvárásainak.
