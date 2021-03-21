---
title: Helyszíni tűzfal és proxybeállítások Azure File Sync | Microsoft Docs
description: A helyszíni proxy-és tűzfalbeállítások Azure File Sync megismerése. Tekintse át a portok, hálózatok és az Azure-hoz való speciális kapcsolatok konfigurációs adatait.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0dbe7f32f14eb4da3d591811d619eb2e9bea397
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729640"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Az Azure File Sync proxy- és tűzfalbeállításai
Azure File Sync összekapcsolja a helyszíni kiszolgálókat a Azure Fileshoz, és lehetővé teszi a többhelyes szinkronizálást és a felhőalapú rétegbeli funkciókat. Ennek megfelelően a helyszíni kiszolgálónak csatlakoznia kell az internethez. A rendszergazdának el kell döntenie, hogy melyik a legjobb elérési út ahhoz, hogy a kiszolgáló elérje az Azure Cloud Services szolgáltatást.

Ez a cikk betekintést nyújt a-kiszolgáló sikeres és biztonságos összekapcsolásához szükséges konkrét követelményekkel és lehetőségekkel Azure File Synchoz.

Javasoljuk, hogy olvassa el a [Azure file Sync hálózati megfontolásokat](storage-sync-files-networking-overview.md) az útmutató elolvasása előtt.

## <a name="overview"></a>Áttekintés
Azure File Sync a Windows-kiszolgáló, az Azure-fájlmegosztás és számos más Azure-szolgáltatás között összehangoló szolgáltatásként működik, hogy szinkronizálja az adatait a szinkronizálási csoportban leírtak szerint. Ahhoz, hogy a Azure File Sync megfelelően működjön, konfigurálnia kell a kiszolgálókat a következő Azure-szolgáltatásokkal való kommunikációhoz:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Hitelesítési szolgáltatások

> [!Note]  
> A Windows Serveren futó Azure File Sync ügynök minden olyan kérést kezdeményez a Cloud Serviceshez, amely csak a tűzfal perspektívából érkező kimenő forgalmat veszi figyelembe. <br /> Egyetlen Azure-szolgáltatás sem kezdeményez kapcsolódást a Azure File Sync ügynökhöz.

## <a name="ports"></a>Portok
Azure File Sync a fájl-és metaadatokat kizárólag HTTPS-en keresztül helyezi át, és az 443-es portot kell megnyitni kimenőként.
Ennek eredményeképpen minden forgalom titkosítva van.

## <a name="networks-and-special-connections-to-azure"></a>Hálózatok és speciális kapcsolódás az Azure-hoz
A Azure File Sync ügynökhöz nem tartoznak követelmények olyan speciális csatornákra vonatkozóan, mint a [ExpressRoute](../../expressroute/expressroute-introduction.md), stb.

Azure File Sync az Azure-ba való belépést lehetővé tevő bármely eszközön elérhetővé válik, és automatikusan alkalmazkodik a különböző hálózati jellemzőkre, például a sávszélességre, a késésre, valamint a felügyelet vezérlésére a finomhangoláshoz. Jelenleg nem minden szolgáltatás érhető el. Ha konkrét viselkedést szeretne beállítani, tudassa velünk [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670)keresztül.

## <a name="proxy"></a>Proxy
Azure File Sync támogatja az alkalmazás-specifikus és a számítógép-szintű proxybeállításokat.

Az **alkalmazásspecifikus proxybeállítások** lehetővé teszik a proxy konfigurációját kifejezetten Azure file Sync forgalom számára. Az alkalmazásspecifikus proxybeállítások az ügynök 4.0.1.0 vagy újabb verziója esetén támogatottak, és az ügynök telepítése vagy az Set-StorageSyncProxyConfiguration PowerShell-parancsmag használatával konfigurálhatók.

alkalmazásspecifikus proxybeállítások konfigurálására alkalmas PowerShell-parancsok használatával konfigurálhatók:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Ha például a proxykiszolgáló felhasználónevet és jelszót igényel a hitelesítéshez, futtassa a következő PowerShell-parancsokat:

