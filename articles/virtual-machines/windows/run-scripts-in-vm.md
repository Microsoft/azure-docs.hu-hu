---
title: Szkriptek futtatása Azure-beli Windows rendszerű virtuális gépen
description: Ez a témakör a parancsfájlok Windows rendszerű virtuális gépeken való futtatását ismerteti
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 7bf62eb2ab8d2ce82ce73e3e8ae26cf303b8ba67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765880"
---
# <a name="run-scripts-in-your-windows-vm"></a>Szkriptek futtatása Windows rendszerű virtuális gépen

Előfordulhat, hogy a feladatok automatizálásához vagy a hibák elhárításához parancsokat kell futtatnia egy virtuális gépen. A következő cikk rövid áttekintést nyújt a szkriptek és parancsok virtuális gépeken való futtatásához elérhető szolgáltatásokról.

## <a name="custom-script-extension"></a>Egyéni szkriptbővítmény

Az [egyéni szkriptbővítményt](../extensions/custom-script-windows.md) elsősorban az üzembe helyezés utáni konfigurációhoz és a szoftvertelepítéshez használják.

* Szkriptek letöltése és futtatása Azure-beli virtuális gépeken.
* Futtatható sablonokkal, Azure Resource Manager Azure CLI-vel, REST API, PowerShell-Azure Portal.
* A szkriptfájlok letölthetők az Azure Storage-ból vagy a GitHubról, vagy meg is érhetők a számítógépről a Azure Portal.
* Futtatassa a PowerShell-szkriptet Windows rendszerű gépeken és Bash-szkriptet Linux rendszerű gépeken.
* Hasznos lehet a telepítés utáni konfigurációhoz, a szoftvertelepítéshez, valamint egyéb konfigurációs vagy felügyeleti feladatokhoz.

## <a name="run-command"></a>Futtassa a(z)  parancsot

Az [parancsfuttatás](run-command.md) szolgáltatás lehetővé teszi a virtuális gépek és alkalmazások kezelését és hibaelhárítását szkriptek használatával, és akkor is elérhető, ha a gép nem érhető el, például ha a vendég tűzfalon nincs megnyitva az RDP- vagy SSH-port.

* Szkriptek futtatása Azure-beli virtuális gépeken.
* A futtatás [a](run-command.md)Azure Portal, [REST API,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) [az Azure CLI](/cli/azure/vm/run-command#az_vm_run_command_invoke)vagy a [PowerShell használatával is futtatható](/powershell/module/az.compute/invoke-azvmruncommand)
* Gyorsan futtathat egy szkriptet, és megtekintheti a kimenetet, és szükség szerint megismételheti a Azure Portal.
* A szkript begépelhető közvetlenül, vagy futtathatja a beépített szkriptek valamelyikét.
* Futtatassa a PowerShell-szkriptet Windows rendszerű gépeken és Bash-szkriptet Linux rendszerű gépeken.
* Virtuális gépek és alkalmazások kezeléséhez, valamint nem elérhető virtuális gépeken futó szkriptek futtatásához hasznos.

## <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó

A [hibrid runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) általános gép-, alkalmazás- és környezetfelügyeletet biztosít az Automation-fiókban tárolt felhasználói egyéni szkriptekkel.

* Szkriptek futtatása Azure-beli és nem Azure-beli gépeken.
* Futtatható a Azure Portal, az Azure CLI,REST API, a PowerShell és a webhook használatával.
* Automation-fiókban tárolt és kezelt szkriptek.
* PowerShell-, PowerShell-munkafolyamat-, Python- vagy grafikus runbookok futtatása
* A szkript futási ideje nincs korlátozva.
* Egyidejűleg több szkript is futtatható.
* A rendszer teljes szkriptkimenetet ad vissza és tárol.
* A feladatelőzmények 90 napig érhetők el.
* A szkriptek helyi rendszerként vagy felhasználó által megadott hitelesítő adatokkal is futtathatók.
* Manuális [telepítést igényel](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Soros konzol

A [Serial console](/troubleshoot/azure/virtual-machines/serial-console-windows) közvetlen hozzáférést biztosít egy virtuális géphez, hasonlóan ahhoz, mintha a billentyűzetet csatlakoztatta volna a virtuális géphez.

* Parancsok futtatása Azure-beli virtuális gépeken.
* Egy szövegalapú konzollal futtatható a gépre a Azure Portal.
* Jelentkezzen be a gépre egy helyi felhasználói fiókkal.
* Akkor hasznos, ha a virtuális géphez való hozzáférésre a gép hálózatától vagy az operációs rendszer állapotától függetlenül van szükség.

## <a name="next-steps"></a>Következő lépések

További információ a szkriptek és parancsok virtuális gépeken való futtatásához elérhető különböző funkciókról.

* [Egyéni szkriptbővítmény](../extensions/custom-script-windows.md)
* [parancsfuttatás](run-command.md)
* [hibrid runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md)
* [Serial console](/troubleshoot/azure/virtual-machines/serial-console-windows)