---
title: Azure File Sync tűzfal- és proxybeállítások konfigurálása | Microsoft Docs
description: A Azure File Sync proxy- és tűzfalbeállításokat. Tekintse át a portok, hálózatok és speciális Azure-kapcsolatok konfigurációjának részleteit.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b2c77c20bfb6fff60f2242d1ac2dad7b3fc9f6fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796896"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Az Azure File Sync proxy- és tűzfalbeállításai
Azure File Sync csatlakoztatja a helyszíni kiszolgálókat a Azure Files, lehetővé téve a többhelyű szinkronizálást és a felhőbeli rétegezést. Ezért a helyszíni kiszolgálónak az internethez kell csatlakozva lennie. A rendszergazdának el kell döntenie, hogy a kiszolgáló melyik útvonalon tudja elérni az Azure-felhőszolgáltatásokat.

Ez a cikk betekintő követelményeket és lehetőségeket tartalmaz, amelyek segítségével sikeresen és biztonságosan csatlakoztathatja a kiszolgálót a Azure File Sync.

Javasoljuk, [Azure File Sync](file-sync-networking-overview.md) útmutató elolvasása előtt olvassa el a hálózati szempontokat.

## <a name="overview"></a>Áttekintés
Azure File Sync szolgáltatásként működik a Windows Server, az Azure-fájlmegosztás és számos más Azure-szolgáltatás között az adatok szinkronizálása érdekében a szinkronizálási csoportban leírtak szerint. Ahhoz Azure File Sync megfelelően működjön, konfigurálnia kell a kiszolgálókat, hogy kommunikáljanak a következő Azure-szolgáltatásokkal:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Hitelesítési szolgáltatások

> [!Note]  
> A Azure File Sync Windows Server-ügynök minden kérést kezdeményez a felhőszolgáltatások felé, így csak tűzfal szempontjából kell figyelembe vennie a kimenő forgalmat. <br /> Egy Azure-szolgáltatás sem kezdeményez kapcsolatot a Azure File Sync ügynökhöz.

## <a name="ports"></a>Portok
Azure File Sync a fájlok adatait és metaadatait kizárólag HTTPS-en keresztül mozgatja, és a 443-as portot kimenő forgalomként kell megnyitni.
Ennek eredményeképpen minden forgalom titkosítva van.

## <a name="networks-and-special-connections-to-azure"></a>Hálózatok és speciális kapcsolatok az Azure-hoz
A Azure File Sync ügynöknek nincsenek követelményei olyan speciális csatornákkal kapcsolatban, mint az [ExpressRoute](../../expressroute/expressroute-introduction.md)stb. az Azure-ba.