```powershell
# IP address or name of the proxy server.
$Address="127.0.0.1"  

# The port to use for the connection to the proxy.
$Port=8080

# The user name for a proxy.
$UserName="user_name" 

# Please type or paste a string with a password for the proxy.
$SecurePassword = Read-Host -AsSecureString

$Creds = New-Object System.Management.Automation.PSCredential ($UserName, $SecurePassword)

# Please verify that you have entered the password correctly.
Write-Host $Creds.GetNetworkCredential().Password

Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"

Set-StorageSyncProxyConfiguration -Address $Address -Port $Port -ProxyCredential $Creds
```
A **számítógép-szintű proxybeállítások** transzparensek a Azure file Sync ügynök számára, mivel a kiszolgáló teljes forgalma a proxyn keresztül irányítható.

A számítógépre vonatkozó proxybeállítások konfigurálásához kövesse az alábbi lépéseket: 

1. Proxybeállítások konfigurálása .NET-alkalmazásokhoz 

   - Szerkessze ezt a két fájlt:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Adja hozzá a machine.config Files <system.net> szakaszt (az <System. serviceModel> szakaszban).  Módosítsa a 127.0.01:8888 a proxykiszolgáló IP-címére és portjára. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. A WinHTTP proxy beállításainak megadása 

   - Futtassa a következő parancsot egy rendszergazda jogú parancssorból vagy a PowerShellből a meglévő proxy beállítás megtekintéséhez:   

     netsh WinHTTP show proxy

   - Futtassa a következő parancsot egy rendszergazda jogú parancssorból vagy a PowerShellből a proxybeállítások beállításához (módosítsa a 127.0.01:8888 a proxykiszolgáló IP-címére és portjára):  

     netsh WinHTTP set proxy 127.0.0.1:8888

3. Indítsa újra a Storage Sync Agent szolgáltatást úgy, hogy futtatja a következő parancsot egy rendszergazda jogú parancssorból vagy a PowerShellből: 

      net stop filesyncsvc

      Megjegyzés: a Storage Sync Agent (filesyncsvc) szolgáltatás a leállítást követően automatikusan elindul.

## <a name="firewall"></a>Firewall
Ahogy az előző szakaszban is említettük, a 443-es portot kell megnyitni. Az adatközpontban, ágban vagy régióban lévő szabályzatok alapján szükség lehet arra, hogy a porton keresztüli adatforgalmat bizonyos tartományokra szűkítse.

A következő táblázat ismerteti a szükséges tartományokat a kommunikációhoz:

| Szolgáltatás | Nyilvános Felhőbeli végpont | Azure Government végpont | Használat |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Bármely felhasználói hívás (például a PowerShell) az ezen az URL-címen halad át, beleértve a kezdeti kiszolgáló regisztrációs hívását is. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | A Azure Resource Manager hívásokat hitelesített felhasználó kell elvégezni. A sikeres végrehajtáshoz ezt az URL-címet használja a rendszer a felhasználói hitelesítéshez. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | A Azure File Sync üzembe helyezésének részeként létrejön egy egyszerű szolgáltatásnév az előfizetés Azure Active Directory. Ezt az URL-címet használja a rendszer. Ez a rendszerbiztonsági tag a Azure File Sync szolgáltatáshoz minimálisan szükséges jogok delegálására szolgál. Azure File Sync kezdeti beállítását végző felhasználónak hitelesített felhasználónak kell lennie az előfizetés tulajdonosának jogosultságokkal. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Használja a nyilvános végpont URL-címét. | Ezt az URL-címet a Active Directory hitelesítési függvénytár éri el, amelyet a Azure File Sync Server-regisztráció felhasználói felülete használ a rendszergazda bejelentkezésekor. |
| **Azure Storage** | &ast;. core.windows.net | &ast;. core.usgovcloudapi.net | Amikor a kiszolgáló letölt egy fájlt, a kiszolgáló hatékonyabban hajtja végre az adatáthelyezést, amikor közvetlenül a Storage-fiókban lévő Azure-fájlmegosztás felé folytatja a kommunikációt. A kiszolgálónak van egy SAS-kulcsa, amely csak a célként megadott fájlmegosztás elérését teszi lehetővé. |
| **Azure File Sync** | &ast;. one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | A kiszolgáló kezdeti regisztrációja után a kiszolgáló megkapja az adott régióban található Azure File Sync szolgáltatási példány regionális URL-címét. A kiszolgáló az URL-cím használatával közvetlenül és hatékonyan tud kommunikálni a szinkronizálást kezelő példánnyal. |
| **Microsoft PKI** |  https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | Miután telepítette a Azure File Sync ügynököt, a PKI URL-címével letöltheti a Azure File Sync szolgáltatással és az Azure-fájlmegosztás használatával folytatott kommunikációhoz szükséges köztes tanúsítványokat. Az OCSP URL-cím segítségével ellenőrizhető a tanúsítvány állapota. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;. ctldl.windowsupdate.com<br>&ast;. dl.delivery.mp.microsoft.com<br>&ast;. emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;. ctldl.windowsupdate.com<br>&ast;. dl.delivery.mp.microsoft.com<br>&ast;. emdl.ws.microsoft.com | A Azure File Sync ügynök telepítése után a rendszer a Microsoft Update URL-címek használatával tölti le Azure File Sync ügynök frissítéseit. |

