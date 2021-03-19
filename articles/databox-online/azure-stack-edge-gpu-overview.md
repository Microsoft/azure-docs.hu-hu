---
title: Microsoft Azure Stack Edge Pro a GPU-val – áttekintés | Microsoft Docs
description: Ismerteti Azure Stack Edge Pro GPU-val való használatát, amely egy fizikai eszközt használó tárolási megoldás az Azure-ba történő hálózati átvitelhez.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 01/18/2021
ms.author: alkohli
ms.openlocfilehash: 8e829d149e96cf658c7a5aaf37291e84def2061e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602655"
---
# <a name="what-is-azure-stack-edge-pro-with-gpu"></a>Mi az a GPU-val rendelkező Azure Stack Edge Pro?

A GPU-val rendelkező Azure Stack Edge-kompatibilis Edge számítástechnikai eszköz, amely hálózati adatátviteli képességekkel rendelkezik. Ez a cikk áttekintést nyújt a Azure Stack Edge Pro-megoldásról, az előnyökről, a főbb képességekről, valamint azokról a forgatókönyvekről, amelyekben telepítheti az eszközt.

Azure Stack Edge Pro a GPU-val egy szolgáltatásként nyújtott hardveres megoldás. A Microsoft egy olyan felhőalapú felügyelt eszközt üzemeltet, amely hálózati tároló átjáróként működik, és beépített grafikus processzorral (GPU) rendelkezik, amely gyorsított AI-következtetést tesz lehetővé. 

## <a name="use-cases"></a>Használati esetek

Az alábbiakban azokat a forgatókönyveket láthatja, amelyekben a Azure Stack Edge Pro gyors Machine Learning (ML), amely a peremhálózat szélén és az adatok előfeldolgozása előtt használható az Azure-ba való küldés előtt.

- **Következtetés a Azure Machine learning** -with Azure stack Edge Pro használatával: ml-modellek futtatásával gyors eredményeket érhet el, amelyek a felhőbe való adatküldés előtt is elhelyezhetők. A teljes adatkészletet igény szerint átviheti, hogy továbbra is újratanítsa és javítsa a ML-modelleket. Az Azure ML hardveres gyorsított modellek az Azure Stack Edge Pro-eszközön történő használatáról további információt az [Azure ml hardveres gyorsított modellek üzembe helyezése Azure stack Edge Pro](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)-ban című témakörben talál.

- Adatátalakítási adatok **előfeldolgozása** az Azure-ba való elküldés előtt a számítási lehetőségek, például a tároló munkaterhelések és a Virtual Machines használatával egy sokkal hatékonyabb adatkészlet hozható létre. Az előfeldolgozás az alábbiakra használható: 

    - Adatok összesítése.
    - Módosítsa például a személyes adattörlést.
    - A tárterület és a sávszélesség optimalizálása, vagy további elemzés céljából.
    - Az IoT-események elemzése és reagálás rájuk 

- **Adatok átvitele a hálózaton keresztül az Azure** -ba – a Azure stack Edge Pro használatával egyszerűen és gyorsan vihet át adatátvitelt az Azure-ba a további számítási és elemzési, illetve archiválási célok lehetővé tételéhez. 

## <a name="key-capabilities"></a>Főbb képességek

Az Azure Stack Edge Pro a következő képességekkel rendelkezik:

|Képesség |Leírás  |
|---------|---------|
|Gyorsított AI-következtetés| A beépített GPU (a modelltől függően egy vagy két) engedélyezte.|
|Edge számítástechnika      |Támogatja a virtuális gépek és a tárolón lévő munkaterhelések használatát az adatok elemzéséhez, feldolgozásához és szűréséhez. |
|Az adatok elérése     | Az adatok közvetlen elérése az Azure Storage-blobokból és az Azure Filesból felhőalapú API-k használatával az adatok további feldolgozásához a felhőben. Az eszköz helyi gyorsítótára a legutóbb használt fájlok gyors elérésére szolgál.|
|Felhő által felügyelt     |Az eszköz és a szolgáltatás kezelése a Azure Portal keresztül történik.  |
|Offline feltöltés     | A kapcsolat nélküli mód támogatja az offline feltöltési forgatókönyveket.|
|Támogatott fájlátviteli protokollok      | A standard SMB, NFS és REST protokollok támogatása az adatok betöltéséhez. <br> További információ a támogatott verziókról: [Azure stack Edge Pro rendszerkövetelményei](azure-stack-edge-system-requirements.md).|
|Adatfrissítés     | Lehetőség van a helyi fájlok legújabb változatra való frissítésére a felhőből.|
|Titkosítás    | A BitLocker támogatja az adatok helyi titkosítását és biztonságos átvitelét a felhőbe *https*-en keresztül.|
|Sávszélesség-szabályozás| Sávszélesség-használat korlátozása csúcsidőben.|
<!--|ExpressRoute | A ExpressRoute használatával bővült a biztonság. Egyenrangú konfiguráció használata, ahol a helyi eszközökről a Felhőbeli tárolási végpontokra irányuló forgalom a ExpressRoute halad át. További információkat az [ExpressRoute áttekintésében](../expressroute/expressroute-introduction.md) találhat.|-->

