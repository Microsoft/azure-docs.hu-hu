---
title: Azure-hoz készült Avere vFXT
description: Ismerje meg az Azure-hoz készült avere-vFXT, amely egy felhőalapú fájlrendszer-gyorsítótárazási megoldás adatigényes, nagy teljesítményű számítási feladatokhoz.
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 5095f05b0c9ce9061781c3558ca6c7091ef37aa5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270956"
---
# <a name="what-is-avere-vfxt-for-azure"></a>Mi az Azure-hoz készült Avere vFXT?

Az Azure-hoz készült Avere vFXT egy adatigényes, nagy teljesítményű feldolgozási (HPC-) feladatokra szabott fájlrendszer-gyorsítótárazási megoldás. E megoldás segítségével a felhőalapú számítástechnika méretezhetőségét kihasználva ott és akkor teheti elérhetővé az adatokat (akár a saját helyszíni hardverén tárolt adatokat is), ahol és amikor arra szükség van.

Az Avere vFXT a következő gyakori számítási megoldásokat támogatja:

* Hibrid felhőalapú architektúra – az Azure-hoz készült avere-vFXT egy hardveres tárolási rendszerrel is működhetnek, amely a felhőalapú számítástechnika előnyeit biztosítja a fájlok áthelyezése nélkül.

* A Cloud burst-avere vFXT for Azure segítségével egyetlen projektbe helyezheti át az adatait a felhőbe, vagy a teljes munkafolyamatot véglegesen átemelheti és áthelyezheti.

![Blob Storage-hez és helyszíni adatközponthoz csatlakoztatott, Azure-előfizetésbeli Avere vFXT rendszer részleteit bemutató ábra](media/avere-vfxt-hybrid.png)

Az Azure-hoz készült avere-vFXT az alábbi helyzetekben ajánlott:

* HPC számítási feladatok intenzív olvasási műveletei
* Közös NFS protokollt használó alkalmazások
* 1000–40 000 processzormagot használó számítási farmok
* Integráció helyszíni hardveres megoldásokkal (NAS, Azure Blob Storage vagy mindkettő)

További információ: <https://azure.microsoft.com/services/storage/avere-vfxt/>

## <a name="who-uses-avere-vfxt-for-azure"></a>Kik használják az Azure-hoz készült Avere vFXT-t?

Az Avere vFXT valamennyi olvasásigényes számítási feladat esetében hatékonyan alkalmazható:

### <a name="visual-effects-rendering"></a>Vizuális effektusok renderelése

Multimédiás és szórakoztatóipari alkalmazásoknál az Avere vFXT-fürt használatával gyorsabb adathozzáférés biztosítható az idő szempontjából kritikus fontosságú renderelési projektek esetében. Rugalmasan, hatékonyan kezelhetők a nagyobb projektek is, mivel a gyorsítótártér kibővíthető, illetve további számítási csomópontok vehetők fel az Azure-ban.

### <a name="life-sciences"></a>Élettudományok

A avere vFXT lehetővé teszi, hogy a kutatók másodlagos elemzési munkafolyamatokat futtassanak az Azure-ban, és a helyüktől függetlenül hozzáférhessenek a genomikai adatokhoz.

A gyógyszeripari kutatásban a avere vFXT-fürtök felgyorsítják a kábítószer-felderítést azáltal, hogy a kutatók megjósolják a kábítószer-célzási interakciókat és elemzik a kutatási

### <a name="financial-services-analytics"></a>Pénzügyi szolgáltatások elemzése

Az Avere vFXT-fürtökkel felgyorsíthatók a mennyiségi elemzési számítások, ezáltal a pénzügyi szolgáltatók megalapozottabb stratégiai döntéseket hozhatnak.

## <a name="features-and-specifications"></a>Funkciók és specifikációk

Az Avere vFXT rendszer három vagy több, fürtben konfigurált virtuális peremhálózati szűrőcsomópontból áll. Elhelyezhető az ügyfélszámítógépek közelében, amelyek így a fürthöz, és nem közvetlenül a tárolóhoz csatlakoznak.

Az Avere vFXT-fürt igény szerint gyorsítótárazza a fájlokat. Az ismételt kérelmek az idő több mint 80%-ában kiszolgálhatók a gyorsítótárból.

### <a name="compatibility"></a>Kompatibilitás

* Kompatibilis a NetApp és a Dell EMC Isilon hardveres NAS-rendszereivel
* Kompatibilis az Azure-blobokkal
* NFSv3 vagy SMB2 protokollt használ

A avere vFXT for Azure a következő Azure-erőforrásokat használja:

|Azure-összetevő| Erőforrás |
|----------|-----------|
|Virtual machines (Virtuális gépek)|3 vagy több E32s_v3|
|Prémium szintű SSD-tár|200 GB operációsrendszer-tárhely és 1–4 TB gyorsítótártér csomópontonként |
|Tárfiók (nem kötelező) |v2|
|Adatháttér-tároló (nem kötelező) | Egy üres LRS-blobtároló |

## <a name="next-steps"></a>Következő lépések

A cikkből megtudhatja, hogyan tervezhet és hozhat létre saját avere-vFXT az Azure üzembe helyezéséhez.

* [A System megtervezése](avere-vfxt-deploy-plan.md)
* [Az üzembe helyezés áttekintése](avere-vfxt-deploy-overview.md)
* [A vFXT-fürt létrehozása](avere-vfxt-deploy.md)
