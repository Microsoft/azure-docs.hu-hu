---
title: A csatlakoztatott gép ügynökének áttekintése
description: Ez a cikk részletes áttekintést nyújt az elérhető Azure Arc-ügynökről, amely támogatja a hibrid környezetekben üzemeltetett virtuális gépek monitorozását.
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd58997a8af86a3789895bfb29bfd5803826fa6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832958"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Az engedélyezett Azure Arc ügynök áttekintése

A Azure Arc csatlakoztatottgép-ügynök lehetővé teszi az Azure-on kívül üzemeltetett Windows és Linux rendszerű gépek kezelését a vállalati hálózaton vagy más felhőszolgáltatón. Ez a cikk részletes áttekintést nyújt az ügynökről, a rendszer- és hálózati követelményekről, valamint a különböző telepítési módszerekről.

>[!NOTE]
>A Azure Arc-kompatibilis kiszolgálók 2020. szeptemberi általános kiadásával kezdődően a Azure Connected Machine-ügynök összes előzetes verziója (az 1.0-s  verziónál régebbi verziójú ügynökök) **2021.** február 2-ig elavult.  Ez az időkeret lehetővé teszi, hogy frissítsen az 1.0-s vagy újabb verzióra, mielőtt az előre kiadott ügynökök már nem tudnak kommunikálni az Azure Arc-kompatibilis kiszolgálószolgáltatással.

## <a name="agent-component-details"></a>Ügynök-összetevő részletei

:::image type="content" source="media/agent-overview/connected-machine-agent.png" alt-text="Az Arc-kompatibilis kiszolgálók ügynökének áttekintése." border="false":::

A Azure Connected Machine ügynökcsomag számos logikai összetevőt tartalmaz, amelyek össze vannak csomagolva.

* A Hibridpéldány-metaadatok szolgáltatás (HIMDS) kezeli az Azure-ral és a csatlakoztatott gép Azure-identitásával való kapcsolatot.

* A Vendégkonfigurációs ügynök In-Guest házirend és a vendégkonfiguráció funkciót, például annak kiértékelése, hogy a gép megfelel-e a szükséges házirendeknek.

    Figyelje meg a következő viselkedést Azure Policy [leválasztott](../../governance/policy/concepts/guest-configuration.md) gép vendégkonfigurációja esetén:

    * A leválasztott gépeket megcélzó vendégkonfigurációs szabályzat-hozzárendeléseket ez nem érinti.
    * A vendég-hozzárendelést a rendszer helyben tárolja 14 napig. Ha a 14 napos időszakban a Connected Machine-ügynök újra csatlakozik a szolgáltatáshoz, a rendszer újraalkalmassa a szabályzat-hozzárendeléseket.
    * A hozzárendelések 14 nap után törlődnek, és a 14 napos időszak után nem kerülnek át a gépre.

* A bővítményügynök kezeli a virtuálisgép-bővítményeket, beleértve a telepítést, az eltávolítást és a frissítést. A bővítményeket a rendszer letölti az Azure-ból, és a windowsos mappába, Linux esetén pedig a mappába `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` `/opt/GC_Ext/downloads` másolja. Windows rendszeren a bővítmény a következő elérési útra van telepítve, Linux rendszeren a bővítmény pedig a `%SystemDrive%\Packages\Plugins\<extension>` következőre: `/var/lib/waagent/<extension>` .

## <a name="instance-metadata"></a>Példány metaadatai

A csatlakoztatott gép metaadat-információit akkor gyűjti a rendszer, amikor a csatlakoztatott gép ügynöke regisztrál az Arc-kompatibilis kiszolgálókra. Ezek konkrétan a következők:

* Operációs rendszer neve, típusa és verziója
* Számítógép neve
* Számítógép teljes tartományneve (FQDN)
* Csatlakoztatott gép ügynökének verziója
* Active Directory DNS teljes tartományneve (FQDN)
* UUID (BIOS-azonosító)
* Csatlakoztatott gép ügynökének szívverése
* Csatlakoztatott gép ügynökének verziója
* Nyilvános kulcs felügyelt identitáshoz
* Szabályzat megfelelőségi állapota és részletei (ha vendégkonfigurációs Azure Policy használ)

