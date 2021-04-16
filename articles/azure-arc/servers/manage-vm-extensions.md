---
title: Virtuálisgép-bővítmény kezelése engedélyezett Azure Arc kiszolgálókkal
description: Azure Arc-kompatibilis kiszolgálók kezelhetik olyan virtuálisgép-bővítmények üzembe helyezését, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak nem Azure-beli virtuális gépekhez.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: 67f1b5b3db6ef446342e8381d54d487af1f3426a
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389791"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Virtuálisgép-bővítmények kezelése az Azure Arc-kompatibilis kiszolgálókon

A virtuálisgép-bővítmények kis méretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha például egy virtuális gép szoftvertelepítést, víruskereső védelmet vagy parancsfájl futtatását igényli, a virtuálisgép-bővítmény használható.

Azure Arc-kompatibilis kiszolgálók lehetővé teszik azure-beli virtuálisgép-bővítmények üzembe helyezését nem Azure-beli Windows és Linux rendszerű virtuális gépeken, így leegyszerűsíthető a hibrid gép kezelése azok életciklusa során. A virtuálisgép-bővítmények a következő módszerekkel kezelhetők a hibrid gépeken vagy az Arc-kompatibilis kiszolgálók által felügyelt kiszolgálókon:

- A [Azure Portal](manage-vm-extensions-portal.md)
- Az [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager sablonok](manage-vm-extensions-template.md)

> [!NOTE]
> Azure Arc kompatibilis kiszolgálók nem támogatják a virtuálisgép-bővítmények Azure-beli virtuális gépeken való üzembe helyezését és felügyeletét. Azure-beli virtuális gépekhez tekintse meg a virtuálisgép-bővítmények [áttekintését ismertető cikket.](../../virtual-machines/extensions/overview.md)

## <a name="key-benefits"></a>Főbb előnyök

Azure Arc engedélyezett kiszolgálók virtuálisgép-bővítmény támogatása a következő fő előnyöket biztosítja:

- A Log Analytics-ügynök virtuálisgép-bővítményének engedélyezésével [naplóadatokat](../../azure-monitor/logs/data-platform-logs.md) gyűjthet Azure Monitor naplóadatok elemzéséhez. Ez a különböző típusú forrásokból származó adatokon végzett összetett elemzésekhez hasznos.

- A [Azure Monitor for VMs](../../azure-monitor/vm/vminsights-overview.md)segítségével elemezheti a Windows és Linux rendszerű virtuális gépek teljesítményét, és figyelheti a folyamatukat és a más erőforrásoktól és külső folyamatoktól való függőségeiket. Ez a Log Analytics-ügynök és a függőségi ügynök virtuálisgép-bővítményeinek engedélyezésével érhető el.

- Szkriptek letöltése és végrehajtása hibrid csatlakoztatott gépeken az egyéni szkriptbővítmény használatával. Ez a bővítmény központi telepítés utáni konfigurációhoz, szoftvertelepítéshez, illetve egyéb konfigurációs vagy felügyeleti feladatokhoz hasznos.

- A tárolóban tárolt tanúsítványok automatikus [frissítése Azure Key Vault.](../../key-vault/general/overview.md)

## <a name="availability"></a>Rendelkezésre állás

