---
title: Azure Stack Edge mini R – áttekintés | Microsoft Docs
description: Ismerteti Azure Stack Edge mini R-t, amely egy olyan katonai alkalmazások tárolási megoldása, amely hordozható fizikai eszközt használ akkumulátoros Wi-Fi-kapcsolaton keresztül az Azure-ba történő átvitelhez.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: 14a425c3aca3a1c296b96855b2c920d558e89f9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585978"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>Mi az a Azure Stack Edge mini R?

Az Azure Stack Edge mini R egy rendkívül hordozható, robusztus, peremhálózati számítástechnikai eszköz, amely kemény környezetekben való használatra készült. Azure Stack Edge mini R szolgáltatás hardveres megoldásként érhető el. A Microsoft egy olyan felhőalapú felügyelt eszközt üzemeltet, amely hálózati tároló átjáróként működik, és beépített VPU rendelkezik, amely lehetővé teszi a gyorsított AI-következtetést.

Ez a cikk áttekintést nyújt a Azure Stack Edge mini R megoldásról, a főbb képességekről, valamint azokról a forgatókönyvekről, amelyekben telepítheti az eszközt.


## <a name="key-capabilities"></a>Főbb képességek

Azure Stack Edge mini R a következő képességekkel rendelkezik:

|Képesség |Leírás  |
|---------|---------|
|Robusztus hardver| Erős környezetekhez tervezett robusztus hardver.|
|Ultra hordozható| Ultra hordozható, akkumulátorral működtetett forma tényező.|
|Felhő által felügyelt|Az eszköz és a szolgáltatás kezelése a Azure Portal keresztül történik.|
|Peremhálózati számítási feladatok|Lehetővé teszi az adatok elemzését, feldolgozását és szűrését.<br>Támogatja a virtuális gépeket és a tárolóban lévő számítási feladatokat. |
|Gyorsított AI-következtetés| Az Intel Movidius számtalan X VPU engedélyezte.|
|Vezetékes és vezeték nélküli | Lehetővé teszi a vezetékes és vezeték nélküli adatátvitelt.|
|Az adatok elérése     | Az adatok közvetlen elérése az Azure Storage-blobokból és az Azure Filesból felhőalapú API-k használatával az adatok további feldolgozásához a felhőben. Az eszköz helyi gyorsítótára a legutóbb használt fájlok gyors elérésére szolgál.|
|Leválasztott üzemmód|  Az eszköz és a szolgáltatás opcionálisan felügyelhető Azure Stack hub használatával. Alkalmazások telepítése, futtatása és kezelése offline módban. <br> A kapcsolat nélküli mód támogatja az offline feltöltési forgatókönyveket.|
|Támogatott fájlátviteli protokollok      |A standard SMB, NFS és REST protokollok használatát teszi lehetővé az adatok betöltéséhez. <br> A támogatott verziókról a [Azure stack Edge mini R rendszerkövetelményei](azure-stack-edge-gpu-system-requirements.md)című témakörben olvashat bővebben.|
|Adatfrissítés     | Lehetőség van a helyi fájlok legújabb változatra való frissítésére a felhőből.|
|Dupla titkosítás    | Az öntitkosító meghajtó használata az első titkosítási réteget biztosítja. A VPN a második titkosítási réteget biztosítja. BitLocker-támogatás az adatok helyileg történő titkosításához és a felhőbe való biztonságos adatátvitelhez *https* -kapcsolaton keresztül.|
|Sávszélesség-szabályozás| Sávszélesség-használat korlátozása csúcsidőben.|

## <a name="use-cases"></a>Használati esetek

Íme a különböző forgatókönyvek, amelyekben a Azure Stack Edge mini R a gyors Machine Learning (ML), amely a peremhálózat szélén és az adatok előfeldolgozásához használható, mielőtt elküldené az Azure-ba.

- **Következtetés a Azure Machine learning** – a Azure stack Edge mini R használatával a rendszer az adatküldés előtt a felhőbe irányuló, gyors eredményekhez is futtathat ml-modelleket. A teljes adatkészletet igény szerint átviheti, hogy továbbra is újratanítsa és javítsa a ML-modelleket. Az Azure ML hardveres gyorsított modellek az Azure Stack Edge mini R-eszközön történő használatáról további információt az [Azure ml hardveres gyorsított modellek üzembe helyezése Azure stack Edge mini r](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)-ben című témakörben talál.

