---
title: Microsoft Azure Stack Edge Pro R – áttekintés | Microsoft Docs
description: Ismerteti Azure Stack Edge Pro R-eszközöket, egy olyan katonai alkalmazások tárolási megoldását, amely fizikai eszközt használ az Azure-ba irányuló hálózati átvitelhez.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 40fa1bc776c5cd457e57cf170f629b6cf92800fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585995"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>Mi az a Azure Stack Edge Pro R?

Azure Stack Edge Pro R robusztus, peremhálózati számítástechnikai eszköz, amely kemény környezetekben használható. Azure Stack Edge Pro R szolgáltatás hardveres megoldásként érhető el. A Microsoft egy olyan felhőalapú felügyelt eszközt üzemeltet, amely hálózati tároló átjáróként működik, és beépített grafikus processzorral (GPU) rendelkezik, amely gyorsított AI-következtetést tesz lehetővé.

Ez a cikk áttekintést nyújt a Azure Stack Edge Pro R megoldásról, a főbb képességekről, valamint azokról a forgatókönyvekről, amelyekben telepítheti az eszközt.


## <a name="key-capabilities"></a>Főbb képességek

Azure Stack Edge Pro R a következő képességekkel rendelkezik:

|Képesség |Leírás  |
|---------|---------|
|Robusztus hardver| Robusztus kiszolgálói osztályú, kemény környezetekhez tervezett hardverek. Hordozható árutovábbítási esetekben található eszköz. |
|Felhő által felügyelt     |Az eszköz és a szolgáltatás kezelése a Azure Portal keresztül történik.|
|Peremhálózati számítási feladatok   |Lehetővé teszi az adatok elemzését, feldolgozását és szűrését. Támogatja a virtuális gépeket és a tárolóban lévő számítási feladatokat.|
|Gyorsított AI-következtetés| Az NVIDIA T4 GPU-val engedélyezhető.|
|Az adatok elérése     | Az adatok közvetlen elérése az Azure Storage-blobokból és az Azure Filesból felhőalapú API-k használatával az adatok további feldolgozásához a felhőben. Az eszköz helyi gyorsítótára a legutóbb használt fájlok gyors elérésére szolgál.|
|Leválasztott üzemmód| Az eszköz és a szolgáltatás opcionálisan felügyelhető Azure Stack hub használatával. Alkalmazások telepítése, futtatása és kezelése offline módban. <br> A kapcsolat nélküli mód támogatja az offline feltöltési forgatókönyveket.|
|Támogatott fájlátviteli protokollok     |A standard SMB, NFS és REST protokollok támogatása az adatok betöltéséhez. <br> A támogatott verziókról a [Azure stack Edge Pro R rendszerkövetelményei](azure-stack-edge-gpu-system-requirements.md)című témakörben olvashat bővebben.|
|Adatfrissítés     | Lehetőség van a helyi fájlok legújabb változatra való frissítésére a felhőből.|
|Dupla titkosítás    | Az öntitkosított meghajtók használata az első titkosítási réteget biztosítja. A VPN a második titkosítási réteget biztosítja. BitLocker-támogatás az adatok helyileg történő titkosításához és a felhőbe való biztonságos adatátvitelhez *https* -kapcsolaton keresztül.|
|Sávszélesség-szabályozás| Sávszélesség-használat korlátozása csúcsidőben.|

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>Használati esetek

Az alábbi forgatókönyvek olyan helyzetekben használhatók, amikor a Azure Stack Edge Pro R gyors Machine Learning (ML), amely a peremhálózat szélén és az adatok előfeldolgozásához szükséges, mielőtt elküldené az Azure-ba.

- **Következtetés a Azure Machine learning** -val Azure stack Edge Pro R-vel: ml-modellek futtatásával gyors eredményeket érhet el, amelyek a felhőbe való adatküldés előtt is elhelyezhetők. A teljes adatkészletet igény szerint átviheti, hogy továbbra is újratanítsa és javítsa a ML-modelleket. Az Azure ML hardveres gyorsított modellek az Azure Stack Edge Pro R-eszközön való használatáról további információt az [Azure ml hardveres gyorsított modellek üzembe helyezése Azure stack Edge Pro r](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)-ben című témakörben talál.

