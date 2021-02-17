---
title: A csatlakoztatott számítógép Windows-ügynökének áttekintése
description: Ez a cikk részletes áttekintést nyújt az Azure arc-kompatibilis kiszolgálók ügynökéről, amely támogatja a hibrid környezetekben üzemeltetett virtuális gépek figyelését.
ms.date: 02/16/2021
ms.topic: conceptual
ms.openlocfilehash: 8c06989d726a30e95f0b9c4dcc15a967d498f92a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580871"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Az Azure arc használatára képes kiszolgálók ügynökének áttekintése

Az Azure arc-kompatibilis kiszolgálókhoz csatlakoztatott gépi ügynök lehetővé teszi az Azure-on kívül üzemeltetett Windows-és Linux-gépek kezelését a vállalati hálózaton vagy más felhőalapú szolgáltatón. Ez a cikk részletes áttekintést nyújt az ügynökről, a rendszerről és a hálózati követelményekről, valamint a különböző üzembe helyezési módszerekről.

>[!NOTE]
>Az Azure arc-kompatibilis kiszolgálók általános kiadásával kezdődően a 2020-es verzióban az Azure-beli csatlakoztatott gépi ügynök (a 1,0-nál kevesebb verziót használó ügynökök) összes előzetes verziójának **2021** **elavultnak** kell lennie.  Ebben az időkeretben a 1,0-es vagy újabb verzióra frissíthet, mielőtt az előzetesen kiadott ügynökök már nem tudnak kommunikálni az Azure arc-kompatibilis kiszolgálók szolgáltatással.

## <a name="agent-component-details"></a>Ügynök-összetevő részletei

Az Azure-beli csatlakoztatott gépi ügynök csomag számos logikai összetevőt tartalmaz, amelyek együtt vannak csomagolva.

* A hibrid példány metaadatainak szolgáltatása (HIMDS) kezeli az Azure-hoz és a csatlakoztatott gép Azure-identitásához való kapcsolódást.

* A vendég konfigurációs ügynök In-Guest szabályzatot és a vendég konfigurációs funkcióját biztosítja, például azt, hogy a gép megfelel-e a szükséges házirendeknek.

    A leválasztott gép Azure Policy [vendég konfigurációjának](../../governance/policy/concepts/guest-configuration.md) következő viselkedését vegye figyelembe:

    * A leválasztott gépeket megcélozó vendég-konfigurációs házirend-hozzárendelés nem érinti.
    * A vendég-hozzárendelést a rendszer 14 napig helyileg tárolja. A 14 napos időszakon belül, ha a csatlakoztatott számítógép ügynöke újracsatlakozik a szolgáltatáshoz, a rendszer újraalkalmazza a házirend-hozzárendeléseket.
    * A hozzárendelések 14 nap után törlődnek, és a 14 napos időszak után nem lesznek hozzárendelve a géphez.

* A bővítmény ügynöke kezeli a virtuálisgép-bővítményeket, beleértve a telepítést, az eltávolítást és a frissítést. A bővítmények az Azure-ból tölthetők le, és a mappába kerülnek a `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` Windowsban, és a Linux rendszerhez `/opt/GC_Ext/downloads` . Windows rendszeren a bővítmény a következő elérési útra van telepítve, `%SystemDrive%\Packages\Plugins\<extension>` és Linux rendszeren a bővítmény telepítve van `/var/lib/waagent/<extension>` .

## <a name="instance-metadata"></a>Példány metaadatainak

A csatlakoztatott gép metaadat-információit a rendszer azt követően gyűjti össze, hogy a csatlakoztatott számítógép ügynöke az ív használatára képes kiszolgálókon regisztrálva van. Ezek konkrétan a következők:

* Operációs rendszer neve, típusa és verziója
* Számítógép neve
* Számítógép teljesen minősített tartományneve (FQDN)
* Csatlakoztatott gépi ügynök verziója
* Active Directory és DNS teljes tartományneve (FQDN)
* UUID (BIOS-AZONOSÍTÓ)
* Csatlakoztatott számítógép-ügynök szívverése
* Csatlakoztatott gépi ügynök verziója
* Felügyelt identitás nyilvános kulcsa
* A szabályzat megfelelőségi állapota és részletei (ha Azure Policy vendég konfigurációs házirendeket használ)