- Adatátalakítási adatok **előfeldolgozása** számítási lehetőségekkel, például tárolókkal vagy virtuális gépekkel, mielőtt elküldené azt az Azure-ba egy további végrehajtható adatkészlet létrehozásához. Az előfeldolgozás az alábbiakra használható:

    - Adatok összesítése.
    - Módosítsa például a személyes adattörlést.
    - A tárterület és a sávszélesség optimalizálása, vagy további elemzés céljából.
    - Az IoT-események elemzése és reagálás rájuk

- **Adatok átvitele a hálózaton keresztül az Azure** -ba – a Azure stack Edge mini R használatával egyszerűen és gyorsan vihet át adatátvitelt az Azure-ba a további számítási és elemzési, illetve archiválási célok lehetővé tételéhez.

## <a name="components"></a>Összetevők

Az Azure Stack Edge mini R megoldás egy Azure Stack Edge-erőforrásból, Azure Stack Edge mini R robusztus, ultra hordozható fizikai eszközből és egy helyi webes felhasználói felületből áll.

* **Azure stack Edge mini R fizikai eszköz** – a Microsoft által biztosított, ultra hordozható, robusztus, számítási és tárolási eszköz. Az eszköznek van bevezető akkumulátora, és kevesebb, mint 7 lbs.

    ![Azure Stack Edge mini R-eszköz](media/azure-stack-edge-mini-r-overview/perspective-view-1.png)

* **Azure stack Edge-erőforrás** – a Azure Portal egyik erőforrása, amely lehetővé teszi egy robusztus, Azure stack Edge mini R-eszköz felügyeletét egy olyan webes felületen, amely különböző földrajzi helyekről érhető el. Az Azure Stack Edge-erőforrás segítségével erőforrásokat hozhat létre és kezelhet, megtekintheti és kezelheti az eszközöket és a riasztásokat, valamint kezelheti a megosztásokat.  

* **Azure stack Edge mini r local Web UI** – egy böngészőalapú helyi felhasználói felület az Azure stack Edge mini r-eszközön, elsődlegesen az eszköz kezdeti konfigurálására szolgál. A helyi webes felhasználói felülettel a diagnosztika futtatására, a Azure Stack Edge Pro-eszköz kikapcsolására, a másolási naplók megtekintésére és az Microsoft ügyfélszolgálata a szolgáltatási kérések fájlba való kapcsolódására is használhatja.


## <a name="region-availability"></a>Régiónkénti elérhetőség

Azure Stack Edge mini R fizikai eszközét, az Azure-erőforrást és a célként megadott Storage-fiókot, amelybe az adatok átvitele nem feltétlenül azonos régióban kell, hogy legyen.

- **Erőforrás rendelkezésre állása** – az összes olyan régió listájához, ahol az Azure stack Edge-erőforrás elérhető, lépjen az [Azure-termékek régiónként elérhető területére](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Eszköz rendelkezésre állása** – az összes olyan országban, ahol az Azure stack Edge mini r-eszköz elérhető, lépjen a rendelkezésre állási szakaszra az [Azure stack Edge mini r díjszabásának](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeMiniR)Azure stack Edge mini r lapján.

- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők. Azok a régiók, amelyekben a Storage-fiókok tárolják Azure Stack Edge mini R-adataikat, közel kell lenniük ahhoz, ahol az eszköz az optimális teljesítmény érdekében található. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható.

Azure Stack Edge szolgáltatás nem regionális szolgáltatás. További információ: [régiók és Availability Zones az Azure-ban](https://docs.microsoft.com/azure/availability-zones/az-overview). Az Azure Stack Edge szolgáltatás nem függ egy adott Azure-régiótól, így rugalmasan kihasználható a zónákra kiterjedő kimaradások és az egész régióra kiterjedő kimaradások terén.

## <a name="next-steps"></a>Következő lépések

- Tekintse át az [Azure stack Edge mini R rendszerkövetelményeit](azure-stack-edge-gpu-system-requirements.md).