## <a name="components"></a>Összetevők

Az Azure Stack Edge Pro-megoldás Azure Stack Edge-erőforrásból, Azure Stack Edge Pro fizikai eszközből és egy helyi webes felhasználói felületből áll.

* **Azure stack Edge Pro fizikai eszköz** – a Microsoft által biztosított 1U állványra csatlakoztatott kiszolgáló, amely úgy konfigurálható, hogy az Azure-ba küldi az adatküldést.
    
* **Azure stack Edge-erőforrás** – a Azure Portal egyik erőforrása, amely lehetővé teszi egy Azure stack Edge Pro-eszköz felügyeletét egy olyan webes felületen, amelyet más földrajzi helyekről érhet el. Az Azure Stack Edge-erőforrás segítségével erőforrásokat hozhat létre és kezelhet, megtekintheti és kezelheti az eszközöket és a riasztásokat, valamint kezelheti a megosztásokat.  

    További információért látogasson el a [Azure stack Edge Pro-eszköz rendelésének létrehozása](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)című témakörre.

* **Azure stack Edge Pro helyi webes felhasználói felület** – a Azure stack Edge Pro-eszközön egy böngészőalapú helyi felhasználói felület, amely elsődlegesen az eszköz kezdeti konfigurálására szolgál. A helyi webes felhasználói felülettel a diagnosztika futtatására, a Azure Stack Edge Pro-eszköz kikapcsolására, a másolási naplók megtekintésére és az Microsoft ügyfélszolgálata a szolgáltatási kérések fájlba való kapcsolódására is használhatja.

    A webalapú felhasználói felület használatával kapcsolatos információkért [tekintse meg a webalapú felhasználói felület használata a Azure stack Edge Pro felügyeletéhez](azure-stack-edge-manage-access-power-connectivity-mode.md)című témakört.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Azure Stack Edge Pro fizikai eszköz, az Azure-erőforrás és a célként megadott Storage-fiók, amelybe az adatok átvitele nem feltétlenül azonos régióban kell, hogy legyen.

- **Erőforrás rendelkezésre állása** – ebben a kiadásban az erőforrás az USA keleti régiójában, Nyugat-Európában és dél-Kelet-Ázsia régióban érhető el.

- **Eszköz rendelkezésre állása** – az összes olyan országban/régióban, ahol az Azure stack Edge Pro-eszköz elérhető, válassza a **rendelkezésre állási** szakaszt az [Azure Stack Edge Pro díjszabásának](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro) **Azure stack Edge Pro** lapján.
    
- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők. Azokat a régiókat, amelyekben a Storage-fiókok Azure Stack Edge Pro-adataikat tárolják, közel kell lennie ahhoz, hogy az eszköz az optimális teljesítmény érdekében legyen. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható.

Azure Stack Edge szolgáltatás nem regionális szolgáltatás. További információ: [régiók és Availability Zones az Azure-ban](https://docs.microsoft.com/azure/availability-zones/az-overview). Az Azure Stack Edge szolgáltatás nem függ egy adott Azure-régiótól, így rugalmasan kihasználható a zónákra kiterjedő kimaradások és az egész régióra kiterjedő kimaradások terén.

## <a name="next-steps"></a>Következő lépések

- Tekintse át az [Azure stack Edge Pro rendszerkövetelményeit](azure-stack-edge-gpu-system-requirements.md).

- Az [Azure stack Edge Pro korlátainak](azure-stack-edge-limits.md)megismerése.
- [Azure stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) üzembe helyezése Azure Portalban.
