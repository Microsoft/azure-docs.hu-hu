---
title: Az Azure biztonsági alapkonfigurációja App Service
description: A App Service biztonsági alapkonfiguráció eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: d3e9d7d8cd8e5ffad74b02179994e299a59c8894
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833939"
---
# <a name="azure-security-baseline-for-app-service"></a>Az Azure biztonsági alapkonfigurációja App Service

Az Azure biztonsági alapkonfigurációja App Service javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági helyzetét. A szolgáltatás alapkonfigurációja az Azure Biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md)verziójából készült, amely javaslatokat tartalmaz arra vonatkozóan, hogyan teheti biztonságossá felhőalapú megoldásait az Azure-ban az ajánlott eljárásokkal kapcsolatos útmutatással. A tartalom az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, a biztonsági App Service. **A** nem alkalmazható App Service ki vannak zárva.

Ha meg App Service, hogyan lehet teljes mértékben leképezni az Azure-biztonsági teljesítménytesztre, tekintse meg a teljes App Service alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Ha az elkülönített App Service tarifacsomagban vagy más néven App Service Environment -t (ASE) használ, akkor közvetlenül üzembe helyezheti az Azure-beli Virtual Network. Hálózati biztonsági csoportokkal biztonságossá Azure App Service Environment a virtuális hálózat erőforrásaira irányuló bejövő és kimenő forgalom blokkolásával, vagy az alkalmazásokhoz való hozzáférés korlátozásával egy App Service Environment.

Alapértelmezés szerint a hálózati biztonsági csoportok tartalmaznak egy implicit megtagadási szabályt a legalacsonyabb prioritással, és explicit engedélyező szabályokat kell hozzáadnia. Adjon hozzá engedélyező szabályokat a hálózati biztonsági csoporthoz a legkevésbé rendszer-jogosultságú hálózati megközelítés alapján. A virtuális gépek gazdagépeként használt mögöttes virtuális App Service Environment nem érhetők el közvetlenül, mert a Microsoft által felügyelt előfizetésben vannak.

Megvédheti a App Service Environment a forgalmat egy engedélyezett Web Application Firewall (WAF) keresztül Azure Application Gateway. A szolgáltatásvégpontokat és a Application Gateway használhatja az alkalmazás bejövő közzétételi forgalmának biztonságossá.  

A több-bérlős App Service (az izolált szinten nem található alkalmazások) hálózati biztonsági csoportokkal blokkolhatja az alkalmazásból kimenő forgalmat. Az integrációs szolgáltatással engedélyezheti, hogy az alkalmazások Virtual Network vagy egy Virtual Network hozzáférjenek. Ez a funkció az alkalmazásból a nyilvános címekre irányuló kimenő forgalom blokkolását is lehetővé teszi. Virtual Network integráció nem használható az alkalmazások bejövő hozzáférésének biztosítanak.  

Az alkalmazás bejövő forgalmának biztonságossá való biztosítása a következővel:
- Hozzáférési korlátozások – a bejövő hozzáférést vezérlő engedélyező vagy megtagadó szabályok sorozata
- Szolgáltatásvégpont – megtagadhatja a megadott virtuális hálózatokon vagy alhálózatokon kívülről bejövő forgalmat
- Privát végpontok – privát IP-Virtual Network teszi elérhetővé az alkalmazást a Virtual Network számára. Ha a privát végpontok engedélyezve vannak az alkalmazásban, az már nem érhető el az internetről

Ha az integrációs Virtual Network szolgáltatást használja az azonos régióban lévő virtuális hálózatokkal, használjon hálózati biztonsági csoportokat és útvonaltáblákat felhasználó által megadott útvonalakkal. A felhasználó által megadott útvonalak elhelyezhetőek az integrációs alhálózaton a kimenő forgalom kívánt küldése érdekében.  

Fontolja meg egy Azure Firewall központilag létrehozni, kikényszeríteni és naplózni az alkalmazás- és hálózati kapcsolati szabályzatokat az előfizetések és virtuális hálózatok között. Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózati erőforrásokhoz, ami lehetővé teszi a külső tűzfalak számára a virtuális hálózatról származó forgalom azonosítását. 

- [Egy App Service Environment](environment/firewall-integration.md)

