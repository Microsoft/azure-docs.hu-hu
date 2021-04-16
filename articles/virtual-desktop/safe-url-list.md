---
title: Windows Virtual Desktop URL-címek listája – Azure
description: Azon URL-címek listája, amelyeket fel kell oldani annak érdekében, hogy a Windows Virtual Desktop megfelelően működik-e.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 00ae761af44b9e6537149c96607c0ba00e6439c8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514985"
---
# <a name="required-url-list"></a>Szükséges URL-címek listája

A virtuális gépek üzembe helyezéséhez és Windows Virtual Desktop kell feloldani bizonyos URL-címek blokkolását, hogy a virtuális gépek (VM-ek) bármikor hozzáférjenek hozzájuk. Ez a cikk felsorolja a tiltás feloldásához szükséges URL-címeket, Windows Virtual Desktop megfelelően működjön. 

>[!IMPORTANT]
>Windows Virtual Desktop nem támogatja az ebben a cikkben felsorolt URL-címeket blokkoló üzemelő példányokat.

## <a name="required-url-check-tool"></a>Szükséges URL-ellenőrző eszköz

A Szükséges URL-cím-ellenőrző eszköz ellenőrzi az URL-címeket, és megjeleníti, hogy elérhetők-e azok az URL-címek, amelyekhez a virtuális gépnek működnie kell. Ha nem, akkor az eszköz felsorolja a nem elérhető URL-címeket, így szükség esetén feloldhatja a tiltásokat.

Fontos szem előtt tartani a következőket:

- A kötelező URL-ellenőrző eszközt csak kereskedelmi felhőkben üzemelő példányok esetén használhatja.
- A Szükséges URL-címellenőrzés eszköz nem tudja helyettesítő karakterekkel ellenőrizni az URL-címeket, ezért először oldja fel az URL-címek blokkolását.

### <a name="requirements"></a>Követelmények

A Szükséges URL-cím-ellenőrző eszköz az alábbiakhoz szükséges:

- A virtuális gépnek .NET 4.6.2 keretrendszerre van szükség
- RDAgent 1.0.2944.400-as vagy újabb verzió
- A WVDAgentUrlTool.exe fájlnak ugyanabban a mappában kell lennie, mint a WVDAgentUrlTool.config fájlnak

### <a name="how-to-use-the-required-url-check-tool"></a>A szükséges URL-ellenőrző eszköz használata

A Szükséges URL-cím-ellenőrző eszköz használata:

1. Nyisson meg egy parancssort rendszergazdaként a virtuális gépen.
2. A következő parancs futtatásával módosítsa a könyvtárat a buildügynökkel azonos mappára:

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. Futtassa az alábbi parancsot:

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. A fájl futtatása után megjelenik az elérhető és nem elérhető URL-címek listája.

    Az alábbi képernyőképen például az látható, hogy két kötelező nem helyettesítő URL-cím blokkolását kell feloldani:

    > [!div class="mx-imgBorder"]
    > ![Nem elérhető URL-címek kimenetének képernyőképe.](media/noaccess.png)
    
    Az összes szükséges nem helyettesítő URL-cím blokkolásának feloldása után a következő kimenetnek kell kinéznie:

    > [!div class="mx-imgBorder"]
    > ![Az akadálymentes URL-címek kimenetének képernyőképe.](media/access.png)

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

A virtuális gépekhez létrehozott Azure-Windows Virtual Desktop hozzáféréssel kell, hogy rendelkezik a következő URL-címekhez az Azure kereskedelmi felhőben:

