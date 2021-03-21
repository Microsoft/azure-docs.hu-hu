---
title: Kimenő forgalom zárolása
description: Ismerje meg, hogyan integrálható a Azure Firewall a kimenő forgalom biztonságos App Service-környezetből történő biztonságossá tételéhez.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 09/24/2020
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: ec506546b52a2d137d448f07f4b7a6827c01b4d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594116"
---
# <a name="locking-down-an-app-service-environment"></a>App Service Environment zárolása

A App Service Environment (beadási) számos külső függőséggel rendelkezik, amelyekhez hozzáférést igényel ahhoz, hogy megfelelően működjön. A beszállító az Azure Virtual Networkban (VNet) él. Az ügyfeleknek engedélyezniük kell a bevezető függőségi forgalmat, ami olyan ügyfelek számára jelent problémát, akik az összes kimenő forgalomból le szeretnék zárni a VNet.

Számos bejövő végpontot használnak a bevezetéshez. A bejövő felügyeleti forgalmat nem lehet tűzfal-eszközön keresztül elküldeni. A forgalomhoz tartozó forrásoldali címek ismertek, és közzé lesznek téve a [app Service Environment felügyeleti címek](./management-addresses.md) dokumentumban. Létezik egy AppServiceManagement nevű szolgáltatási címke is, amely hálózati biztonsági csoportokkal (NSG) is használható a bejövő forgalom biztonságossá tételéhez.

A kihelyezett kifelé irányuló kimenő függőségek szinte teljesen meg vannak határozva teljes TARTOMÁNYNEVEk használatával, amelyek nem rendelkeznek a mögöttes statikus címekkel. A statikus címek hiánya azt jelenti, hogy a hálózati biztonsági csoportok nem használhatók a kifelé irányuló kimenő forgalom zárolására. A címek elég gyakran változnak, hogy az aktuális felbontáson alapuló szabályok nem állíthatók be, és ezt a NSG létrehozásához használja. 

A kimenő címek biztonságossá tételére szolgáló megoldás egy olyan tűzfal-eszköz használata, amely a tartománynevek alapján képes a kimenő forgalom vezérlésére. A Azure Firewall a cél teljes tartományneve alapján korlátozhatja a kimenő HTTP-és HTTPS-forgalmat.  

## <a name="system-architecture"></a>Rendszer-architektúra

Egy olyan, a tűzfalon áthaladó kimenő forgalommal rendelkező bevezetéshez, amely az útvonalakat a bevezető alhálózaton át kívánja módosítani. Az útvonalak IP-szinten működnek. Ha nem érdekli az útvonalak meghatározása, a TCP-válasz forgalmát más címről is kikényszerítheti a forrásra. Ha a válasz címe eltér a címek adatforgalmával, akkor a problémát aszimmetrikus útválasztásnak nevezzük, és a rendszer megszakítja a TCP-t.

Az útvonalakat úgy kell meghatározni, hogy a beérkező forgalom ugyanúgy reagáljon, mint a forgalom. Az útvonalakat meg kell határozni a bejövő felügyeleti kérelmekhez és a bejövő alkalmazásokra vonatkozó kérésekhez.

A befelé irányuló adatforgalomnak a következő egyezményeknek kell megfelelnie

* Az Azure SQL, a Storage és az Event hub szolgáltatásba irányuló adatforgalom nem támogatott a tűzfal eszközének használata esetén. Ezt a forgalmat közvetlenül a szolgáltatásoknak kell elküldeni. Ennek az az oka, hogy a szolgáltatás-végpontokat konfigurálja a három szolgáltatáshoz. 
* Az útválasztási táblázat szabályait úgy kell meghatározni, hogy a bejövő felügyeleti forgalom vissza legyen küldve onnan, ahonnan származik.
* Az útválasztási táblázat szabályait úgy kell meghatározni, hogy a bejövő alkalmazások forgalmát visszaküldjék a Honnan érkezett helyről. 
* Az összes többi, a bevezetőt elhagyó adatforgalmat elküldhetik a tűzfal eszközére egy útválasztási táblázat szabály segítségével.

![BeAzure Firewalli kapcsolatok folyamata][5]

## <a name="locking-down-inbound-management-traffic"></a>Bejövő felügyeleti forgalom zárolása

