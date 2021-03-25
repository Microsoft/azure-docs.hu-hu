---
title: Az Azure Security bemutatása | Microsoft Docs
description: Ismerkedjen meg az Azure biztonságával, annak különböző szolgáltatásaival, és hogyan működik együtt az Áttekintés elolvasásával.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: TomSh
ms.openlocfilehash: b5f9df4e6f682b5d1e9e3cd35affe6e4191e3d53
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047779"
---
# <a name="introduction-to-azure-security"></a>Bevezetés az Azure biztonsági megoldásaiba

## <a name="overview"></a>Áttekintés

Tudjuk, hogy a biztonság az egyik a felhőben, és mennyire fontos, hogy pontos és kellő időben tájékozódjon az Azure biztonságáról. Az Azure az alkalmazások és szolgáltatások használatának egyik legjobb oka az, hogy kiaknázza a biztonsági eszközök és képességek széles körét. Ezek az eszközök és képességek segítik a biztonságos megoldások létrehozását a biztonságos Azure platformon. Microsoft Azure biztosítja az ügyféladatok titkosságát, integritását és rendelkezésre állását, ugyanakkor lehetővé teszi az átlátható elszámoltathatóságot is.

Ez a cikk átfogó képet nyújt az Azure-ban elérhető biztonságról.

### <a name="azure-platform"></a>Azure-platform

Az Azure egy nyilvános felhőalapú szolgáltatási platform, amely az operációs rendszerek, a programozási nyelvek, a keretrendszerek, az eszközök, az adatbázisok és az eszközök széles választékát támogatja. Linux-tárolókat is futtathat a Docker-integrációval; JavaScript-, Python-, .NET-, PHP-, Java-és Node.js-alkalmazásokat hozhat létre. az iOS-, Android-és Windows-eszközökön is létrehozhatók háttérrendszer.

Az Azure nyilvános felhőalapú szolgáltatásai ugyanazokat a technológiákat támogatják, mint a fejlesztők és az informatikai szakemberek, akik már használják és bíznak. Ha a-ra épít, vagy áttelepíti az IT-eszközöket, egy nyilvános felhőalapú szolgáltatóra támaszkodik, amely a szervezet képességei alapján gondoskodik az alkalmazások és adatok védelméről a szolgáltatásokkal és az általuk biztosított vezérlőkkel a felhőalapú eszközök biztonságának kezeléséhez.

Az Azure infrastruktúráját olyan létesítményekből tervezték, amelyek egyszerre több millió ügyfelet üzemeltetnek, és olyan megbízható alapot biztosítanak, amely alapján a vállalatok megfelelnek a biztonsági követelményeknek.

Emellett az Azure a konfigurálható biztonsági beállítások széles körét biztosítja, és lehetővé teszi, hogy a biztonság testre szabható legyen, hogy megfeleljen a szervezete üzembe helyezésének egyedi követelményeinek. Ez a dokumentum segít megérteni, hogy az Azure biztonsági képességei hogyan segíthetnek a követelmények teljesítésében.

> [!Note]
> A dokumentum elsődleges célja az ügyfelek felé irányuló vezérlők használata, amelyek segítségével testre szabhatja és növelheti az alkalmazások és szolgáltatások biztonságát.
>
> További információ arról, hogy miként védi a Microsoft az Azure platformot: az [Azure-infrastruktúra biztonsága](infrastructure.md).

## <a name="summary-of-azure-security-capabilities"></a>Az Azure biztonsági képességeinek összefoglalása

A Cloud Service-modelltől függően az alkalmazás vagy szolgáltatás biztonságának kezeléséért felelős személy felelőssége is fennáll. Az Azure platformon elérhető lehetőségek állnak rendelkezésre, hogy segítséget nyújtsanak a feladatoknak a beépített funkciókkal, valamint az Azure-előfizetésben üzembe helyezhető partneri megoldásokkal.

A beépített funkciók hat funkcionális területen vannak rendszerezve: műveletek, alkalmazások, tárolás, hálózatkezelés, számítás és identitás. Az Azure platformon elérhető funkciókkal és képességekkel kapcsolatos további részleteket az alábbi hat területen tekintheti meg összefoglaló információk alapján.

## <a name="operations"></a>Üzemeltetés

Ez a szakasz további információkat tartalmaz a biztonsági műveletek főbb funkcióiról, valamint az ezekkel a képességekkel kapcsolatos összefoglaló információkkal kapcsolatban.

### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-introduction.md) segít megakadályozni, észlelni és reagálni a fenyegetésekre az Azure-erőforrások jobb láthatóságával és kézben tartásával. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Emellett a Security Center a biztonsági műveleteket is segíti azáltal, hogy egyetlen irányítópultot biztosít, amely a felszínre kerülő riasztásokat és javaslatokat azonnal végrehajtja. Gyakran a Security Center-konzolon egyetlen kattintással javíthatja a problémákat.

### <a name="azure-resource-manager"></a>Azure Resource Manager

A [Azure Resource Manager](../../azure-resource-manager/management/overview.md) lehetővé teszi, hogy csoportként működjön együtt a megoldás erőforrásaival. A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. Az üzembe helyezéshez [Azure Resource Manager sablont](../../azure-resource-manager/templates/overview.md) használ, és ez a sablon különböző környezetekben, például tesztelési, átmeneti és éles környezetben is működhet. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

Azure Resource Manager sablon alapú központi telepítések segítenek az Azure-ban üzembe helyezett megoldások biztonságának javításában, mivel a szabványos biztonsági vezérlési beállítások és a szabványosított sablon alapú központi telepítések integrálására is használhatók. Ez csökkenti a manuális központi telepítések során esetlegesen felmerülő biztonsági konfigurációs hibák kockázatát.

### <a name="application-insights"></a>Application Insights

A [Application Insights](../../azure-monitor/app/app-insights-overview.md) egy bővíthető Application Performance Management (APM) szolgáltatás webes fejlesztőknek. A Application Insights segítségével figyelheti az élő webalkalmazásokat, és automatikusan észlelheti a teljesítménnyel kapcsolatos rendellenességeket. Hatékony elemzési eszközöket tartalmaz, amelyek segítségével diagnosztizálhatja a problémákat, és megtudhatja, hogy a felhasználók miként tudják ténylegesen az alkalmazásaihoz. Az alkalmazást folyamatosan figyeli az alkalmazás futása során, a tesztelés során, illetve a közzététel vagy a telepítés után.