> [!Important]
> Ha engedélyezi a forgalmat a &ast; . AFS.Azure.net, a forgalom csak a szinkronizálási szolgáltatás számára lehetséges. Nincsenek más Microsoft-szolgáltatások, amelyek ezt a tartományt használják.
> Ha a. one.microsoft.com-re irányuló forgalom lehetővé teszi, a- &ast; kiszolgálóról érkező forgalom több, mint a szinkronizálási szolgáltatásra van lehetséges. Számos további Microsoft-szolgáltatás érhető el az altartományok alatt.

Ha &ast; a. AFS.Azure.net vagy &ast; a. One.microsoft.com túl széles, korlátozhatja a kiszolgáló kommunikációját azáltal, hogy lehetővé teszi a kommunikációt az Azure Files Sync szolgáltatás explicit regionális példányaira. A kiválasztható példány (ok) a Storage Sync szolgáltatás azon régiójától függ, amelyet üzembe helyezett, és amelybe regisztrálta a kiszolgálót. Ezt a régiót az alábbi táblázatban "elsődleges végpont URL-ként" nevezzük.

Az üzletmenet folytonossága és a vész-helyreállítás (BCDR) miatt előfordulhat, hogy az Azure-fájlmegosztást globálisan redundáns (GRS) Storage-fiókban adta meg. Ha ez az eset áll fenn, akkor az Azure-fájlmegosztás egy tartós regionális kimaradás esetén átveszi a feladatokat a párosított régióba. Azure File Sync ugyanazokat a regionális párosításokat használja, mint a Storage. Így ha GRS-fiókokat használ, engedélyeznie kell a további URL-címeket, hogy a kiszolgáló a párosított régióval beszéljen a Azure File Sync. Az alábbi táblázat ezt a "párosított régiót" hívja meg. Ezen kívül egy Traffic Manager-profil URL-címét is engedélyezni kell. Ez biztosítja, hogy a hálózati forgalom zökkenőmentesen átirányítható legyen a párosított régióba feladatátvétel esetén, és az alábbi táblázatban "felderítési URL-cím" néven jelenik meg.