A virtuálisgép-bővítmény funkciói csak a támogatott régiók [listájában érhetők el.](overview.md#supported-regions) Győződjön meg arról, hogy a gépét az alábbi régiók egyikében fogja bevetni.

## <a name="extensions"></a>Bővítmények

Ebben a kiadásban a következő virtuálisgép-bővítményeket támogatjuk Windows és Linux rendszerű gépeken.

További információ a Azure Connected Machine ügynökcsomagról és a Bővítményügynök összetevővel kapcsolatos részletekről: [Ügynök áttekintése.](agent-overview.md#agent-component-details)

> [!NOTE]
> A közelmúltban eltávolítottuk a DSC virtuálisgép-bővítmény támogatását az Arc-kompatibilis kiszolgálók esetében. Azt is javasoljuk, hogy használja az egyéni szkriptbővítményt a kiszolgáló vagy gép üzembe helyezés utáni konfigurációjának kezeléséhez.

### <a name="windows-extensions"></a>Windows-bővítmények

|Mellék |Publisher |Típus |További információ |
|----------|----------|-----|-----------------------|
|Azure Defender biztonsági rések integrált ellenőrzőeszköze |Qualys |WindowsAgent.AzureSecurityCenter |[Azure Defender azure-beli és hibrid gépekre vonatkozó biztonsági rések felmérésére vonatkozó integrált megoldás](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Egyéni parancsfájl-bővítmény |Microsoft.Compute | CustomScriptExtension |[Egyéni Windows-szkriptbővítmény](../../virtual-machines/extensions/custom-script-windows.md)|
|Log Analytics-ügynök |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Log Analytics virtuálisgép-bővítmény Windowshoz](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor for VMs (elemzések) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [Függőségi ügynök virtuálisgép-bővítménye Windowshoz](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault tanúsítványszinkronizálás | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [Key Vault windowsos virtuálisgép-bővítmény](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure Monitor-ügynök |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[A Azure Monitor ügynök telepítése (előzetes verzió)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Linux-bővítmények

|Mellék |Publisher |Típus |További információ |
|----------|----------|-----|-----------------------|
|Azure Defender biztonsági rések beolvasója |Qualys |LinuxAgent.AzureSecurityCenter |[Azure Defender Azure-beli és hibrid gépek biztonsági rések felmérésére kínál integrált megoldást](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Egyéni parancsfájl-bővítmény |Microsoft.Azure.Extensions |CustomScript |[Egyéni Linux-szkriptbővítmény 2-es verziója](../../virtual-machines/extensions/custom-script-linux.md) |
|Log Analytics-ügynök |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Log Analytics virtuálisgép-bővítmény Linuxhoz](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor for VMs (elemzések) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[Függőségi ügynök virtuálisgép-bővítménye Linuxhoz](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault tanúsítványszinkronizálás | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [Key Vault linuxos virtuálisgép-bővítmény létrehozása](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure Monitor-ügynök |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[A Azure Monitor ügynök telepítése (előzetes verzió)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az előfizetés alábbi Azure-erőforrás-szolgáltatóitól függ:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Ha még nincsenek regisztrálva, kövesse az Azure-erőforrás-szolgáltatók [regisztrálása alatt található lépéseket.](agent-overview.md#register-azure-resource-providers)

Tekintse át az előző táblázatban hivatkozott virtuálisgép-bővítmények dokumentációját, és ellenőrizze, hogy rendelkezik-e hálózati vagy rendszerkövetelményekkel. Ezzel elkerülheti, hogy csatlakozási problémákat tapasztalja az adott virtuálisgép-bővítményre támaszkodó Azure-szolgáltatással vagy -funkcióval kapcsolatban.

### <a name="log-analytics-vm-extension"></a>Log Analytics virtuálisgép-bővítmény

A Linuxhoz készült Log Analytics-ügynök virtuálisgép-bővítményéhez a Python 2.x telepítése szükséges a célgépen.

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault virtuálisgép-bővítmény (előzetes verzió)

A Key Vault (előzetes verzió) virtuálisgép-bővítmény nem támogatja a következő Linux operációs rendszereket:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

A virtuálisgép-Key Vault (előzetes verzió) üzembe helyezése csak a következő használatával támogatott:

- Azure CLI
- A Azure PowerShell
- Azure Resource Manager-sablon

A bővítmény üzembe helyezése előtt el kell látnia a következőket:

1. [Tároló és tanúsítvány létrehozása](../../key-vault/certificates/quick-create-portal.md) (önaírt vagy importált).

2. Adjon hozzáférést Azure Arc engedélyezett kiszolgálónak a titkos tanúsítványhoz. Ha az [RBAC](../../key-vault/general/rbac-guide.md)előzetes kiadását használja, keressen rá a Azure Arc-erőforrás nevére, és rendelje hozzá a **Key Vault Secrets User (előzetes verzió)** szerepkört. Ha a hozzáférési szabályzatot [Key Vault,](../../key-vault/general/assign-access-policy-portal.md)rendeljen Secret **Get** engedélyeket Azure Arc erőforrás rendszer által hozzárendelt identitásához.

### <a name="connected-machine-agent"></a>Csatlakoztatott gép ügynöke

Ellenőrizze, hogy a gép [megfelel-e a](agent-overview.md#supported-operating-systems) Windows és a Linux operációs rendszer támogatott verzióinak a Azure Connected Machine ügynökhöz.

A Connected Machine ügynöknek a windowsos és linuxos rendszeren támogatott minimális verziója az 1.0-s kiadás.

Ha frissítenie kell a gépet a szükséges ügynök verziójára, tekintse meg a [következőt: Ügynök frissítése.](manage-agent.md#upgrading-agent)

## <a name="next-steps"></a>Következő lépések

A virtuálisgép-bővítményeket az [Azure CLI,](manage-vm-extensions-cli.md)a [Azure PowerShell](manage-vm-extensions-powershell.md), a Azure Portal [vagy](manage-vm-extensions-portal.md)a Azure Resource Manager használatával [helyezheti üzembe, kezelheti és távolíthatja el.](manage-vm-extensions-template.md)