A Application Insights diagramokat és táblákat hoz létre, amelyek megmutatják, hogy a legtöbb felhasználó mikor kapja meg a legtöbb felhasználót, hogyan reagál az alkalmazásra, és milyen jól szolgálja ki azokat a külső szolgáltatások, amelyektől függenek.

Ha összeomlik, hiba vagy teljesítménnyel kapcsolatos probléma merül fel, részletesen megkeresheti a telemetria adatokat az OK diagnosztizálásához. A szolgáltatás pedig e-maileket küld, ha az alkalmazás rendelkezésre állása és teljesítménye megváltozik. Az alkalmazás betekintése így értékes biztonsági eszközvé válik, mivel segít a titkosság, integritás és rendelkezésre állási biztonság hármasban való rendelkezésre állásában.

### <a name="azure-monitor"></a>Azure Monitor

A [Azure monitor](/azure/monitoring-and-diagnostics/) vizualizációt, lekérdezést, útválasztást, riasztást, automatikus méretezést és automatizálást kínál az adatokhoz mind az Azure-előfizetésből ([műveletnapló](../../azure-monitor/essentials/platform-logs-overview.md)), mind az egyes Azure-erőforrásokból ([erőforrás-naplók](../../azure-monitor/essentials/platform-logs-overview.md)). A Azure Monitor használatával riasztást kaphat az Azure-naplókban létrehozott biztonsággal kapcsolatos eseményekről.

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

[Azure monitor naplók](../../azure-monitor/logs/log-query-overview.md) – az Azure-erőforrások mellett helyszíni és harmadik féltől származó felhőalapú infrastruktúra (például AWS) számára biztosít informatikai felügyeleti megoldást. Azure Monitorból származó adatok közvetlenül átirányíthatók Azure Monitor naplókba, így egy helyen láthatja a teljes környezet mérőszámait és naplóit.

