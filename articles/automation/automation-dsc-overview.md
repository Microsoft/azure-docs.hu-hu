---
title: Azure Automation állapot konfigurációjának áttekintése
description: Ez a cikk áttekintést nyújt a Azure Automation állapotának konfigurálásáról.
keywords: PowerShell DSC, a kívánt állapot konfigurálása, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 01/26/2021
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 752d7f86941967c218b3a57fa163698b9f502057
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897020"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation állapot konfigurációjának áttekintése

Azure Automation állapot-konfiguráció egy [Azure-beli](/powershell/scripting/dsc/configurations/configurations) konfiguráció-felügyeleti szolgáltatás, amely lehetővé teszi a PowerShell kívánt állapot-konfiguráció (DSC) konfigurációjának írását, kezelését és fordítását bármely Felhőbeli vagy helyszíni adatközpontban. A szolgáltatás a [DSC-erőforrásokat](/powershell/scripting/dsc/resources/resources)is importálja, és a konfigurációkat a célként megadott csomópontokhoz rendeli hozzá a felhőben. A Azure Portal Azure Automation állapot konfigurációját az **állapot konfigurálása (DSC)** elemre kattintva érheti el a **konfiguráció** felügyelete alatt.

A Azure Automation állapot-konfiguráció számos különböző gép kezelésére használható:

- Azure-beli virtuális gépek
- Azure-beli virtuális gépek (klasszikus)
- Fizikai/virtuális Windowsos gépek a helyszínen vagy az Azure-tól eltérő felhőben (beleértve az AWS EC2-példányokat)
- Fizikai/virtuális Linux rendszerű számítógépek a helyszínen, az Azure-ban vagy az Azure-tól eltérő felhőben

Ha nem áll készen a számítógép-konfiguráció felhőből való kezelésére, akkor a Azure Automation állapot konfigurációját csak jelentési végpontként használhatja. Ezzel a funkcióval a DSC-n keresztül állíthatja be (leküldéses) konfigurációkat, és megtekintheti a jelentéskészítés részleteit Azure Automationban.