|Cím|Kimenő TCP-port|Cél|Szolgáltatáscímke|
|---|---|---|---|
|*.wvd.microsoft.com|443|Szolgáltatásforgalom|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Ügynökforgalom|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Ügynökforgalom|AzureCloud|
|*xt.blob.core.windows.net|443|Ügynökforgalom|AzureCloud|
|*eh.servicebus.windows.net|443|Ügynökforgalom|AzureCloud|
|*xt.table.core.windows.net|443|Ügynökforgalom|AzureCloud|
|*xt.queue.core.windows.net|443|Ügynökforgalom|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Piactér|AzureCloud|
|kms.core.windows.net|1688|A Windows aktiválása|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Ügynök- és SXS-veremfrissítések|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure Portal támogatás|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata szolgáltatásvégpont](../virtual-machines/windows/instance-metadata-service.md) | N/A |
| 168.63.129.16 | 80 | [Munkamenetgazda állapotfigyelése](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/A |

>[!IMPORTANT]
>Windows Virtual Desktop már támogatja az FQDN címkét. További információ: [Use Azure Firewall to protect Window Virtual Desktop deployments](../firewall/protect-windows-virtual-desktop.md)(A Windows Virtual Desktop üzemelő példányai Azure Firewall használatával).
>
>Javasoljuk, hogy URL-címek helyett FQDN-címkéket vagy szolgáltatáscímkéket használjon a szolgáltatás problémáinak elkerülése érdekében. A felsorolt URL-címek és címkék csak a Windows Virtual Desktop és erőforrásoknak felelnek meg. Nem tartalmaznak URL-címeket más szolgáltatásokhoz, például a Azure Active Directory.

A felhőben a Windows Virtual Desktop létrehozott Azure-beli virtuális gépeknek a következő URL-címekhez kell Azure Government hozzáféréssel:

|Cím|Kimenő TCP-port|Cél|Szolgáltatáscímke|
|---|---|---|---|
|*.wvd.microsoft.us|443|Szolgáltatásforgalom|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|A Windows aktiválása|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Ügynök- és SXS-veremfrissítések|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure Portal támogatás|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata szolgáltatásvégpont](../virtual-machines/windows/instance-metadata-service.md) | N/A |
| 168.63.129.16 | 80 | [Munkamenetgazda állapotfigyelése](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/A |

A következő táblázat felsorolja azokat a választható URL-címeket, amelyekhez az Azure-beli virtuális gépek hozzáférhetnek:

|Cím|Kimenő TCP-port|Cél|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Hitelesítés a Microsoft Online Services szolgáltatásban|login.microsoftonline.us|
|*.events.data.microsoft.com|443|Telemetriai szolgáltatás|Nincsenek|
|www.msftconnecttest.com|443|Észleli, hogy az operációs rendszer csatlakozik-e az internethez|Nincsenek|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Nincsenek|
|login.windows.net|443|Jelentkezzen be a Microsoft Online Servicesbe, majd Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|A OneDrive-ügyfélszoftver frissítései|oneclient.sfx.ms|
|*.digicert.com|443|Tanúsítvány visszavonási állapotának ellenőrzése|Nincsenek|
|*.azure-dns.com|443|Azure DNS feloldás|Nincsenek|
|*.azure-dns.net|443|Azure DNS feloldás|Nincsenek|

>[!NOTE]
>Windows Virtual Desktop jelenleg nem tartalmazza azon IP-címtartományok listáját, amelyek letiltásának feloldásával engedélyezheti a hálózati forgalmat. Jelenleg csak bizonyos URL-címek tiltásának feloldását támogatjuk.
>
>Ha új generációs tűzfalat (NGFW) használ, a csatlakozáshoz egy kifejezetten az Azure IP-hez készült dinamikus listát kell használnia.
>
>A biztonságos Office-hez kapcsolódó URL-címek listáját, beleértve a szükséges Azure Active Directory URL-címeket is, lásd: [Office 365 URL-címek és IP-címtartományok.](/office365/enterprise/urls-and-ip-address-ranges)
>
>A szolgáltatásforgalmat tartalmazó URL-címekhez a (*) helyettesítő karaktert kell használnia. Ha nem szeretné használni a * karaktereket az ügynökkel kapcsolatos forgalomhoz, az url-címeket helyettesítő karakterek nélkül a következőben találhatja meg:
>
>1. Regisztrálja a virtuális gépeket a Windows Virtual Desktop gazdagépkészletben.
>2. Nyissa **meg az Eseménynaplót,** majd nyissa meg a **Windows-naplók** WVD-Agent alkalmazást, és keresse meg a  >    >   3701-es eseményazonosítót.
>3. Oldja fel a 3701-es eseményazonosító alatt található URL-címek blokkolását. A 3701-es eseményazonosító alatti URL-címek régióspecifikusak. Meg kell ismételnie a tiltási folyamatot a megfelelő URL-címekkel minden olyan régióban, ahol üzembe szeretné helyezni a virtuális gépeket.

## <a name="remote-desktop-clients"></a>Távoli asztali ügyfelek

Minden Távoli asztal ügyfélnek hozzá kell férnie a következő URL-címekhez:

|Cím|Kimenő TCP-port|Cél|Ügyfél(k)|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|Szolgáltatásforgalom|Mind|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|Adatok hibaelhárítása|Mind|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLinkek|Mind|Nincsenek|
|aka.ms|443|A Microsoft URL-címe rövidebb|Mind|Nincsenek|
|docs.microsoft.com|443|Dokumentáció|Mind|Nincsenek|
|privacy.microsoft.com|443|Adatvédelmi nyilatkozat|Mind|Nincsenek|
|query.prod.cms.rt.microsoft.com|443|Ügyfélfrissítések|Windows asztali rendszer|Nincsenek|

>[!IMPORTANT]
>Ezeknek az URL-címeknek a megnyitása elengedhetetlen a megbízható ügyfélélményhez. Az URL-címekhez való hozzáférés letiltása nem támogatott, és hatással van a szolgáltatás funkcióira.
>
>Ezek az URL-címek csak az ügyfélhelyeknek és -erőforrásoknak felelnek meg. Ez a lista nem tartalmazza az olyan más szolgáltatások URL-címeit, mint Azure Active Directory. Azure Active Directory URL-címek az [Office 365](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)URL-címeinek és IP-címtartományának 56-os azonosítója alatt találhatók.
