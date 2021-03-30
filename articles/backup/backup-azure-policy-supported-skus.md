---
title: Az Azure Policy támogatott virtuálisgép-termékváltozatai
description: A biztonsági mentés által biztosított beépített Azure-szabályzatok által támogatott, támogatott virtuális gépek (közzétevő, képajánlat és rendszerkép SKU) leírását ismertető cikk
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 4658d1beffe707682e173491edea1eac0db9c811
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "82183619"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Az Azure Policy támogatott virtuálisgép-termékváltozatai

A Azure Backup beépített szabályzatot biztosít (Azure Policy használatával), amely az **előfizetésben vagy az erőforráscsoport egy adott helyén lévő összes Azure-beli virtuális** géphez hozzárendelhető. Ha a szabályzatot egy adott hatókörhöz rendeli hozzá, az abban a hatókörben létrehozott összes új virtuális gépet a rendszer automatikusan konfigurálja egy meglévő tárolóba **ugyanazon a helyen és előfizetésben**. Az alábbi táblázat felsorolja a szabályzat által támogatott összes VM-SKU-t.

## <a name="supported-vms"></a>Támogatott virtuális gépek *

**Házirend neve:** Egy helyen lévő virtuális gépek biztonsági mentésének konfigurálása ugyanazon a helyen lévő központi tárba

Rendszerkép kiadója | Rendszerkép-ajánlat | Rendszerkép SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter (2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Datacenter (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter (2016-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter – Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter – Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core with containers (2016-Datacenter-with-containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Távoli asztal munkamenet-gazdagép 2016 (2016-Datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (2019-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core tárolókkal (2019-Datacenter-Core-with-containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core tárolókkal (2019-Datacenter-Core-with-containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter tárolókkal (2019-Datacenter-with-containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter tárolókkal (2019-Datacenter-with-containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-CN) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-with-containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-with-containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Összes rendszerkép SKU
MicrosoftSQLServer | SQL2016SP1-WS2016 | Összes rendszerkép SKU
MicrosoftSQLServer | SQL2016-WS2016 | Összes rendszerkép SKU
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Összes rendszerkép SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Összes rendszerkép SKU
MicrosoftSQLServer | SQL2016-WS2012R2 | Összes rendszerkép SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Összes rendszerkép SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Összes rendszerkép SKU
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Összes rendszerkép SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Összes rendszerkép SKU
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Összes rendszerkép SKU
MicrosoftRServer | MLServer-WS2016 | Összes rendszerkép SKU
MicrosoftVisualStudio | VisualStudio | Összes rendszerkép SKU
MicrosoftVisualStudio | Windows | Összes rendszerkép SKU
MicrosoftDynamicsAX | Dynamics | Pre-REQ-AX7-beépített-U8
Microsoft – ADS | Windows – adattudomány – virtuális gép | Összes rendszerkép SKU
MicrosoftWindowsDesktop | Windows-10 | Összes rendszerkép SKU
RedHat | RHEL | 6,7, 6,8, 6,9, 6,10, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7
RedHat | RHEL – SAP-HANA | 6,7, 7,2, 7,3
SUSE | SLES | 12. X
SUSE | SLES – HPC | 12. X
SUSE | SLES-HPC-Priority | 12. X
SUSE | SLES – SAP | 12. X
SUSE | SLES – SAP – BYOS | 12. X
SUSE | SLES-Priority | 12. X
SUSE | SLES – BYOS | 12. X
SUSE | SLES – SAPCAL | 12. X
SUSE | SLES-Standard | 12. X
Canonical | UbuntuServer | 14.04.0 – LTS
Canonical | UbuntuServer | 14.04.1 – LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3 – LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5 – NAPI – LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16,04 – NAPONTA – LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0 – LTS
Canonical | UbuntuServer | 18,04 – NAPONTA – LTS
Canonical | UbuntuServer | 18,04 – LTS
Oracle | Oracle-Linux | 6,8, 6,9, 6,10, 7,3, 7,4, 7,5, 7,6
OpenLogic | CentOS | 6. X, 7. X
OpenLogic | CentOS – LVM | 6. X, 7. X
OpenLogic | CentOS – CSATOLÓJÁHOZ nincs SR | 6. X, 7. X
cloudera | cloudera – CentOS-os | 7. X