Azure Monitor naplók hasznos eszközök lehetnek a kriminalisztikai és egyéb biztonsági elemzésekben, mivel az eszköz lehetővé teszi, hogy gyorsan keressen nagy mennyiségű biztonsággal kapcsolatos bejegyzést rugalmas lekérdezési megközelítéssel. Emellett a helyszíni [tűzfal és a proxy naplói is exportálhatók az Azure-ba, és az elemzéshez Azure monitor naplók használatával is elérhetők.](../../azure-monitor/agents/agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor

A [Azure Advisor](../../advisor/advisor-overview.md) egy személyre szabott felhőalapú tanácsadó, amely segít az Azure-beli üzembe helyezések optimalizálásában. A program elemzi az erőforrás-konfigurációs és -használati telemetriákat, Ezután olyan megoldásokat ajánl fel, amelyek segítenek az erőforrások [teljesítményének](../../advisor/advisor-performance-recommendations.md), [biztonságának](../../advisor/advisor-security-recommendations.md)és [megbízhatóságának](../../advisor/advisor-high-availability-recommendations.md) javításában, miközben lehetőség van a [teljes Azure-ráfordítás csökkentésére](../../advisor/advisor-cost-recommendations.md). A Azure Advisor biztonsági javaslatokat tesz elérhetővé, ami jelentősen javíthatja az Azure-ban üzembe helyezett megoldások általános biztonsági állapotát. Ezek az ajánlások a Azure Security Center által végzett biztonsági elemzésből származnak [.](../../security-center/security-center-introduction.md)

## <a name="applications"></a>Alkalmazások

A szakasz további információkat tartalmaz az alkalmazások biztonságának főbb funkcióiról, valamint az ezekkel a képességekkel kapcsolatos összegző információkkal kapcsolatban.

### <a name="web-application-vulnerability-scanning"></a>Webalkalmazások sebezhetőségének vizsgálata

Az [app Service-alkalmazás](../../app-service/overview.md) biztonsági rések tesztelésének megkezdéséhez az egyik legegyszerűbb módszer az, hogy az [integráció a sztaniol biztonsággal](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) lehetővé tegye az alkalmazáson belüli, egykattintásos biztonsági rések vizsgálatát. A teszt eredményeit egy könnyen értelmezhető jelentésben tekintheti meg, és megtudhatja, hogyan javíthatja ki az egyes biztonsági réseket részletes utasításokkal.

### <a name="penetration-testing"></a>Behatolástesztelés

Nem végzünk [bevezetési tesztelést](./pen-testing.md) az alkalmazásában, de tisztában vagyunk vele, hogy a saját alkalmazásaiban szeretné elvégezni a tesztelést. Ez jó dolog, mert az alkalmazások biztonságának növelése révén biztonságosabbá teheti a teljes Azure-ökoszisztémát. Miközben a Microsoft a toll tesztelési tevékenységeinek értesítése már nem szükséges, az ügyfeleknek továbbra is meg kell felelniük a [Bevezetési Microsoft Cloud behatolás tesztelési szabályainak](https://www.microsoft.com/msrc/pentest-rules-of-engagement).

### <a name="web-application-firewall"></a>Webalkalmazási tűzfal

Az [Azure Application Gateway](../../application-gateway/features.md#web-application-firewall) webalkalmazási TŰZFALA (WAF) megvédi a webalkalmazásokat a gyakori webalapú támadásoktól, például az SQL-injektálástól, a helyközi parancsfájl-támadásoktól és a munkamenet-eltérítéstől. Előre konfigurálva van az [Open Web Application Security Project (OWASP) által azonosított fenyegetésekkel szemben, mint az első 10 leggyakoribb biztonsági rés](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service-ben

[App Service hitelesítés/engedélyezés](../../app-service/overview-authentication-authorization.md) funkció lehetővé teszi, hogy az alkalmazás bejelentkezzen a felhasználókba, így nem kell módosítania a kódot az alkalmazás-háttérön. Egyszerű módszert biztosít az alkalmazás védelme és a felhasználónkénti adatmennyiség használata.

### <a name="layered-security-architecture"></a>Rétegzett biztonsági architektúra

Mivel [app Service környezetek](../../app-service/environment/app-service-app-service-environment-intro.md) egy [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md)üzembe helyezett elkülönített futtatókörnyezeti környezetet biztosítanak, a fejlesztők létrehozhatnak egy rétegzett biztonsági architektúrát, amely különböző szintű hálózati hozzáférést biztosít az egyes alkalmazások szintjeihez. Gyakori a célja, hogy elrejtse az API-t az általános Internet-hozzáféréstől, és csak a felsőbb rétegbeli webalkalmazások számára engedélyezze az API-k meghívását. A [hálózati biztonsági csoportok (NSG-k)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) olyan Azure Virtual Network alhálózatokon használhatók, amelyek app Service környezetet tartalmaznak az API-alkalmazásokhoz való nyilvános hozzáférés korlátozására.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Webkiszolgáló-diagnosztika és Application Diagnostics
[App Service Web Apps](../../app-service/troubleshoot-diagnostic-logs.md) diagnosztikai funkciókat biztosít a webkiszolgálóról és a webalkalmazásból származó adatok naplózásához. Ezeket a rendszer logikailag elkülöníti a webkiszolgáló-diagnosztika és az Application Diagnostics szolgáltatásban. A webkiszolgáló két fő előrelépést tartalmaz a webhelyek és alkalmazások diagnosztizálásában és hibaelhárításában.

Az első új szolgáltatás valós idejű állapotinformációkat tartalmaz az alkalmazáskészletekről, a munkavégző folyamatokról, a helyekről, az alkalmazás-tartományokról és a futó kérelmekről. A második új előnyök azok a részletes nyomkövetési események, amelyek a kérelem nyomon követésére szolgálnak a teljes kérelem-és reagálási folyamat során.

A nyomkövetési események összegyűjtésének engedélyezéséhez az IIS 7 konfigurálható úgy, hogy automatikusan rögzítse a teljes nyomkövetési naplókat XML formátumban az eltelt idő vagy a hibakódok alapján megadott kérelmek esetében.

## <a name="storage"></a>Tárolás
A szakasz további információkat tartalmaz az Azure Storage biztonságának főbb funkcióiról, valamint az ezekkel a képességekkel kapcsolatos összegző információkkal kapcsolatban.

### <a name="azure-role-based-access-control-azure-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)
Az [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../../role-based-access-control/overview.md)biztonságossá teheti a Storage-fiókját. Az adathozzáférésre vonatkozó biztonsági szabályzatokat kényszerítő szervezetek számára elengedhetetlen a hozzáférés korlátozása a [szükséges ismeret](https://en.wikipedia.org/wiki/Need_to_know) és a [legalacsonyabb jogosultsági szintű](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági elvek alapján. Ezek a hozzáférési jogosultságok akkor érhetők el, ha a megfelelő Azure-szerepkört hozzárendeli a csoportokhoz és alkalmazásokhoz egy bizonyos hatókörben. A jogosultságok felhasználókhoz való hozzárendeléséhez használhatja az [Azure beépített szerepköreit](../../role-based-access-control/built-in-roles.md), például a Storage-fiók közreműködőjét. A Storage-fiókok [Azure Resource Manager](../../storage/blobs/security-recommendations.md#data-protection) modell használatával történő elérését az Azure RBAC keresztül lehet szabályozni.

### <a name="shared-access-signature"></a>Közös hozzáférési aláírás
A [közös hozzáférésű jogosultságkód (SAS)](../../storage/common/storage-sas-overview.md) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz. Az SAS azt jelenti, hogy a megadott időszakra és meghatározott engedélyekkel engedélyezheti az ügyfél számára a Storage-fiókban lévő objektumokra vonatkozó korlátozott engedélyeket. Ezeket a korlátozott engedélyeket a fiók hozzáférési kulcsainak megosztása nélkül is megadhatja.

### <a name="encryption-in-transit"></a>Titkosítás átvitel közben
Az átvitel közbeni titkosítás egy olyan mechanizmus, amely az adatok védelmét a hálózatokon keresztül továbbítja. Az Azure Storage használatával az alábbiakkal védheti meg az adatvédelmet:
- [Átviteli szintű titkosítás](../../storage/blobs/security-recommendations.md), például https, ha az Azure Storage-ba vagy az-ba helyezi át az adatátvitelt.

- [Vezetékes titkosítás](../../storage/blobs/security-recommendations.md), például [SMB 3,0 titkosítás](../../storage/blobs/security-recommendations.md) az [Azure-fájlmegosztás](../../storage/files/storage-dotnet-how-to-use-files.md)számára.

- Ügyféloldali titkosítás, amely a tárterületre való átvitel előtt titkosítja az adatátvitelt, és visszafejti az adatmennyiséget a tárterületről való átadást követően.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
Számos szervezet esetében az adattitkosítás az adatok védelme, a megfelelőség és az adatok szuverenitása szempontjából kötelező lépés. Három Azure Storage-beli biztonsági funkció áll rendelkezésre, amelyek a "nyugalmi állapotban" lévő adatok titkosítását teszik lehetővé:

- [Storage Service encryption](../../storage/common/storage-service-encryption.md) lehetővé teszi, hogy a Storage szolgáltatás automatikusan titkosítsa az adattitkosítást az Azure Storage-ba való íráskor.

- Az [ügyféloldali titkosítás](../../storage/common/storage-client-side-encryption.md) emellett biztosítja a titkosítás nyugalmi funkcióját is.

- [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) lehetővé teszi a IaaS virtuális gépek által használt operációsrendszer-lemezek és adatlemezek titkosítását.

### <a name="storage-analytics"></a>Storage Analytics

[Azure Storage Analytics](/rest/api/storageservices/fileservices/storage-analytics) naplózást végez, és metrikai adatokat biztosít egy Storage-fiókhoz. Ezeket az adatokat felhasználhatja a kérelmek nyomon követéséhez, a használati trendek elemzéséhez és a Storage-fiókkal kapcsolatos problémák diagnosztizálásához. A Storage Analytics naplózza a tárolási szolgáltatásoknak elküldött sikeres és sikertelen kérelmek részletes információit. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kéréseket a rendszer a lehető legjobb módon naplózza. A hitelesített kérések alábbi típusai vannak rögzítve:

- Sikeres kérések.
- Sikertelen kérések, beleértve az időtúllépést, a szabályozást, a hálózatot, az engedélyezést és az egyéb hibákat.
- Közös hozzáférésű aláírást (SAS) használó kérelmek, beleértve a sikertelen és sikeres kérelmeket.
- Elemzési adatkérések.

### <a name="enabling-browser-based-clients-using-cors"></a>Browser-Based-ügyfelek engedélyezése a CORS használatával

Az eltérő [eredetű erőforrás-megosztás (CORS)](/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) egy olyan mechanizmus, amely lehetővé teszi, hogy a tartományok minden más engedélyt adjanak a többi erőforráshoz való hozzáféréshez. A felhasználói ügynök további fejléceket küld, így biztosítva, hogy az adott tartományból betöltött JavaScript-kód hozzáférjen egy másik tartományban található erőforrásokhoz. Az utóbbi tartomány ezután további fejlécekkel válaszol, amelyek engedélyezik vagy megtagadják az eredeti tartományhoz való hozzáférést az erőforrásokhoz.

Az Azure Storage Services mostantól támogatja a CORS-t, így ha beállította a szolgáltatás CORS-szabályait, a rendszer kiértékeli, hogy a szolgáltatásra vonatkozó megfelelő hitelesített kérést egy másik tartományon keresztül hozták-e létre.

## <a name="networking"></a>Hálózatkezelés

A szakasz további információkat tartalmaz az Azure hálózati biztonságának főbb funkcióiról, valamint az ezekkel a képességekkel kapcsolatos összegző információkkal kapcsolatban.

### <a name="network-layer-controls"></a>Hálózati réteg vezérlői

A hálózati hozzáférés-vezérlés az adott eszközök vagy alhálózatok kapcsolatának korlátozására szolgál, és a hálózati biztonság magját jelenti. A hálózati hozzáférés-vezérlés célja annak biztosítása, hogy a virtuális gépek és szolgáltatások csak azokhoz a felhasználókhoz és eszközökhöz legyenek elérhetők, amelyekhez elérhetővé szeretné tenni őket.

#### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

A [hálózati biztonsági csoport (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md#security) egy alapszintű, állapot-nyilvántartó csomagszűrő tűzfal, amely lehetővé teszi, hogy 5 rekord alapján vezérelje a hozzáférést. A NSG nem biztosítanak alkalmazás-rétegbeli ellenőrzést vagy hitelesített hozzáférés-vezérlést. Használhatók az alhálózatok közötti adatforgalom vezérlésére az Azure Virtual Network és az Azure-Virtual Network és az Internet közötti forgalom között.

#### <a name="route-control-and-forced-tunneling"></a>Irányítás és kényszerített bújtatás

Az útválasztási viselkedés az Azure-beli virtuális hálózatokon való vezérlésének lehetősége kritikus hálózati biztonsági és hozzáférés-vezérlési képesség. Ha például biztos szeretne lenni abban, hogy az Azure Virtual Network felé irányuló összes forgalom áthalad a virtuális biztonsági berendezésen, meg kell tudnia határozni és testreszabni az útválasztási viselkedést. Ezt úgy teheti meg, hogy User-Defined útvonalakat konfigurál az Azure-ban.

A [felhasználó által megadott útvonalak](../../virtual-network/virtual-networks-udr-overview.md#custom-routes) lehetővé teszik a bejövő és kimenő útvonalak testreszabását az egyes virtuális gépekre vagy alhálózatokra áthelyezett forgalom számára, hogy a lehető legbiztonságosabb útvonalat biztosítsa. A [kényszerített bújtatás](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) olyan mechanizmus, amellyel biztosítható, hogy a szolgáltatások ne kezdeményezzenek kapcsolatot az internethez csatlakozó eszközökhöz.

Ez különbözik a bejövő kapcsolatok fogadásának és a rájuk adott válaszoknak. Az előtér-webkiszolgálóknak válaszolnia kell az internetes gazdagépekről érkező kérésekre, és így az internetre irányuló forgalom engedélyezve van a webkiszolgálók számára, és a webkiszolgálók válaszolni tudnak.

A kényszerített bújtatás általában arra szolgál, hogy a kimenő forgalmat az internetre kényszerítse, hogy a helyszíni biztonsági proxykat és tűzfalakat is átugorjon.

#### <a name="virtual-network-security-appliances"></a>Biztonsági berendezések Virtual Network

A hálózati biztonsági csoportok, User-Defined útvonalak és a kényszerített bújtatás biztosítja az [OSI-modell](https://en.wikipedia.org/wiki/OSI_model)hálózati és átviteli rétegeinek biztonsági szintjét, előfordulhat, hogy a verem magasabb szintjein engedélyezni szeretné a biztonságot. Ezeket a kibővített hálózati biztonsági szolgáltatásokat Azure Partner Network Security Appliance-megoldás használatával érheti el. Az Azure [Marketplace](https://azure.microsoft.com/marketplace/) -en megtekintheti a legfrissebb Azure Partner Network biztonsági megoldásokat, és megkeresheti a "biztonság" és a "hálózati biztonság" kifejezést.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Az Azure virtuális hálózat (VNet) a saját, felhőben található hálózatának megfelelője. Ez az előfizetéshez dedikált Azure Network Fabric logikai elkülönítése. A hálózaton belül teljes mértékben irányíthatja az IP-címblokkokat, a DNS-beállításokat, a biztonsági házirendeket és az útválasztási táblázatokat. A VNet alhálózatokra is feloszthatók, és az Azure IaaS virtuális gépek (VM-EK) és/vagy [Cloud Services (Péter szerepkör példányai)](../../cloud-services/cloud-services-choose-me.md) helyezhetők üzembe az Azure Virtual Networks szolgáltatásban.

A virtuális hálózatot ezen felül a helyszíni hálózathoz is csatlakoztathatja az Azure-ban elérhető [kapcsolati lehetőségek](../../vpn-gateway/index.yml) egyikével. Lényegében kiterjesztheti a hálózatot az Azure-ra, az IP-címblokkok teljes körű irányítása és a vállalati méretű Azure által nyújtott előnyök mellett.

Az Azure Networking különböző biztonságos távelérési forgatókönyveket támogat. Ilyenek például a következők:

- [Különálló munkaállomások összekötése egy Azure-Virtual Network](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

- [Helyszíni hálózat összekapcsolása Azure-Virtual Network VPN-kapcsolattal](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Helyszíni hálózat csatlakoztatása Azure-Virtual Network dedikált WAN-kapcsolattal](../../expressroute/expressroute-introduction.md)

- [Azure-beli virtuális hálózatok összekapcsolhatók egymással](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="azure-private-link"></a>Azure Private Link

Az [Azure Private link](https://azure.microsoft.com/services/private-link/) lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage és a SQL Database) és az Azure-beli üzemeltetett ügyfél-szolgáltatások elérését privát [végponton](../../private-link/private-endpoint-overview.md)keresztül a virtuális hálózaton. Az Azure Private link használatával történő beállítás és felhasználás konzisztens az Azure Pásti, az ügyfél és a megosztott partneri szolgáltatások között. A virtuális hálózatról az Azure-szolgáltatásra irányuló forgalom mindig a Microsoft Azure gerinc hálózatán marad.

A [privát végpontok](../../private-link/private-endpoint-overview.md) lehetővé teszik a kritikus Azure-szolgáltatási erőforrások védelmét csak a virtuális hálózatok számára. Az Azure Private Endpoint egy magánhálózati IP-címet használ a VNet, hogy egy privát és biztonságos kapcsolatot létesítsen az Azure privát kapcsolaton keresztül, és hatékonyan hozza a szolgáltatást a VNet. A virtuális hálózatnak a nyilvános internethez való kimutatása már nem szükséges az Azure-szolgáltatások használatához. 

Saját privát kapcsolati szolgáltatást is létrehozhat a virtuális hálózatában. Az [Azure Private link Service](../../private-link/private-link-service-overview.md) a saját szolgáltatására vonatkozik, amelyet az Azure privát kapcsolata működtet. Az Azure standard Load Balancer mögött futó szolgáltatás engedélyezhető a magánjellegű kapcsolatokhoz való hozzáféréshez, így a szolgáltatás felhasználóinak saját virtuális hálózatról férhetnek hozzá. Ügyfelei létrehozhatnak egy privát végpontot a virtuális hálózaton belül, és leképezhetők a szolgáltatásba. A szolgáltatásnak a nyilvános internethez való kimutatása már nem szükséges az Azure-szolgáltatások megjelenítéséhez. 

### <a name="vpn-gateway"></a>VPN Gateway

Az Azure-Virtual Network és a helyszíni hely közötti hálózati forgalom elküldéséhez létre kell hoznia egy VPN-átjárót az Azure-Virtual Networkhoz. A [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) olyan virtuális hálózati átjáró, amely titkosított forgalmat küld nyilvános kapcsolaton keresztül. A VPN-átjárók használatával az Azure-beli virtuális hálózatok közötti adatforgalmat az Azure hálózati hálón keresztül is elküldheti.

### <a name="express-route"></a>Express Route

A Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) egy dedikált WAN-kapcsolat, amely lehetővé teszi, hogy a helyszíni hálózatait a Microsoft Cloud-ra bővítse egy olyan dedikált privát kapcsolaton keresztül, amely egy kapcsolati szolgáltató által könnyíti meg.

![Express Route](./media/overview/azure-security-figure-1.png)

A ExpressRoute használatával kapcsolatokat létesíthet a Microsoft Cloud Services, például a Microsoft Azure, a Microsoft 365 és a CRM Online számára. A kapcsolatok lehetnek: bármely elemek közötti (IP VPN) hálózat, pontok közötti Ethernet-hálózat vagy egy virtuális keresztkapcsolat egy kapcsolatszolgáltatón keresztül egy közös elhelyezési létesítményben.

A ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, így a VPN-alapú megoldásoknál biztonságosabbnak tekinthetik. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.

### <a name="application-gateway"></a>Application Gateway

A Microsoft [Azure Application Gateway](../../application-gateway/overview.md) egy [alkalmazás-továbbítási vezérlőt (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) biztosít szolgáltatásként, amely különböző 7. rétegbeli terheléselosztási funkciókat kínál az alkalmazásához.

![Application Gateway](./media/overview/azure-security-figure-2.png)

Lehetővé teszi a webfarmok hatékonyságának optimalizálását azáltal, hogy kiszervezi a CPU-igényes TLS-megszakítást a Application Gateway (más néven "TLS-kiszervezés" vagy "TLS-áthidaló"). Emellett további 7. rétegbeli útválasztási lehetőségeket is biztosít, beleértve a bejövő forgalom ciklikus időszeleteléses elosztását, a cookie-alapú munkamenet-affinitást, az URL-alapú útválasztást, valamint több webhely üzemeltetését egyetlen Application Gateway mögött. Az Azure Application Gateway egy 7. rétegbeli terheléselosztó.

Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak.

Az alkalmazás számos Application Delivery Controller (ADC) funkciót kínál, többek között a HTTP-terheléselosztást, a cookie-alapú munkamenet-affinitást, a [TLS-kiszervezést](../../web-application-firewall/ag/tutorial-restrict-web-traffic-powershell.md), az egyéni állapotú mintavételeket, a többhelyes és sok más támogatást.

### <a name="web-application-firewall"></a>Webalkalmazási tűzfal

A webalkalmazási tűzfal az [Azure Application Gateway](../../application-gateway/overview.md) szolgáltatása, amely védelmet nyújt az Application Gatewayt használó webalkalmazásoknak a standard Application Delivery Control (ADC) függvényekhez. A webalkalmazási tűzfal ezt úgy éri el, hogy védelmet nyújt az alkalmazásoknak az OWASP 10 leggyakoribb webes biztonsági résének többségével szemben.

![Webalkalmazási tűzfal](./media/overview/azure-security-figure-3.png)

- SQL-injektálás elleni védelem

- Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

- HTTP protokoll megsértése elleni védelem

- HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

- Robotprogramok, webbejárók és képolvasók elleni védelem

- Az alkalmazások gyakori konfigurációs beállításainak észlelése (vagyis Apache, IIS stb.)

A webes támadásokkal szembeni védelmet nyújtó központi webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és nagyobb biztonságot ad az alkalmazásnak a behatolások jelentette veszéllyel szemben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen lehet átalakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

### <a name="traffic-manager"></a>Traffic Manager

A Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) segítségével szabályozható a különböző adatközpontokban lévő szolgáltatási végpontok felhasználói forgalmának elosztása. A Traffic Manager által támogatott szolgáltatási végpontok közé tartoznak az Azure-beli virtuális gépek, a Web Apps és a Cloud Services. A Traffic Manager külső, nem Azure-beli végpontokkal is használható. Traffic Manager a tartománynévrendszer (DNS) használatával irányítja az ügyfelek kérelmeit a legmegfelelőbb végpontra a [forgalmi útválasztási módszer](../../traffic-manager/traffic-manager-routing-methods.md) és a végpontok állapota alapján.

Traffic Manager számos forgalom-útválasztási módszert kínál a különböző alkalmazási igények kielégítésére, a végpont állapotának [figyelésére](../../traffic-manager/traffic-manager-monitoring.md)és az automatikus feladatátvételre. A Traffic Manager ellenáll a meghibásodásoknak, beleértve akár egy egész Azure-régió meghibásodását is.

### <a name="azure-load-balancer"></a>Azure Load Balancer

Az [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) magas rendelkezésre állást és hálózati teljesítményt biztosít alkalmazásai számára. Ez egy 4. rétegbeli (TCP, UDP) terheléselosztó, amely a bejövő forgalmat egy elosztott terhelésű készletben definiált szolgáltatások kifogástalan példányai között osztja szét. A Azure Load Balancer a következőre konfigurálható:

- A bejövő internetes forgalom terheléselosztása a virtuális gépekre. Ezt a konfigurációt [nyilvános terheléselosztásnak](../../load-balancer/components.md#frontend-ip-configurations)nevezzük.

- A virtuális hálózatban lévő virtuális gépek, a Cloud Services-beli virtuális gépek, illetve a helyszíni számítógépek és a virtuális gépek közötti adatforgalom terheléselosztása egy telephelyi virtuális hálózaton. Ezt a konfigurációt [belső terheléselosztásnak](../../load-balancer/components.md#frontend-ip-configurations)nevezzük.

- Külső forgalom továbbítása egy adott virtuális géphez

### <a name="internal-dns"></a>Belső DNS

A VNet használt DNS-kiszolgálók listáját a felügyeleti portál vagy a hálózati konfigurációs fájlban kezelheti. Az ügyfél akár 12 DNS-kiszolgálót is hozzáadhat az egyes VNet. A DNS-kiszolgálók megadásakor fontos ellenőrizni, hogy az ügyfél DNS-kiszolgálóit az ügyfél környezetének megfelelő sorrendben sorolja-e fel. A DNS-kiszolgálók listája nem működik ciklikus multiplexelés esetén. Ezeket a rendszer a megadott sorrendben használja. Ha a lista első DNS-kiszolgálója képes elérni, az ügyfél ezt a DNS-kiszolgálót használja, függetlenül attól, hogy a DNS-kiszolgáló megfelelően működik-e. Ha módosítani szeretné az ügyfél virtuális hálózatához tartozó DNS-kiszolgáló sorrendjét, távolítsa el a DNS-kiszolgálókat a listából, és adja őket vissza az ügyfél által kívánt sorrendben. A DNS a "CIA" biztonsági hármas rendelkezésre állási aspektusát támogatja.

### <a name="azure-dns"></a>Azure DNS

A tartománynévrendszer vagy a DNS a webhely vagy szolgáltatás nevének az IP-címére való fordítására (vagy feloldására) felelős. Az [Azure DNS](../../dns/dns-overview.md) egy üzemeltetési szolgáltatás, amely a Microsoft Azure infrastruktúráját használja a DNS-tartományok névfeloldásához. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. A DNS a "CIA" biztonsági hármas rendelkezésre állási aspektusát támogatja.

### <a name="azure-monitor-logs-nsgs"></a>Azure Monitor naplók NSG

A következő diagnosztikai naplózási kategóriákat engedélyezheti a NSG:

- Esemény: olyan bejegyzéseket tartalmaz, amelyek NSG-szabályait a rendszer MAC-címen alapuló virtuális gépekre és példány-szerepkörökre alkalmazza. A szabályok állapotát 60 másodpercenként gyűjti a rendszer.

- Szabályok számláló: azokat a bejegyzéseket tartalmazza, amelyekkel az egyes NSG-szabályok a forgalom megtagadására vagy engedélyezésére vonatkoznak.

### <a name="security-center"></a>Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) folyamatosan elemzi az Azure-erőforrások biztonsági állapotát a hálózati biztonsággal kapcsolatos ajánlott eljárásokhoz. Ha Security Center észleli a potenciális biztonsági réseket, [javaslatokat](../../security-center/security-center-recommendations.md) hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának lépésein az erőforrások megerősítéséhez és védelméhez.

## <a name="compute"></a>Compute
A szakasz további információkat tartalmaz ezen terület főbb funkcióiról, valamint az ezekkel a képességekkel kapcsolatos összegző információkkal kapcsolatban.

### <a name="antimalware--antivirus"></a>Antimalware & víruskereső
Az Azure IaaS olyan biztonsági gyártóktól származó kártevő szoftvereket használhat, mint például a Microsoft, a Symantec, a Trend Micro, a McAfee és a Kaspersky a virtuális gépek kártékony fájlokkal, adware-mel és más fenyegetésekkel szembeni védelméhez. A [Microsoft antimalware](antimalware.md) for Azure Cloud Services és Virtual Machines egy védelmi képesség, amely segít azonosítani és eltávolítani a vírusokat, kémprogramokat és egyéb kártevő szoftvereket. A Microsoft antimalware konfigurálható riasztásokat biztosít, amikor az ismert kártékony vagy nemkívánatos szoftverek megkísérlik telepíteni vagy futtatni magukat az Azure-rendszereken. A Microsoft antimalware szolgáltatás a Azure Security Center használatával is telepíthető

### <a name="hardware-security-module"></a>Hardveres biztonsági modul
A titkosítás és a hitelesítés nem javítja a biztonságot, kivéve, ha maguk a kulcsok védve vannak. A kritikus fontosságú titkok és kulcsok felügyeletét és biztonságát leegyszerűsítheti, ha [Azure Key Vault](../../key-vault/general/overview.md)tárolja őket. A Key Vault lehetővé teszi, hogy a kulcsokat hardveres biztonsági modulokban (HSM) tárolja, amely a FIPS 140-2 2. szintű szabványokhoz van hitelesítve. A biztonsági mentéshez vagy [transzparens adattitkosításhoz](/sql/relational-databases/security/encryption/transparent-data-encryption) használt SQL Server titkosítási kulcsainak mindegyike Key Vault tárolható az alkalmazásokból származó kulcsokkal vagy titkos kulcsok használatával. Az engedélyeket és a védett elemek elérését [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)kezelik.

### <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése
[Azure Backup](../../backup/backup-overview.md) egy olyan megoldás, amely az alkalmazás adatait nulla tőkebefektetéssel és minimális működési költségekkel védi. Az alkalmazások hibái sérültek az adataikat, és az emberi hibák olyan hibákat is bevezethetnek az alkalmazásokba, amelyek biztonsági problémákat okozhatnak. A Azure Backup a Windows és a Linux rendszerű virtuális gépek védettek.

### <a name="azure-site-recovery"></a>Azure Site Recovery
A szervezete [üzletmenet-folytonossági/vész-helyreállítási (BCDR)](../../best-practices-availability-paired-regions.md) stratégiájának fontos része, hogy a vállalati számítási feladatok és alkalmazások a tervezett és nem tervezett leállások esetén is naprakészek maradjanak. [Azure site Recovery](../../site-recovery/site-recovery-overview.md) segíti a munkaterhelések és alkalmazások replikálásának, feladatátvételének és helyreállításának összehangolását, hogy azok másodlagos helyről is elérhetők legyenek, ha az elsődleges hely leáll.

### <a name="sql-vm-tde"></a>SQL VIRTUÁLIS GÉP TDE
A transzparens adattitkosítás (TDE) és az oszlop szintű titkosítás (CLE) az SQL Server titkosítási funkciói. A titkosítás ezen formája megköveteli, hogy az ügyfelek kezeljék és tárolják a titkosításhoz használt titkosítási kulcsokat.

A Azure Key Vault (AKV) szolgáltatás úgy lett kialakítva, hogy javítsa a kulcsok biztonságát és felügyeletét egy biztonságos és magasan elérhető helyen. A SQL Server Connector lehetővé teszi, hogy a SQL Server ezeket a kulcsokat a Azure Key Vault használatával használhassa.

Ha a SQL Servert a helyszíni gépekkel futtatja, akkor a helyszíni SQL Server-példányon lévő Azure Key Vault eléréséhez szükséges lépéseket követheti. Az Azure-beli virtuális gépek SQL Server azonban időt takaríthat meg a Azure Key Vault integrációs funkciójával. Néhány Azure PowerShell parancsmaggal engedélyezheti ezt a funkciót, automatizálhatja az SQL-alapú virtuális gépekhez szükséges konfigurációt a kulcstartó eléréséhez.

### <a name="vm-disk-encryption"></a>VM-lemez titkosítása
A [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) egy új képesség, amely segít a Windows-és Linux-IaaS virtuális gépek lemezeinek titkosításában. Ez a Windows iparági szabványnak megfelelő BitLocker funkcióját és a Linux DM-Crypt funkcióját alkalmazza az operációs rendszer és az adatlemezek mennyiségi titkosításának biztosítására. A megoldás integrálva van Azure Key Vault a Key Vault-előfizetésében lévő lemezes titkosítási kulcsok és titkos kódok felügyeletéhez és kezeléséhez. A megoldás azt is biztosítja, hogy a virtuális gépek lemezein lévő összes adatok titkosítva legyenek az Azure Storage-ban.

### <a name="virtual-networking"></a>Virtuális hálózat
A virtuális gépeknek hálózati kapcsolatra van szükségük. Ezen követelmény támogatásához az Azure megköveteli, hogy a virtuális gépek egy Azure-Virtual Network kapcsolódjanak. Az Azure Virtual Network egy logikai szerkezet, amely a fizikai Azure hálózati hálóra épül. Minden egyes logikai [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md) el van különítve az összes többi Azure-beli virtuális hálózattól. Ez az elkülönítés segít biztosítani, hogy a központi telepítések hálózati forgalma ne legyen elérhető más Microsoft Azure ügyfelek számára.

### <a name="patch-updates"></a>Javítások frissítései
A javítások frissítései lehetővé teszik a lehetséges problémák megkeresését és kijavítását, valamint a szoftverfrissítés-felügyeleti folyamat egyszerűsítését, a vállalaton belül üzembe helyezni kívánt szoftverfrissítések számának csökkentésével, valamint a megfelelőség monitorozására való képesség növelésével.

### <a name="security-policy-management-and-reporting"></a>Biztonsági házirendek kezelése és jelentéskészítés
A [Security Center](../../security-center/security-center-introduction.md) segít megakadályozni, észlelni és reagálni a fenyegetésekre, és az Azure-erőforrások jobb láthatóságát és felügyeletét teszi lehetővé. Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetésekben, segít észlelni azokat a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és a biztonsági megoldások széles körű ökoszisztémával működnek.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés
A rendszerek, alkalmazások és adatok védelme az identitás-alapú hozzáférés-vezérléssel kezdődik. A Microsoft üzleti termékeibe és szolgáltatásaiba beépített identitás-és hozzáférés-kezelési funkciók segítenek a szervezeti és személyes adatok jogosulatlan hozzáférés elleni védelmében, miközben a legitim felhasználók számára, amikor csak szükségük van rá.

### <a name="secure-identity"></a>Biztonságos identitás
A Microsoft több biztonsági gyakorlatot és technológiát használ termékei és szolgáltatásai között az identitás és a hozzáférés kezeléséhez.

- [Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) a felhasználóknak több módszert kell használniuk a helyszíni és a Felhőbeli hozzáféréshez. Erős hitelesítést biztosít számos egyszerű ellenőrzési lehetőséggel, miközben a felhasználók számára egyszerű bejelentkezési folyamattal rendelkezik.

- A [Microsoft Authenticator](https://aka.ms/authenticator) felhasználóbarát multi-Factor Authentication élményt nyújt, amely Microsoft Azure Active Directory és Microsoft-fiókkal is működik, és támogatja a hordozható és ujjlenyomat-alapú jóváhagyásokat.

- A [jelszóházirend kényszerítése](../../active-directory/authentication/concept-sspr-policy.md) növeli a hagyományos jelszavak biztonságát azáltal, hogy a sikertelen hitelesítési kísérleteket követően hosszúsági és összetettségi követelmények, kényszerített időszakos rotáció és fiókzárolás miatt megterheli a fiók zárolását.

- A [jogkivonat-alapú hitelesítés](../../active-directory/develop/authentication-vs-authorization.md) lehetővé teszi a hitelesítés Azure Active Directory használatával.

- Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/built-in-roles.md) lehetővé teszi, hogy a felhasználó hozzárendelt szerepköre alapján engedélyezze a hozzáférést, így a felhasználók csak a feladat feladataik elvégzéséhez szükséges hozzáférést biztosíthatják. Az Azure-RBAC testre szabhatja a szervezet üzleti modelljében és a kockázati tolerancián.

- Az [integrált Identitáskezelés (hibrid identitás)](../../active-directory/hybrid/plan-hybrid-identity-design-considerations-overview.md) lehetővé teszi a felhasználók hozzáférésének felügyeletét a belső adatközpontokban és a Felhőbeli platformokon, így egyetlen felhasználói identitást hozhat létre a hitelesítéshez és az összes erőforrás engedélyezéséhez.

### <a name="secure-apps-and-data"></a>Biztonságos alkalmazások és adatkezelés
A [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)egy átfogó identitás-és hozzáférés-kezelési felhőalapú megoldás, amely segít a helyszíni és Felhőbeli alkalmazásokban tárolt adatokhoz való biztonságos hozzáférésben, és leegyszerűsíti a felhasználók és csoportok felügyeletét. Az alapszintű címtárszolgáltatás, a fejlett identitás-szabályozás, a biztonság és az alkalmazás-hozzáférés kezelése révén megkönnyíti a fejlesztők számára a házirend-alapú Identitáskezelés összeállítását az alkalmazásokba. Az Azure Active Directoryt kiegészítheti fizetős képességekkel is, az Azure Active Directory Alapszintű, Prémium P1 és Prémium P2 kiadásával.

| Ingyenes/közös funkciók     | Alapszintű szolgáltatások    |Prémium P1 funkciók |Prémium P2 funkciók | Azure Active Directory JOIN – csak a Windows 10 kapcsolódó funkciói|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Directory-objektumok](../../active-directory/fundamentals/active-directory-whatis.md),    [felhasználók/csoportok kezelése (Hozzáadás/frissítés/törlés)/felhasználó-alapú kiépítés, eszköz regisztrálása](../../active-directory/fundamentals/active-directory-whatis.md),  [egyetlen Sign-On (SSO)](../../active-directory/fundamentals/active-directory-whatis.md),     [önkiszolgáló jelszó módosítása a Felhőbeli felhasználók](../../active-directory/fundamentals/active-directory-whatis.md)számára,     [Kapcsolódás (a helyi címtárakat bővítő szinkronizálási motor, amely Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md),     [biztonsági/használati jelentések](../../active-directory/fundamentals/active-directory-whatis.md)       |  [Csoportos hozzáférés-kezelés/kiépítés](../../active-directory/fundamentals/active-directory-whatis.md), [önkiszolgáló jelszó-visszaállítás a Felhőbeli felhasználók](../../active-directory/fundamentals/active-directory-whatis.md)számára,  [vállalati védjegyezés (bejelentkezési lapok/hozzáférési panel testreszabása)](../../active-directory/fundamentals/active-directory-whatis.md),    [alkalmazásproxy](../../active-directory/fundamentals/active-directory-whatis.md),    [SLA 99,9%](../../active-directory/fundamentals/active-directory-whatis.md) |  Önkiszolgáló [csoport és alkalmazás-kezelés/önkiszolgáló alkalmazás-Hozzáadás/dinamikus csoportok](../../active-directory/fundamentals/active-directory-whatis.md), [önkiszolgáló jelszó-visszaállítás/-módosítás/-feloldás helyszíni visszaírással](../../active-directory/fundamentals/active-directory-whatis.md), [multi-Factor Authentication (FELHŐbeli és helyszíni (MFA-kiszolgáló))](../../active-directory/fundamentals/active-directory-whatis.md), a felügyeleti webszolgáltatások [kiszolgálója](../../active-directory/fundamentals/active-directory-whatis.md), a [Cloud app Discovery](../../active-directory/fundamentals/active-directory-whatis.md), a [kapcsolat állapota](../../active-directory/fundamentals/active-directory-whatis.md), [az automatikus jelszó-átgörgetés a csoportfiókok esetében](../../active-directory/fundamentals/active-directory-whatis.md)|   [Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md),  [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Eszköz csatlakoztatása az Azure ad-hez, asztali SSO, Microsoft Passport az Azure ad-hez, rendszergazdai BitLocker-helyreállítás](../../active-directory/fundamentals/active-directory-whatis.md),    [Mdm automatikus igénylése, Self-Service BitLocker-helyreállítás, további helyi rendszergazdák a Windows 10-es eszközökhöz az Azure ad JOIN használatával](../../active-directory/fundamentals/active-directory-whatis.md)|

- A [Cloud app Discovery](/cloud-app-security/set-up-cloud-discovery) a Azure Active Directory prémium funkciója, amely lehetővé teszi a szervezet alkalmazottai által használt felhőalapú alkalmazások azonosítását.

- A [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) olyan biztonsági szolgáltatás, amely Azure Active Directory anomália észlelési képességeket használ, hogy összevont nézetet nyújtson a kockázati észlelések és a szervezet identitását befolyásoló potenciális sebezhetőségek számára.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) lehetővé teszi az Azure-beli virtuális gépek tartományhoz való csatlakoztatását anélkül, hogy tartományvezérlőket kellene üzembe helyeznie. A felhasználók vállalati Active Directory hitelesítő adataikkal jelentkezhetnek be ezekre a virtuális gépekre, és zökkenőmentesen érhetik el az erőforrásokat.

- A [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy olyan, a felhasználók felé irányuló alkalmazások számára elérhető, globális Identitáskezelés, amely több száz millió identitásra és a mobil-és webes platformok integrálására képes. Ügyfelei az összes alkalmazásba bejelentkezhetnek a meglévő közösségi média-fiókokat használó testreszabható tapasztalatokkal, vagy új, önálló hitelesítő adatokat is létrehozhatnak.

- A [Azure Active Directory B2B Collaboration](../../active-directory/external-identities/what-is-b2b.md) egy biztonságos partner-integrációs megoldás, amely támogatja a több vállalatra kiterjedő kapcsolatokat azáltal, hogy lehetővé teszi a partnerek számára, hogy a saját felügyelt identitásuk alapján szelektíven férhessenek hozzá vállalati alkalmazásaihoz és adataihoz.

- A [Azure Active Directory](../../active-directory/devices/overview.md) összevont lehetővé teszi a Felhőbeli funkciók kiterjesztését a Windows 10-es eszközökre a központosított felügyelethez. Lehetővé teszi a felhasználók számára, hogy Azure Active Directoryon keresztül csatlakozzanak a vállalati vagy szervezeti felhőhöz, és leegyszerűsítse az alkalmazásokhoz és erőforrásokhoz való hozzáférést.

- A [Azure Active Directory Application proxy](../../active-directory/manage-apps/application-proxy.md) egyszeri bejelentkezést és biztonságos távoli hozzáférést biztosít a helyszínen üzemeltetett webalkalmazásokhoz.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [megosztott felelősségét a felhőben](shared-responsibility.md).

- Ismerje meg, hogyan segítheti a [Azure Security Center](../../security-center/security-center-introduction.md) a fenyegetések megelőzését, észlelését és reagálását az Azure-erőforrások jobb láthatóságával és kézben tartásával.