> [!NOTE]
> Az Azure-beli virtuális gépek Azure Automation állapotú konfigurációval való kezelése külön díj nélkül történik, ha a telepített Azure-beli virtuális gép kívánt állapot-konfigurációs bővítményének verziója nagyobb, mint 2,70. További információ: Automation- [**díjszabási oldal**](https://azure.microsoft.com/pricing/details/automation/).

## <a name="why-use-azure-automation-state-configuration"></a>Miért érdemes Azure Automation állapot konfigurációját használni

Azure Automation állapot-konfiguráció számos előnnyel jár, mint a DSC használata az Azure-on kívül. Ez a szolgáltatás lehetővé teszi, hogy a központi és biztonságos helyről gyorsan és egyszerűen lehessen méretezni a több ezer gépen. Könnyedén engedélyezheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapottal együtt mutatják.

A Azure Automation állapot-konfigurációs szolgáltatás azt a DSC-t adja meg, amit Azure Automation runbookok a PowerShell-parancsfájlok futtatásához. Más szóval ugyanúgy, ahogy a Azure Automation segíti a PowerShell-parancsfájlok kezelését, a DSC-konfigurációk kezelését is segíti.

### <a name="built-in-pull-server"></a>Beépített lekéréses kiszolgáló

Azure Automation állapot-konfiguráció egy DSC lekérési kiszolgálót biztosít a Windows-szolgáltatás [DSC-szolgáltatásához](/powershell/scripting/dsc/pull-server/pullserver)hasonló módon. A célként megadott csomópontok automatikusan megkapják a konfigurációkat, megfelelnek a kívánt állapotnak, és jelentést készítenek a megfelelőségéről. A Azure Automation beépített lekérési kiszolgálója kiküszöböli a saját lekéréses kiszolgáló beállításának és karbantartásának szükségességét. A Azure Automation a felhőben vagy a helyszínen is megcélozhatja a virtuális vagy fizikai Windows vagy Linux rendszerű gépeket.

### <a name="management-of-all-your-dsc-artifacts"></a>Az összes DSC-összetevő kezelése

Azure Automation állapot-konfiguráció ugyanazokat a felügyeleti réteget hozza a [PowerShell kívánt állapotának konfigurálásához](/powershell/scripting/dsc/overview/overview) , mint a PowerShell-parancsfájlok futtatásához. A Azure Portal vagy a PowerShellből kezelheti az összes DSC-konfigurációt, erőforrást és a cél csomópontokat.

![Képernyőkép a Azure Automation oldalról](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Jelentéskészítési adathalmazok importálása Azure Monitor naplókba

Azure Automation állapot-konfigurációval felügyelt csomópontok részletes jelentési állapotinformációkat küldenek a beépített lekérési kiszolgálónak. Konfigurálhatja Azure Automation állapot konfigurációját, hogy ezeket az adatLog Analytics-munkaterületre küldje. Lásd: [Azure Automation állapot-konfiguráció jelentési információinak továbbítása Azure monitor naplókhoz](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Előfeltételek

Azure Automation állapot konfigurációjának használatakor vegye figyelembe a jelen szakasz követelményeit.

### <a name="operating-system-requirements"></a>Operációsrendszer-követelmények

A Windows rendszert futtató csomópontok esetében a következő verziók támogatottak:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>A [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) önálló termék SKU nem tartalmazza a DSC implementációját. Így nem felügyelhető a PowerShell DSC-vel vagy Azure Automation állapot-konfigurációval.

A Linux rendszerű csomópontok esetében a DSC Linux bővítmény támogatja a [POWERSHELL DSC dokumentációjában](/powershell/scripting/dsc/getting-started/lnxgettingstarted)felsorolt összes Linux-disztribúciót.

### <a name="dsc-requirements"></a>DSC-követelmények

Az Azure-ban futó összes Windows-csomópont esetében a [WMF 5,1](/powershell/scripting/wmf/setup/install-configure) telepítve van, ha a gépek engedélyezve vannak. A Windows Server 2012 és Windows 7 rendszert futtató csomópontok esetén a [WinRM](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) engedélyezve van.

Az Azure-ban futó összes Linux-csomópont esetében a [POWERSHELL DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) telepítve van, ha a gépek engedélyezve vannak.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Magánhálózatok konfigurálása

A magánhálózaton lévő csomópontok számára szükséges portok, URL-címek és egyéb hálózati adatok részletes adatainak megtekintéséhez [Azure Automation hálózati konfigurációt](automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration) .

#### <a name="proxy-support"></a>Proxy-támogatás

A DSC-ügynök proxy-támogatása a Windows 1809-es és újabb verzióiban érhető el. Ez a beállítás a `ProxyURL` `ProxyCredential` csomópontok regisztrálásához használt [metaconfiguration-szkript](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) értékeinek és tulajdonságainak beállításával engedélyezhető.

>[!NOTE]
>Azure Automation állapot-konfiguráció nem biztosít DSC-proxy támogatást a Windows korábbi verzióihoz.

Linux-csomópontok esetén a DSC-ügynök támogatja a proxyt, és a `http_proxy` változó használatával határozza meg az URL-címet. További információ a proxyk támogatásáról: a [DSC-metaconfigurations előállítása](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="dns-records-per-region"></a>DNS-rekordok régiónként

A kivételek meghatározásakor a [DNS-rekordok](how-to/automation-region-dns-records.md) régiónként felsorolt címeinek használatát javasoljuk.

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának első lépései](automation-dsc-getting-started.md)című témakört.
- A csomópontok engedélyezésével kapcsolatos további információkért lásd: [Azure Automation állapot konfigurációjának engedélyezése](automation-dsc-onboarding.md).
- Ha szeretne többet megtudni a DSC-konfigurációk fordításáról, hogy hozzá lehessen rendelni őket a célcsoportokhoz, tekintse meg [a DSC-konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md)című témakört.
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse [meg a folyamatos üzembe helyezés a chocolatey](automation-dsc-cd-chocolatey.md)használatával című témakört.
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
- A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
