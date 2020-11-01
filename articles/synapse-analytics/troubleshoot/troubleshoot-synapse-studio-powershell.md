---
title: A szinapszis Studio-kapcsolat hibáinak megoldása
description: Az Azure szinapszis Studio-kapcsolat hibáinak megoldása a PowerShell használatával
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: e5e433fd857f638c1c13e4545c19e0b6314ee62e
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146506"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>A szinapszis Studio-kapcsolat hibáinak megoldása a PowerShell-lel

Az Azure szinapszis Studio (előzetes verzió) a webes API-végpontok megfelelő működéséhez függ. Ez az útmutató segítséget nyújt a kapcsolódási problémák okának azonosításához a következő esetekben:
- a helyi hálózat (például a vállalati tűzfal mögötti hálózat) konfigurálása az Azure szinapszis Studio eléréséhez.
- kapcsolódási problémák léptek fel az Azure szinapszis Studio használatával.

## <a name="prerequisite"></a>Előfeltétel

* PowerShell 5,0 vagy újabb verzió Windows rendszeren, vagy
* A PowerShell Core 6,0 vagy újabb verziója Windows vagy Linux rendszeren.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Kattintson a jobb gombbal a következő hivatkozásra, és válassza a "cél mentése másként" lehetőséget:

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Másik lehetőségként közvetlenül is megnyithatja a hivatkozást, és mentheti a megnyitott parancsfájlt. Ne mentse a fenti hivatkozáshoz tartozó címet, mert a jövőben változhat.

A Fájlkezelőben kattintson a jobb gombbal a letöltött parancsfájlra, és válassza a Futtatás a PowerShell használatával lehetőséget.

![Letöltött parancsfájl futtatása a PowerShell-lel](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Ha a rendszer kéri, adja meg az Azure-beli szinapszis-munkaterület nevét, amely jelenleg problémát észlel, vagy amelyet a kapcsolat teszteléséhez szeretne használni, majd nyomja le az ENTER billentyűt.

![Munkaterület nevének megadása](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

A rendszer elindítja a diagnosztikai munkamenetet. Várjon, amíg befejeződik.

![Várjon, amíg a diagnosztika befejeződik](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

A végén egy diagnosztikai összefoglalás jelenik meg. Ha a számítógép nem tud csatlakozni egy vagy több végponthoz, akkor az "összefoglalás" szakaszban néhány javaslat jelenik meg.

![Diagnosztikai összefoglalás áttekintése](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Emellett a munkamenethez tartozó diagnosztikai naplófájlt a hibaelhárítási parancsfájlt tartalmazó mappában hozza létre a rendszer. A helye az "általános tippek" szakaszban () látható `D:\TestAzureSynapse_2020....log` . Ezt a fájlt szükség esetén technikai támogatásra is elküldheti.

Ha Ön hálózati rendszergazda, és az Azure szinapszis studióhoz tartozó tűzfal-konfigurációt hangolja be, akkor az "összefoglalás" szakasz fölötti technikai részletek segíthetnek.

* Az "átadott" értékkel jelölt összes tesztelési elem (kérés) kapcsolati teszteket kapott, a HTTP-állapotkódtől függetlenül.
 A sikertelen kérelmek esetében az OK sárga színnel jelenik meg, például: `NamedResolutionFailure` vagy `ConnectFailure` . Ezek az okok segíthetnek kideríteni, hogy vannak-e helytelen konfiguráció a hálózati környezettel.


## <a name="next-steps"></a>Következő lépések
Ha az előző lépések nem segítenek a probléma megoldásában, [hozzon létre egy támogatási jegyet](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
