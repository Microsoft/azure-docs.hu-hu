---
title: Kibővített feldolgozási kiszolgáló beállítása a VMware virtuális gépek és a fizikai kiszolgálók vész-helyreállítása során Azure Site Recovery | Microsoft Docs "
description: Ez a cikk azt ismerteti, hogyan állítható be a kibővített folyamat kiszolgálója a VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítása során.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 40f912122e6ffb9cccbd32a747f6f0d46fd6c330
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019138"
---
# <a name="scale-with-additional-process-servers"></a>Skálázás további folyamat-kiszolgálókkal

Ha a VMware virtuális gépeket vagy fizikai kiszolgálókat [site Recovery](site-recovery-overview.md)használatával replikálja az Azure-ba, alapértelmezés szerint a rendszer a konfigurációs kiszolgáló számítógépre telepíti a kiszolgálót, és a site Recovery és a helyszíni infrastruktúra közötti adatátvitel koordinálására szolgál. A kapacitás növeléséhez és a replikáció központi telepítésének méretezéséhez további önálló folyamat-kiszolgálókat is hozzáadhat. Ez a cikk a kibővíthető folyamat kiszolgálójának beállítását ismerteti.

## <a name="before-you-start"></a>Előkészületek

### <a name="capacity-planning"></a>Kapacitástervezés

Győződjön meg arról, hogy végrehajtotta a [kapacitás megtervezését](site-recovery-plan-capacity-vmware.md) a VMware-replikációhoz. Ez segít annak azonosításában, hogyan és mikor érdemes további folyamat-kiszolgálókat telepíteni.

A 9,24-es verzióról a Process Server új replikálások kiválasztásakor útmutatást adunk. A Process Server bizonyos feltételek alapján kifogástalan, figyelmeztetést és kritikus jelölésű lesz. A Process Server állapotának befolyásolására alkalmas különböző forgatókönyvek megismeréséhez tekintse át a [folyamat kiszolgálójának riasztásait](vmware-physical-azure-monitor-process-server.md#process-server-alerts).

> [!NOTE]
> A klónozott Process Server-összetevők használata nem támogatott. Hajtsa végre a jelen cikkben ismertetett lépéseket minden egyes PS-felskálázáshoz.

### <a name="sizing-requirements"></a>Méretezési követelmények 

Ellenőrizze a táblázatban összefoglalt méretezési követelményeket. Általánosságban elmondható, hogy ha az üzemelő példányt több mint 200 forrásoldali gépre szeretné méretezni, vagy ha 2 TB-nál nagyobb teljes napi adatforgalomra van szüksége, további folyamat-kiszolgálókra van szükség a forgalmi mennyiség kezeléséhez.

| **További folyamat-kiszolgáló** | **Gyorsítótárazott lemez mérete** | **Adatváltozási arány** | **Védett gépek** |
| --- | --- | --- | --- |
|4 vCPU (2 szoftvercsatorna * 2 mag \@ 2,5 GHz), 8 GB memória |300 GB |250 GB vagy kevesebb |85 vagy kevesebb gép replikálása. |
|8 vCPU (2 szoftvercsatorna * 4 mag \@ 2,5 GHz), 12 GB memória |600 GB |250 GB – 1 TB |Replikálás 85-150 gép között. |
|12 vCPU (2 szoftvercsatorna * 6 mag \@ 2,5 GHz) 24 GB memória |1 TB |1 TB – 2 TB |Replikálás 150-225 gép között. |

Az egyes védett forrásoldali gépek mindegyike 100 GB-os 3 lemezzel van konfigurálva.

### <a name="prerequisites"></a>Előfeltételek

A további folyamat-kiszolgáló előfeltételeit a következő táblázat foglalja össze.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Telepítési fájl letöltése

Töltse le a Process Server telepítési fájlját a következőképpen:

1. Jelentkezzen be a Azure Portalba, és keresse meg a Recovery Services-tárolót.
2. Nyissa meg **site Recovery-infrastruktúra**  >  **VMware-és fizikai gépek**  >  **konfigurációs kiszolgálóit** (a VMware & fizikai gépeknél).
3. Válassza ki a konfigurációs kiszolgálót a kiszolgáló adatainak részletezéséhez. Ezután kattintson a **+ folyamat kiszolgáló** elemre.
4. A **Process Server hozzáadása**  >   lapon válassza ki,**hogy hová szeretné telepíteni a Process Servert**, válassza a **helyszíni kibővíthető folyamat kiszolgálójának központi telepítése** lehetőséget.

   ![Kiszolgálók hozzáadása lap](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Kattintson **a Microsoft Azure site Recovery egyesített telepítő letöltése** elemre. Ezzel letölti a telepítési fájl legújabb verzióját.

   > [!WARNING]
   > A Process Server telepítési verziójának meg kell egyeznie a futtatott konfigurációs kiszolgáló verziószámával, vagy annál korábbinak kell lennie. A verziók kompatibilitásának egyszerű módja, ha ugyanazt a telepítőt használja, amelyet legutóbb a konfigurációs kiszolgáló telepítéséhez vagy frissítéséhez használt.

## <a name="install-from-the-ui"></a>Telepítés a felhasználói felületen

A telepítés a következőképpen történik. A kiszolgáló beállítása után áttelepíti a forrásoldali gépeket a használatára.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Telepítés parancssorból

A telepítést a következő parancs futtatásával telepítheti:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMware/NonVMware>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Ahol a parancssori paraméterek a következők:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Például:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMware" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Proxybeállítások fájljának létrehozása

Ha proxyt kell beállítania, a ProxySettingsFilePath paraméter bemenetként veszi fel a fájlt. A fájlt a következőképpen hozhatja létre, és átadhatja a bemeneti ProxySettingsFilePath paraméternek.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Következő lépések
Tudnivalók a [Process Server beállításainak kezeléséről](vmware-azure-manage-process-server.md)