Az ügynök az alábbi metaadat-információkat kéri az Azure-tól:

* Erőforrás helye (régió)
* Virtuális gép azonosítója
* Címkék
* Azure Active Directory felügyelt identitás tanúsítványa
* Vendég-konfigurációs szabályzatok hozzárendelései
* Kiterjesztési kérelmek – telepítés, frissítés és törlés.

## <a name="download-agents"></a>Ügynökök letöltése

A Windows és a Linux rendszerhez készült Azure Connected Machine Agent csomagot az alább felsorolt helyekről töltheti le.

* [Windows agent Windows Installer csomag](https://aka.ms/AzureConnectedMachineAgent) a Microsoft letöltőközpontból.

* A Linux-ügynök csomagja a Microsoft [csomag adattárában](https://packages.microsoft.com/) található, az elosztás előnyben részesített csomag formátuma alapján (. RPM vagy. DEB).

A Windows és a Linux rendszerhez készült Azure-beli csatlakoztatott gépi ügynök a követelményektől függően manuálisan vagy automatikusan is frissíthető a legújabb kiadásra. További információ: [itt](manage-agent.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Windows és a Linux operációs rendszer következő verziói hivatalosan támogatottak az Azure-beli csatlakoztatott gépi ügynöknél:

- Windows Server 2008 R2, Windows Server 2012 R2 és újabb (beleértve a Server Core-t)
- Ubuntu 16,04 és 18,04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)
- Oracle Linux 7

> [!WARNING]
> A Linux-állomásnév vagy a Windows-számítógép neve nem használhatja a név egyik foglalt kifejezését vagy védjegyét, ellenkező esetben a csatlakoztatott gép Azure-ba való regisztrálására tett kísérlet sikertelen lesz. A fenntartott szavak listáját a [fenntartott erőforrás-hibák feloldása](../../azure-resource-manager/templates/error-reserved-resource-name.md) című témakörben tekintheti meg.

### <a name="required-permissions"></a>Szükséges engedélyek

* A gépek bevezetéséhez Ön az **Azure Connected Machine** bevezetési szerepkör tagja.

* Egy gép olvasásához, módosításához és törléséhez az **Azure Connected machine erőforrás-rendszergazdai** szerepkör tagja. 

### <a name="azure-subscription-and-service-limits"></a>Az Azure-előfizetések és -szolgáltatások korlátozásai

Mielőtt a gépeket az Azure arc-kompatibilis kiszolgálókhoz konfigurálja, tekintse át a Azure Resource Manager [előfizetési korlátait](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) és az [erőforráscsoport korlátait](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) , hogy megtervezze a csatlakoztatni kívánt gépek számát.

Az Azure arc-kompatibilis kiszolgálók legfeljebb 5 000 gépi példányt támogatnak egy erőforráscsoporthoz.

### <a name="transport-layer-security-12-protocol"></a>Transport Layer Security 1,2 protokoll

Az Azure-ba irányuló adatátvitel biztonságának biztosítása érdekében határozottan javasoljuk, hogy a gépet a Transport Layer Security (TLS) 1,2 használatára konfigurálja. A TLS/SSL (SSL) régebbi verziói sebezhetőnek találták, és miközben jelenleg is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak**.

|Platform/nyelv | Támogatás | További információ |
| --- | --- | --- |
|Linux | A Linux-disztribúciók általában az [OpenSSL](https://www.openssl.org) -t használják a TLS 1,2 támogatásához. | Ellenőrizze az OpenSSL- [changelog](https://www.openssl.org/news/changelog.html) , hogy az OpenSSL verziója támogatott-e.|
| Windows Server 2012 R2 és újabb | Támogatott, és alapértelmezés szerint engedélyezve van. | Annak megerősítéséhez, hogy továbbra is az [alapértelmezett beállításokat](/windows-server/security/tls/tls-registry-settings)használja.|

### <a name="networking-configuration"></a>Hálózatkezelési konfiguráció

A Linux és a Windows rendszerhez csatlakoztatott gépi ügynök a 443-as TCP-porton keresztül kommunikál az Azure-beli ív felé. Ha a gép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztüli kommunikációhoz, tekintse át a következőt a hálózati konfigurációs követelmények megismeréséhez.

> [!NOTE]
> Az ív használatára képes kiszolgálók nem támogatják [log Analytics átjáró](../../azure-monitor/agents/gateway.md) proxyként való használatát a csatlakoztatott gépi ügynökhöz.
>

Ha a kimenő kapcsolatot a tűzfal vagy a proxykiszolgáló korlátozza, győződjön meg arról, hogy az alább felsorolt URL-címek nincsenek letiltva. Ha csak az ügynöknek a szolgáltatással való kommunikációhoz szükséges IP-tartományokat vagy tartományneveket engedélyezi, engedélyeznie kell a következő szolgáltatás-címkék és URL-címek elérését.

Szolgáltatás címkéi:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure

URLs

| Ügynök erőforrása | Description |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Vendégkonfiguráció|
|`*.his.arc.azure.com`|Hibrid identitási szolgáltatás|
|`www.office.com`|Office 365|

Az előzetes verziójú ügynököknek (0,11-es és alacsonyabb verzió) a következő URL-címekhez is hozzáférést kell adni:

| Ügynök erőforrása | Description |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Vendégkonfiguráció|
|`*-agentservice-prod-1.azure-automation.net`|Vendégkonfiguráció|

Az egyes szolgáltatási címkék/régiók IP-címeinek listáját lásd: JSON-fájl – [Azure IP-címtartományok és szolgáltatás-címkék – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519). A Microsoft az egyes Azure-szolgáltatásokat és az általa használt IP-tartományokat tartalmazó heti frissítéseket tesz közzé. A JSON-fájlban szereplő információk az egyes szolgáltatási címkéknek megfelelő IP-címtartományok aktuális időpontra vonatkozó listája. Az IP-címek változhatnak. Ha a tűzfal-konfigurációhoz IP-címtartományok szükségesek, akkor a **AzureCloud** szolgáltatás címkét kell használni az összes Azure-szolgáltatás elérésének engedélyezéséhez. Ne tiltsa le ezen URL-címek biztonsági figyelését vagy ellenőrzését, és engedélyezze azokat más internetes forgalomként.

További információkért tekintse át a [szolgáltatási címkék áttekintése](../../virtual-network/service-tags-overview.md)című témakört.

### <a name="register-azure-resource-providers"></a>Azure-erőforrás-szolgáltatók regisztrálása

Az Azure arc használatára képes kiszolgálók a szolgáltatás használatához a következő Azure-erőforrás-szolgáltatótól függenek:

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

Ha nincsenek regisztrálva, akkor az alábbi parancsokkal regisztrálhat:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Az erőforrás-szolgáltatókat a Azure Portal is regisztrálhatja a [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)szakaszban leírt lépéseket követve.

## <a name="installation-and-configuration"></a>Telepítés és konfigurálás

A hibrid környezetben az Azure-ban közvetlenül csatlakoztatható gépek a követelményektől függően különböző módszerekkel végezhetők el. Az alábbi táblázat az egyes módszereket ismerteti, amelyek alapján meghatározhatja, hogy melyik a legmegfelelőbb a szervezet számára.

> [!IMPORTANT]
> A csatlakoztatott számítógép ügynöke nem telepíthető Azure Windows rendszerű virtuális gépre. Ha megkísérli a-t, a telepítés észleli ezt, és Visszagörgeti azt.

| Metódus | Leírás |
|--------|-------------|
| Interaktív módon | Manuálisan telepítse az ügynököt egy vagy több gépen a [gépek Azure Portal-ból való összekapcsolása](onboard-portal.md)című témakör lépéseit követve.<br> A Azure Portal létrehozhat egy parancsfájlt, és végrehajthatja azt a gépen, hogy automatizálja az ügynök telepítésének és konfigurálásának lépéseit.|
| Skálán | Telepítse és konfigurálja az ügynököt több gépen a [számítógépek összekapcsolását követően egy egyszerű szolgáltatásnév használatával](onboard-service-principal.md).<br> Ez a metódus létrehoz egy egyszerű szolgáltatást, amely nem interaktív módon csatlakozik a gépekhez.|
| Skálán | Telepítse és konfigurálja az ügynököt több gépre, a módszert követve a [Windows POWERSHELL DSC használatával](onboard-dsc.md).<br> Ez a metódus egyszerű szolgáltatásnév használatával csatlakozik a gépekhez a PowerShell DSC-vel nem interaktív módon. |

## <a name="connected-machine-agent-technical-overview"></a>Csatlakoztatott gépi ügynök – technikai áttekintés

### <a name="windows-agent-installation-details"></a>A Windows-ügynök telepítésének részletei

A Windows rendszerhez készült csatlakoztatott számítógép-ügynök a következő három módszer egyikével telepíthető:

* Kattintson duplán a fájlra `AzureConnectedMachineAgent.msi` .
* Manuálisan futtassa a Windows Installer csomagot `AzureConnectedMachineAgent.msi` a parancs-rendszerhéjból.
* Egy PowerShell-munkamenetből egy parancsfájlból álló metódus használatával.

A Windowshoz készült csatlakoztatott számítógép-ügynök telepítése után a rendszer a következő rendszerszintű konfigurációs módosításokat alkalmazza.

* A telepítés során a következő telepítési mappák jönnek létre.

    |Mappa |Description |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Az ügynök által támogatott fájlokat tartalmazó alapértelmezett telepítési útvonal.|
    |%ProgramData%\AzureConnectedMachineAgent |Az ügynök konfigurációs fájljait tartalmazza.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |A beszerzett jogkivonatokat tartalmazza.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Az ügynök konfigurációs fájlját tartalmazza `agentconfig.json` , amely rögzíti a regisztrációs adatokat a szolgáltatással.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | A vendég konfigurációs ügynök fájljait tartalmazó telepítési útvonal. |
    |%ProgramData%\GuestConfig |Az Azure-ból származó (alkalmazott) szabályzatokat tartalmazza.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | A bővítmények letölthetők az Azure-ból, és ide másolhatók.|

* Az ügynök telepítése során a következő Windows-szolgáltatások jönnek létre a célszámítógépen.

    |Szolgáltatásnév |Megjelenített név |Folyamatnév |Description |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds |Ez a szolgáltatás implementálja az Azure-példány metaadatainak szolgáltatását (IMDS) az Azure-hoz és a csatlakoztatott gép Azure-identitásához való csatlakozás kezeléséhez.|
    |GCArcService |Vendég konfigurációs ív szolgáltatás |gc_service |Figyeli a gép kívánt állapotának konfigurációját.|
    |ExtensionService |Vendég konfigurációs bővítmény szolgáltatás | gc_service |Telepíti a gépet célzó szükséges bővítményeket.|

* Az ügynök telepítése során az alábbi környezeti változók jönnek létre.

    |Name |Alapértelmezett érték |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Több naplófájl is elérhető a hibaelhárításhoz. Ezeket a következő táblázat ismerteti.

    |Napló |Description |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Az ügynökök (HIMDS) szolgáltatás és az Azure interakciójának adatait rögzíti.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |A azcmagent eszköz parancsainak kimenetét tartalmazza a részletes (-v) argumentum használatakor.|
    |%ProgramData%\GuestConfig\ gc_agent_logs \ gc_agent. log |A DSC szolgáltatási tevékenység részleteit rögzíti,<br> különösen a HIMDS szolgáltatás és a Azure Policy közötti kapcsolat.|
    |% ProgramData% \GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |A DSC szolgáltatás telemetria és részletes naplózási adatait rögzíti.|
    |%ProgramData%\GuestConfig\ ext_mgr_logs|A bővítmény ügynök összetevőjének adatait rögzíti.|
    |%ProgramData%\GuestConfig\ extension_logs\<Extension>|A telepített bővítmény adatait rögzíti.|

* Létrejön a helyi biztonsági csoport **hibrid ügynök bővítményének alkalmazásai** .

* Az ügynök eltávolítása során a rendszer nem távolítja el a következő összetevőket.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent és alkönyvtárak
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Linux-ügynök telepítésének részletei

A Linux rendszerhez csatlakoztatott számítógép-ügynök a terjesztés előnyben részesített csomag formátumban van megadva (. RPM vagy. DEB), amely a Microsoft [Package adattárában](https://packages.microsoft.com/)található. Az ügynök telepítve van és konfigurálva van a shell script Bundle [Install_linux_azcmagent. sh](https://aka.ms/azcmagent).

A Linux rendszerhez készült csatlakoztatott gépi ügynök telepítése után a következő rendszerszintű konfigurációs módosítások lesznek alkalmazva.

* A telepítés során a következő telepítési mappák jönnek létre.

    |Mappa |Description |
    |-------|------------|
    |/var/opt/azcmagent/ |Az ügynök által támogatott fájlokat tartalmazó alapértelmezett telepítési útvonal.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | A vendég konfigurációs ügynök fájljait tartalmazó telepítési útvonal.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |A beszerzett jogkivonatokat tartalmazza.|
    |/var/lib/GuestConfig |Az Azure-ból származó (alkalmazott) szabályzatokat tartalmazza.|
    |/opt/GC_Ext/downloads|A bővítmények letölthetők az Azure-ból, és ide másolhatók.|

* Az ügynök telepítése során a következő démonok jönnek létre a célszámítógépen.

    |Szolgáltatásnév |Megjelenített név |Folyamatnév |Description |
    |-------------|-------------|-------------|------------|
    |himdsd. Service |Azure-beli csatlakoztatott gépi ügynök szolgáltatás |himds |Ez a szolgáltatás implementálja az Azure-példány metaadatainak szolgáltatását (IMDS) az Azure-hoz és a csatlakoztatott gép Azure-identitásához való csatlakozás kezeléséhez.|
    |gcad. servce |GC arc szolgáltatás |gc_linux_service |Figyeli a gép kívánt állapotának konfigurációját. |
    |extd. Service |Bővítmény szolgáltatás |gc_linux_service | Telepíti a gépet célzó szükséges bővítményeket.|

* Több naplófájl is elérhető a hibaelhárításhoz. Ezeket a következő táblázat ismerteti.

    |Napló |Description |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Az ügynökök (HIMDS) szolgáltatás és az Azure interakciójának adatait rögzíti.|
    |/var/opt/azcmagent/log/azcmagent.log |A azcmagent eszköz parancsainak kimenetét tartalmazza a részletes (-v) argumentum használatakor.|
    |/opt/logs/dsc.log |A DSC szolgáltatási tevékenység részleteit rögzíti,<br> különösen a himds szolgáltatás és a Azure Policy közötti kapcsolat.|
    |/opt/logs/dsc.telemetry.txt |A DSC szolgáltatás telemetria és részletes naplózási adatait rögzíti.|
    |/var/lib/GuestConfig/ext_mgr_logs |A bővítmény ügynök összetevőjének adatait rögzíti.|
    |/var/lib/GuestConfig/extension_logs|A telepített bővítmény adatait rögzíti.|

* Az ügynök telepítése során az alábbi környezeti változók jönnek létre. Ezek a változók a ben vannak beállítva `/lib/systemd/system.conf.d/azcmagent.conf` .

    |Name |Alapértelmezett érték |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Az ügynök eltávolítása során a rendszer nem távolítja el a következő összetevőket.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Következő lépések

* Az Azure arc-kompatibilis kiszolgálók kiértékelésének megkezdéséhez kövesse a [hibrid gépek az Azure-ba való összekapcsolását ismertető cikket a Azure Portal](onboard-portal.md).

* A hibaelhárítási információ a [csatlakoztatott gép ügynökének hibaelhárítása című útmutatóban](troubleshoot-agent-onboard.md)található.
