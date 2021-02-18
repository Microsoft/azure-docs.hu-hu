---
title: Virtuálisgép-bővítmények kezelése az Azure arc-kompatibilis kiszolgálókkal
description: Az Azure arc-kompatibilis kiszolgálók kezelhetik azokat a virtuálisgép-bővítmények központi telepítését, amelyek a telepítés utáni konfigurálást és az automatizálási feladatokat nem Azure-beli virtuális gépekkel is rendelkeznek.
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: b39149eb7ac572ac3bd50bb6303f28d2340f387d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580854"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Virtuálisgép-bővítmények kezelése az Azure Arc-kompatibilis kiszolgálókon

A virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha például egy virtuális gépnek szoftver telepítésére, vírusvédelmi védelemre vagy parancsfájl futtatására van szüksége, a rendszer egy virtuálisgép-bővítményt is használhat.

Az Azure arc-kompatibilis kiszolgálók lehetővé teszik az Azure-beli virtuálisgép-bővítmények üzembe helyezését a nem Azure-beli Windows-és Linux-alapú virtuális gépeken, így egyszerűbbé válik a hibrid gép felügyelete életciklusuk során. A virtuálisgép-bővítmények a következő módszerekkel kezelhetők a hibrid gépeken vagy az arc-kompatibilis kiszolgálók által felügyelt kiszolgálókon:

- A [Azure Portal](manage-vm-extensions-portal.md)
- Az [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager-sablonok](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Főbb előnyök

Az Azure arc-kompatibilis kiszolgálók virtuálisgép-bővítmények támogatása a következő főbb előnyöket nyújtja:

- [Azure Automation állapot konfigurációjának](../../automation/automation-dsc-overview.md) használatával központilag tárolhatja a konfigurációkat, és megtarthatja a DSC virtuálisgép-bővítményen keresztül engedélyezett hibrid csatlakoztatott gépek kívánt állapotát.

- Begyűjti a naplózási adatokat az elemzéshez [Azure monitor a naplókat](../../azure-monitor/logs/data-platform-logs.md) az log Analytics ügynök virtuálisgép-bővítményének használatával. Ez hasznos lehet a különböző típusú forrásokból származó adatok összetett elemzéséhez.

- A [Azure monitor for VMS](../../azure-monitor/vm/vminsights-overview.md)a elemzi a Windows-és Linux-alapú virtuális gépek teljesítményét, és figyeli a folyamatokat és a függőségeket más erőforrásokra és külső folyamatokra. Ez a Log Analytics ügynök és a függőségi ügynök virtuálisgép-bővítményeinek engedélyezésével érhető el.

- Parancsfájlokat tölthet le és futtathat a hibrid csatlakoztatott gépeken az egyéni szkriptek bővítmény használatával. Ez a bővítmény az üzembe helyezési konfiguráció, a Szoftvertelepítés vagy bármely egyéb konfigurációs vagy felügyeleti feladat után hasznos.

- [Azure Key Vault](../../key-vault/general/overview.md)tárolt tanúsítványok automatikus frissítése.

## <a name="availability"></a>Rendelkezésre állás

A VM-bővítmény funkció csak a [támogatott régiók](overview.md#supported-regions)listájában érhető el. Győződjön meg arról, hogy a számítógépe ezen régiók egyikében található.

## <a name="extensions"></a>Bővítmények

Ebben a kiadásban a következő virtuálisgép-bővítményeket támogatjuk a Windows és Linux rendszerű gépeken.

Ha többet szeretne megtudni az Azure-beli csatlakoztatott gépi ügynök csomagról és a bővítmény ügynök összetevőjéről, tekintse meg az [ügynök áttekintése](agent-overview.md#agent-component-details)című témakört.

### <a name="windows-extensions"></a>Windows-bővítmények

|Mellék |Publisher |Típus |További információ |
|----------|----------|-----|-----------------------|
|Azure Defender integrált biztonsági rések képolvasó |Qualys |WindowsAgent.AzureSecurityCenter |[Az Azure Defender integrált sebezhetőségi felmérési megoldása az Azure-hoz és a hibrid gépekhez](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Egyéni parancsfájl-bővítmény |Microsoft.Compute | CustomScriptExtension |[Egyéni Windows-szkriptek bővítménye](../../virtual-machines/extensions/custom-script-windows.md)|
|PowerShell DSC |Microsoft. PowerShell |DSC |[Windows PowerShell DSC-bővítmény](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics-ügynök |Microsoft. EnterpriseCloud. monitoring |MicrosoftMonitoringAgent |[Log Analytics virtuálisgép-bővítmény a Windowshoz](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor for VMs (bepillantások) |Microsoft. Azure. monitoring. DependencyAgent |DependencyAgentWindows | [Függőségi ügynök virtuálisgép-bővítménye Windows rendszerhez](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault tanúsítvány-szinkronizálás | Microsoft. Azure. key. Vault |KeyVaultForWindows | [A Windows rendszerhez készült virtuálisgép-bővítmény Key Vault](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure Monitor-ügynök |Microsoft. Azure. monitor |AzureMonitorWindowsAgent |[A Azure Monitor-ügynök telepítése (előzetes verzió)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Linux-bővítmények

|Mellék |Publisher |Típus |További információ |
|----------|----------|-----|-----------------------|
|Azure Defender integrált biztonsági rések képolvasó |Qualys |LinuxAgent.AzureSecurityCenter |[Az Azure Defender integrált sebezhetőségi felmérési megoldása az Azure-hoz és a hibrid gépekhez](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Egyéni parancsfájl-bővítmény |Microsoft. Azure. Extensions |CustomScript |[Linux Custom script Extension 2. verzió](../../virtual-machines/extensions/custom-script-linux.md) |
|PowerShell DSC |Microsoft. OSTCExtensions |DSCForLinux |[PowerShell DSC-bővítmény Linux rendszerhez](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics-ügynök |Microsoft. EnterpriseCloud. monitoring |OmsAgentForLinux |[A Linux rendszerhez készült virtuálisgép-bővítmény Log Analytics](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor for VMs (bepillantások) |Microsoft. Azure. monitoring. DependencyAgent |DependencyAgentLinux |[Függőségi ügynök linuxos virtuálisgép-bővítménye](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault tanúsítvány-szinkronizálás | Microsoft. Azure. key. Vault |KeyVaultForLinux | [A Linux rendszerhez készült virtuálisgép-bővítmény Key Vault](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure Monitor-ügynök |Microsoft. Azure. monitor |AzureMonitorLinuxAgent |[A Azure Monitor-ügynök telepítése (előzetes verzió)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az előfizetés alábbi Azure-erőforrás-szolgáltatói függ:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Ha még nincsenek regisztrálva, kövesse az [Azure erőforrás-szolgáltatók regisztrálása](agent-overview.md#register-azure-resource-providers)című témakör lépéseit.

Ügyeljen arra, hogy az előző táblázatban hivatkozott minden virtuálisgép-bővítmény dokumentációjában ellenőrizze, hogy rendelkezik-e hálózati vagy rendszerkövetelményekkel. Ez segít elkerülni az Azure-szolgáltatásokkal vagy-szolgáltatásokkal kapcsolatos kapcsolódási problémák elkerülését, amely az adott virtuálisgép-bővítményre támaszkodik.

### <a name="log-analytics-vm-extension"></a>Log Analytics virtuálisgép-bővítmény

A Linux rendszerhez készült Log Analytics Agent virtuálisgép-bővítményhez a Python 2. x verziója szükséges a célszámítógépen. 

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM-bővítmény (előzetes verzió)

A Key Vault VM-bővítmény (előzetes verzió) nem támogatja a következő Linux operációs rendszereket:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

A Key Vault virtuálisgép-bővítmény (előzetes verzió) üzembe helyezése csak a következő használatával támogatott:

- Azure CLI
- A Azure PowerShell
- Azure Resource Manager-sablon

A bővítmény telepítése előtt a következőket kell elvégeznie:

1. [Hozzon létre egy tárolót és egy tanúsítványt](../../key-vault/certificates/quick-create-portal.md) (önaláírt vagy importált).

2. Adja meg az Azure arc számára engedélyezett kiszolgáló hozzáférését a tanúsítvány titkos kódjához. Ha a [RBAC előzetes](../../key-vault/general/rbac-guide.md)verzióját használja, keresse meg az Azure arc-erőforrás nevét, és rendelje hozzá a **Key Vault Secrets User (előzetes verzió)** szerepkört. Ha [Key Vault hozzáférési szabályzatot](../../key-vault/general/assign-access-policy-portal.md)használ, rendeljen titkos **Get** engedélyeket az Azure arc-erőforrás rendszerhez rendelt identitásához.

### <a name="connected-machine-agent"></a>Csatlakoztatott számítógép ügynöke

Ellenőrizze, hogy a gép megfelel-e a Windows és Linux operációs rendszer [támogatott verzióival](agent-overview.md#supported-operating-systems) az Azure Connected Machine Agent ügynökhöz.

A Windows és Linux rendszeren a szolgáltatással támogatott csatlakoztatott gépi ügynök minimális verziója a 1,0-es kiadás.

Ha a gépet a szükséges ügynök verziójára szeretné frissíteni, tekintse meg az [ügynök frissítése](manage-agent.md#upgrading-agent)című témakört.

## <a name="next-steps"></a>Következő lépések

Virtuálisgép-bővítményeket telepíthet, kezelhet és eltávolíthat az [Azure CLI](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), [Azure Portal](manage-vm-extensions-portal.md)vagy [Azure Resource Manager sablonok](manage-vm-extensions-template.md)használatával.
