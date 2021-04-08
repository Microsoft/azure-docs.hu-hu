---
title: Windows rendszerű virtuális asztali kötelező URL-címek listája – Azure
description: A feloldani kívánt URL-címek listája, amely biztosítja, hogy a Windows rendszerű virtuális asztali környezet a kívánt módon működjön.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 872a67d8d9f41c0c809df54304352b2a5f58e011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251760"
---
# <a name="required-url-list"></a>Szükséges URL-címek listája

A Windows rendszerű virtuális asztalok üzembe helyezéséhez és használatához fel kell oldania bizonyos URL-címek blokkolását, hogy a virtuális gépek (VM-EK) bármikor hozzáférhessenek hozzájuk. Ez a cikk felsorolja azokat a szükséges URL-címeket, amelyeknek meg kell szüntetnie a tiltást ahhoz, hogy a Windows virtuális asztal megfelelően működjön. 

>[!IMPORTANT]
>A Windows virtuális asztal nem támogatja az ebben a cikkben felsorolt URL-címeket blokkoló központi telepítéseket.

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

A Windows rendszerű virtuális asztali környezethez létrehozott Azure-beli virtuális gépeknek hozzáféréssel kell rendelkezniük a következő URL-címekhez az Azure kereskedelmi felhőben:

|Cím|Kimenő TCP-port|Cél|Szolgáltatás címkéje|
|---|---|---|---|
|*. wvd.microsoft.com|443|Szolgáltatás forgalma|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Ügynök forgalma|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Ügynök forgalma|AzureCloud|
|* xt.blob.core.windows.net|443|Ügynök forgalma|AzureCloud|
|* eh.servicebus.windows.net|443|Ügynök forgalma|AzureCloud|
|* xt.table.core.windows.net|443|Ügynök forgalma|AzureCloud|
|* xt.queue.core.windows.net|443|Ügynök forgalma|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Piactér|AzureCloud|
|kms.core.windows.net|1688|A Windows aktiválása|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Ügynök-és SXS-verem frissítései|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure Portal támogatás|AzureCloud|
| 169.254.169.254 | 80 | [Azure-példány metaadatainak szolgáltatási végpontja](../virtual-machines/windows/instance-metadata-service.md) | N/A |
| 168.63.129.16 | 80 | [Munkamenet-gazdagép állapotának figyelése](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/A |

>[!IMPORTANT]
>A Windows virtuális asztal mostantól támogatja a FQDN címkét. További információ: [a Azure Firewall használata a Windows rendszerű virtuális asztali környezetek elleni védelemhez](../firewall/protect-windows-virtual-desktop.md).
>
>Javasoljuk, hogy az URL-címek helyett a teljes tartománynevet vagy a szolgáltatás címkéit használja a szolgáltatási problémák megelőzése érdekében. A felsorolt URL-címek és címkék csak a Windows rendszerű virtuális asztali helyekhez és erőforrásokhoz tartoznak. Nem tartalmaznak URL-címeket más szolgáltatásokhoz, például a Azure Active Directoryhoz.

A Windows rendszerű virtuális asztali környezethez létrehozott Azure-beli virtuális gépeknek hozzáféréssel kell rendelkezniük a következő URL-címekhez a Azure Government-felhőben:

|Cím|Kimenő TCP-port|Cél|Szolgáltatás címkéje|
|---|---|---|---|
|*. wvd.microsoft.us|443|Szolgáltatás forgalma|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|* xt.blob.core.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|*. servicebus.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|* xt.table.core.usgovcloudapi.net|443|Ügynök forgalma|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|A Windows aktiválása|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Ügynök-és SXS-verem frissítései|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure Portal támogatás|AzureCloud|
| 169.254.169.254 | 80 | [Azure-példány metaadatainak szolgáltatási végpontja](../virtual-machines/windows/instance-metadata-service.md) | N/A |
| 168.63.129.16 | 80 | [Munkamenet-gazdagép állapotának figyelése](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/A |

A következő táblázat felsorolja azokat az opcionális URL-címeket, amelyekhez az Azure-beli virtuális gépek hozzáférhetnek:

|Cím|Kimenő TCP-port|Cél|Azure gov|
|---|---|---|---|
|*.microsoftonline.com|443|Hitelesítés a Microsoft Online Services szolgáltatásban|login.microsoftonline.us|
|*. events.data.microsoft.com|443|Telemetriai szolgáltatás|Nincsenek|
|www.msftconnecttest.com|443|Észleli, ha az operációs rendszer csatlakozik az internethez|Nincsenek|
|*. prod.do.dsp.mp.microsoft.com|443|Windows Update|Nincsenek|
|login.windows.net|443|Bejelentkezés a Microsoft Online Servicesbe, Microsoft 365|login.microsoftonline.us|
|*. sfx.ms|443|OneDrive-ügyfélszoftver frissítései|oneclient.sfx.ms|
|*. digicert.com|443|Tanúsítvány visszavonási állapotának ellenőrzése|Nincsenek|
|*. azure-dns.com|443|Azure DNS felbontás|Nincsenek|
|*. azure-dns.net|443|Azure DNS felbontás|Nincsenek|

>[!NOTE]
>A Windows virtuális asztal jelenleg nem tartalmaz olyan IP-címtartományok listáját, amelyekkel engedélyezhető a hálózati forgalom. Jelenleg csak bizonyos URL-címek blokkolását támogatjuk.
>
>Ha a következő generációs tűzfalat (NGFW) használja, az Azure-beli IP-címekre vonatkozó dinamikus listát kell használnia, hogy biztosan csatlakozhasson.
>
>A biztonságos Office-hoz kapcsolódó URL-címek listáját, beleértve a szükséges Azure Active Directory kapcsolódó URL-címeket, lásd: [Office 365 URL-címek és IP-címtartományok](/office365/enterprise/urls-and-ip-address-ranges).
>
>A szolgáltatás forgalmát tartalmazó URL-címekhez a helyettesítő karaktert (*) kell használnia. Ha nem kívánja használni az ügynökkel kapcsolatos forgalmat, a következő módon keresheti meg az URL-címeket helyettesítő karakterek nélkül:
>
>1. Regisztrálja a virtuális gépeket a Windows rendszerű virtuális asztali gazdaszámítógépen.
>2. Nyissa meg az **eseménynaplót**, majd lépjen a **Windows logs**  >  **Application**  >  **WVD-Agent** elemre, és keresse meg a 3701-es azonosítójú eseményt.
>3. Oldja fel a 3701-es AZONOSÍTÓJÚ esemény alatt megtalált URL-címeket. Az 3701-es AZONOSÍTÓJÚ esemény URL-címei régiónként jellemzőek. Az összes olyan régióhoz tartozó URL-címmel meg kell ismételnie a blokkolási folyamatot, amelyre telepíteni szeretné a virtuális gépeket a alkalmazásban.

## <a name="remote-desktop-clients"></a>Távoli asztali ügyfelek

A használt Távoli asztal ügyfeleknek hozzáféréssel kell rendelkezniük a következő URL-címekhez:

|Cím|Kimenő TCP-port|Cél|Ügyfél (ek)|Azure gov|
|---|---|---|---|---|
|*. wvd.microsoft.com|443|Szolgáltatás forgalma|Mind|*. wvd.microsoft.us|
|*.servicebus.windows.net|443|Hibaelhárítási hibák|Mind|*. servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft-Fwlinkek|Mind|Nincsenek|
|aka.ms|443|Microsoft URL-rövidített|Mind|Nincsenek|
|docs.microsoft.com|443|Dokumentáció|Mind|Nincsenek|
|privacy.microsoft.com|443|Adatvédelmi nyilatkozat|Mind|Nincsenek|
|query.prod.cms.rt.microsoft.com|443|Ügyfelek frissítései|Windows asztali rendszer|Nincsenek|

>[!IMPORTANT]
>Az URL-címek megnyitása elengedhetetlen a megbízható ügyfél-élményhez. Az ezekhez az URL-címekhez való hozzáférés letiltása nem támogatott, és a szolgáltatás funkcióit is befolyásolja.
>
>Ezek az URL-címek csak az ügyfél helyei és erőforrásainak felelnek meg. A lista nem tartalmaz URL-címeket más olyan szolgáltatásokhoz, mint például a Azure Active Directory. Azure Active Directory URL-címek az [Office 365 URL-címek és IP-címtartományok](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)alatt találhatók az 56 azonosító alatt.