| Felhőbeli  | Region | Elsődleges végpont URL-címe | Párosított régió | Felderítési URL-cím |
|--------|--------|----------------------|---------------|---------------|
| Nyilvános |Kelet-Ausztrália | https: \/ /australiaeast01.AFS.Azure.net<br>https: \/ /Kailani-Aue.One.microsoft.com | Délkelet-Ausztrália | https: \/ /TM-australiaeast01.AFS.Azure.net<br>https: \/ /TM-Kailani-Aue.One.microsoft.com |
| Nyilvános |Délkelet-Ausztrália | https: \/ /australiasoutheast01.AFS.Azure.net<br>https: \/ /Kailani-AUS.One.microsoft.com | Kelet-Ausztrália | https: \/ /TM-australiasoutheast01.AFS.Azure.net<br>https: \/ /TM-Kailani-AUS.One.microsoft.com |
| Nyilvános | Dél-Brazília | https: \/ /brazilsouth01.AFS.Azure.net | USA déli középső régiója | https: \/ /TM-brazilsouth01.AFS.Azure.net |
| Nyilvános | Közép-Kanada | https: \/ /canadacentral01.AFS.Azure.net<br>https: \/ /Kailani-CAC.One.microsoft.com | Kelet-Kanada | https: \/ /TM-canadacentral01.AFS.Azure.net<br>https: \/ /TM-Kailani-CAC.One.microsoft.com |
| Nyilvános | Kelet-Kanada | https: \/ /canadaeast01.AFS.Azure.net<br>https: \/ /Kailani-CAE.One.microsoft.com | Közép-Kanada | https: \/ /TM-canadaeast01.AFS.Azure.net<br>https: \/ /TM-Kailani.CAE.One.microsoft.com |
| Nyilvános | Közép-India | https: \/ /centralindia01.AFS.Azure.net<br>https: \/ /Kailani-cin.One.microsoft.com | Dél-India | https: \/ /TM-centralindia01.AFS.Azure.net<br>https: \/ /TM-Kailani-cin.One.microsoft.com |
| Nyilvános | Az USA középső régiója | https: \/ /centralus01.AFS.Azure.net<br>https: \/ /Kailani-cus.One.microsoft.com | USA 2. keleti régiója | https: \/ /TM-centralus01.AFS.Azure.net<br>https: \/ /TM-Kailani-cus.One.microsoft.com |
| Nyilvános | Kelet-Ázsia | https: \/ /eastasia01.AFS.Azure.net<br>https: \/ /kailani11.One.microsoft.com | Délkelet-Ázsia | https: \/ /TM-eastasia01.AFS.Azure.net<br>https: \/ /TM-kailani11.One.microsoft.com |
| Nyilvános | USA keleti régiója | https: \/ /eastus01.AFS.Azure.net<br>https: \/ /kailani1.One.microsoft.com | USA nyugati régiója | https: \/ /TM-eastus01.AFS.Azure.net<br>https: \/ /TM-kailani1.One.microsoft.com |
| Nyilvános | USA 2. keleti régiója | https: \/ /eastus201.AFS.Azure.net<br>https: \/ /Kailani-ESS.One.microsoft.com | Az USA középső régiója | https: \/ /TM-eastus201.AFS.Azure.net<br>https: \/ /TM-Kailani-ESS.One.microsoft.com |
| Nyilvános | Észak-Németország | https: \/ /germanynorth01.AFS.Azure.net | Középnyugat-Németország | https: \/ /TM-germanywestcentral01.AFS.Azure.net |
| Nyilvános | Középnyugat-Németország | https: \/ /germanywestcentral01.AFS.Azure.net | Észak-Németország | https: \/ /TM-germanynorth01.AFS.Azure.net |
| Nyilvános | Kelet-Japán | https: \/ /japaneast01.AFS.Azure.net | Nyugat-Japán | https: \/ /TM-japaneast01.AFS.Azure.net |
| Nyilvános | Nyugat-Japán | https: \/ /japanwest01.AFS.Azure.net | Kelet-Japán | https: \/ /TM-japanwest01.AFS.Azure.net |
| Nyilvános | Dél-Korea középső régiója | https: \/ /koreacentral01.AFS.Azure.net/ | Dél-Korea déli régiója | https: \/ /TM-koreacentral01.AFS.Azure.net/ |
| Nyilvános | Dél-Korea déli régiója | https: \/ /koreasouth01.AFS.Azure.net/ | Dél-Korea középső régiója | https: \/ /TM-koreasouth01.AFS.Azure.net/ |
| Nyilvános | USA északi középső régiója | https: \/ /northcentralus01.AFS.Azure.net | USA déli középső régiója | https: \/ /TM-northcentralus01.AFS.Azure.net |
| Nyilvános | Észak-Európa | https: \/ /northeurope01.AFS.Azure.net<br>https: \/ /kailani7.One.microsoft.com | Nyugat-Európa | https: \/ /TM-northeurope01.AFS.Azure.net<br>https: \/ /TM-kailani7.One.microsoft.com |
| Nyilvános | USA déli középső régiója | https: \/ /southcentralus01.AFS.Azure.net | USA északi középső régiója | https: \/ /TM-southcentralus01.AFS.Azure.net |
| Nyilvános | Dél-India | https: \/ /southindia01.AFS.Azure.net<br>https: \/ /Kailani-Sin.One.microsoft.com | Közép-India | https: \/ /TM-southindia01.AFS.Azure.net<br>https: \/ /TM-Kailani-Sin.One.microsoft.com |
| Nyilvános | Délkelet-Ázsia | https: \/ /southeastasia01.AFS.Azure.net<br>https: \/ /kailani10.One.microsoft.com | Kelet-Ázsia | https: \/ /TM-southeastasia01.AFS.Azure.net<br>https: \/ /TM-kailani10.One.microsoft.com |
| Nyilvános | Észak-Svájc | https: \/ /switzerlandnorth01.AFS.Azure.net<br>https: \/ /TM-switzerlandnorth01.AFS.Azure.net | Nyugat-Svájc | https: \/ /switzerlandwest01.AFS.Azure.net<br>https: \/ /TM-switzerlandwest01.AFS.Azure.net |
| Nyilvános | Nyugat-Svájc | https: \/ /switzerlandwest01.AFS.Azure.net<br>https: \/ /TM-switzerlandwest01.AFS.Azure.net | Észak-Svájc | https: \/ /switzerlandnorth01.AFS.Azure.net<br>https: \/ /TM-switzerlandnorth01.AFS.Azure.net |
| Nyilvános | Az Egyesült Királyság déli régiója | https: \/ /uksouth01.AFS.Azure.net<br>https: \/ /Kailani-UKs.One.microsoft.com | Az Egyesült Királyság nyugati régiója | https: \/ /TM-uksouth01.AFS.Azure.net<br>https: \/ /TM-Kailani-UKs.One.microsoft.com |
| Nyilvános | Az Egyesült Királyság nyugati régiója | https: \/ /ukwest01.AFS.Azure.net<br>https: \/ /Kailani-Ukw.One.microsoft.com | Az Egyesült Királyság déli régiója | https: \/ /TM-ukwest01.AFS.Azure.net<br>https: \/ /TM-Kailani-Ukw.One.microsoft.com |
| Nyilvános | USA nyugati középső régiója | https: \/ /westcentralus01.AFS.Azure.net | USA 2. nyugati régiója | https: \/ /TM-westcentralus01.AFS.Azure.net |
| Nyilvános | Nyugat-Európa | https: \/ /westeurope01.AFS.Azure.net<br>https: \/ /kailani6.One.microsoft.com | Észak-Európa | https: \/ /TM-westeurope01.AFS.Azure.net<br>https: \/ /TM-kailani6.One.microsoft.com |
| Nyilvános | USA nyugati régiója | https: \/ /westus01.AFS.Azure.net<br>https: \/ /Kailani.One.microsoft.com | USA keleti régiója | https: \/ /TM-westus01.AFS.Azure.net<br>https: \/ /TM-Kailani.One.microsoft.com |
| Nyilvános | USA 2. nyugati régiója | https: \/ /westus201.AFS.Azure.net | USA nyugati középső régiója | https: \/ /TM-westus201.AFS.Azure.net |
| Államigazgatás | USA-beli államigazgatás – Arizona | https: \/ /usgovarizona01.AFS.Azure.us | USA-beli államigazgatás – Texas | https: \/ /TM-usgovarizona01.AFS.Azure.us |
| Államigazgatás | USA-beli államigazgatás – Texas | https: \/ /usgovtexas01.AFS.Azure.us | USA-beli államigazgatás – Arizona | https: \/ /TM-usgovtexas01.AFS.Azure.us |

