---
title: Azure Defender for Kubernetes – az előnyök és funkciók
description: Ismerje meg a Kubernetes készült Azure Defender előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 04/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c500c7b7afb36ffbe04fb63551c3a7d17c1347d9
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029080"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Bevezetés az Azure Defender for Kubernetes használatába

Az Azure Defender for Kubernetes az Azure Defender-csomag, amely védelmet biztosít a Kubernetes-fürtök számára, bárhol is futnak. 

A fürtök védelme a ben:

- **Azure Kubernetes szolgáltatás (ak)** – a Microsoft felügyelt szolgáltatása a tároló alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez

- Helyszíni **és többfelhős környezetek** – az [ív használatára képes Kubernetes bővítmény](defender-for-kubernetes-azure-arc.md) használata

A Azure Security Center és az AK egy Felhőbeli natív Kubernetes biztonsági ajánlatot biztosít a környezet megerősítése, a munkaterhelések védelme és a futásidejű védelem terén, ahogyan azt a [Security Centerban található tárolók biztonsága](container-security.md)is ismerteti.

Ha engedélyezi a kiszolgálók és a Log Analytics ügynöke [számára az Azure Defendert](defender-for-servers-introduction.md) , akkor a linuxos AK-csomópontok esetében a gazdagép szintű fenyegetések észlelése is elérhető. Ha azonban a fürt virtuálisgép-méretezési csoportra van telepítve, a Log Analytics ügynök jelenleg nem támogatott.



## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|A **Kubernetes készült Azure Defender** számlázása [Security Center díjszabásban](https://azure.microsoft.com/pricing/details/security-center/) látható|
|Szükséges szerepkörök és engedélyek:|A **biztonsági rendszergazda** figyelmen kívül hagyhatja a riasztásokat.<br>A **biztonsági olvasó** megtekintheti az eredményeket.|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Milyen előnyökkel jár az Azure Defender for Kubernetes?

A Kubernetes készült Azure Defender a fürtök naplófájljainak figyelésével biztosítja a **fürt szintű veszélyforrások elleni védelmet** .

Példák azokra a biztonsági eseményekre, amelyeket az Azure Defender a Kubernetes-figyelőknek tartalmaz, így elérhetővé teszi a Kubernetes-irányítópultokat, a magas jogosultsági szintű szerepkörök létrehozását és a bizalmas csatlakoztatások létrehozását. A fürt szintű riasztások teljes listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-akscluster)tartalmazza.

> [!TIP]
> A tárolói riasztások szimulálása a [blogbejegyzés](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)utasításait követve végezhető el.

Emellett a biztonsági kutatók globális csapata folyamatosan figyeli a fenyegetés tájképét. A felderített tároló-specifikus riasztásokat és biztonsági réseket adják hozzá.

>[!NOTE]
> Az Azure Defender biztonsági riasztásokat hoz létre olyan műveletekhez és központi telepítésekhez, amelyek akkor lépnek fel, ha engedélyezte a Defender for Kubernetes-csomagot az előfizetésében.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender for Kubernetes – gyakori kérdések

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>Továbbra is lekérhetem a fürtök védelmét a Log Analytics ügynök nélkül?

A Kubernetes-csomaghoz készült **Azure Defender** a fürt szintjén biztosít védelmet. Ha az Azure Defender Log Analytics-ügynökét is telepíti a **kiszolgálók számára**, akkor az adott csomaghoz tartozó csomópontok fenyegetés elleni védelmét fogja kérni. További információ: az [Azure Defender for Servers bemutatása](defender-for-servers-introduction.md).

Javasoljuk, hogy mindkét esetben a lehető legteljesebb védelem érdekében végezze el a telepítést.

Ha úgy dönt, hogy nem telepíti az ügynököt a gazdagépekre, a fenyegetések elleni védelem előnyeinek és biztonsági riasztásoknak csak egy részhalmazát kapja meg. A hálózati elemzéssel és a rosszindulatú kiszolgálókkal folytatott kommunikációval kapcsolatos riasztásokat továbbra is megkapja.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Az AK lehetővé teszi, hogy egyéni virtuálisgép-bővítményeket telepítsek az AK-csomópontokon?
Az Azure Defender számára az AK-csomópontok figyeléséhez a Log Analytics ügynököt kell futtatnia. 

Az AK felügyelt szolgáltatás, és mivel a log Analytics-ügynök egy Microsoft által felügyelt bővítmény, az AK-fürtökön is támogatott.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Ha a fürtön már fut egy Azure Monitor a containers Agent számára, akkor is szükség van a Log Analytics-ügynökre?
Ahhoz, hogy az Azure Defender figyelje a csomópontjait, futtatnia kell a Log Analytics ügynököt.

Ha a fürtök már futtatják a Azure Monitor a tárolók ügynöke számára, akkor a Log Analytics-ügynököt is telepítheti, és a két ügynök anélkül is dolgozhat egymással, hogy problémák lépnek fel.

[További információ a Azure monitor for containers agentről](../azure-monitor/containers/container-insights-manage-agent.md).


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta Security Center Kubernetes-védelmét, beleértve a Kubernetes készült Azure Defendert is. 

> [!div class="nextstepaction"]
> [Az Azure Defender engedélyezése](enable-azure-defender.md)

A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket: 

- [Riasztások továbbítása SIEM, SOAR vagy IT Service Management megoldásba](export-to-siem.md)
- [Riasztások hivatkozási táblázata](alerts-reference.md)