- [Open Web Application Security Project (OWASP) Az első 10 biztonsági rés elleni védelem](https://owasp.org/www-project-top-ten/)

- [Hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md)

- [Alkalmazás integrálása egy Azure-beli virtuális hálózattal](web-sites-integrate-with-vnet.md)

- [App Service-környezet hálózati megfontolásai](environment/network-info.md)

- [Külső ASE létrehozása](environment/create-external-ase.md)

- [Belső ASE létrehozása](environment/create-ilb-ase.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

**Azure Policy definíciók – Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Implementálja a hálózatvédelmi javaslatokat a Azure Security Center az alkalmazásokhoz és API-khoz kapcsolódó hálózati erőforrások és konfigurációk App Service érdekében.

A Azure Firewall küldhet forgalmat, és központilag hozhat létre, kényszeríthet ki és naplózhet alkalmazás- és hálózati kapcsolati szabályzatokat az előfizetések és virtuális hálózatok között. Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózati erőforrásokhoz, ami lehetővé teszi a külső tűzfalak számára, hogy azonosítsák a virtuális gépről származó Virtual Network. A Azure Firewall szolgáltatás teljesen integrálva van a Azure Monitor naplózáshoz és elemzéshez.

- [Azure Firewall áttekintés](../firewall/overview.md)

- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Az adatok monitorozásának és védelmének App Service](../security-center/defender-for-app-service-introduction.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** Internetről elérhető alkalmazás biztonságossá tétele egy App Service Environment (ASE) használatával:
- Virtuális gép Web Application Firewall (WAF) üzembe helyezése Azure Application Gateway internetkapcsolattal elérhető alkalmazás előtt
- Hozzáférési korlátozások használata a bejövő forgalom biztonságossá Application Gateway
- Az alkalmazás biztonságossá Azure Active Directory (Azure AD) használatával a hitelesítés biztosítása érdekében
- A minimális TLS-verzió beállítása 1.2-re
- Az alkalmazás beállítása csak HTTPS-re

Az alkalmazás minden kimenő forgalmát egy Azure Firewall, és figyelje a naplókat. 

Internetről elérhető alkalmazás biztonságossá tétele a több-bérlős App Service(például nem az elkülönített szinten)
- Alkalmazás Web Application Firewall eszköz üzembe helyezése
- Hozzáférési korlátozások vagy szolgáltatásvégpont használata a bejövő forgalom biztonságossá Web Application Firewall (WAF) eszközre
- Az alkalmazás biztonságossá biztosítása az Azure AD-val a hitelesítés biztosítása érdekében
- A minimális TLS-verzió beállítása 1.2-re
- Az alkalmazás beállítása csak HTTPS-re
- A Virtuális hálózati integráció és az alkalmazásbeállítás WEBSITE_VIRTUAL NETWORK_ROUTE_ALL, hogy az összes kimenő forgalmat az integrációs alhálózat hálózati biztonsági csoportjainak és felhasználó által megadott útvonalának tegye ki.

Az Application Service Environment alkalmazáshoz hasonlóan az alkalmazás minden kimenő forgalmát egy Azure Firewall, és figyelje az alkalmazás naplóit.

Emellett tekintse át és kövesse a Zárolási kulcs zárolása App Service Environment vonatkozó javaslatokat.

- [Egy App Service Environment](environment/firewall-integration.md)

- [Azure webalkalmazási tűzfal a Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Azure App Service hozzáférési korlátozások](app-service-ip-restrictions.md)

- [A WAF-riasztások nyomon követése és a trendek egyszerű figyelése Azure Monitor ](../azure-monitor/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** A App Service Environment biztonságossá App Service Environment dokumentációban leírtak szerint. Az Integrated Threat Intelligence (Fenyegetésintelligencia) Azure Security Center az ismert kártékony vagy nem használt nyilvános IP-címekkel való kommunikáció megtagadása érdekében. Használja a hozzáférési korlátozásokat a bejövő forgalom biztonságossá Application Gateway. 

A több-bérlős App Service (egy nem izolált szinten található alkalmazás) nyilvános internetkapcsolattal elérhető végponttal. Csak az adott alhálózatról származó forgalmat engedélyezi a Virtual Network minden mást letilt. A hozzáférési korlátozások segítségével konfigurálhatja a hálózati Access Control listáit (IP-korlátozásokat) az engedélyezett bejövő forgalom zárolásához.

Határozza meg a prioritást a rendezett engedélyező vagy megtagadási lista között az alkalmazás hálózati hozzáférésének kezeléséhez. A lista IP-címeket vagy alhálózatokat Virtual Network tartalmazhat. Egy implicit "az összes elutasítása" szabály a lista végén található, ha egy vagy több bejegyzést tartalmaz. Ez a képesség az összes App Service terheléssel működik, beleértve a következőket: Web Apps, API Apps, Linux-alkalmazások, Linux-tárolóalkalmazások és Functions. 

A szolgáltatásvégpontokkal korlátozhatja a webalkalmazás azure-beli virtuális Virtual Network. Korlátozza a hozzáférést egy több-bérlős App Service (egy nem izolált szinten található alkalmazáshoz) a szolgáltatásvégpontokkal megadott alhálózatok esetében. 

- [Az Azure App Service statikus IP-korlátozásai](app-service-ip-restrictions.md)

- [Azure webalkalmazási tűzfal a Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [How to configure a Web Application Firewall (WAF) for App Service Environment](environment/app-service-app-service-environment-web-application-firewall.md)

- [Az ASE biztonságának biztosítása az ASE zárolását App Service Environment](environment/firewall-integration.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Monitorozza a App Service alkalmazásokba és alkalmazásokból küldött kéréseket és válaszokat Security Center. A webalkalmazások elleni támadások egy valós idejű Application Gateway használatával monitorozásra használhatók, amely Web Application Firewall integrált naplózással van engedélyezve az Azure Monitor Web Application Firewall-ból a riasztások nyomon követéséhez és a trendek egyszerű monitorozásához.

- [Azure Web Application Firewall a Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Webalkalmazások forgalmának kezelése

**Útmutató:** Alkalmazások forgalmának kezelése egy App Service Environment:

- Biztonságossá App Service Environment a Zárolási kulcs zárolása App Service Environment
- Olyan Application Gateway üzembe helyezése, amely Azure Web Application Firewall rendelkezik az internetkapcsolattal bíró alkalmazások előtt
- Állítsa az alkalmazást úgy, hogy csak HTTPS-protokollon keresztül legyen elérhető

Egy internetről elérhető alkalmazás forgalmának kezelése a több-bérlős App Service (nem az elkülönített szinten): 

- Olyan Application Gateway üzembe helyezése, Azure Web Application Firewall engedélyezve van az internetkapcsolattal rendelkező alkalmazások előtt
- Használjon hozzáférési korlátozásokat vagy szolgáltatásvégpontokat a bejövő forgalom biztonságossá Web Application Firewall. A hozzáférés-korlátozások funkció az üzemeltetett munkaterhelések App Service működik, beleértve a Web Apps, API Apps, Linux-alkalmazások, Linux-tárolóalkalmazások és Functions-alkalmazásokat.

- Az alkalmazás elérhetővé tétele csak HTTPS-protokollon keresztül
- Korlátozza a hozzáférést az App Service alkalmazáshoz statikus IP-korlátozásokkal, hogy csak az alkalmazásátjárón a VIP-címről fogad forgalmat egyetlen hozzáféréssel.

További információért tekintse át a hivatkozott hivatkozásokat.

- [Az Azure App Service statikus IP-korlátozásai](app-service-ip-restrictions.md)

- [Azure webalkalmazási tűzfal a Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Végpontok között elérhető TLS konfigurálása a Application Gateway portálon való használatával](../application-gateway/end-to-end-ssl-portal.md)

- [Az ASE biztonságának biztosítása az ASE zárolását App Service](/azure/app-service/environment/firewall-integration)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** App Service szolgáltatás számos végponttal rendelkezik, amelyek a szolgáltatás kezelésére szolgálnak. Ezeket a végpontcímeket az AppServiceManagement IP-szolgáltatáscímke is tartalmazza. Az AppServiceManagement címke csak egy App Service Environment a forgalom engedélyezése érdekében. 

Engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát, ha megadja a szolgáltatáscímke nevét egy szabály megfelelő forrás- vagy célmezőben. App Service bejövő címeket az AppService IP-szolgáltatáscímke követi nyomon. Nincs olyan IP-szolgáltatáscímke, amely a szolgáltatás által használt kimenő címeket App Service.

A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásával.

- [Virtuális hálózati szolgáltatáscímkék](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Szabványos biztonsági konfigurációkat határozhat meg és valósíthat meg a App Service beállításaihoz. 

Biztonsági konfigurációk fenntartása Azure Policy "Microsoft.Web" és "Microsoft.Network" névterek aliasai használatával. Egyéni szabályzatokat hozhat létre a hálózati konfiguráció naplózásához vagy kikényszeríteni App Service alkalmazásokban. 

Használjon beépített szabályzatdefiníciókat a App Service, például:
- Az alkalmazásnak virtuális hálózati szolgáltatásvégpontot kell használnia
- Az alkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie
- A minimális TLS-verzió beállítása az aktuális verzióra

További információért tekintse át a hivatkozott hivatkozásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Végpontok között elérhető TLS konfigurálása a Application Gateway használatával](../application-gateway/end-to-end-ssl-portal.md)

- [Az ASE biztonságának biztosítása a Zárolási adatok App Service](/azure/app-service/environment/firewall-integration)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Címkék használata hálózati biztonsági csoportokhoz és más kapcsolódó erőforrásokhoz, beleértve a forgalom áramlását a App Service.

Adja meg az üzleti igényeket, az időtartamot stb. a "Leírás" mezővel minden olyan szabályhoz, amely engedélyezi a hálózatra vagy hálózatról származó forgalmat az egyes hálózati biztonsági csoportok szabályaihoz.

A címkézési hatásokkal kapcsolatos beépített Azure Policy-definíciók (például "Címke és érték megkövetelése" ) alkalmazásával biztosíthatja, hogy az összes erőforrás címkékkel legyen létrehozva, és értesítést küld a meglévő, címkézetlen erőforrásokról. Az Azure PowerShell vagy az Azure CLI használatával erőforrásokat keres vagy hajt végre a címkék alapján.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Azure App Service hozzáférési korlátozások](/azure/app-service/app-service-ip-restrictions)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnaplóval monitorozhatja a hálózati erőforrás-konfigurációkat, és észlelheti a hálózati beállítások és a App Service. 

Alkalmazza a szolgáltatáshoz Azure Policy beépített definíciók App Service, például egy szabályzatot, amely naplóz alkalmazásokat a virtuális hálózati végpont szolgáltatás használata érdekében. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha kritikus hálózati beállítások vagy erőforrások módosulnak. 

Tekintse át a részletes biztonsági riasztásokat és javaslatokat a Security Center, a portálon vagy programozott eszközökkel. Exportálja ezt az információt, vagy küldje el a környezetben található más monitorozási eszközöknek. Az eszközök elérhetők a riasztások és javaslatok manuális vagy folyamatos exportálására. Ezekkel az eszközökkel a következőre van képes:
 
- Folyamatos exportálás Log Analytics-munkaterületre
- Folyamatos exportálás Azure Event Hubs (külső SIEM-ekkel való integrációhoz)
- Exportálás CSV-fájlba (egyszer)

Javasoljuk, hogy hozzon létre egy folyamatot automatizált eszközökkel a hálózati erőforrások konfigurációjának figyelése és a változások gyors észlelése érdekében.

- [Azure-tevékenységnapló eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Biztonsági riasztások és javaslatok exportálása](../security-center/continuous-export.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** A App Service Environment (ASE) integrálása a Azure Monitor, hogy naplókat küldjön az Azure Storage-ba, Azure Event Hubs Log Analyticsbe. Engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait a vezérlősík naplózásához. A biztonsági riasztások Security Center közzé vannak téve az Azure-tevékenységnaplóban. Naplózhatja az Azure-tevékenységnapló adatait, amelyek segítségével meghatározhatja a vezérlősík szintjén a Azure App Service és más Azure-erőforrások írási műveleteinek (PUT, POST, DELETE) "mit, ki és mikor" műveletét. Mentse a lekérdezéseket későbbi használatra, rögzítse a lekérdezési eredményeket az Azure-irányítópultokon, és hozzon létre naplóriasztásokat. Emellett az adatelérési REST API a Application Insights a telemetriai adatok programozott módon való eléréséhez.

A Microsoft Azure Sentinel, egy méretezhető, natív felhőbeli biztonságiadat-eseménykezelő (SIEM) használatával különböző adatforrások és összekötők érhetők el az üzleti igényeknek megfelelően. Az adatokat külső biztonságiadat-kezelési (SIEM) rendszerekben is engedélyezheti és felhasználhatja, például a Barracuda Azure Marketplace.

- [ASE-tevékenység naplózása](./environment/using-an-ase.md#logging)

- [Diagnosztikai beállítások engedélyezése a Azure App Service](troubleshoot-diagnostic-logs.md)

- [A Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Telemetria exportálása az Application Insightsból](../azure-monitor/app/export-telemetry.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Az Azure-tevékenységnapló diagnosztikai beállításainak engedélyezése a vezérlősík naplózásához a App Service. Küldje el a naplókat egy Log Analytics-munkaterületre, egy Azure Event Hubs-eseményközpontba vagy egy Azure Storage-fiókba.

A vezérlősík szintjén végrehajtott írási műveletek (PUT, POST, DELETE) "mit, ki és mikor" meghatározása az Azure-tevékenységnapló adataival határozható meg a App Service és más Azure-erőforrásokhoz.

Emellett a Azure Key Vault titkos adatok központi kezelését is biztosítja hozzáférési házirendekkel és naplózási előzményekkal. 

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

- [Diagnosztikai beállítások engedélyezése a Azure App Service](troubleshoot-diagnostic-logs.md)

- [Resource Manager műveletek](../role-based-access-control/resource-provider-operations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Biztonsági naplók tárolásának konfigurálása

**Útmutató:** A Azure Monitor a vállalat megfelelőségi szabályzatai alapján állítsa be a naplómegőrzési megőrzési időszakot a App Service erőforrásaihoz társított Log Analytics-munkaterületek számára.
- [Naplómegőrzési paraméterek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Tekintse át az Azure-tevékenységnapló diagnosztikai beállításait a App Service a Log Analytics-munkaterületre küldött naplók segítségével. Lekérdezéseket hajthat végre a Log Analyticsben a kifejezések kereséséhez, trendek azonosításához, minták elemzéséhez és számos további elemzéshez az összegyűjtött adatok alapján.

A Application Insights alkalmazáshoz App Service, valamint napló-, teljesítmény- és hibaadatok gyűjtéséhez. Tekintse meg a Application Insights által gyűjtött telemetriai Azure Portal.

Ha üzembe helyezett egy Web Application Firewall (WAF), valós idejű naplóval figyelheti a App Service-alkalmazásokat Web Application Firewall támadásokat. A napló integrálva van a Azure Monitor a riasztások Web Application Firewall és a trendek egyszerű figyelése érdekében.

A Azure Sentinel, egy méretezhető és natív felhőbeli biztonságiadat-eseménykezelő (SIEM) használatával különböző adatforrásokkal és összekötővel integrálhatja az követelményeket. Az adatok engedélyezését és felvételét egy külső biztonságiadat-eseménykezelési megoldásba is engedélyezheti a Azure Marketplace.

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

- [Az Application Insights](../azure-monitor/app/app-insights-overview.md)

- [A App Service Environment integrálása a Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Az Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Konfigurálja Security Center Azure-előfizetésében, és tekintse át a létrehozott riasztásokat. A Azure Monitor használatával lekért tevékenységnapló-adatokat egy eseményközpontba, ahol egy biztonságiadat-eseménykezelési (SIEM-) megoldás, például a Azure Sentinel. 

Valós idejű naplók és üzembe App Service (WAF Web Application Firewall használatával figyelheti a Azure Web Application Firewall elleni támadásokat. A napló integrálva van a Azure Monitor a Web Application Firewall (WAF) riasztások nyomon követéséhez és a trendek egyszerű megfigyeléséhez.

- [A App Service Environment integrálása a Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Biztonsági riasztások és javaslatok exportálása](../security-center/continuous-export.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Azure Active Directory (Azure AD) beépített szerepkörökkel rendelkezik, amelyekhez explicit módon hozzá kell rendelni és lekérdezhetőnek kell lennie. Az Azure AD PowerShell-modullal alkalmi lekérdezéseket hajt végre a rendszergazdai csoportok tagjainak felderítéséhez.

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Felügyelt identitások használata a App Service és Azure Functions](./overview-managed-identity.md?tabs=dotnet&context=azure%2factive-directory%2fmanaged-identities-azure-resources%2fcontext%2fmsi-context)

- [Azure-szerepkörök hozzárendelése a Azure Portal](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** Azure Active Directory (Azure AD) nem rendelkezik az alapértelmezett jelszavak fogalmával. Vezérlősík-hozzáférést biztosít a App Service.

Saját alkalmazásai fejlesztésekor általában kerülje a felhasználói hozzáférés alapértelmezett jelszavainak használatát. Használja az alapértelmezett identitásszolgáltatókat, például az Azure AD App Service Microsoft-fiókot, a Facebookot, a Google-t vagy a Twittert.

Tiltsa le a névtelen hozzáférést, kivéve, ha támogatásra van szüksége. 

- [Alapértelmezés szerint elérhető identitásszolgáltatók a Azure App Service](./overview-authentication-authorization.md#identity-providers)

- [Hitelesítés és engedélyezés a Azure App Service és Azure Functions](overview-authentication-authorization.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használatával kapcsolatban. A felügyeleti fiókok számának figyelése és nyomon követése Security Center identitás- és hozzáférés-kezelési funkcióival. 

Használja a Security Center vagy beépített Azure-szabályzatok javaslatait, például:

- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie. 
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Hozzon létre egy folyamatot a hálózati erőforrások konfigurációjának figyelése és a rendszergazdai fiókok változásainak észlelése érdekében.

- [A Azure Security Center identitás és hozzáférés figyelése](../security-center/security-center-identity-access.md)

- [A Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [További információ a felhasználók alkalmazásokhoz való hozzáférésének megadásáról](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** App Service hitelesítése Azure Active Directory (Azure AD) használatával. OAuth 2.0-szolgáltatást biztosít az identitásszolgáltató számára, és lehetővé teszi a mobil- és webalkalmazások engedélyezett hozzáférését. 

App Service alkalmazások összevont identitást használnak, amelyben egy külső identitásszolgáltató felügyeli Ön számára a felhasználói identitásokat és a hitelesítési folyamatot. Ezek az identitásszolgáltatók alapértelmezés szerint elérhetők:

- Azure AD
- Microsoft-fiók

- Facebook

- Google

- Twitter

Ha engedélyezi a hitelesítést és az engedélyezést ezen szolgáltatók egyikével, a bejelentkezési végpontja elérhető a felhasználóhitelesítéshez és a szolgáltató hitelesítési jogkivonatának ellenőrzéséhez.

- [A hitelesítés és az engedélyezés Azure App Service](./overview-authentication-authorization.md#identity-providers)

- [Tudnivalók a hitelesítésről és az engedélyezésről a Azure App Service](overview-authentication-authorization.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze a többtényezős hitelesítési funkciót a Azure Active Directory (Azure AD) szolgáltatásban, és kövesse az Identitás- és hozzáférés-kezelésre vonatkozó javaslatokat a Security Center.

Többtényezős hitelesítés megvalósítása az Azure AD-hez. A rendszergazdáknak gondoskodniuk kell arról, hogy a portálon az előfizetési fiókok védettek. Az előfizetés sebezhető a támadásokkal szemben, mivel az kezeli a létrehozott erőforrásokat. 

- [Azure Security többtényezős hitelesítés](/previous-versions/azure/security/develop/secure-aad-app)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató:** Emelt szintű hozzáférésű munkaállomások (PAW) használata az Azure-erőforrásokba való bejelentkezéshez és konfiguráláshoz konfigurált többtényezős hitelesítéssel. 

- [Tudnivalók az emelt szintű hozzáférésű munkaállomásról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** Privileged Identity Management (PIM) használata a Azure Active Directory -ban (Azure AD) naplók és riasztások generálása, ha gyanús vagy nem biztonságos tevékenységek fordulnak elő a környezetben.

Emellett az Azure AD kockázatészlelései segítségével megtekintheti a kockázatos felhasználói viselkedéssel kapcsolatos riasztásokat és jelentéseket.

A veszélyforrások Security Center átfogó védelmet biztosít a környezet számára, beleértve az Olyan Azure-beli számítási erőforrások veszélyforrások elleni védelmét, mint a Windows rendszerű gépek, a Linux rendszerű gépek, a App Service- és az Azure-tárolók.

- [A Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázatészlelései](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure-beli számítási erőforrások veszélyforrások elleni védelme](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés elnevezett helyeinek használatával hozzáférést engedélyez a Azure Portal IP-címtartományok, országok vagy régiók meghatározott logikai csoportosításaiból.

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként a App Service alkalmazásokban. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt, valamint a sót, a hashe-eket, valamint biztonságosan tárolja a felhasználói hitelesítő adatokat.

- [A Azure App Service konfigurálása az Azure AD-bejelentkezés használatára](configure-authentication-provider-aad.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Elavult fiókok felderítése a Azure Active Directory (Azure AD) által biztosított naplók használatával. Az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságokat és a vállalati alkalmazásokhoz való hozzáférést, valamint a szerepkör-hozzárendeléseket. Rendszeresen tekintse át a felhasználói hozzáférést, és győződjön meg arról, hogy csak a kívánt felhasználók férnek hozzá. 

- [Az Azure AD-jelentéskészítés](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity Hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként a App Service alkalmazásokban. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt, a sót és a hasheket, valamint biztonságosan tárolja a felhasználói hitelesítő adatokat.

Az Azure AD bejelentkezési tevékenységéhez, naplózási és kockázatiesemény-naplózási forrásaihoz való hozzáférés lehetővé teszi az Azure Sentinel vagy egy külső biztonsági információs eseménykezelési (SIEM) megoldással való integrációt. Egyszerűsítheti a folyamatot az Azure AD felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplózási és bejelentkezési naplók Log Analytics-munkaterületre való küldésével. A kívánt naplóriasztásokat a Log Analyticsben lehet konfigurálni.

- [A Azure App Service konfigurálása az Azure AD-bejelentkezés használatára](configure-authentication-provider-aad.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Az Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként a App Service alkalmazásokhoz. 

A Azure AD Identity Protection konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre adott automatikus válaszokat, például a fiók bejelentkezési viselkedésének eltérését a vezérlősíkon a Azure Portal. Az adatokat be is Azure Sentinel vizsgálathoz. 

- [A Azure App Service konfigurálása az Azure AD-bejelentkezés használatára](configure-authentication-provider-aad.md)

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Hozzáférés biztosítanak a Microsoft számára a releváns ügyféladatokhoz a támogatási forgatókönyvek során

**Útmutató:** Nem érhető el; Ügyfélszéf nem támogatott a Azure App Service.

- [A Ügyfélszéf szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon App Service bizalmas adatokat tároló vagy feldolgozó erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** A App Service Environment különálló előfizetéseket, felügyeleti csoportokat vagy mindkettőt implementálja fejlesztési, tesztelési és éles környezetekhez. Ugyanígy elkülönítheti azokat az alkalmazásokat, amelyek más alkalmazásokból származó bizalmas adatokat feldolgoznak. Üzembe helyezheti App Service alkalmazást egy Virtual Network. Használjon hálózati biztonsági csoportokat és alhálózatokat az alkalmazások további elkülönítése érdekében. 

Az üzembe helyezési környezetek (ASE App Service két központi telepítési típust használnak. Mindkettővel elkülönítheti a forgalmat az üzleti igényei alapján.

- Külső alkalmazásszolgáltatás-környezet – Elérhetővé teszi App Service Environment üzemeltetett alkalmazásokat egy internetről elérhető IP-címen.

-  belső terheléselosztási (ILB) Alkalmazásszolgáltatás-környezet – Elérhetővé teszi a App Service Environment üzemeltetett alkalmazásokat egy IP-címen a Virtual Network. A belső végpont egy belső terheléselosztási (ILB), ezért nevezzük ILB ASE-nek. 

A több-bérlős App Service (az Izolált szinten nem lévő alkalmazások) esetében az Virtual Network Integration (Integráció) használatával férhet hozzá az alkalmazás a virtuális hálózatban lévő erőforrásokhoz.  Privát webhely-hozzáféréssel egy alkalmazást csak magánhálózatról, például egy Azure-beli virtuális hálózaton belülről lehet elérhetővé tenni. Virtual Network integráció csak arra használható, hogy kimenő hívásokat kezdeményezsen az alkalmazásból a Virtual Network. A Virtual Network integrációs szolgáltatás eltérően viselkedik, ha egy azonos régióban lévő virtuális hálózattal és más régiókban lévő virtuális hálózatokkal használják. 
 
- [App Service-környezet hálózati megfontolásai](environment/network-info.md)

- [Külső ASE létrehozása](environment/create-external-ase.md)

- [Belső ASE létrehozása](environment/create-ilb-ase.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** Bár az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók még nem érhetők el a App Service-hoz, csökkentheti a virtuális hálózatból való adatkiszivárgás kockázatát, ha eltávolít minden olyan szabályt, amely szerint a célhely "címkét" használ az internethez vagy az Azure-szolgáltatásokhoz. 

A Microsoft kezeli a mögöttes infrastruktúra App Service és szigorú vezérlőket valósított meg az adatok elvesztésének vagy kitettségének megakadályozására.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** Használja a TLS/SSL-beállításokban konfigurált TLS 1.2 alapértelmezett minimális verzióját az átvitel alatt található összes információ titkosításához. Győződjön meg arról is, hogy az összes HTTP-kapcsolatkérés a HTTPS-re van átirányítva.

- [Az átvitel közbeni titkosítás Azure App Service webalkalmazások esetében](security-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: A bizalmas adatok azonosítása aktív felderítési eszközzel

**Útmutató:** Jelenleg nem érhető el. Az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el a App Service. 

Címkéz App Service bizalmas adatokat feldolgozó alkalmazásokat. Implementálja a harmadik féltől származó megoldást, ha szükséges a megfelelőség érdekében.

A Microsoft kezeli a mögöttes platformot, és bizalmasként kezeli az összes ügyféladatot, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Erőforrásokhoz való hozzáférés vezérlése az Azure RBAC használatával 

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata az Azure Active Directory-ban (Azure AD) a App Service vezérlősíkhoz való hozzáférés szabályozására a Azure Portal.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Az App Service alkalmazásokban lévő webhelytartalmakat, például a fájlokat a rendszer az Azure Storage-ban tárolja, amely automatikusan titkosítja a tartalmat. Válassza ki, hogy az alkalmazás titkos Key Vault tárolja, és lekéri őket futásidőben.

Az ügyfél által megadott titkos kulcsok titkosítva vannak az App Service konfigurációs adatbázisokban.

Vegye figyelembe, hogy bár a helyileg csatlakoztatott lemezeket a webhelyek opcionálisan ideiglenes tárolóként is használhatók (például D:\local és %TMP%), ezek nincsenek titkosítva az aktív formában.

- [Az adatvédelmi vezérlők Azure App Service](./security-recommendations.md#data-protection)

- [Az Azure Storage-titkosítás használata az azure-beli használatban](../storage/common/storage-service-encryption.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Azure Monitor azure-tevékenységnaplóval való használatával riasztásokat hozhat létre az éles környezetben App Service alkalmazások és egyéb kritikus vagy kapcsolódó erőforrások változásairól.

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált sebezhetőség-ellenőrzési eszközök futtatása

**Útmutató:** DevSecOps-eljárás alkalmazása annak érdekében, hogy a App Service alkalmazásai biztonságosak és biztonságban maradjanak az életciklusuk során. A DevSecOps a szervezet biztonsági csapatát és képességeit beépíti a DevOps-eljárásokba, így a csapat minden tagja biztonságba kerül.

Tekintse át és kövesse a Security Center a saját alkalmazásait App Service javaslatait.

- [Folyamatos biztonsági ellenőrzés hozzáadása a CI-/CD-folyamathoz](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&preserve-view=true)

- [Biztonsági rések felmérésére Azure Security Center javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** A Microsoft elvégzi a biztonsági rések kezelését a biztonsági réseket támogató mögöttes App Service. A környezeten belüli kockázatok méréséhez azonban használhatja a Security Center javaslatok súlyosságát és a biztonsági pontszámot. A biztonsági pontszám azon alapul, hogy hány Security Center javaslatokat enyhített. A megoldáshoz szükséges javaslatok rangsorolása érdekében vegye figyelembe az egyes ajánlások súlyosságát.

- [Biztonsági javaslatok referencia-útmutatója](../security-center/recommendations-reference.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetések összes erőforrását (pl. számítás, tárolás, hálózat, portok, protokollok stb.). Győződjön meg arról, hogy a bérlőre megfelelő engedélyek vonatkoznak, és enumerálhatja az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

Bár a klasszikus Azure-erőforrások a Resource Graph felderíthetőek, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&preserve-view=true)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása Azure-erőforrásokra metaadatok használatával, hogy logikailag rendszerezze őket egy taxonómiába.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Az Azure-erőforrások rendszerezéséhez és nyomon követéséhez szükség szerint használjon címkézést, felügyeleti csoportokat és különálló előfizetéseket. A leltár rendszeres egyeztetése és a jogosulatlan erőforrások előfizetésből való eltávolítása a folyamat részeként.

A Azure Policy az alábbi beépített szabályzatdefiníciók használatával korlátozható az ügyfél-előfizetésben létretehető erőforrások típusa:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

További információért tekintse át a hivatkozott hivatkozásokat.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Új Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és leltározása

**Útmutató:** Hozzon létre egy leltárt a jóváhagyott Azure-erőforrásokról és a jóváhagyott szoftverekről a számítási erőforrásokhoz a szervezeti igények alapján.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** A Azure Policy segítségével korlátozásokat korlátozhat az előfizetésében létrehozható erőforrások típusára vonatkozóan.

A Azure Resource Graph lekérdezheti vagy felderítheti az előfizetéseikben található erőforrásokat.  Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató:** A Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetések erőforrásait, és biztosíthatja, hogy a felderített Azure-erőforrások jóváhagyása a szervezeti szabályzatok alapján megszabadul.

A webjobs-App Service a számítási erőforrásokon belül üzembe helyezett, jóvá nem hagyott szoftveralkalmazások figyelése. A WebJobs használatával egy webalkalmazással, API-alkalmazással vagy mobilalkalmazással egy példányban futtathat programokat vagy szkripteket. WebJobs-konfigurációk és monitorozás naplók segítségével. A WebJobs-feladat futtatás részletei lapon válassza a Kimenet ki- és bekapcsolása lehetőséget a napló tartalmának szövegének a megtekintése érdekében. Vegye figyelembe, hogy a WebJobs-feladat még nem támogatott a App Service on Linux.

- [Háttérfeladatok futtatása a WebJobs-feladatokkal a Azure App Service](webjobs-create.md)

- [Oktatóanyag – Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítenie](../governance/policy/tutorials/create-and-manage.md)

- [Rövid útmutató – Az első lekérdezés Resource Graph a Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Nem jóváhagyott Azure-erőforrások és -szoftveralkalmazások eltávolítása

**Útmutató:** Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva. A Azure Policy az előfizetésben létrehozható erőforrások típusára vonatkozó korlátozásokat. Távolítsa el az összes olyan telepített szoftveralkalmazást, amely nem lett jóváhagyva a szervezeti házirendek szerint.

- [Háttérfeladatok futtatása a WebJobs-feladatokkal a Azure App Service](webjobs-create.md)

- [Oktatóanyag – Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítenie](../governance/policy/tutorials/create-and-manage.md)

- [Rövid útmutató – Az első lekérdezés Resource Graph a Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazások használata

**Útmutató:** Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva. A Azure Policy az előfizetésben létrehozható erőforrások típusára vonatkozó korlátozásokat. Távolítsa el az összes olyan telepített szoftveralkalmazást, amely nem lett jóváhagyva a szervezeti házirendek szerint. 

- [Háttérfeladatok futtatása a WebJobs-feladatokkal a Azure App Service](webjobs-create.md)

- [Oktatóanyag – Szabályzatok létrehozása és kezelése a megfelelőség kikényszeríteni érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Rövid útmutató – Az első lekérdezés Resource Graph az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Hozzon létre egy folyamatot a nem engedélyezett Azure-szolgáltatások rendszeres áttekintéséhez, hogy csak az engedélyezett Azure-szolgáltatásokat használja az előfizetései.

A Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetéseikben található erőforrásokat. Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

Konfigurálja Azure Policy, hogy az alábbi beépített szabályzatdefiníciók használatával korlátozásokat állítson be az előfizetésében létrehozható erőforrások típusára:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

A webjobs-App Service a számítógép-erőforrásokon belül telepített, jóvá nem hagyott szoftveralkalmazások figyelését. A WebJobs használatával egy webalkalmazással, API-alkalmazással vagy mobilalkalmazással egy példányban futtathat programokat vagy szkripteket. WebJobs-konfigurációk és monitorozás meghatározása naplók segítségével. A WebJobs-feladat futtatás részletei lapon válassza a Kimenet ki- és bekapcsolása lehetőséget a napló tartalmának a szövegének a megtekintése érdekében. Vegye figyelembe, hogy a WebJobs-feladat még nem támogatott App Service on Linux.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Háttérfeladatok futtatása a WebJobs-feladatokkal a Azure App Service](webjobs-create.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: A jóváhagyott szoftvercímek leltárának fenntartása

**Útmutató:** Az előfizetések szoftvercímek leltározásának és áttekintésének folyamata rendszeres időközönként, annak biztosítása érdekében, hogy csak az engedélyezett Azure-szolgáltatások legyen használatban az előfizetésben.

A Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetések erőforrásait. Győződjön meg arról, hogy a környezetben felderített összes Azure-erőforrás jóvá van hagyva.

Konfigurálja Azure Policy, hogy az alábbi beépített szabályzatdefiníciók használatával korlátozásokat állítson be az ügyfél-előfizetésekben létretehető erőforrások típusára:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Hasonlóképpen használja a WebJobs-App Service a számítógép-erőforrásokon belül telepített, jóvá nem hagyott szoftveralkalmazások leltározását. A konfiguráció és a monitorozás meghatározása naplók alapján. A WebJobs-feladat futtatás részletei lapon válassza a Kimenet ki- és bekapcsolása lehetőséget a napló tartalmának szövegének a megtekintése érdekében. Vegye figyelembe, hogy a WebJobs-feladat még nem támogatott a App Service on Linux.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférés konfigurálásával korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: A felhasználók számítási erőforrásokon belüli szkriptek végrehajtására vonatkozó képességének korlátozása

**Útmutató:** A App Service webjobs lehetővé teszi az ügyfelek számára, hogy egy webalkalmazással, API-alkalmazással vagy mobilalkalmazással egy példányban futtassanak egy programot vagy szkriptet. Az Ön felelőssége, hogy meghatározza a konfigurációt a szervezet által nem engedélyezett parancsfájlok korlátozására vagy korlátozására. App Service nem biztosít olyan mechanizmust, amely natív módon korlátozza a szkriptek végrehajtását. Vegye figyelembe, hogy a WebJobs-feladat még nem támogatott App Service on Linux.

- [Háttérfeladatok futtatása a WebJobs-feladatokkal a Azure App Service](webjobs-create.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Magas kockázatú alkalmazások fizikai vagy logikai elkülönítése

**Útmutató:** Külön előfizetéseket vagy felügyeleti csoportokat implementálja a magas kockázatú alkalmazások elkülönítése App Service érdekében. Magasabb kockázatú alkalmazás üzembe helyezése a saját Virtual Network, mivel a peremhálózati App Service virtuális hálózatok használatával érhető el. A App Service Environment az Azure-App Service egy alhálózatán üzemelő Virtual Network. 

Az alkalmazás-szolgáltatási környezetnek két típusa létezik: a külső alkalmazásszolgáltatás-környezet és az ILB (belső Load Balancer) alkalmazásszolgáltatás-környezet. A követelmények alapján válassza ki a legjobb architektúrát.

- [App Service-környezet hálózati megfontolásai](environment/network-info.md)

- [Külső App Service létrehozása](environment/create-external-ase.md)

- [Belső alkalmazás létrehozása és Load Balancer App Service Environment](environment/create-ilb-ase.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Szabványos biztonsági konfigurációkat határozhat meg és valósíthat meg App Service telepített alkalmazásokhoz a Azure Policy.

A Azure Policy a "Microsoft.Web" névtérben található aliasok használatával egyéni szabályzatokat hozhat létre az adatbázis konfigurációjának naplózása vagy kényszerít App Service Web Apps.

Beépített szabályzatdefiníciók alkalmazása, például:

- App Service hálózati szolgáltatásvégpontot kell használnia

- A webalkalmazások csak HTTPS protokollon keresztül érhetők el

- A legújabb TLS-verzió használata az alkalmazásokban

Javasoljuk, hogy dokumentálja a beépített szabályzatdefiníciók szabványosított használatra való alkalmazásának folyamatát.   

- [Az elérhető aliasok Azure Policy megtekintése](/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&preserve-view=true)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** A Azure Policy [megtagadás] és a [telepítés, ha nem létezik] hatásokkal kényszerítheti a biztonságos beállításokat a Azure App Service alkalmazásokban.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Válassza az Azure DevOps vagy az Azure Repos lehetőséget a kód biztonságos tárolására és kezelésére egyéni definíciók Azure Policy esetén.

A meglévő folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamat használatával üzembe helyezhet egy ismerten biztonságos konfigurációt.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Az Azure Repos dokumentációja](/azure/devops/repos/?view=azure-devops&preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** Beépített Azure Policy és aliasok használata Azure Policy a "Microsoft.Web" névtérben egyéni szabályzatok létrehozásához a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Folyamat és folyamat fejlesztése szabályzati kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** Beépített Azure Policy és aliasok használata Azure Policy a "Microsoft.Web" névtérben egyéni szabályzatok létrehozásához a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. 

Alkalmazza Azure Policy [audit], [deny] és [deploy if not exist] ([üzembe helyezés, ha nem létezik]) hatást az Azure-erőforrások konfigurációjának automatikus kényszerítéséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** A Felügyelt identitások használatával automatikusan felügyelt identitást App Service az alkalmazásokat a Azure Active Directory (Azure AD) szolgáltatásban. A felügyelt identitások lehetővé teszik, hogy az alkalmazások bármely, az Azure AD-hitelesítést támogató szolgáltatásban hitelesítsen, beleértve a Key Vault is, anélkül, hogy a kódban hitelesítő adatokat használ. Győződjön meg arról, hogy a szoftverben engedélyezve van a Azure Key Vault.

- [A szoftveres törlés engedélyezése a Azure Key Vault](../key-vault/general/key-vault-recovery.md)

- [Felügyelt identitások használata App Service](overview-managed-identity.md)

- [Felügyelt identitással Key Vault hitelesítés engedélyezése](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Felügyelt identitások használatával biztosítson App Service üzembe helyezett alkalmazásokat egy automatikusan felügyelt identitással a Azure Active Directory (Azure AD) szolgáltatásban. A Felügyelt identitások lehetővé teszik, hogy az alkalmazások bármely, az Azure AD-hitelesítést támogató szolgáltatásban hitelesítsék magukat, beleértve a Key Vault is, anélkül, hogy a kódban hitelesítő adatokat használ.

- [Felügyelt identitások használata App Service](overview-managed-identity.md)

- [Felügyelt identitással Key Vault hitelesítés engedélyezése](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódon belül. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** A biztonsági mentési és visszaállítási App Service lehetővé teszi az alkalmazások manuális vagy ütemezett biztonsági mentését. A biztonsági másolatokat beállíthatja úgy, hogy határozatlan ideig megőrizze őket. Az alkalmazást visszaállíthatja egy korábbi állapot pillanatképében a meglévő alkalmazás felülírása vagy egy másik alkalmazásba való visszaállítás segítségével.

App Service az alábbi adatokról egy Azure Storage-fiókba és -tárolóba, amelyet az alkalmazás használatára konfigurált:
- Alkalmazáskonfiguráció
- Fájl tartalma
- Az alkalmazáshoz csatlakoztatott adatbázis

Győződjön meg arról, hogy a rendszeres és automatizált biztonságimentési műveletek a szervezeti szabályzatok által meghatározott gyakorisággal történik.

- [A Azure App Service funkció](manage-backup.md)

- [Felhasználó által kezelt kulcsok az Azure Storage-titkosításhoz](../storage/common/customer-managed-keys-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és ügyfél által kezelt kulcsok biztonsági mentése

**Útmutató:** Az alkalmazások biztonsági mentéséhez használja App Service biztonsági mentési és visszaállítási szolgáltatását. A biztonsági mentési funkciókhoz Azure Storage-fiókra van szükség az alkalmazás biztonsági mentési információinak tárolásához.

- Az Azure Storage titkosítást biztosít az azure-beli használat során – Használjon rendszer által biztosított kulcsokat vagy saját, ügyfél által kezelt kulcsokat. Itt tárolja a rendszer az alkalmazásadatokat, ha nem az Azure-beli webalkalmazásban futnak.
- A központi telepítési csomagból való futtatás a központi telepítési App Service. Lehetővé teszi a webhely tartalmának üzembe helyezését egy Azure Storage-fiókból egy közös hozzáférésű jogosultság jogosultsága (SAS) URL-címével.

- Key Vault hivatkozások az adatok biztonsági App Service. Lehetővé teszi a titkos kulcsok futásidőben történő importálását alkalmazásbeállításokként. Ezzel titkosíthatja az Azure Storage-fiók SAS URL-címét.

További információ a hivatkozott hivatkozásokon érhető el.

- [Adatok biztonsági mentése az Azure-ban](manage-backup.md)

- [Egy alkalmazásban futó alkalmazás Azure App Service](web-sites-restore.md)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Titkosítási modell és kulcskezelési táblázat](../security/fundamentals/encryption-atrest.md)

- [Titkosítás az ügyfelek által kezelt kulcsokkal](configure-encrypt-at-rest-using-cmk.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Rendszeres időközönként tesztelje a visszaállítási folyamatot a App Service biztonsági másolatait.

- [Adatok biztonsági mentése az Azure-ban](manage-backup.md)

- [Új webalkalmazás Azure App Service visszaállítása](web-sites-restore.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** App Service biztonsági másolatok egy Azure Storage-fiókban vannak tárolva. Az Azure Storage-ban tárolt adatok titkosítása és visszafejtése transzparens módon, 256 bites AES-titkosítással történik, amely az egyik legerősebb elérhető blokktitkosítás, és FIPS 140-2 szabványnak felel meg. Az Azure Storage-titkosítás hasonló a Windows rendszeren történő BitLocker-titkosításhoz.

Az Azure Storage-titkosítás minden tárfiókhoz engedélyezve van, beleértve a Resource Manager a klasszikus tárfiókokat is. Az Azure Storage-titkosítás nem tiltható le. Mivel az adatok alapértelmezés szerint védettek, nem kell módosítania a kódot vagy az alkalmazásokat az Azure Storage-titkosítás előnyeinek kihasználása érdekében.

Alapértelmezés szerint a tárfiókban található adatok a Microsoft által kezelt kulcsokkal vannak titkosítva. Az adatok titkosítása a Microsoft által felügyelt kulcsokkal, vagy a saját kulcsokkal is kezelhető. Győződjön meg arról, hogy a szoftverben engedélyezve van a Azure Key Vault.

- [Az Azure Storage-titkosítás használata az adattároláshoz](../storage/common/storage-service-encryption.md)

- [A soft delete engedélyezése a Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálások konfigurálása a Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Az ügyfél az NIST számítógépes biztonsági incidenskezelési útmutatóját is felhasználhatja, hogy segítséget nyújt a saját incidenskezelési tervének létrehozásában](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így priorizálhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, Security Center mennyire magabiztos a riasztás kiadásához használt eredmény vagy elemzés, valamint hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.

Emellett egyértelműen jelölheti meg az előfizetéseket (például éles, nem éles), és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszer incidensválasz-képességeinek rendszeres teszteléséhez. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg az NIST kiadványát – Útmutató az it-csomagokhoz és -képességekhez : Útmutató a tesztelési, képzési és gyakorlatprogramokhoz](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha az Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy az ügyfél adatait illetéktelen vagy illetéktelen fél férte hozzá.  Tekintse át az incidenseket a probléma megoldása érdekében.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A Security Center riasztások és javaslatok exportálása a Folyamatos exportálás funkcióval. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A Security Center adat-összekötővel streamelhet riasztásokat, hogy Azure Sentinel az üzleti igényeknek megfelelően.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A Security Center munkafolyamat-automatizálási funkcióját használhatja a biztonsági riasztások és javaslatok "Logic Apps" funkcióval történő automatikus aktiválásához.

- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolási tesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft Felhőbevezetési tesztelési szabályait annak ellenőrzéséhez, hogy a behatolási tesztek nem sértik-e meg a Microsoft szabályzatát. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