Az ügynök a következő metaadatokat kéri le az Azure-tól:

* Erőforrás helye (régió)
* Virtuális gép azonosítója
* Címkék
* Azure Active Directory identitás tanúsítványa
* Vendégkonfigurációs szabályzat-hozzárendelések
* Bővítménykérések – telepítés, frissítés és törlés.

## <a name="download-agents"></a>Ügynökök letöltése

A Windowsra Azure Connected Machine Linuxra használható ügynökcsomagot az alábbi helyekről töltheti le.

* [Windows-Windows Installer a](https://aka.ms/AzureConnectedMachineAgent) Microsoft letöltőközpontból.

* A Linux-ügynökcsomag terjesztése a [](https://packages.microsoft.com/) Microsoft csomagtárában a disztribúció előnyben részesített csomagformátumával (. RPM vagy . DEB).

A Azure Connected Machine Windows- és Linux-ügynök a követelményektől függően manuálisan vagy automatikusan frissíthető a legújabb kiadásra. További információ: [.](manage-agent.md)

## <a name="prerequisites"></a>Előfeltételek

### <a name="supported-environments"></a>Támogatott környezetek

Az Arc-kompatibilis kiszolgálók támogatják a csatlakoztatottgép-ügynök telepítését az Azure-on kívül üzemeltetett fizikai kiszolgálókon *és virtuális* gépeken. Ide tartoznak az olyan platformokon futó virtuális gépek, mint a VMware, Azure Stack HCI és más felhőkörnyezetek. Az Arc-kompatibilis kiszolgálók nem támogatják az ügynök telepítését az Azure-ban futó virtuális gépekre vagy az Azure Stack Hub-on vagy Azure Stack Edge-on futó virtuális gépekre, mivel azok már Azure-beli virtuális gépekként vannak modellezve.

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Windows és Linux operációs rendszer következő verzióit hivatalosan támogatja a Azure Connected Machine ügynök:

- Windows Server 2008 R2, Windows Server 2012 R2 vagy újabb (beleértve a Server Core-t is)
- Ubuntu 16.04 és 18.04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)
- Oracle Linux 7.

> [!WARNING]
> A Linux-állomásnév vagy a Windows-számítógépnév nem használhatja a névben foglalt szavak vagy védjegyek valamelyikét, ellenkező esetben a csatlakoztatott gép Azure-beli regisztrálása sikertelen lesz. A [fenntartott szavak listájáért lásd:](../../azure-resource-manager/templates/error-reserved-resource-name.md) Fenntartott erőforrásnévvel kapcsolatos hibák elhárítása.

### <a name="required-permissions"></a>Szükséges engedélyek

* A gépek az erőforráscsoportBan Azure Connected Machine  Közreműködő szerepkör tagja. [](../../role-based-access-control/built-in-roles.md#contributor)

* A gépek olvasása, módosítása és törlése az  erőforráscsoportban Azure Connected Machine erőforrás-rendszergazdai szerepkör tagja.

* Ha kiválaszt egy erőforráscsoportot a legördülő  listából a Szkript létrehozása metódus használata esetén, legalább az adott erőforráscsoport [Olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkörének tagja kell, hogy legyen.

### <a name="azure-subscription-and-service-limits"></a>Az Azure-előfizetések és -szolgáltatások korlátozásai

Mielőtt konfigurálja a gépeket Azure Arc engedélyezett kiszolgálókkal, [](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) tekintse [](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) át az Azure Resource Manager-előfizetések és az erőforráscsoportok korlátait, hogy megtervezze a csatlakoztatni szükséges gépek számát.

Azure Arc engedélyezett kiszolgálók legfeljebb 5000 géppéldányt támogatnak egy erőforráscsoportban.

### <a name="transport-layer-security-12-protocol"></a>Transport Layer Security 1.2 protokoll

Az Azure-ba átvitel közbeni adatok biztonságának garantálása érdekében határozottan javasoljuk, hogy konfigurálja a gépet a Transport Layer Security (TLS) 1.2 használatára. A TLS/SSL (SSL) régebbi verziói sebezhetőnek találhatók, és bár jelenleg is dolgoznak a visszamenőleges kompatibilitás érdekében, nem **ajánlottak.**

|Platform/Nyelv | Támogatás | További információ |
| --- | --- | --- |
|Linux | A Linux-disztribúciók általában [OpenSSL-re](https://www.openssl.org) támaszkodnak a TLS 1.2 támogatásához. | Ellenőrizze az [OpenSSL-változásnaplóban,](https://www.openssl.org/news/changelog.html) hogy az OpenSSL-verziója támogatott-e.|
| Windows Server 2012 R2 és újabb verziók | Támogatott és alapértelmezés szerint engedélyezve van. | Annak megerősítéséhez, hogy továbbra is az alapértelmezett [beállításokat használja.](/windows-server/security/tls/tls-registry-settings)|

### <a name="networking-configuration"></a>Hálózatkezelési konfiguráció

A Linux és Windows rendszerű csatlakoztatottgép-ügynök biztonságosan kommunikál a Azure Arc 443-as TCP-porton keresztül. Ha a gép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztüli kommunikációhoz, tekintse át a következőket a hálózati konfigurációs követelmények áttekintéséhez.

> [!NOTE]
> Az Arc-kompatibilis kiszolgálók nem támogatják a [Log Analytics-átjárók](../../azure-monitor/agents/gateway.md) proxyként való használatát a csatlakoztatottgép-ügynökhöz.
>

Ha a kimenő kapcsolatot a tűzfal vagy a proxykiszolgáló korlátozza, győződjön meg arról, hogy az alább felsorolt URL-címek nincsenek blokkolva. Ha csak az ügynöknek a szolgáltatással való kommunikációhoz szükséges IP-címtartományokat vagy tartományneveket engedélyezi, engedélyeznie kell a hozzáférést a következő szolgáltatáscímkékhöz és URL-címekhez.

Szolgáltatáscímkék:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureRinInfrastructure

Urls:

| Ügynök erőforrása | Description |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Vendégkonfiguráció|
|`*.his.arc.azure.com`|Hibrid identitásszolgáltatás|
|`www.office.com`|Office 365|

Az előzetes verziójú ügynököknek (0.11-es és újabb verziók) szintén hozzá kell férni a következő URL-címekhez:

| Ügynök erőforrása | Description |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Vendégkonfiguráció|
|`*-agentservice-prod-1.azure-automation.net`|Vendégkonfiguráció|

Az egyes szolgáltatáscímkék/régiók IP-címeinek listáját az [Azure IP-tartományok](https://www.microsoft.com/download/details.aspx?id=56519)és szolgáltatáscímkék – nyilvános felhő JSON-fájljában láthatja. A Microsoft hetente közzéteszi a frissítéseket, amelyek tartalmazzák az egyes Azure-szolgáltatásokat és az ip-címtartományokat. A JSON-fájlban található információk az egyes szolgáltatáscímkéknek megfelelő IP-címtartományok aktuális, időponthoz igaz listája. Az IP-címek változhatnak. Ha a tűzfal konfigurációja IP-címtartományokat igényel, akkor az **AzureCloud** szolgáltatáscímkével engedélyezze a hozzáférést az összes Azure-szolgáltatáshoz. Ne tiltsa le ezeknek az URL-címeknek a biztonsági monitorozását vagy vizsgálatát, hagyja őket úgy, mintha más internetes forgalmat engedélyezne.

További információkért tekintse meg a [szolgáltatáscímkék áttekintését.](../../virtual-network/service-tags-overview.md)

### <a name="register-azure-resource-providers"></a>Azure-erőforrásszolgáltatók regisztrálása

Azure Arc engedélyezett kiszolgálók az előfizetés alábbi Azure-erőforrás-szolgáltatóitól függnek a szolgáltatás használata érdekében:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Ha nincsenek regisztrálva, a következő parancsokkal regisztrálhatja őket:

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

Az erőforrás-szolgáltatókat a Azure Portal alábbi lépésekkel is [regisztrálhatja Azure Portal.](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)

## <a name="installation-and-configuration"></a>Telepítés és konfigurálás

A hibrid környezetben a gépek közvetlenül az Azure-ral való összekapcsolása a követelményektől függően különböző módszerekkel valósítható meg. Az alábbi táblázat az egyes metódusokat emeli ki annak meghatározásához, hogy melyik működik a legjobban a szervezet számára.

> [!IMPORTANT]
> A Csatlakoztatott gép ügynök nem telepíthető Azure Windows rendszerű virtuális gépre. Ha megpróbálja, a telepítés észleli ezt, és visszaállítja a telepítést.

| Metódus | Leírás |
|--------|-------------|
| Interaktívan | Telepítse manuálisan az ügynököt egy vagy kis számú gépre a [Gépek](onboard-portal.md)csatlakoztatása a virtuális gépről Azure Portal.<br> A Azure Portal létrehozhat egy szkriptet, és végrehajthatja a gépen az ügynök telepítési és konfigurációs lépésének automatizálása érdekében.|
| Nagy méretekben | Telepítse és konfigurálja az ügynököt több géphez a [Connect machines using a Service Principal (Gépek csatlakoztatása szolgáltatásnévvel) után.](onboard-service-principal.md)<br> Ez a metódus létrehoz egy szolgáltatásnév, amely nem interaktív módon csatlakoztatja a gépeket.|
| Nagy méretekben | Telepítse és konfigurálja az ügynököt több géphez a [Using Windows PowerShell DSC Windows PowerShell metódust.](onboard-dsc.md)<br> Ez a metódus egy szolgáltatásnévvel nem interaktív módon csatlakoztatja a gépeket a PowerShell DSC-hez. |

## <a name="connected-machine-agent-technical-overview"></a>A csatlakoztatott gép ügynökének technikai áttekintése

### <a name="windows-agent-installation-details"></a>A Windows-ügynök telepítésének részletei

A Windowshoz csatlakoztatott gép ügynöke az alábbi három módszer egyikével telepíthető:

* Kattintson duplán a `AzureConnectedMachineAgent.msi` fájlra.
* Manuálisan futtathatja a Windows Installer csomagot `AzureConnectedMachineAgent.msi` a parancshéjból.
* Egy PowerShell-munkamenetből parancsfájlt használó metódussal.

A Windows connected machine ügynökének telepítése után a rendszer a következő, a rendszer egészére kiterjedő konfigurációs módosításokat alkalmazza.

* A telepítés során a következő telepítési mappákat kell létrehozni.

    |Mappa |Description |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Az ügynök támogatási fájljait tartalmazó alapértelmezett telepítési útvonal.|
    |%ProgramData%\AzureConnectedMachineAgent |Az ügynökkonfigurációs fájlokat tartalmazza.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |A beszerzett jogkivonatokat tartalmazza.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Az ügynök konfigurációs fájlját `agentconfig.json` tartalmazza, amely rögzíti a regisztrációs adatait a szolgáltatással.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | A vendégkonfigurációs ügynök fájljait tartalmazó telepítési útvonal. |
    |%ProgramData%\GuestConfig |Az (alkalmazott) Azure-szabályzatokat tartalmazza.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | A bővítmények az Azure-ból tölthetők le, és innen másolhatók ki.|

* Az ügynök telepítése során a célgépen a következő Windows-szolgáltatások vannak létrehozva.

    |Szolgáltatásnév |Megjelenített név |Folyamatnév |Description |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds |Ez a szolgáltatás az Azure Instance Metadata szolgáltatást (IMDS) implementálja az Azure-hoz és a csatlakoztatott gép Azure-identitásához való csatlakozás kezeléséhez.|
    |GCVonalService |Vendégkonfiguráció Arc-szolgáltatás |gc_service |A gép célállapot-konfigurációját figyeli.|
    |ExtensionService |Vendégkonfigurációs bővítményszolgáltatás | gc_service |Telepíti a géphez szükséges bővítményeket.|

* Az ügynök telepítése során az alábbi környezeti változók vannak létrehozva.

    |Name |Alapértelmezett érték |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* A hibaelhárításhoz több naplófájl is rendelkezésre áll. Ezeket az alábbi táblázat ismerteti.

    |Napló |Description |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Az ügynökök (HIMDS) szolgáltatás és az Azure-ral való interakció adatait rögzíti.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Az azcmagent eszközparancsok kimenetét tartalmazza a verbose (-v) argumentum használata esetén.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |A DSC szolgáltatás tevékenységével kapcsolatos adatokat rögzíti,<br> különösen a HIMDS szolgáltatás és a Azure Policy.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |A DSC-szolgáltatás telemetriája és részletes naplózása adatait rögzíti.|
    |%ProgramData%\GuestConfig\ext_mgr_logs|A bővítményügynök összetevővel kapcsolatos adatokat rögzíti.|
    |%ProgramData%\GuestConfig\extension_logs\<Extension>|A telepített bővítmény adatait rögzíti.|

* Létrejönnek a helyi biztonsági csoport **hibrid ügynökbővítmény-alkalmazásai.**

* Az ügynök eltávolítása során a következő összetevők nem törlődnek.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent és alkönyvtárak
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Linux-ügynök telepítésének részletei

A Linuxhoz használható Connected Machine-ügynök az elosztás előnyben részesített csomagformátumában (. RPM vagy . DEB), amely a Microsoft [csomagtárában található.](https://packages.microsoft.com/) A rendszer telepíti és konfigurálja az ügynököt a következő héjszk szkriptcsomaggal: [Install_linux_azcmagent.sh](https://aka.ms/azcmagent).

A Linuxhoz való Csatlakoztatott gép ügynök telepítése után a rendszer a következő, a rendszer egészére kiterjedő konfigurációs módosításokat alkalmazza.

* A telepítés során a rendszer az alábbi telepítési mappákat hozta létre.

    |Mappa |Description |
    |-------|------------|
    |/var/opt/azcmagent/ |Az ügynök támogatási fájljait tartalmazó alapértelmezett telepítési útvonal.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | A vendégkonfigurációs ügynök fájljait tartalmazó telepítési útvonal.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |A beszerzett jogkivonatokat tartalmazza.|
    |/var/lib/GuestConfig |Az (alkalmazott) Azure-szabályzatokat tartalmazza.|
    |/opt/GC_Ext/downloads|A bővítményeket a rendszer letölti az Azure-ból, és innen másolja.|

* Az ügynök telepítése során a célgépen a következő démonok vannak létrehozva.

    |Szolgáltatásnév |Megjelenített név |Folyamatnév |Description |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure Connected Machine Agent Szolgáltatás |himds |Ez a szolgáltatás az Azure Instance Metadata szolgáltatást (IMDS) implementálja az Azure-ral és a csatlakoztatott gép Azure-identitásával való kapcsolat kezeléséhez.|
    |gcad.servce |GC Arc szolgáltatás |gc_linux_service |Figyeli a gép célállapot-konfigurációját. |
    |extd.service |Bővítményszolgáltatás |gc_linux_service | Telepíti a géphez szükséges bővítményeket.|

* A hibaelhárításhoz több naplófájl is rendelkezésre áll. Ezeket az alábbi táblázat ismerteti.

    |Napló |Description |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Az ügynökök (HIMDS) szolgáltatás és az Azure-ral való interakció adatait rögzíti.|
    |/var/opt/azcmagent/log/azcmagent.log |Az azcmagent eszközparancsok kimenetét tartalmazza a verbose (-v) argumentum használata esetén.|
    |/opt/logs/dsc.log |A DSC-szolgáltatási tevékenység részleteit rögzíti,<br> különösen a himds szolgáltatás és a Azure Policy.|
    |/opt/logs/dsc.telemetry.txt |A DSC-szolgáltatás telemetriája és részletes naplózása adatait rögzíti.|
    |/var/lib/GuestConfig/ext_mgr_logs |A bővítményügynök összetevővel kapcsolatos adatokat rögzíti.|
    |/var/lib/GuestConfig/extension_logs|A telepített bővítmény adatait rögzíti.|

* Az alábbi környezeti változók az ügynök telepítése során vannak létrehozva. Ezek a változók a következőben vannak `/lib/systemd/system.conf.d/azcmagent.conf` beállítva: .

    |Name |Alapértelmezett érték |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Az ügynök eltávolítása során a következő összetevők nem törlődnek.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Következő lépések

* Az engedélyezett Azure Arc értékelésének megkezdéséhez kövesse a Hibrid gépek csatlakoztatása az [Azure-hoz az Azure Portal.](onboard-portal.md)

* A hibaelhárítási információkat a Csatlakoztatott gép ügynök hibaelhárítása [útmutatóban talál.](troubleshoot-agent-onboard.md)
