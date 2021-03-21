---
title: Azure Stream Analytics-fürtök áttekintése
description: Ismerkedjen meg a Stream Analytics-fürt egyetlen bérlői dedikált ajánlatával.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e5157a02f97370b20db85bf5e3e8aae98a2d8668
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709291"
---
# <a name="overview-of-azure-stream-analytics-cluster"></a>Azure Stream Analytics-fürt áttekintése

Azure Stream Analytics-fürt egy egybérlős üzembe helyezést biztosít összetett és igényes adatfolyam-forgatókönyvekhez. A Stream Analytics-fürtök teljes méretben több mint 200 MB/másodpercet dolgozhatnak fel valós időben. A dedikált fürtökön futó Stream Analytics-feladatok a standard ajánlat összes funkcióját kihasználhatják, és a saját bemenetekhez és kimenetekhez kapcsolódó magánhálózati kapcsolatokhoz is támogatást biztosítanak.

Stream Analytics a fürtöket a folyamatos átviteli egységek (SUs-EK) számlázzák, amelyek a fürthöz lefoglalt CPU-és memória-erőforrások mennyiségét jelölik. A folyamatos átviteli egységek a standard és a dedikált ajánlatok esetében is megegyeznek. Minden fürthöz 36, 72, 108, 144, 180 vagy 216 SUs megvásárolható. A Stream Analytics-fürt a szervezete folyamatos átviteli platformként szolgál, és különböző munkahelyeken különböző csapatok is megoszthatók.

## <a name="what-are-stream-analytics-clusters"></a>Mik azok a Stream Analytics fürtök

A Stream Analytics-fürtöket ugyanaz a motor működteti, mint a több-bérlős környezetekben futó Stream Analytics-feladatokat. Az egybérlős, dedikált fürt az alábbi funkciókkal rendelkezik:

* Egybérlős üzemeltetés, a többi bérlőtől származó zaj nélkül. Az erőforrások valóban „izoláltak”, és jobban teljesítenek ingadozó forgalom esetében.

* A fürt méretezése 36 és 216 SUs között történik, mivel a folyamatos átviteli használat az idő múlásával növekszik.

* VNet-támogatás, amely lehetővé teszi, hogy a Stream Analytics feladatok biztonságosan kapcsolódjanak más erőforrásokhoz privát végpontok használatával.

* A felhasználó által definiált C#-függvények és az egyéni deszerializálók bármely régióban való létrehozása.

* A karbantartási költségeket úgy teheti meg, hogy a valós idejű elemzési megoldások létrehozásával kapcsolatos erőfeszítéseit koncentrálja.

## <a name="how-to-get-started"></a>Első lépések

Stream Analytics- [fürtöt](create-cluster.md) a [Azure Portal](https://aka.ms/asaclustercreateportal)használatával hozhat létre. Ha bármilyen kérdése van, vagy segítségre van szüksége a bevezetéshez, lépjen kapcsolatba a [stream Analytics csapatával](mailto:askasa@microsoft.com).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>Hogyan választhat egy Stream Analytics-fürt és egy Stream Analytics-feladatok között?

Első lépésként a legegyszerűbb módszer, ha egy Stream Analytics feladatot hoz létre és fejleszt, hogy ismerkedjen meg a szolgáltatással, és Ismerje meg, hogy miként felel meg az elemzési követelményeknek.

Stream Analytics a feladatok önállóan nem támogatják a virtuális hálózatok. Ha a bemenetek vagy a kimenetek védettek egy tűzfal vagy egy Azure-Virtual Network mögött, a következő két lehetőség közül választhat:

* Ha a helyi gép hozzáfér a VNet által biztosított bemeneti és kimeneti erőforrásokhoz (például Azure Event Hubs vagy Azure SQL Database), akkor a [Visual studióhoz Azure stream Analytics eszközöket telepíthet](stream-analytics-tools-for-visual-studio-install.md) a helyi gépen. Az eszközön helyileg fejlesztheti és [tesztelheti stream Analytics-feladatait](stream-analytics-live-data-local-testing.md) anélkül, hogy költségeket kellene fizetnie. Ha készen áll a Stream Analytics használatára az architektúrában, létrehozhat egy Stream Analytics-fürtöt, konfigurálhatja a saját végpontokat, és a feladatokat méretezhető módon futtathatja.

* Létrehozhat egy Stream Analytics fürtöt, konfigurálhatja a fürtöt a folyamathoz szükséges privát végpontokkal, és futtathatja Stream Analytics feladatait a fürtön.

### <a name="what-performance-can-i-expect"></a>Milyen teljesítményt várhatok?

A SU ugyanaz, mint a standard és a dedikált ajánlaton belül. Egy teljes 36 SU-fürtöt használó egyetlen feladatnál körülbelül 36 MB/másodperc átviteli sebesség érhető el ezredmásodperces késéssel. A pontos szám az események formájától és az elemzés típusától függ. Mivel dedikált, Stream Analytics-fürt megbízhatóbb teljesítménybeli garanciákat nyújt. A fürtön futó összes feladat csak az Ön tulajdonában van.

### <a name="can-i-scale-my-cluster"></a>Méretezhető a fürtem?

Igen. A fürt kapacitása egyszerűen beállítható úgy [, hogy megfeleljen](scale-cluster.md) a változó igényeknek.

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>Futtathatom a meglévő feladatokat a létrehozott új fürtökön?

Igen. Meglévő feladatait összekapcsolhatja az újonnan létrehozott Stream Analytics-fürttel, és a szokásos módon futtathatja. Nem kell újból létrehoznia meglévő Stream Analytics-feladatait.

### <a name="how-much-will-these-clusters-cost-me"></a>Mennyibe kerül ezek a fürtök?

A Stream Analytics-fürtök díját a kiválasztott SU-kapacitás alapján számítjuk fel. A fürtök számlázása óránként történik, és az ezekben a fürtökben futó feladatok esetében nem számítunk fel további díjakat. A privát végpont számlázási frissítéseivel kapcsolatban lásd a [Private link Service díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/private-link/) .

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>Mely bemenetek és kimenetek kapcsolódhatnak a saját Stream Analytics-fürthöz?

Stream Analytics támogatja a különböző bemeneti és kimeneti típusokat. Létrehozhat olyan [magánhálózati végpontokat](private-endpoints.md) a fürtben, amelyek lehetővé teszik a feladatok számára a bemeneti és kimeneti erőforrások elérését. Jelenleg Azure SQL Database, az Azure Storage, a Azure Data Lake Storage Gen2, az Azure Event hub és a Azure Service Bus támogatott szolgáltatások és egyéb típusok hamarosan elérhetők lesznek. 

## <a name="next-steps"></a>Következő lépések

Most már áttekintheti Azure Stream Analytics-fürtöt. Ezután létrehozhatja a fürtöt, és futtathatja a Stream Analytics feladatot: 

* [Stream Analytics-fürt létrehozása](create-cluster.md)
* [Privát végpontok kezelése Azure Stream Analytics-fürtben](private-endpoints.md)
* [Stream Analytics feladatok kezelése Stream Analytics-fürtben](manage-jobs-cluster.md)
* [Stream Analytics-feladatok létrehozása](stream-analytics-quick-create-portal.md)