Azure File Sync bármilyen elérhető módon működik, amely lehetővé teszi az Azure-ba való elérést, automatikusan alkalmazkodik a különböző hálózati jellemzőkhez, például a sávszélességhez, a késéshez, valamint rendszergazdai vezérlést biztosít a finomhangoláshoz. Jelenleg nem minden funkció érhető el. Ha konkrét viselkedést szeretne konfigurálni, a [UserVoice-on Azure Files tudassa velünk.](https://feedback.azure.com/forums/217298-storage?category_id=180670)

## <a name="proxy"></a>Proxy
Azure File Sync támogatja az alkalmazás- és gép-szintű proxybeállításokat.

**Az alkalmazásspecifikus proxybeállítások** lehetővé teszik a proxyk kifejezetten a Azure File Sync konfigurálást. Az alkalmazásspecifikus proxybeállítások az ügynök 4.0.1.0-s vagy újabb verzióiban támogatottak, és az ügynök telepítése során vagy az Set-StorageSyncProxyConfiguration PowerShell-parancsmag használatával konfigurálhatóak.

alkalmazásspecifikus proxybeállítások konfigurálására alkalmas PowerShell-parancsok használatával konfigurálhatók:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Ha például a proxykiszolgáló felhasználónévvel és jelszóval történő hitelesítést igényel, futtassa a következő PowerShell-parancsokat:

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
**A gépre kiterjedő proxybeállítások** transzparensek a Azure File Sync ügynök számára, mivel a kiszolgáló teljes forgalma a proxyn keresztül van irányítva.

A számítógépre vonatkozó proxybeállítások konfigurálásához kövesse az alábbi lépéseket: 

1. Proxybeállítások konfigurálása .NET-alkalmazásokhoz 

   - Szerkessze a következő két fájlt:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Adja hozzá <system.net> szakaszt a machine.config fájlokhoz (a <system.serviceModel> alatt).  Módosítsa a 127.0.01:8888 címet a proxykiszolgáló IP-címére és portjére. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. A WinHTTP proxybeállításai 

   > [!Note]  
   > A Windows Server proxykiszolgáló használatára való konfigurálásának számos módja van (WPAD, PAC-fájl, netsh stb.). Az alábbi lépések azt mutatjak, hogyan konfigurálhatja a proxybeállításokat a netsh használatával, de a Proxykiszolgáló beállításainak konfigurálása [a Windowsban](https://docs.microsoft.com/troubleshoot/windows-server/networking/configure-proxy-server-settings) dokumentációban felsorolt bármely módszer támogatott.


   - Futtassa a következő parancsot egy rendszergazda jogú parancssorból vagy a PowerShellből a meglévő proxybeállításhoz:   

     netsh winhttp show proxy

   - Futtassa a következő parancsot egy rendszergazda jogú parancssorból vagy PowerShellből a proxybeállítás beállításának beállítására (módosítsa a 127.0.01:8888-at a proxykiszolgáló IP-címére és portjára):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Indítsa újra a Storage Sync Agent szolgáltatást a következő parancs rendszergazda jogú parancssorból vagy PowerShellből való futtatásával: 

      net stop filesyncsvc

      Megjegyzés: A Storage Sync Agent (filesyncsvc) szolgáltatás a leállítás után automatikusan elindul.

## <a name="firewall"></a>Firewall
Ahogy az előző szakaszban említettük, a 443-as portnak nyitott kimenő portnak kell lennie. Az adatközpontban, ágban vagy régióban található szabályzatok alapján a porton keresztüli forgalom további korlátozása adott tartományokra lehet szükséges vagy szükséges.

Az alábbi táblázat a kommunikációhoz szükséges tartományokat ismerteti:

| Szolgáltatás | Nyilvános felhő végpontja | Azure Government végpont | Használat |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Minden felhasználói hívás (például a PowerShell) ezen az URL-címen halad át, beleértve a kezdeti kiszolgálóregisztrációs hívást is. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager hitelesített felhasználónak kell kezdeményeznie a hívásokat. A sikeres művelethez a rendszer ezt az URL-címet használja a felhasználói hitelesítéshez. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | A Azure File Sync részeként létrejön egy szolgáltatásnév az előfizetés Azure Active Directory előfizetésében. Erre az URL-címre lesz használva. Ez a rendszerbiztonsági tag a minimális jogosultságkészletnek a Azure File Sync delegál. A kezdeti beállítását végző Azure File Sync felhasználónak kell lennie, előfizetés-tulajdonosi jogosultságokkal rendelkező hitelesített felhasználónak. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Használja a nyilvános végpont URL-címét. | Ezt az URL-címet Active Directory a Azure File Sync-kiszolgáló regisztrációs felhasználói felülete által a rendszergazdai bejelentkezéshez használt hitelesítési kódtár. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Amikor a kiszolgáló letölt egy fájlt, a kiszolgáló hatékonyabban végzi el az adatok mozgását, amikor közvetlenül a tárfiókban található Azure-fájlmegosztással beszélget. A kiszolgáló olyan SAS-kulccsal rendelkezik, amely csak célzott fájlmegosztás-hozzáférést tesz lehetővé. |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | A kezdeti kiszolgálóregisztrációt követően a kiszolgáló egy regionális URL-címet kap a Azure File Sync szolgáltatáspéldányhoz. A kiszolgáló az URL-cím használatával közvetlenül és hatékonyan kommunikálhat a szinkronizálást kezelő példányokkal. |
| **Microsoft PKI** |  https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | A Azure File Sync ügynök telepítése után a PKI URL-címével letölthet köztes tanúsítványokat, amelyek az Azure File Sync szolgáltatással és az Azure-fájlmegosztással való kommunikációhoz szükségesek. Az OCSP URL-cím a tanúsítvány állapotának ellenőrzésére használható. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | Az Azure File Sync ügynök telepítése után a rendszer a Microsoft Update URL-címeit használja az ügynökfrissítések Azure File Sync letöltéséhez. |

> [!Important]
> Ha engedélyezi a .afs.azure.net, a forgalom csak a szinkronizálási &ast; szolgáltatásra lesz lehetséges. A tartományt Microsoft-szolgáltatások más felhasználó nem használja.
> Ha engedélyezi a .one.microsoft.com, a kiszolgálóról nem csak a szinkronizálási szolgáltatásra lehet &ast; forgalmat generálni. Az altartományok alatt Microsoft-szolgáltatások sok más lehetőség is rendelkezésre áll.

Ha a .afs.azure.net vagy a .one.microsoft.com túl széles körű, korlátozhatja a kiszolgáló kommunikációját, ha a kommunikációt csak a szinkronizálási szolgáltatás explicit &ast; &ast; Azure Files engedélyezi. A kiválasztható példány(ok) attól függ, hogy melyik régióban van(k) a társzinkronizálási szolgáltatás, amelyben üzembe helyezett és regisztrálta a kiszolgálót. Az alábbi táblázatban ez a régió az "Elsődleges végpont URL-címe".

Az üzletmenet folytonossága és a vészhelyreállítás (BCDR) érdekében előfordulhat, hogy egy globálisan redundáns (GRS) tárfiókban adott meg Azure-fájlmegosztásokat. Ebben az esetben az Azure-fájlmegosztások a régió tartós kimaradása esetén átveszik a feladatátvételt a párosított régióba. Azure File Sync régiópárokat használja, mint a tároló. Ha GRS-tárfiókokat használ, további URL-címeket kell engedélyeznie, hogy a kiszolgáló a párosított régióval is Azure File Sync. Az alábbi táblázat ezt a "párosított régiót" hívja. Emellett egy Traffic Manager-profil URL-címét is engedélyezni kell. Ez biztosítja, hogy feladatátvétel esetén a hálózati forgalom zökkenőmentesen át legyen irányítva a párosított régióba, és az alábbi táblázatban "Felderítési URL"-nek nevezzük.

| Felhőbeli  | Region | Elsődleges végpont URL-címe | Párosított régió | Felderítési URL-cím |
|--------|--------|----------------------|---------------|---------------|
| Nyilvános |Kelet-Ausztrália | https: \/ /australiaeast01.afs.azure.net<br>https: \/ /kailani-aue.one.microsoft.com | Délkelet-Ausztrália | https: \/ /tm-australiaeast01.afs.azure.net<br>https: \/ /tm-kailani-aue.one.microsoft.com |
| Nyilvános |Délkelet-Ausztrália | https: \/ /australiasoutheast01.afs.azure.net<br>https: \/ /kailani-aus.one.microsoft.com | Kelet-Ausztrália | https: \/ /tm-australiasoutheast01.afs.azure.net<br>https: \/ /tm-kailani-aus.one.microsoft.com |
| Nyilvános | Dél-Brazília | https: \/ /brazilsouth01.afs.azure.net | USA déli középső régiója | https: \/ /tm-brazilsouth01.afs.azure.net |
| Nyilvános | Közép-Kanada | https: \/ /canadacentral01.afs.azure.net<br>https: \/ /kailani-cac.one.microsoft.com | Kelet-Kanada | https: \/ /tm-canadacentral01.afs.azure.net<br>https: \/ /tm-kailani-cac.one.microsoft.com |
| Nyilvános | Kelet-Kanada | https: \/ /canadaeast01.afs.azure.net<br>https: \/ /kailani-cae.one.microsoft.com | Közép-Kanada | https: \/ /tm-canadaeast01.afs.azure.net<br>https: \/ /tm-kailani.cae.one.microsoft.com |
| Nyilvános | Közép-India | https: \/ /centralindia01.afs.azure.net<br>https: \/ /kailani-cin.one.microsoft.com | Dél-India | https: \/ /tm-centralindia01.afs.azure.net<br>https: \/ /tm-kailani-cin.one.microsoft.com |
| Nyilvános | Az USA középső régiója | https: \/ /centralus01.afs.azure.net<br>https: \/ /kailani-cus.one.microsoft.com | USA 2. keleti régiója | https: \/ /tm-centralus01.afs.azure.net<br>https: \/ /tm-kailani-cus.one.microsoft.com |
| Nyilvános | Kelet-Ázsia | https: \/ /eastasia01.afs.azure.net<br>https: \/ /kailani11.one.microsoft.com | Délkelet-Ázsia | https: \/ /tm-eastasia01.afs.azure.net<br>https: \/ /tm-kailani11.one.microsoft.com |
| Nyilvános | USA keleti régiója | https: \/ /eastus01.afs.azure.net<br>https: \/ /kailani1.one.microsoft.com | USA nyugati régiója | https: \/ /tm-eastus01.afs.azure.net<br>https: \/ /tm-kailani1.one.microsoft.com |
| Nyilvános | USA 2. keleti régiója | https: \/ /eastus201.afs.azure.net<br>https: \/ /kailani-ess.one.microsoft.com | Az USA középső régiója | https: \/ /tm-eastus201.afs.azure.net<br>https: \/ /tm-kailani-ess.one.microsoft.com |
| Nyilvános | Észak-Németország | https: \/ /germanynorth01.afs.azure.net | Nyugat-Németország – Középső régió | https: \/ /tm-germanywestcentral01.afs.azure.net |
| Nyilvános | Nyugat-Németország – Középső régió | https: \/ /germanywestcentral01.afs.azure.net | Észak-Németország | https: \/ /tm-germanynorth01.afs.azure.net |
| Nyilvános | Kelet-Japán | https: \/ /japaneast01.afs.azure.net | Nyugat-Japán | https: \/ /tm-japaneast01.afs.azure.net |
| Nyilvános | Nyugat-Japán | https: \/ /japanwest01.afs.azure.net | Kelet-Japán | https: \/ /tm-japanwest01.afs.azure.net |
| Nyilvános | Dél-Korea középső régiója | https: \/ /koreacentral01.afs.azure.net/ | Dél-Korea déli régiója | https: \/ /tm-koreacentral01.afs.azure.net/ |
| Nyilvános | Dél-Korea déli régiója | https: \/ /koreasouth01.afs.azure.net/ | Dél-Korea középső régiója | https: \/ /tm-koreasouth01.afs.azure.net/ |
| Nyilvános | USA északi középső régiója | https: \/ /northcentralus01.afs.azure.net | USA déli középső régiója | https: \/ /tm-northcentralus01.afs.azure.net |
| Nyilvános | Észak-Európa | https: \/ /northeurope01.afs.azure.net<br>https: \/ /kailani7.one.microsoft.com | Nyugat-Európa | https: \/ /tm-northeurope01.afs.azure.net<br>https: \/ /tm-kailani7.one.microsoft.com |
| Nyilvános | USA déli középső régiója | https: \/ /southcentralus01.afs.azure.net | USA északi középső régiója | https: \/ /tm-southcentralus01.afs.azure.net |
| Nyilvános | Dél-India | https: \/ /southindia01.afs.azure.net<br>https: \/ /kailani-sin.one.microsoft.com | Közép-India | https: \/ /tm-southindia01.afs.azure.net<br>https: \/ /tm-kailani-sin.one.microsoft.com |
| Nyilvános | Délkelet-Ázsia | https: \/ /southeastasia01.afs.azure.net<br>https: \/ /kailani10.one.microsoft.com | Kelet-Ázsia | https: \/ /tm-southeastasia01.afs.azure.net<br>https: \/ /tm-kailani10.one.microsoft.com |
| Nyilvános | Észak-Svájc | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net | Nyugat-Svájc | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net |
| Nyilvános | Nyugat-Svájc | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net | Észak-Svájc | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net |
| Nyilvános | Az Egyesült Királyság déli régiója | https: \/ /uksouth01.afs.azure.net<br>https: \/ /kailani-uks.one.microsoft.com | Az Egyesült Királyság nyugati régiója | https: \/ /tm-uksouth01.afs.azure.net<br>https: \/ /tm-kailani-uks.one.microsoft.com |
| Nyilvános | Az Egyesült Királyság nyugati régiója | https: \/ /ukwest01.afs.azure.net<br>https: \/ /kailani-ukw.one.microsoft.com | Az Egyesült Királyság déli régiója | https: \/ /tm-ukwest01.afs.azure.net<br>https: \/ /tm-kailani-ukw.one.microsoft.com |
| Nyilvános | USA nyugati középső régiója | https: \/ /westcentralus01.afs.azure.net | USA 2. nyugati régiója | https: \/ /tm-westcentralus01.afs.azure.net |
| Nyilvános | Nyugat-Európa | https: \/ /westeurope01.afs.azure.net<br>https: \/ /kailani6.one.microsoft.com | Észak-Európa | https: \/ /tm-westeurope01.afs.azure.net<br>https: \/ /tm-kailani6.one.microsoft.com |
| Nyilvános | USA nyugati régiója | https: \/ /westus01.afs.azure.net<br>https: \/ /kailani.one.microsoft.com | USA keleti régiója | https: \/ /tm-westus01.afs.azure.net<br>https: \/ /tm-kailani.one.microsoft.com |
| Nyilvános | USA 2. nyugati régiója | https: \/ /westus201.afs.azure.net | USA nyugati középső régiója | https: \/ /tm-westus201.afs.azure.net |
| Államigazgatás | USA-beli államigazgatás – Arizona | https: \/ /usgovarizona01.afs.azure.us | USA-beli államigazgatás – Texas | https: \/ /tm-usgovarizona01.afs.azure.us |
| Államigazgatás | USA-beli államigazgatás – Texas | https: \/ /usgovtexas01.afs.azure.us | USA-beli államigazgatás – Arizona | https: \/ /tm-usgovtexas01.afs.azure.us |

- Ha helyileg redundáns (LRS) vagy zónaredundáns (ZRS) tárfiókokat használ, csak az "Elsődleges végpont URL-címe" alatt felsorolt URL-címet kell engedélyeznie.

- Ha globálisan redundáns (GRS) tárfiókokat használ, engedélyezzen három URL-t.

**Példa:** Üzembe helyez egy tárolószinkronizálási szolgáltatást a `"West US"` -ban, és regisztrálja vele a kiszolgálót. Az URL-címek, amelyek lehetővé teszik, hogy a kiszolgáló kommunikáljon ezzel az esettal kapcsolatban:

> - https: \/ /westus01.afs.azure.net (elsődleges végpont: USA nyugati régiója)
> - https: \/ /eastus01.afs.azure.net (párosított feladatátvételi régió: USA keleti régiója)
> - https: \/ /tm-westus01.afs.azure.net (az elsődleges régió felderítési URL-címe)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Ip-címek Azure File Sync listájának engedélyezése
Azure File Sync támogatja a szolgáltatáscímkék [használatát,](../../virtual-network/service-tags-overview.md)amelyek egy adott Azure-szolgáltatás IP-címelőtag-csoportját képviselik. A szolgáltatáscímkék használatával olyan tűzfalszabályokat hozhat létre, amelyek engedélyezik a kommunikációt a Azure File Sync szolgáltatással. A szolgáltatáscímkéje a Azure File Sync: `StorageSyncService` .

Ha az Azure-Azure File Sync használ, a szolgáltatáscímke nevét közvetlenül a hálózati biztonsági csoportban használhatja a forgalom engedélyezése érdekében. További információ erről: Hálózati [biztonsági csoportok.](../../virtual-network/network-security-groups-overview.md)

Ha helyszíni Azure File Sync használ, a szolgáltatáscímke API-jának használatával lekért konkrét IP-címtartományokat a tűzfala engedélyező listájához. Ezt az információt két módszerrel lehet lekérte:

- A szolgáltatáscímkéket támogató Azure-szolgáltatások jelenlegi IP-címtartományai hetente megjelenik a Microsoft letöltőközpontban egy JSON-dokumentum formájában. Minden Azure-felhő saját JSON-dokumentummal rendelkezik az adott felhőhöz kapcsolódó IP-címtartományokkal:
    - [Nyilvános Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
- A szolgáltatáscímke-felderítési API (előzetes verzió) lehetővé teszi a szolgáltatáscímkék aktuális listájának programozott lekérését. Előzetes verzióban a szolgáltatáscímke-felderítési API olyan információkat is visszaadhat, amelyek kevésbé aktuálisak, mint a Microsoft letöltőközpontban közzétett JSON-dokumentumokból visszaadott adatok. Az API-felületet az automatizálási beállítások alapján használhatja:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az_network_list_service_tags)

Mivel a szolgáltatáscímke-felderítési API nem frissül olyan gyakran, mint a Microsoft letöltőközpontban közzétett JSON-dokumentumok, javasoljuk, hogy a JSON-dokumentum használatával frissítse a helyszíni tűzfal engedélyezőlistáját. Ez a következőképpen használhatja:

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

Ezután a IP-címtartományai segítségével `$ipAddressRanges` frissítheti a tűzfalat. A tűzfal frissítésével kapcsolatos információkért tekintse meg a tűzfal/hálózati berendezés webhelyét.

## <a name="test-network-connectivity-to-service-endpoints"></a>Szolgáltatásvégpontokkal való hálózati kapcsolat tesztelése
Miután regisztrált egy kiszolgálót az Azure File Sync szolgáltatásban, az Test-StorageSyncNetworkConnectivity-parancsmag és az ServerRegistration.exe a kiszolgálóra jellemző összes végponttal (URL-cím) tesztelheti a kommunikációt. Ez a parancsmag segíthet a hibaelhárításban, ha a hiányos kommunikáció megakadályozza, hogy a kiszolgáló teljes mértékben együttműködjon a Azure File Sync és a proxy- és tűzfal-konfigurációk finomhangolása érdekében használható.

A hálózati kapcsolati teszt futtatásához telepítse a Azure File Sync 9.1-es vagy újabb verzióját, és futtassa a következő PowerShell-parancsokat:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Összegzés és kockázati korlátozások
A dokumentum korábbi listái azokat az URL-címeket tartalmazzák, Azure File Sync jelenleg kommunikálnak. A tűzfalaknak engedélyezniük kell az ezekre a tartományokra kimenő forgalmat. A Microsoft igyekszik naprakészen tartani ezt a listát.

A tartományt korlátozó tűzfalszabályok beállítása segíthet a biztonság javításában. Ha ezeket a tűzfalkonfigurációkat használja, fontos szem előtt tartani, hogy a rendszer HOZZÁADJA az URL-címeket, és idővel változhat is. Rendszeresen tekintse meg ezt a cikket.

## <a name="next-steps"></a>Következő lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)
- [Az Azure File Sync üzembe helyezése](file-sync-deployment-guide.md)
- [Az Azure File Sync monitorozása](file-sync-monitoring.md)