Ha a betanító alhálózat még nincs hozzárendelve NSG, hozzon létre egyet. A NSG belül állítsa be az első szabályt, hogy engedélyezze a forgalmat a AppServiceManagement nevű szolgáltatási címkétől az 454-es, 455-as porton. A AppServiceManagement címkéből való hozzáférést engedélyező szabály az egyetlen, ami szükséges a nyilvános IP-címekről a bevezetésének kezeléséhez. A szolgáltatás címkéje mögött található címek csak a Azure App Service felügyeletére használhatók. Az ezeken a kapcsolatokon áthaladó felügyeleti forgalom titkosítva van, és biztonságos a hitelesítési tanúsítványokkal. Az ezen a csatornán jellemző forgalom olyan dolgokat tartalmaz, mint például az ügyfél által kezdeményezett parancsok és az állapot-mintavétel. 

A portálon egy új alhálózattal létrehozott ASE egy olyan NSG készülnek, amely tartalmazza a AppServiceManagement címke engedélyezési szabályát.  

A kiegészítő szolgáltatásnak a 16001-as porton lévő Load Balancer címkén is engedélyeznie kell a bejövő kérelmeket. Az 16001-as porton lévő Load Balancertól érkező kérések életben maradnak a Load Balancer és a központba való bejáratok között. Ha a 16001-es port le van tiltva, a bevezetés nem kifogástalan állapotú lesz.

## <a name="configuring-azure-firewall-with-your-ase"></a>Azure Firewall konfigurálása a beadással 

A meglévő és a Azure Firewall rendszerből kifelé irányuló kimenő forgalom zárolásának lépései a következők:

1. Engedélyezze a szolgáltatási végpontokat az SQL, a Storage és az Event hub számára a szolgáltatói alhálózatán. A szolgáltatási végpontok engedélyezéséhez lépjen a hálózatkezelési portál > alhálózatok elemre, és válassza a Microsoft. EventHub, a Microsoft. SQL és a Microsoft. Storage lehetőséget a szolgáltatás végpontjai legördülő listából. Ha a szolgáltatási végpontok engedélyezve vannak az Azure SQL-ben, az alkalmazások által használt Azure SQL-függőségeket is konfigurálni kell a szolgáltatás-végpontokkal. 

   ![szolgáltatási végpontok kiválasztása][2]
  
1. Hozzon létre egy AzureFirewallSubnet nevű alhálózatot abban a VNet, ahol a központjának létezik. A Azure Firewall létrehozásához kövesse az [Azure Firewall dokumentációjának](../../firewall/index.yml) utasításait.

1. Az Azure Firewall felhasználói felület > szabályok > az alkalmazási szabályok gyűjteménye területen válassza az alkalmazás-szabály gyűjtemény hozzáadása elemet. Adjon meg egy nevet, egy prioritást, és állítsa be az Engedélyezés lehetőséget. A FQDN-címkék szakaszban adjon meg egy nevet, állítsa be a címeket a * értékre, és válassza ki a App Service Environment FQDN címkét és a Windows Update. 
   
   ![Alkalmazás-szabály hozzáadása][1]
   
1. A Azure Firewall felhasználói felület > szabályok > hálózati szabály gyűjteménye területen válassza a hálózati szabálygyűjtemény hozzáadása elemet. Adjon meg egy nevet, egy prioritást, és állítsa be az Engedélyezés lehetőséget. A szabályok szakaszban az IP-címek területen adjon meg egy nevet, válasszon ki egy **tetszőleges** protokollt, állítsa be a forrás és a cél címet, majd állítsa a portokat 123-re. Ez a szabály lehetővé teszi, hogy a rendszerállapot-szinkronizálást az NTP használatával hajtsa végre. Hozzon létre egy másik szabályt úgy, hogy az a 12000-es porttal azonos módon segítse a rendszerproblémák osztályozását. 

   ![NTP hálózati szabály hozzáadása][3]
   
1. A Azure Firewall felhasználói felület > szabályok > hálózati szabály gyűjteménye területen válassza a hálózati szabálygyűjtemény hozzáadása elemet. Adjon meg egy nevet, egy prioritást, és állítsa be az Engedélyezés lehetőséget. A szabályok szakaszban a szolgáltatás címkék területén adjon meg egy nevet, válasszon ki egy **tetszőleges** protokollt, állítsa * a forrás címekre, válassza ki a AzureMonitor szolgáltatási címkéjét, és állítsa a portokat 80, 443 értékre. Ez a szabály lehetővé teszi, hogy a Azure Monitor állapot-és mérőszámokkal kapcsolatos információkat adjon meg.

   ![NTP szolgáltatási címke hálózati szabályának hozzáadása][6]
   