- Ha a helyileg redundáns (LRS) vagy a Zone redundáns (ZRS) Storage-fiókokat használja, csak engedélyeznie kell az "elsődleges végpont URL-címe" alatt felsorolt URL-címet.

- Ha globálisan redundáns (GRS) tárolási fiókokat használ, engedélyezze a három URL-címet.

**Példa:** Üzembe helyezi a Storage Sync szolgáltatást a-ben, `"West US"` és regisztrálja azt a kiszolgálóval. Az URL-címek, amelyek lehetővé teszik, hogy a kiszolgáló kommunikáljon a következő esetekben:

> - https: \/ /westus01.AFS.Azure.net (elsődleges végpont: USA nyugati régiója)
> - https: \/ /eastus01.AFS.Azure.net (párosított feladatátvételi régió: USA keleti régiója)
> - https: \/ /TM-westus01.AFS.Azure.net (az elsődleges régió felderítési URL-címe)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Azure File Sync IP-címek engedélyezési listájának engedélyezése
Azure File Sync támogatja a szolgáltatás- [címkék](../../virtual-network/service-tags-overview.md)használatát, amely az adott Azure-szolgáltatáshoz tartozó IP-cím előtagjainak egy csoportját jelöli. A szolgáltatás-címkék használatával olyan tűzfalszabályok hozhatók létre, amelyek lehetővé teszik a Azure File Sync szolgáltatással folytatott kommunikációt. Azure File Sync a szolgáltatás címkéje `StorageSyncService` .

