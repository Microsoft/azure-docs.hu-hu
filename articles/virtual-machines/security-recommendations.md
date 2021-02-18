---
title: Biztonsági javaslatok az Azure-beli virtuális gépekhez
description: Alkalmazza ezeket a javaslatokat az Azure-beli virtuális gépekre a megosztott felelősségi modellben ismertetett biztonsági kötelezettségek teljesítése érdekében, és javítsa az üzemelő példányok általános biztonságát.
author: msmbaldwin
manager: RKarlin
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.reviewer: cynthn
ms.openlocfilehash: afb985ecbe91835369738966e39e751f06f8d796
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571335"
---
# <a name="security-recommendations-for-virtual-machines-in-azure"></a>Biztonsági javaslatok az Azure-beli virtuális gépekhez


Ez a cikk az Azure Virtual Machines biztonsági javaslatait tartalmazza. Kövesse ezeket az ajánlásokat a modellben ismertetett biztonsági kötelezettségek teljesítéséhez a közös felelősség érdekében. A javaslatok a webalkalmazás-megoldások általános biztonságának javításához is segítséget nyújtanak. További információ arról, hogy a Microsoft miként teljesíti a szolgáltatói felelősségeket, lásd: [a felhőalapú számítástechnika megosztott feladatai](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Ennek a cikknek a javaslatait a Azure Security Center automatikusan tudja kezelni. A Azure Security Center az Azure-beli erőforrásainak első védelmi vonala. Rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezt követően a biztonsági rések kezelésének módját ajánljuk. További információ: [biztonsági javaslatok a Azure Security Center](../security-center/security-center-recommendations.md).

A Azure Security Centertel kapcsolatos általános információkért lásd: [Mi az Azure Security Center?](../security-center/security-center-introduction.md)

## <a name="general"></a>Általános

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Egyéni virtuálisgép-rendszerképek létrehozásakor alkalmazza a legújabb frissítéseket. | A lemezképek létrehozása előtt telepítse az operációs rendszer legújabb frissítéseit, és minden olyan alkalmazáshoz, amely a lemezkép részét képezi majd.  | - |
| Tartsa naprakészen a virtuális gépeket. | Az Azure-ban lévő Windows-és Linux-számítógépeken az operációs rendszer frissítéseinek kezeléséhez használhatja a Azure Automation [Update Management](../automation/update-management/overview.md) megoldását. | [Igen](../security-center/asset-inventory.md) |
| Készítse elő a virtuális gépek biztonsági mentését. | [Azure Backup](../backup/backup-overview.md) segít az alkalmazásadatok védelme terén, és minimális működési költségekkel jár. Az alkalmazások hibái sérültek az adataikat, és az emberi hibák hibákat okozhatnak az alkalmazásaiban. Azure Backup védi a Windows és a Linux rendszerű virtuális gépeket. | - |
| Több virtuális gép használata nagyobb rugalmasság és rendelkezésre állás érdekében. | Ha a virtuális gép olyan alkalmazásokat futtat, amelyek számára elérhetőnek kell lennie, használjon több virtuális gépet vagy [rendelkezésre állási készletet](./manage-availability.md). | - |
| Egy üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégiát kell alkalmaznia. | Azure Site Recovery lehetővé teszi az üzletmenet folytonosságának támogatására szolgáló különböző lehetőségek kiválasztását. Különböző replikációs és feladatátvételi forgatókönyveket támogat. További információ:  [About site Recovery](../site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Adatbiztonság

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Operációs rendszer lemezek titkosítása. | A [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) segítségével titkosíthatja Windows-és Linux-IaaS VM-lemezeit. A szükséges kulcsok nélkül a titkosított lemezek tartalma nem olvasható. A lemezes titkosítás védi a tárolt adatok jogosulatlan hozzáférését, amely egyébként a lemez másolásakor lenne lehetséges.| [Igen](../security-center/asset-inventory.md) |
| Adatlemezek titkosítása. | A [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) segítségével titkosíthatja Windows-és Linux-IaaS VM-lemezeit. A szükséges kulcsok nélkül a titkosított lemezek tartalma nem olvasható. A lemezes titkosítás védi a tárolt adatok jogosulatlan hozzáférését, amely egyébként a lemez másolásakor lenne lehetséges.| -  |
| A telepített szoftverek korlátozása. | A telepített szoftverek korlátozása a megoldás sikeres alkalmazásához szükséges értékre. Ez az útmutató segít csökkenteni a megoldás támadási felületét. | - |
| Vírusirtó vagy antimalware használata. | Az Azure-ban olyan biztonsági gyártóktól származó kártevő szoftvereket is használhat, mint például a Microsoft, a Symantec, a Trend Micro és a Kaspersky. Ez a szoftver segít megvédeni a virtuális gépeket a kártékony fájlokkal, a reklámprogramokkal és más fenyegetésekkel szemben. Az alkalmazás munkaterhelései alapján telepítheti a Microsoft antimalware-t. A Microsoft antimalware csak Windows rendszerű gépeken érhető el. Használja az alapszintű biztonságos vagy a speciális egyéni konfigurációt. További információ: [Microsoft antimalware for Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md). | - |
| Kulcsok és titkos kódok biztonságos tárolása. | Egyszerűbbé teheti a titkok és kulcsok felügyeletét azáltal, hogy az alkalmazás tulajdonosait biztonságos, központilag felügyelt lehetőséggel látja el. Ez a kezelés csökkenti a véletlen sérülés vagy szivárgás kockázatát. Azure Key Vault biztonságosan tárolhatók a kulcsokat hardveres biztonsági modulokban (HSM), amelyek a 2. szintű FIPS 140-2-es szintre vannak hitelesítve. Ha a kulcsokat és titkos kulcsokat az FIPs 140,2 3. szintű használatával szeretné tárolni, használhatja az [Azure DEDIKÁLT HSM](../dedicated-hsm/overview.md)-et. | - |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés 

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| A virtuális gép hitelesítésének központosítása. | A Windows-és Linux-alapú virtuális gépek hitelesítését [Azure Active Directory hitelesítés](../active-directory/develop/authentication-vs-authorization.md)használatával központosíthatja. | - |

## <a name="monitoring"></a>Figyelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| A virtuális gépek figyelése. | Az Azure-beli virtuális gépek és a virtuálisgép-méretezési csoportok állapotának figyeléséhez [Azure monitor for VMst](../azure-monitor/vm/vminsights-overview.md) használhat. A virtuális gépekkel kapcsolatos teljesítményproblémák a szolgáltatás megszakadásához vezethetnek, ami sérti a rendelkezésre állás biztonsági elvét. | - |

## <a name="networking"></a>Hálózatkezelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| A felügyeleti portok elérésének korlátozása. | A támadók nyilvános Felhőbeli IP-tartományokat vizsgálnak meg a nyílt felügyeleti portok számára, és megkísérlik a közös jelszavakat, valamint az ismert, nem javított biztonsági réseket. Igény szerinti [(JIT)](../security-center/security-center-just-in-time.md) virtuálisgép-hozzáférést használhat az Azure-beli virtuális gépek bejövő forgalmának zárolására, így csökkentve a támadásoknak való kitettséget, miközben egyszerű kapcsolatokat biztosít a virtuális gépekhez, amikor szükségesek. | - |
| Korlátozza a hálózati hozzáférést. | A hálózati biztonsági csoportok lehetővé teszik a hálózati hozzáférés korlátozását és a kitett végpontok számának szabályozását. További információ: [hálózati biztonsági csoport létrehozása, módosítása vagy törlése](../virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Következő lépések

A további biztonsági követelmények megismeréséhez forduljon az alkalmazás-szolgáltatóhoz. A biztonságos alkalmazások fejlesztésével kapcsolatos további információkért lásd: a [biztonságos fejlesztés dokumentációja](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).