1. Hozzon létre egy útválasztási táblázatot a [app Service Environment felügyeleti címekből]( ./management-addresses.md) származó felügyeleti címekkel az Internet következő ugrásával. Az útválasztási tábla bejegyzéseinek elkerülheti az aszimmetrikus útválasztási problémákat. Vegyen fel útvonalakat az IP-címek függőségeinél alább látható, az Internet következő ugrásával elérhető IP-címek függőségeibe. Adjon hozzá egy virtuális berendezési útvonalat a 0.0.0.0/0 útválasztási táblázathoz, és a következő ugrás a Azure Firewall magánhálózati IP-címe legyen. 

   ![Útválasztási táblázat létrehozása][4]
   
1. Rendelje hozzá a létrehozott útválasztási táblázatot a bevezető alhálózathoz.

#### <a name="deploying-your-ase-behind-a-firewall"></a>A bevezetése tűzfal mögötti üzembe helyezése

A központnak a tűzfal mögötti üzembe helyezésének lépései ugyanazok, mint a meglévő beadási Azure Firewall konfigurálása, kivéve, ha létre kell hoznia a bevezetési alhálózatot, majd követnie kell az előző lépéseket. Ha egy meglévő alhálózatban szeretné létrehozni a bevezetőt, akkor egy Resource Manager-sablont kell használnia a szolgáltató [Resource Manager-sablonnal történő létrehozásával foglalkozó](./create-from-template.md)dokumentumban leírtak szerint.

## <a name="application-traffic"></a>Alkalmazás forgalma 

A fenti lépések lehetővé teszik, hogy a bevezetés problémamentesen működjön. Továbbra is konfigurálnia kell a dolgokat az alkalmazás igényeinek megfelelően. Két probléma merült fel olyan alkalmazásokkal kapcsolatban, amelyek Azure Firewall-vel vannak konfigurálva.  

- Az alkalmazás függőségeit fel kell venni a Azure Firewall vagy az útválasztási táblába. 
- A aszimmetrikus útválasztási problémák elkerülése érdekében létre kell hozni útvonalakat az alkalmazás forgalmához

Ha az alkalmazásai függőségekkel rendelkeznek, azokat hozzá kell adni a Azure Firewallhoz. Alkalmazás-szabályok létrehozása a HTTP/HTTPS-forgalom és a hálózati szabályok minden más számára történő engedélyezéséhez. 

Ha ismeri azt a címtartományt, amelyet az alkalmazás kérelmének adatforgalma fog származni, hozzáadhatja azt a bevezető alhálózathoz rendelt útválasztási táblához. Ha a címtartomány nagy vagy nincs meghatározva, akkor a Application Gateway egy olyan hálózati berendezést használhat, amely az útválasztási táblázatba való felvételhez egy-egy-egy-egy IP-címeket biztosít. A Application Gateway ILB-vel történő konfigurálásával kapcsolatos részletekért olvassa el a következőt: a ILB-előállítók [integrálása egy Application Gateway](./integrate-with-application-gateway.md)

A Application Gateway használata csupán egy példa arra, hogyan konfigurálhatja a rendszerét. Ha ezt az elérési utat követte, akkor hozzá kell adnia egy útvonalat a bevezető alhálózat útválasztási táblájához, hogy a Application Gateway küldött válasz-forgalom azonnal elérhető legyen. 

## <a name="logging"></a>Naplózás 

Azure Firewall küldhet naplókat az Azure Storage-ba, az Event hub-ba vagy a Azure Monitor naplókba. Az alkalmazás bármely támogatott célhoz való integrálásához lépjen a Azure Firewall-portálra > a diagnosztikai naplókat, és engedélyezze a kívánt célhelyhez tartozó naplókat. Ha integrálja Azure Monitor-naplókat, akkor a Azure Firewallba érkező forgalom naplózása látható. A megtagadott forgalom megjelenítéséhez nyissa meg a Log Analytics munkaterület-portálon > naplókat, és adjon meg egy lekérdezést, például: 

```kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Ha nem ismeri az összes alkalmazás-függőséget, akkor hasznos, ha a Azure Firewall Azure Monitor naplókkal való integrációja során először működik. További tudnivalókat Azure Monitor naplók [elemzése a naplófájlok elemzéséről Azure monitor](../../azure-monitor/logs/log-query-overview.md).
 
## <a name="dependencies"></a>Függőségek

A következő információkra csak akkor van szükség, ha nem Azure Firewall tűzfalat kíván konfigurálni. 

- A szolgáltatási végponttal kompatibilis szolgáltatásokat a szolgáltatási végpontokkal kell konfigurálni.
- Az IP-címek függőségei nem HTTP/S forgalomra vonatkoznak (TCP-és UDP-forgalom)
- Az FQDN HTTP-/HTTPS-végpontok a tűzfal eszközén helyezhetők el.
- A helyettesítő HTTP-/HTTPS-végpontok olyan függőségek, amelyek számos minősítőtől függően eltérőek lehetnek a kiszervezettől. 
- A Linux-függőségek csak akkor fontosak, ha Linux-alkalmazásokat telepít a központba. Ha nem telepít linuxos alkalmazásokat a bevezetésbe, akkor ezeket a címeket nem kell hozzáadni a tűzfalhoz. 

#### <a name="service-endpoint-capable-dependencies"></a>Szolgáltatási végpontok számára alkalmas függőségek 

| Végpont |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP-címek függőségei

| Végpont | Részletek |
|----------| ----- |
| \*: 123 | NTP órajel-ellenőrzési. A forgalom a 123-es porton több végponton van bejelölve |
| \*: 12000 | A rendszer ezt a portot használja a rendszerfigyeléshez. Ha blokkolva van, bizonyos problémák nehezebbek lesznek az osztályozáshoz, de a bevezetés továbbra is működni fog |
| 40.77.24.27:80 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 40.77.24.27:443 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 13.90.249.229:80 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 13.90.249.229:443 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 104.45.230.69:80 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 104.45.230.69:443 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 13.82.184.151:80 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 13.82.184.151:443 | A kimutatott problémák monitorozásához és riasztásához szükséges |

A Azure Firewall automatikusan megkapja a teljes tartománynév-címkékkel konfigurált összes értéket. 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-függőségek 

| Végpont |
|----------|
|graph.microsoft.com:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|ocsp.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|prod.microsoftmetrics.com:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|gcs.prod.monitoring.core.windows.net:80|
|gcs.prod.monitoring.core.windows.net:443|
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |
|global-dsms.dsms.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Helyettesítő HTTP/HTTPS-függőségek 

| Végpont |
|----------|
|gr-Prod- \* . cloudapp.net:443 |
| \*. management.azure.com:443 |
| \*. update.microsoft.com:443 |
| \*. windowsupdate.microsoft.com:443 |
| \*. identity.azure.net:443 |
| \*. ctldl.windowsupdate.com:80 |
| \*. ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Linux-függőségek 

| Végpont |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
|oryx-cdn.microsoft.io:443 |
| \*. cdn.mscr.io:443 |
| \*. data.mcr.microsoft.com:443 |
|mcr.microsoft.com:443 |
|\*. data.mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>US Gov függőségek

US Gov régiókban található ASE esetében kövesse a jelen dokumentum [Azure Firewall konfigurálása](#configuring-azure-firewall-with-your-ase) a beadással című szakaszának utasításait, és konfigurálja az Azure Firewallt a központhoz.

Ha a Azure Firewallon kívül más eszközt szeretne használni US Gov 

* A szolgáltatási végponttal kompatibilis szolgáltatásokat a szolgáltatási végpontokkal kell konfigurálni.
* Az FQDN HTTP-/HTTPS-végpontok a tűzfal eszközén helyezhetők el.
* A helyettesítő HTTP-/HTTPS-végpontok olyan függőségek, amelyek számos minősítőtől függően eltérőek lehetnek a kiszervezettől.

A Linux US Gov régióban nem érhető el, ezért nem választható konfigurációként szerepel.

#### <a name="service-endpoint-capable-dependencies"></a>Szolgáltatási végpontok számára alkalmas függőségek ####

| Végpont |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP-címek függőségei

| Végpont | Részletek |
|----------| ----- |
| \*: 123 | NTP órajel-ellenőrzési. A forgalom a 123-es porton több végponton van bejelölve |
| \*: 12000 | A rendszer ezt a portot használja a rendszerfigyeléshez. Ha blokkolva van, bizonyos problémák nehezebbek lesznek az osztályozáshoz, de a bevezetés továbbra is működni fog |
| 40.77.24.27:80 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 40.77.24.27:443 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 13.90.249.229:80 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 13.90.249.229:443 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 104.45.230.69:80 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 104.45.230.69:443 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 13.82.184.151:80 | A kimutatott problémák monitorozásához és riasztásához szükséges |
| 13.82.184.151:443 | A kimutatott problémák monitorozásához és riasztásához szükséges |

#### <a name="dependencies"></a>Függőségek ####

| Végpont |
|----------|
| \*. ctldl.windowsupdate.com:80 |
| \*. management.usgovcloudapi.net:80 |
| \*. update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com:80
|ocsp.digicert.com:80 |
|ocsp.verisign.com:80 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*. management.usgovcloudapi.net:443 |
|\*. update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|gcs.monitoring.core.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |
|global-dsms.dsms.core.usgovcloudapi.net:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