Ha az Azure-ban Azure File Synct használ, a hálózati biztonsági csoportban található szolgáltatás neve közvetlenül a forgalom engedélyezéséhez használható. Ha többet szeretne megtudni Ennek módjáról, tekintse meg a [hálózati biztonsági csoportok](../../virtual-network/network-security-groups-overview.md)című témakört.

Ha helyszíni Azure File Sync használ, a Service tag API-val adott IP-címtartományok beolvasása a tűzfal engedélyezési listájához. A következő két módszer használható az adatok lekéréséhez:

- A szolgáltatási címkéket támogató összes Azure-szolgáltatáshoz tartozó IP-címtartományok aktuális listája a Microsoft letöltőközpontban, egy JSON-dokumentum formájában, hetente van közzétéve. Mindegyik Azure-felhő saját JSON-dokumentummal rendelkezik, amely a felhőhöz kapcsolódó IP-címtartományt tartalmazza:
    - [Nyilvános Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
- A Service tag Discovery API (előzetes verzió) lehetővé teszi az aktuális szolgáltatási címkék programozott lekérését. Az előzetes verzióban a Service tag Discovery API a Microsoft letöltőközpontból közzétett JSON-dokumentumokból származó információknál kevésbé naprakész adatokat adhat vissza. Az API felületét az automatizálási beállítások alapján használhatja:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az-network-list-service-tags)

Mivel a Service tag felderítési API-ját a Microsoft letöltőközpontban közzétett JSON-dokumentumok nem frissítik, a JSON-dokumentum használatával a helyszíni tűzfal engedélyezési listájának frissítését javasoljuk. Ezt a következőképpen teheti meg:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

Ezután az IP-címtartományok használatával `$ipAddressRanges` frissítheti a tűzfalat. A tűzfal vagy a hálózati készülék webhelyének frissítésével kapcsolatos információkért olvassa el a tűzfal frissítését ismertető témakört.

## <a name="test-network-connectivity-to-service-endpoints"></a>Hálózati kapcsolat tesztelése a szolgáltatási végpontokhoz
Ha egy kiszolgáló regisztrálva van a Azure File Sync szolgáltatásban, a Test-StorageSyncNetworkConnectivity-parancsmag és a ServerRegistration.exe használható a kiszolgálóra jellemző összes végponttal (URL-címmel) folytatott kommunikáció tesztelésére. Ez a parancsmag segít elhárítani a hiányos kommunikációt, ami megakadályozza, hogy a kiszolgáló teljes mértékben működjön a Azure File Sync, és használható a proxy és a tűzfal konfigurációjának finomhangolására.

A hálózati kapcsolat teszt futtatásához telepítse a Azure File Sync Agent 9,1-es vagy újabb verzióját, és futtassa a következő PowerShell-parancsokat:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Összefoglalás és kockázati korlátozás
A dokumentum korábbi listája tartalmazza azokat az URL-címeket, Azure File Sync jelenleg kommunikál. A tűzfalaknak képesnek kell lenniük a kimenő forgalom engedélyezésére ezen tartományokban. A Microsoft igyekszik megőrizni a listát.

A tartomány korlátozására vonatkozó tűzfalszabályok beállítása lehet egy mérték a biztonság növelése érdekében. Ha ezeket a tűzfal-konfigurációkat használja, az egyiknek figyelembe kell vennie, hogy az URL-címek fel lesznek véve, és akár idővel is változhatnak. Ebben a cikkben rendszeresen tájékozódhat.

## <a name="next-steps"></a>Következő lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
