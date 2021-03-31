---
title: Konfigurációs és felügyeleti problémák – gyakori kérdések
description: Ez a cikk a Microsoft Azure Cloud Services konfigurálásával és kezelésével kapcsolatos gyakori kérdéseket sorolja fel.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 31659f4e8e4f9e25a997be54223b8856edfa8abe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612983"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Konfigurációs és felügyeleti problémák az Azure Cloud Services (klasszikus) szolgáltatáshoz: gyakori kérdések (GYIK)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ez a cikk a [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)konfigurálásával és kezelésével kapcsolatos gyakori kérdéseket tartalmazza. A mérettel kapcsolatos információkért tekintse meg a [Cloud Services virtuális gép mérete lapot](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Tanúsítványok**

- [Miért nem fejeződött be a Cloud Service TLS/SSL-tanúsítványának tanúsítványlánc?](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [Mi a célja a "Windows Azure-eszközök titkosítási tanúsítványa bővítmények számára"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Hogyan hozható elő tanúsítvány-aláírási kérelem (CSR) a példányban "RDP-ing" nélkül?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [A felhőalapú szolgáltatás felügyeleti tanúsítványa lejár. Hogyan újítható meg?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [A fő TLS/SSL-tanúsítvány (. pfx) és a köztes tanúsítvány (. p7b) telepítésének automatizálása](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [Mi a "Microsoft Azure Service Management for MachineKey" tanúsítvány célja?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitorozás és naplózás**

- [Melyek az Azure Portal közelgő Cloud Service-képességei, amelyek segíthetnek az alkalmazások kezelésében és figyelésében?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Miért állítja le az IIS a naplózási könyvtárat?](#why-does-iis-stop-writing-to-the-log-directory)
- [Hogyan a WAD-naplózás engedélyezése a Cloud Serviceshoz?](#how-do-i-enable-wad-logging-for-cloud-services)

**Hálózati konfiguráció**

- [Hogyan beállítani az Azure Load Balancer üresjárati időkorlátját?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Hogyan statikus IP-címet rendelni a felhőalapú szolgáltatáshoz?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Milyen funkciókat és képességeket biztosít az Azure alapszintű IP-címei/AZONOSÍTÓi és a DDOS?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [A HTTP/2 engedélyezése Cloud Services virtuális gépen](#how-to-enable-http2-on-cloud-services-vm)

**Engedélyek**

- [Jogosult a Microsoft belső mérnökök a Távoli asztalról a Cloud Service-példányokra engedély nélkül?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Az RDP-fájllal nem lehet távoli asztalt használni a Cloud Service virtuális géphez. A következő hibaüzenet jelenik meg: hitelesítési hiba történt (kód: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Méretezés**

- [X példányokon túl nem tudom méretezni](#i-cannot-scale-beyond-x-instances)
- [Hogyan állíthatom be az automatikus méretezést a memória metrikái alapján?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Általános**

- [Hogyan Hozzáadás `nosniff` a saját webhelyhez?](#how-do-i-add-nosniff-to-my-website)
- [Az IIS webes szerepkörhöz való Hogyan testreszabása](#how-do-i-customize-iis-for-a-web-role)
- [Mi a felhőalapú szolgáltatás kvótájának korlátja?](#what-is-the-quota-limit-for-my-cloud-service)
- [Miért jelenik meg a Cloud Service-beli virtuális gép meghajtója nagyon kevés szabad lemezterülettel?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Hogyan adhatok hozzá egy antimalware-bővítményt a Cloud Serviceshoz automatizált módon?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Hogyan engedélyezhető a Kiszolgálónév jelzése (SNI) a Cloud Serviceshoz?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Hogyan adhatok hozzá címkéket az Azure Cloud Service-hez?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [A Azure Portal nem jeleníti meg a felhőalapú szolgáltatás SDK-verzióját. Hogyan szerezhetem be?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Több hónapig szeretnék leállítani a Cloud Service-t. Hogyan csökkentheti a Cloud Service számlázási költségeit az IP-cím elvesztése nélkül?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Tanúsítványok

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>Miért nem fejeződött be a Cloud Service TLS/SSL-tanúsítványának tanúsítványlánc?
    
Azt javasoljuk, hogy az ügyfél a teljes tanúsítványláncot (levél-tanúsítvány, közbenső tanúsítványok és főtanúsítvány) telepítse a levél-tanúsítvány helyett. Ha csak a levél tanúsítványát telepíti, a Windows rendszerre támaszkodva kiépítheti a tanúsítványláncot a CTL bejárásával. Ha időszakos hálózati vagy DNS-problémák történnek az Azure-ban vagy Windows Update ha a Windows a tanúsítvány érvényesítését kísérli meg, akkor a tanúsítvány érvénytelennek tekintendő. A teljes tanúsítványlánc telepítésével elkerülhető a probléma. A [LÁNCOLT SSL-tanúsítványok telepítéséhez](/archive/blogs/azuredevsupport/how-to-install-a-chained-ssl-certificate) szükséges blog bemutatja, hogyan teheti ezt meg.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Mi a célja a "Windows Azure-eszközök titkosítási tanúsítványa bővítmények számára"?

A rendszer automatikusan létrehozza ezeket a tanúsítványokat, amikor hozzáadnak egy bővítményt a felhőalapú szolgáltatáshoz. Leggyakrabban ez a WAD-bővítmény vagy az RDP-bővítmény, de mások is lehetnek, például a antimalware vagy a log Collector bővítmény. Ezeket a tanúsítványokat csak a bővítmény privát konfigurációjának titkosítására és visszafejtésére használják. A lejárati dátum soha nem lesz ellenőrizve, ezért nem számít, hogy a tanúsítvány lejárt-e. 

Ezeket a tanúsítványokat figyelmen kívül hagyhatja. Ha törölni szeretné a tanúsítványokat, próbálja meg az összes törlését. Az Azure hibát jelez, ha olyan tanúsítványt próbál törölni, amely használatban van.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Hogyan hozható elő tanúsítvány-aláírási kérelem (CSR) a példányban "RDP-ing" nélkül?

Tekintse meg a következő útmutató dokumentumát:

[Tanúsítvány beszerzése a Windows Azure webhelyek (WAWS) használatához](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

A CSR csak szöveges fájl. Nem kell létrehozni azt a gépről, ahol a tanúsítványt végül használni fogjuk.Bár ez a dokumentum egy App Servicere íródott, a CSR létrehozása általános, és a Cloud Services is érvényes.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>A felhőalapú szolgáltatás felügyeleti tanúsítványa lejár. Hogyan újítható meg?

A felügyeleti tanúsítványok megújításához a következő PowerShell-parancsokat használhatja:

```powershell
Add-AzureAccount
Select-AzureSubscription -Current -SubscriptionName <your subscription name>
Get-AzurePublishSettingsFile
```

A **Get-AzurePublishSettingsFile** új felügyeleti tanúsítványt hoz létre az **előfizetés**-  >  **kezelési tanúsítványokban** a Azure Portalban. Az új tanúsítvány neve a következőhöz hasonló: "YourSubscriptionNam]-[CurrentDate]-hitelesítő adatok".

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>A fő TLS/SSL-tanúsítvány (. pfx) és a köztes tanúsítvány (. p7b) telepítésének automatizálása

Ezt a feladatot indítási parancsfájl (batch/cmd/PowerShell) használatával automatizálhatja, és regisztrálhatja az indítási parancsfájlt a szolgáltatás definíciós fájljában. Adja hozzá az indítási parancsfájlt és a tanúsítványt (. p7b fájl) az indítási parancsfájl ugyanazon könyvtárának projekt mappájához.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Mi a "Microsoft Azure Service Management for MachineKey" tanúsítvány célja?

Ezzel a tanúsítvánnyal titkosíthatja a számítógép kulcsait az Azure web roles szolgáltatásban. További információért tekintse meg [ezt a tanácsadót](/security-updates/securityadvisories/2018/4092731).

További információért tekintse át a következő cikkeket:
- [Felhőalapú szolgáltatás indítási feladatainak konfigurálása és futtatása](./cloud-services-startup-tasks.md)
- [Gyakori Cloud Service indítási feladatai](./cloud-services-startup-tasks-common.md)

## <a name="monitoring-and-logging"></a>Monitorozás és naplózás

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Melyek az Azure Portal közelgő Cloud Service-képességei, amelyek segíthetnek az alkalmazások kezelésében és figyelésében?

Hamarosan új tanúsítvány hozható elő RDP protokoll (RDP) számára. Azt is megteheti, hogy a következő parancsfájlt futtatja:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Kiválaszthatja a blob vagy a helyi lehetőséget a csdef, és a cscfg feltöltési helye hamarosan elérhető lesz. A [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment)használatával megadhatja az egyes helyek értékét.

A metrikák figyelésének lehetősége a példány szintjén. További figyelési képességek érhetők el a [Cloud Services figyelése című témakörben](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Miért állítja le az IIS a naplózási könyvtárat?
Kimerítette a helyi tárolási kvótát a naplózási könyvtárba való íráshoz.Ennek kijavítania a következő három dolog egyikét teheti:
* Az IIS-diagnosztika engedélyezése, és a diagnosztika rendszeres időközönként áthelyezhető a blob Storage-ba.
* Távolítsa el manuálisan a naplófájlokat a naplózási könyvtárból.
* Növelje a helyi erőforrások kvótájának korlátját.

További információ a következő dokumentumokban található:
* [Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban](../storage/common/storage-introduction.md)
* [IIS-naplók – írások leállítása a Cloud Service-ben](/archive/blogs/cie/iis-logs-stops-writing-in-cloud-service)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Hogyan a WAD-naplózás engedélyezése a Cloud Serviceshoz?
A Windows Azure Diagnostics (WAD) naplózását a következő beállításokkal engedélyezheti:
1. [Engedélyezés a Visual studióból](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Engedélyezés .NET-kóddal](./cloud-services-dotnet-diagnostics.md)
3. [Engedélyezés a PowerShell-lel](./cloud-services-diagnostics-powershell.md)

A Cloud Service jelenlegi WAD-beállításainak beszerzéséhez használja a [Get-AzureServiceDiagnosticsExtensions](./cloud-services-diagnostics-powershell.md#get-current-diagnostics-extension-configuration) PowerShell cmd parancsot, vagy megtekintheti a portálon a "Cloud Services--> Extensions" panelen.


## <a name="network-configuration"></a>Hálózati konfiguráció

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Hogyan beállítani az Azure Load Balancer üresjárati időkorlátját?
A következőhöz hasonló időkorlátot adhat meg a Service Definition (csdef) fájlban:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
További információ [: Azure Load Balancer konfigurálható Üresjárati időkorlát](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) .

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Hogyan statikus IP-címet rendelni a felhőalapú szolgáltatáshoz?
Statikus IP-cím beállításához létre kell hoznia egy fenntartott IP-címet. Ez a fenntartott IP-cím társítható egy új felhőalapú szolgáltatáshoz vagy egy meglévő üzembe helyezéshez is. A részletekért tekintse meg a következő dokumentumokat:
* [Fenntartott IP-cím létrehozása](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Meglévő felhőalapú szolgáltatás IP-címének lefoglalása](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Fenntartott IP-cím hozzárendelése egy új felhőalapú szolgáltatáshoz](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Fenntartott IP-cím hozzárendelése futó központi telepítéshez](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Fenntartott IP-cím hozzárendelése egy felhőalapú szolgáltatáshoz szolgáltatás-konfigurációs fájl használatával](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Milyen funkciókat és képességeket biztosít az Azure alapszintű IP-címei/AZONOSÍTÓi és a DDOS?
Az Azure az adatközpont-fizikai kiszolgálók IP-címeivel és AZONOSÍTÓkkal rendelkezik a fenyegetések elleni védelemhez. Emellett az ügyfelek telepíthetnek harmadik féltől származó biztonsági megoldásokat, például a webalkalmazási tűzfalakat, a hálózati tűzfalakat, a kártevő szoftvereket, a behatolás észlelését, a megelőzési rendszereket (AZONOSÍTÓk/IP-címek) és egyebeket. További információkért tekintse [meg az adatok és eszközök védelme és a globális biztonsági szabványok betartása](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)című témakört.

A Microsoft folyamatosan figyeli a kiszolgálókat, a hálózatokat és az alkalmazásokat a fenyegetések észlelése érdekében. Az Azure többtényezős fenyegetés-kezelési megközelítése a behatolás észlelését, az elosztott szolgáltatásmegtagadási (DDoS) támadások megelőzését, a behatolások tesztelését, a viselkedés elemzését, a anomáliák észlelését és a gépi tanulást használja, hogy folyamatosan erősítse a védelmet, és csökkentse a kockázatokat. Az Azure-hoz készült Microsoft antimalware védi az Azure Cloud Services és a virtuális gépeket. Lehetősége van harmadik féltől származó biztonsági megoldások üzembe helyezésére is, például a webalkalmazási tűzoltó falakra, a hálózati tűzfalakra, az antimalware-ra, a behatolás-észlelésre és a megelőzési rendszerekre (AZONOSÍTÓk/IP-címek) és egyebekre.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>A HTTP/2 engedélyezése Cloud Services virtuális gépen

A Windows 10 és a Windows Server 2016 támogatja a HTTP/2-t mind az ügyfél, mind a kiszolgáló oldalon. Ha az ügyfél (böngésző) a TLS protokollon keresztül csatlakozik az IIS-kiszolgálóhoz, amely TLS-bővítményekkel egyeztet a HTTP/2 protokollon keresztül, akkor nem kell módosítania a kiszolgálót. Ennek az az oka, hogy a TLS-n keresztül a HTTP/2 használatát megadó H2-14 fejléc alapértelmezés szerint elküldésre kerül. Ha viszont az ügyfél egy frissítési fejlécet küld a HTTP/2 verzióra való frissítéshez, akkor az alábbi módosítást kell végrehajtania a kiszolgáló oldalon, hogy a frissítés működőképes legyen, és egy HTTP/2-alapú kapcsolatban legyen. 

1. regedit.exe futtatása.
2. Keresse meg a következő beállításkulcsot: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Hozzon létre egy új, **DuoEnabled** nevű DWORD értéket.
4. Állítsa az értékét 1-re.
5. Indítsa újra a kiszolgálót.
6. Nyissa meg az **alapértelmezett** webhelyet, és a **kötések** területen hozzon létre egy új TLS-kötést az imént létrehozott önaláírt tanúsítvánnyal. 

További információkért lásd:

- [HTTP/2 az IIS-kiszolgálón](https://blogs.iis.net/davidso/http2)
- [Videó: HTTP/2 a Windows 10-es verzióban: böngésző, alkalmazások és webkiszolgáló](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Ezek a lépések egy indítási feladaton keresztül automatizálható, így amikor új Pásti-példányt hoz létre, akkor a fenti módosításokat a beállításjegyzékben végezheti el. További információ: a [felhőalapú szolgáltatás indítási feladatainak konfigurálása és futtatása](cloud-services-startup-tasks.md).

 
Ha ez megtörtént, ellenőrizheti, hogy a HTTP/2 engedélyezve van-e, vagy sem a következő módszerek egyikének használatával:

- Engedélyezze a protokoll verzióját az IIS-naplókban, és tekintse meg az IIS-naplókat. A naplókban megjelenik a HTTP/2. 
- Engedélyezze az F12 fejlesztői eszközt az Internet Explorerben vagy a Microsoft Edge-ben, és váltson a hálózat lapra a protokoll ellenőrzéséhez. 

További információ: [http/2 az IIS](https://blogs.iis.net/davidso/http2)-ben.

## <a name="permissions"></a>Engedélyek

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Hogyan valósítható meg a Cloud Services szerepköralapú hozzáférés?
A Cloud Services nem támogatja az Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) modelljét, mert nem Azure Resource Manager-alapú szolgáltatás.

Lásd: [Az Azure különböző szerepköreinek megismerése](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Távoli asztal

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Jogosult a Microsoft belső mérnökök a Távoli asztalról a Cloud Service-példányokra engedély nélkül?
A Microsoft szigorú eljárást követ, amely nem teszi lehetővé a belső mérnökök számára, hogy írásos engedély (e-mail-cím vagy más írásos kommunikáció) nélkül ne engedélyezzék a Távoli asztalt a felhőalapú szolgáltatásban.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Az RDP-fájllal nem lehet távoli asztalt használni a Cloud Service virtuális géphez. A következő hibaüzenet jelenik meg: hitelesítési hiba történt (kód: 0x80004005)

Ez a hiba akkor fordulhat elő, ha az RDP-fájlt egy Azure Active Directoryhoz csatlakoztatott gépről használja. A probléma megoldásához kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a letöltött RDP-fájlra, majd válassza a **Szerkesztés** lehetőséget.
2. Adja hozzá a "&#92;" előtagot a Felhasználónév előtt. Használja például a **.\username**  **nevet a Felhasználónév** helyett.

## <a name="scaling"></a>Méretezés

### <a name="i-cannot-scale-beyond-x-instances"></a>X példányokon túl nem tudom méretezni
Az Azure-előfizetése korlátozza a használható magok számát. Ha már használta az összes elérhető magot, a skálázás nem fog működni. Ha például 100 mag van, ez azt jelenti, hogy 100 a1 méretű virtuálisgép-példányok lehetnek a Cloud Service-hez vagy a 50 a2 méretű virtuálisgép-példányokhoz.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Hogyan állíthatom be az automatikus méretezést a memória metrikái alapján?

A Cloud Serviceshoz tartozó memória-metrikák alapján történő automatikus méretezés jelenleg nem támogatott. 

A probléma megkerüléséhez használhatja a Application Insights. Az automatikus méretezés támogatja a Application Insights metrikák forrásaként, és a szerepkör-példányok számának méretezése a vendég metrikája alapján (például "memória").  Be kell állítania Application Insights a Cloud Service Project csomagfájl (*. cspkg) fájljában, és engedélyeznie kell Azure Diagnostics bővítményt a szolgáltatáson a feat megvalósításához.

Ha további információt szeretne arról, hogyan használhatja az egyéni metrikákat a Application Insights segítségével az automatikus skálázás konfigurálásához Cloud Serviceson, tekintse meg az [Azure-beli egyéni metrika automatikus méretezésének első lépéseit](../azure-monitor/autoscale/autoscale-custom-metric.md) ismertető témakört.

A Azure Diagnostics és a Cloud Services Application Insights integrálásával kapcsolatos további információkért lásd: [felhőalapú szolgáltatás, virtuális gép vagy Service Fabric diagnosztikai adatok küldése Application Insights](../azure-monitor/agents/diagnostics-extension-to-application-insights.md)

További információ a Cloud Services Application Insightsének engedélyezéséről: [Application Insights for Azure Cloud Services](../azure-monitor/app/cloudservices.md)

További információ a Cloud Services Azure Diagnostics naplózásának engedélyezéséről: [diagnosztika beállítása az Azure Cloud Services és a Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them) szolgáltatáshoz

## <a name="generic"></a>Általános

### <a name="how-do-i-add-nosniff-to-my-website"></a>Hogyan Hozzáadás `nosniff` a saját webhelyhez?
Ha meg szeretné akadályozni, hogy az ügyfelek a MIME-típusokat felszippantsák, adjon hozzá egy beállítást a *web.config* fájlban.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Ezt lehetőségként is hozzáadhatja az IIS-ben. Használja az alábbi parancsot a [Common Startup Tasks](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) cikkben.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Az IIS webes szerepkörhöz való Hogyan testreszabása
Használja az IIS indítási parancsfájlt a [gyakori indítási feladatok](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) cikkből.

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Mi a felhőalapú szolgáltatás kvótájának korlátja?
Lásd a [szolgáltatásra vonatkozó korlátozásokat](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Miért jelenik meg a Cloud Service-beli virtuális gép meghajtója nagyon kevés szabad lemezterülettel?
Ez a várt viselkedés, és nem okoz problémát az alkalmazásában. A naplózás be van kapcsolva a (z)% AppRoot% meghajtóhoz az Azure Pásti virtuális gépeken, ami lényegében a fájlok szokásosan felhasználható területének kétszeres mennyiségét használja fel. Azonban több dolgot is figyelembe kell vennie, amelyek lényegében ezt nem jelentik be.

A (z)% AppRoot% meghajtó méretének kiszámításához <méretet kell kiszámítani. a cspkg + maximális napló mérete + a szabad terület> vagy 1,5 GB, amelyik nagyobb. A virtuális gép mérete nem befolyásolja ezt a számítást. (A virtuális gép mérete csak az ideiglenes C: meghajtó méretére vonatkozik.) 

A (z)% AppRoot% meghajtóra való írás nem támogatott. Ha az Azure-beli virtuális gépre ír, ezt egy ideiglenes LocalStorage-erőforrásban (vagy más megoldásban, például blob Storage, Azure Files stb.) kell végrehajtania. Így a (z)% AppRoot% mappában lévő szabad terület mennyisége nem értelmezhető. Ha nem biztos abban, hogy az alkalmazás a (z)% AppRoot% meghajtóra ír, bármikor lefuttathatja a szolgáltatást néhány napig, majd összehasonlíthatja a "Before" és a "After" méretet. 

Az Azure nem fog semmit írni a (z)% AppRoot% meghajtóra. Miután létrehozta a virtuális merevlemezt `.cspkg` , és az Azure-beli virtuális gépre csatlakoztatta, az egyetlen dolog, ami a meghajtóra írható az alkalmazás. 

A napló beállításai nem konfigurálhatók, így nem kapcsolhatja ki.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Hogyan adhatok hozzá egy antimalware-bővítményt a Cloud Serviceshoz automatizált módon?

Az indítási feladatban a PowerShell-parancsfájl használatával engedélyezheti az antimalware-bővítményt. Az alábbi cikkekben ismertetett lépéseket követve implementálhatja azt: 
 
- [PowerShell indítási feladat létrehozása](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](/powershell/module/servicemanagement/azure.service/Set-AzureServiceAntimalwareExtension)

A kártevő szoftverek üzembe helyezésével kapcsolatos forgatókönyvekről és a portálról történő engedélyezéséről a [kártevők elleni telepítési forgatókönyvek](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)című témakörben olvashat bővebben.

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Hogyan engedélyezhető a Kiszolgálónév jelzése (SNI) a Cloud Serviceshoz?

A SNI a következő módszerek egyikével engedélyezheti Cloud Servicesban:

**1. módszer: a PowerShell használata**

A SNI kötést a következő PowerShell **-** parancsmaggal konfigurálhatja egy felhőalapú szolgáltatási szerepkör-példány indítási feladatában:

```powershell
New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags
```

Az [itt](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee790567(v=technet.10))leírtak szerint a $sslFlags az alábbi értékek egyike lehet:

|Érték|Értelmezés|
------|------
|0|Nincs SNI|
|1|SNI engedélyezve|
|2|A központi tanúsítványtárolót használó nem SNI kötés|
|3|Központi tanúsítványtárolót használó SNI kötés|
 
**2. módszer: kód használata**

Az SNI-kötés a következő [blogbejegyzésben](/archive/blogs/jianwu/expose-ssl-service-to-multi-domains-from-the-same-cloud-service)leírtak szerint is konfigurálható kód használatával a szerepkör indításakor:

```csharp
//<code snip> 
                var serverManager = new ServerManager(); 
                var site = serverManager.Sites[0]; 
                var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                serverManager.CommitChanges(); 
    //</code snip>
```
    
A fenti módszerek bármelyikének használatával az adott állomásnevek megfelelő tanúsítványait (*. pfx) először telepíteni kell a szerepkör példányain indítási feladattal vagy programkódon keresztül, hogy a SNI-kötés érvénybe kerüljön.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Hogyan adhatok hozzá címkéket az Azure Cloud Service-hez? 

A Cloud Service egy klasszikus erőforrás. Csak Azure Resource Manager támogatási címkékkel létrehozott erőforrások. Nem alkalmazhat címkéket olyan klasszikus erőforrásokra, mint a Cloud Service. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>A Azure Portal nem jeleníti meg a felhőalapú szolgáltatás SDK-verzióját. Hogyan szerezhetem be?

Dolgozunk azon, hogy ezt a funkciót a Azure Portal. Eközben a következő PowerShell-parancsokkal kérheti le az SDK verzióját:

```powershell
Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot
```

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Több hónapig szeretnék leállítani a Cloud Service-t. Hogyan csökkentheti a Cloud Service számlázási költségeit az IP-cím elvesztése nélkül?

Egy már üzembe helyezett felhőalapú szolgáltatás számlázása a felhasznált számítási és tárolási kapacitást terheli. Így még ha leállítja az Azure-beli virtuális gépet, akkor is számlázni fogja a tárterületet. 

A szolgáltatáshoz tartozó IP-cím elvesztése nélkül elvégezhető a számlázás csökkentése:

1. A központi telepítések törlése előtt [foglalja le az IP-címet](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) .  Ezt az IP-címet csak a számlázás után számítjuk fel. Az IP-cím számlázásával kapcsolatos további információkért lásd: az [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Törölje az üzemelő példányokat. Ne törölje a xxx.cloudapp.net, hogy később is használhassa.
3. Ha a Cloud Service-t ugyanazzal a tartalék IP-címmel szeretné telepíteni, amelyet az előfizetésében foglalt le, tekintse meg a [Cloud Services és a Virtual Machines fenntartott IP címei](https://azure.microsoft.com/blog/reserved-ip-addresses/)című témakört.