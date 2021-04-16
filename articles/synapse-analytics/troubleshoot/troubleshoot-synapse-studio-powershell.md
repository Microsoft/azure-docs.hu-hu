---
title: A Synapse Studio kapcsolati problémáinak hibaelhárítása
description: Az Azure Synapse Studio kapcsolódási hibáinak elhárítása a PowerShell használatával
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 2bbdaef9268239005cdf5ea7fbee6734dadc8113
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566249"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>A Synapse Studio PowerShell-sel való kapcsolat hibaelhárítása

Azure Synapse Studio a megfelelő működését a webes API-végpontok egy készletétől függ. Ez az útmutató segít azonosítani a kapcsolódási problémák okait, amikor:
- konfigurálja a helyi hálózatot (például a vállalati tűzfal mögötti hálózatot) a Azure Synapse eléréséhez.
- csatlakozási problémákat tapasztal a Azure Synapse Studióban.

## <a name="prerequisite"></a>Előfeltétel

* PowerShell 5.0 vagy újabb verzió Windows rendszeren, vagy
* PowerShell Core 6.0-s vagy újabb verziót használ Windows vagy Linux rendszeren.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Kattintson a jobb gombbal a következő hivatkozásra, és válassza a "Cél mentése másként" lehetőséget:

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Másik lehetőségként megnyithatja a hivatkozást közvetlenül, és mentheti a megnyitott szkriptfájlt. Ne mentse a fenti hivatkozás címét, mert az a jövőben változhat.

A fájlkezelőben kattintson a jobb gombbal a letöltött szkriptfájlra, és válassza a "Futtatás a PowerShell használatával" lehetőséget.

![Letöltött szkriptfájl futtatása a PowerShell használatával](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Amikor a rendszer kéri, Azure Synapse adja meg annak a munkaterületnek a nevét, amely jelenleg problémát tapasztal, vagy ha tesztelni szeretné a kapcsolatot, majd nyomja le az Enter billentyűt.

![Adja meg a munkaterület nevét](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

A diagnosztikai munkamenet elindul. Várjon, amíg befejeződik.

![Várjon, amíg a diagnosztika befejeződik](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Végül megjelenik a diagnózis összegzése. Ha a számítógép nem tud csatlakozni egy vagy több végponthoz, az "Összefoglalás" szakaszban javaslatokat fog látni.

![Diagnosztikai összegzés áttekintése](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Emellett a munkamenet diagnosztikai naplófájlja ugyanabban a mappában jön létre, mint a hibaelhárítási szkript. A helye az "Általános tippek" szakaszban ( `D:\TestAzureSynapse_2020....log` ) látható. Szükség esetén elküldheti ezt a fájlt a technikai támogatásnak.

Ha Ön hálózati rendszergazda, és finomhangolja a Azure Synapse Studio tűzfal-konfigurációját, az "Összefoglalás" szakaszban látható technikai részletek segíthetnek.

* Az "Átadott" jelzéssel jelölt tesztelemek (kérések) azt jelentik, hogy a HTTP-állapotkódtól függetlenül már átesnek a kapcsolati teszteken.
 A sikertelen kérelmek esetén az ok sárga színben jelenik meg, például vagy `NamedResolutionFailure` `ConnectFailure` . Ezek az okok segíthetnek annak kiderítésében, hogy hibásan vannak-e konfigurálva a hálózati környezetben.


## <a name="next-steps"></a>Következő lépések
Ha az előző lépések nem oldják meg a problémát, [hozzon létre egy támogatási jegyet.](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)