---
title: Azure Security Center funkciói az operációs rendszer, a gép típusa és a felhő szerint
description: Ismerje meg, hogy mely Azure Security Center szolgáltatások érhetők el az operációs rendszer, a típus és a felhő üzembe helyezése alapján.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2021
ms.author: memildin
ms.openlocfilehash: 7034d1b5f06fcbcead79d85df10042eee3581b0b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369356"
---
# <a name="feature-coverage-for-machines"></a>Szolgáltatások lefedettsége gépekhez

Az alábbi két lap a Windows és Linux rendszerű virtuális gépekhez és kiszolgálókhoz elérhető Azure Security Center funkcióit mutatja be.

## <a name="supported-features-for-virtual-machines-and-servers"></a>A virtuális gépek és a kiszolgálók által támogatott funkciók <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows rendszerű gépek**](#tab/features-windows)

|**Szolgáltatás**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure arc-kompatibilis gépek**|**Azure Defender szükséges**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender az Endpoint Integration szolgáltatáshoz](security-center-wdatp.md)|✔</br>(támogatott verziók esetén)|✔</br>(támogatott verziók esetén)|✔|Yes|
|[Virtuális gép viselkedési elemzési (és biztonsági riasztások)](alerts-reference.md)|✔|✔|✔|Yes|
|[Nem biztonságos biztonsági riasztások](alerts-reference.md#alerts-windows)|✔|✔|✔|Yes|
|[Hálózati biztonsági riasztások](other-threat-protections.md#network-layer)|✔|✔|-|Yes|
|[Virtuális gépek igény szerinti elérése](security-center-just-in-time.md)|✔|-|-|Yes|
|[Natív sebezhetőségi felmérés](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Yes|
|[Fájlintegritás monitorozása](security-center-file-integrity-monitoring.md)|✔|✔|✔|Yes|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Yes|
|[Hálózati Térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Yes|
|[Adaptív hálózat-megerősítés](security-center-adaptive-network-hardening.md)|✔|-|-|Yes|
|[Szabályozási megfelelőségi irányítópult & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Yes|
|Javaslatok és veszélyforrások elleni védelem a Docker által üzemeltetett IaaS-tárolókban|-|-|-|Yes|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Azure: nem<br><br>Arc-kompatibilis: igen|
|Biztonsági konfigurációs beállítások értékelése|✔|✔|✔|Azure: nem<br><br>Arc-kompatibilis: igen|
|[Endpoint Protection-Értékelés](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: nem<br><br>Arc-kompatibilis: igen|
|Lemez titkosításának felmérése|✔</br>( [támogatott forgatókönyvek](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)esetén)|✔|-|No|
|Harmadik féltől származó sebezhetőségi felmérés|✔|-|✔|No|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|No|


### <a name="linux-machines"></a>[**Linux rendszerű gépek**](#tab/features-linux)

|**Szolgáltatás**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure arc-kompatibilis gépek**|**Azure Defender szükséges**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender az Endpoint Integration szolgáltatáshoz](security-center-wdatp.md)|-|-|-|-|
|[Virtuális gép viselkedési elemzési (és biztonsági riasztások)](./azure-defender.md)|✔</br>(támogatott verziók esetén)|✔</br>(támogatott verziók esetén)|✔|Yes|
|[Nem biztonságos biztonsági riasztások](alerts-reference.md#alerts-windows)|-|-|-|Yes|
|[Hálózati biztonsági riasztások](other-threat-protections.md#network-layer)|✔|✔|-|Yes|
|[Virtuális gépek igény szerinti elérése](security-center-just-in-time.md)|✔|-|-|Yes|
|[Natív sebezhetőségi felmérés](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Yes|
|[Fájlintegritás monitorozása](security-center-file-integrity-monitoring.md)|✔|✔|✔|Yes|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Yes|
|[Hálózati Térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Yes|
|[Adaptív hálózat-megerősítés](security-center-adaptive-network-hardening.md)|✔|-|-|Yes|
|[Szabályozási megfelelőségi irányítópult & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Yes|
|Javaslatok és veszélyforrások elleni védelem a Docker által üzemeltetett IaaS-tárolókban|✔|✔|✔|Yes|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Azure: nem<br><br>Arc-kompatibilis: igen|
|Biztonsági konfigurációs beállítások értékelése|✔|✔|✔|Azure: nem<br><br>Arc-kompatibilis: igen|
|[Endpoint Protection-Értékelés](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|No|
|Lemez titkosításának felmérése|✔</br>( [támogatott forgatókönyvek](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)esetén)|✔|-|No|
|Harmadik féltől származó sebezhetőségi felmérés|✔|-|✔|No|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|No|

--- 


> [!TIP]
>A csak az Azure Defender szolgáltatással elérhető funkciókkal való kísérletezéshez 30 napos próbaverziót regisztrálhat. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.


## <a name="supported-endpoint-protection-solutions"></a>Támogatott Endpoint Protection-megoldások <a name="endpoint-supported"></a>

A következő táblázat a következőkhöz nyújt mátrixot:

 - Azt határozza meg, hogy használható-e a Azure Security Center az egyes megoldások telepítéséhez.
 - Melyik Endpoint Protection-megoldás Security Center képes felderíteni. Ha a listán szereplő Endpoint Protection-megoldás észlelhető, Security Center nem javasolja, hogy telepítsen egyet.

További információ az egyes védelemekhez kapcsolódó javaslatok létrehozásáról: [Endpoint Protection Értékelés és javaslatok](security-center-endpoint-protection.md).

| Endpoint Protection| Platformok | Security Center telepítése | Security Center felderítése |
|------|------|-----|-----|
| Microsoft Defender víruskereső| Windows Server 2016 vagy újabb| Nincs, az operációs rendszerbe van beépítve| Yes |
| System Center Endpoint Protection (Microsoft-kártevőirtó) | Windows Server 2012 R2, 2012, 2008 R2 (lásd az alábbi megjegyzést) | Bővítmény útján | Yes |
| Trend Micro – Deep Security | Windows Server termékcsalád  | Nem | Igen |
| Symantec v12.1.1100+| Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Linux Server termékcsalád  | Nem | Igen |
| Sophos v9 +| Linux Server termékcsalád  | Nem | Igen |

> [!NOTE]
> A System Center Endpoint Protection (SCEP) észlelése Windows Server 2008 R2 rendszerű virtuális gépen a PowerShell (v 3.0 vagy újabb) után telepíteni kell a SCEP-t.



## <a name="feature-support-in-government-clouds"></a>Szolgáltatások támogatása a kormányzati felhőkben

| Szolgáltatás/szolgáltatás | US Gov | Kínai gov |
|------|:----:|:----:|
|Igény szerinti virtuális gépekhez [való hozzáférés](security-center-just-in-time.md) (1)|✔|✔|
|[Fájl integritásának figyelése](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[Adaptív alkalmazások vezérlői](security-center-adaptive-application.md) (1)|✔|✔|
|[Adaptív hálózat megerősítése](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Docker-gazdagép megerősítése](harden-docker-hosts.md) (1)|✔|✔|
|[A számítógépek integrált sebezhetőségi felmérése](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[Microsoft Defender a végponthoz](harden-docker-hosts.md) (1)|✔|-|
|[AWS-fiók összekötése](quickstart-onboard-aws.md) (1)|-|-|
|[GCP-fiók összekötése](quickstart-onboard-gcp.md) (1)|-|-|
|[Folyamatos exportálás](continuous-export.md)|✔|✔ (2)|
|[Munkafolyamat-automatizálás](workflow-automation.md)|✔|✔|
|[Javaslat alóli kivétel szabályai](exempt-resource.md)|-|-|
|[Riasztáselnyomási szabályok](alerts-suppression-rules.md)|✔|✔|
|[Biztonsági riasztások e-mail-értesítései](security-center-provide-security-contact-details.md)|✔|✔|
|[Tárgyieszköz-leltár](asset-inventory.md)|✔|✔|
|[Azure Defender App Service-hez](defender-for-app-service-introduction.md)|-|-|
|[Azure Defender tároláshoz](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender SQL-hez](defender-for-sql-introduction.md)|✔|✔ (2)|
|[Azure Defender Key Vaulthoz](defender-for-key-vault-introduction.md)|-|-|
|[Azure Defender a Resource Managerhez](defender-for-resource-manager-introduction.md)|-|-|
|[Azure Defender DNS-hez](defender-for-dns-introduction.md)|-|-|
|[Azure Defender tárolóregisztrációs adatbázisokhoz](defender-for-container-registries-introduction.md)|✔ (2)|✔ (2)|
|[Azure Defender Kuberneteshez](defender-for-kubernetes-introduction.md)|✔|✔|
|[Kubernetes munkaterhelés-védelem](kubernetes-workload-protections.md)|-|-|
|||

(1) **a kiszolgálókhoz Azure Defender** szükséges

(2) részleges


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogy [a Security Center hogyan gyűjt adatokat az log Analytics ügynökkel](security-center-enable-data-collection.md).
- Megtudhatja [, hogyan kezeli és védi a Security Center az információkat](security-center-data-security.md).
- Tekintse át a [Security centert támogató platformokat](security-center-os-coverage.md).