- Adatátalakítási adatok **előfeldolgozása** az Azure-ba való elküldés előtt, amely egy hatékonyabb adatkészlet létrehozásához szükséges. Az előfeldolgozás az alábbiakra használható:

    - Adatok összesítése.
    - Módosítsa például a személyes adattörlést.
    - A tárterület és a sávszélesség optimalizálása, vagy további elemzés céljából.
    - Az IoT-események elemzése és reagálás rájuk

- **Adatok átvitele hálózaton keresztül az Azure** -ba – a Azure stack Edge Pro R használatával egyszerűen és gyorsan vihet át adatátvitelt az Azure-ba a további számítási és elemzési vagy archiválási célokra.

## <a name="components"></a>Összetevők

Az Azure Stack Edge Pro R megoldás egy Azure Stack Edge-erőforrásból, Azure Stack Edge Pro R robusztus, fizikai eszközből és egy helyi webes felhasználói felületből áll.

- **Azure stack Edge Pro R fizikai eszköz** – egy 1 csomópontos számítási és tárolóeszköz, amely robusztus továbbítási esetben található. Opcionális szünetmentes áramforrás (UPS) is elérhető.

    ![Az Azure Stack Edge Pro R 1 – Node eszköz](media/azure-stack-edge-pro-r-overview/device-image-1.png)

- **Azure stack Edge-erőforrás** – a Azure Portal egyik erőforrása, amely lehetővé teszi egy robusztus, Azure stack Edge Pro R-eszköz felügyeletét egy olyan webes felületen, amelyet más földrajzi helyekről érhet el. Az Azure Stack Edge-erőforrás segítségével erőforrásokat hozhat létre és kezelhet, megtekintheti és kezelheti az eszközöket és a riasztásokat, valamint kezelheti a megosztásokat.  

- **Azure stack Edge Pro r local Web UI** – egy böngészőalapú helyi felhasználói felület az Azure stack Edge Pro r-eszközön, elsődlegesen az eszköz kezdeti konfigurálására szolgál. A helyi webes felhasználói felülettel a diagnosztika futtatására, a Azure Stack Edge Pro-eszköz kikapcsolására, a másolási naplók megtekintésére és az Microsoft ügyfélszolgálata a szolgáltatási kérések fájlba való kapcsolódására is használhatja.


## <a name="region-availability"></a>Régiónkénti elérhetőség

Azure Stack Edge Pro R fizikai eszköz, az Azure-erőforrás és a célként megadott Storage-fiók, amelybe az adatok átvitele nem feltétlenül azonos régióban kell, hogy legyen.

- **Erőforrás rendelkezésre állása** – az összes olyan régió listájához, ahol az Azure stack Edge-erőforrás elérhető, lépjen az [Azure-termékek régiónként elérhető területére](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Eszköz rendelkezésre állása** – az összes olyan országban, ahol az Azure stack Edge Pro r-eszköz elérhető, lépjen a **rendelkezésre állás** szakaszra az **Azure Stack Edge Pro R** lapján [Azure stack Edge Pro r díjszabásához](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeProR).

- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők. Azokat a régiókat, amelyekben a Storage-fiókok Azure Stack Edge Pro R-adatait el kell helyezni, közel kell lennie ahhoz, hogy az eszköz az optimális teljesítmény érdekében legyen. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható.

Azure Stack Edge szolgáltatás nem regionális szolgáltatás. További információ: [régiók és Availability Zones az Azure-ban](https://docs.microsoft.com/azure/availability-zones/az-overview). Az Azure Stack Edge szolgáltatás nem függ egy adott Azure-régiótól, így rugalmasan kihasználható a zónákra kiterjedő kimaradások és az egész régióra kiterjedő kimaradások terén.

## <a name="next-steps"></a>Következő lépések

- Tekintse át az [Azure stack Edge Pro R rendszerkövetelményeit](azure-stack-edge-gpu-system-requirements.md).
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->
