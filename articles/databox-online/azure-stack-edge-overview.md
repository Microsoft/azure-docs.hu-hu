---
title: Microsoft Azure Stack Edge Pro – áttekintés | Microsoft Docs
description: Ismerteti Azure Stack Edge Pro-t, amely egy fizikai eszközt használ az Azure-ba irányuló hálózati átvitelhez.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/15/2021
ms.author: alkohli
ms.openlocfilehash: 3973235991a16d118b47d7289f3a1825621a9023
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574679"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>Mi az FPGA-val rendelkező Azure Stack Edge Pro?

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

Azure Stack Edge Pro és a FPGA a hálózati adatátviteli képességekkel rendelkező, AI-kompatibilis Edge számítástechnikai eszköz. Ez a cikk áttekintést nyújt a FPGA-megoldással, az előnyökkel, a főbb képességekkel és a telepítési forgatókönyvekkel kapcsolatos Azure Stack Edge Pro-ról.

Azure Stack Edge Pro és a FPGA egy szolgáltatásként nyújtott hardveres megoldás. A Microsoft a felhőben felügyelt eszközön egy beépített, programozható Gate array (FPGA) tömböt biztosít, amely lehetővé teszi a gyorsított mesterséges intelligenciát, és rendelkezik a hálózati tárolók átjárójának összes képességével.

A Azure Data Box Edge Azure Stack Edge-ként lett átnevezve.

## <a name="use-cases"></a>Használati esetek

Az alábbiakban azokat a forgatókönyveket láthatja, amelyekben a Azure Stack Edge Pro gyors Machine Learning (ML), amely a peremhálózat szélén és az adatok előfeldolgozása előtt használható az Azure-ba való küldés előtt.

- **Következtetés a Azure Machine learning** -with Azure stack Edge Pro használatával: ml-modellek futtatásával gyors eredményeket érhet el, amelyek a felhőbe való adatküldés előtt is elhelyezhetők. A teljes adatkészletet igény szerint átviheti, hogy továbbra is újratanítsa és javítsa a ML-modelleket. Az Azure ML hardveres gyorsított modellek az Azure Stack Edge Pro-eszközön történő használatáról további információt az [Azure ml hardveres gyorsított modellek üzembe helyezése Azure stack Edge Pro](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)-ban című témakörben talál.

- Adatátalakítási adatok **előfeldolgozása** az Azure-ba való elküldés előtt, amely egy hatékonyabb adatkészlet létrehozásához szükséges. Az előfeldolgozás az alábbiakra használható: 

    - Adatok összesítése.
    - Módosítsa például a személyes adattörlést.
    - A tárterület és a sávszélesség optimalizálása, vagy további elemzés céljából.
    - Az IoT-események elemzése és reagálás rájuk 

- **Adatok átvitele a hálózaton keresztül az Azure** -ba – a Azure stack Edge Pro használatával egyszerűen és gyorsan vihet át adatátvitelt az Azure-ba a további számítási és elemzési, illetve archiválási célok lehetővé tételéhez. 

## <a name="key-capabilities"></a>Főbb képességek

Az Azure Stack Edge Pro a következő képességekkel rendelkezik:

|Képesség |Leírás  |
|---------|---------|
|Gyorsított AI-következtetés| A beépített FPGA engedélyezte.|
|Számítástechnika       |Lehetővé teszi az adatok elemzését, feldolgozását és szűrését.|
|Nagy teljesítmény | Nagy teljesítményű számítási és adatforgalom.|
|Az adatok elérése     | Az adatok közvetlen elérése az Azure Storage-blobokból és az Azure Filesból felhőalapú API-k használatával az adatok további feldolgozásához a felhőben. Az eszköz helyi gyorsítótára a legutóbb használt fájlok gyors elérésére szolgál.|
|Felhő által felügyelt     |Az eszköz és a szolgáltatás kezelése a Azure Portal keresztül történik.  |
|Offline feltöltés     | A kapcsolat nélküli mód támogatja az offline feltöltési forgatókönyveket.|
|Támogatott protokollok     | A standard SMB és NFS protokollok támogatása az adatbetöltéshez. <br> További információ a támogatott verziókról: [Azure stack Edge Pro rendszerkövetelményei](azure-stack-edge-system-requirements.md).|
|Adatfrissítés     | Lehetőség van a helyi fájlok legújabb változatra való frissítésére a felhőből.|
|Titkosítás    | A BitLocker támogatja az adatok helyi titkosítását és biztonságos átvitelét a felhőbe *https*-en keresztül.|
|Sávszélesség-szabályozás| Sávszélesség-használat korlátozása csúcsidőben.|
|ExpressRoute | A ExpressRoute használatával bővült a biztonság. Egyenrangú konfiguráció használata, ahol a helyi eszközökről a Felhőbeli tárolási végpontokra irányuló forgalom a ExpressRoute halad át. További információkat az [ExpressRoute áttekintésében](../expressroute/expressroute-introduction.md) találhat.

## <a name="components"></a>Összetevők

Az Azure Stack Edge Pro-megoldás Azure Stack Edge-erőforrásból, Azure Stack Edge Pro fizikai eszközből és egy helyi webes felhasználói felületből áll.

* **Azure stack Edge Pro fizikai eszköz**: a Microsoft által biztosított 1U állványra csatlakoztatott kiszolgáló, amely úgy konfigurálható, hogy az Azure-ba küldje az adatküldést.
    
* **Azure stack Edge-erőforrás**: a Azure Portal egyik erőforrása, amely lehetővé teszi egy Azure stack Edge Pro-eszköz felügyeletét egy olyan webes felületen, amelyet különböző földrajzi helyekről érhet el. Az Azure Stack Edge erőforrás segítségével erőforrásokat hozhat létre és kezelhet, kezelheti a megosztásokat, és megtekintheti és kezelheti az eszközöket és a riasztásokat.
  
   <!--[The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

   Ahogy Azure Stack Edge Pro megközelíti az élettartamát, az új Azure Stack Edge Pro-eszközökre vonatkozó megrendelések kitöltése nem történik meg. Ha Ön új ügyfél, javasoljuk, hogy vizsgálja meg Azure Stack Edge Pro-GPU-eszközök használatát a számítási feladatokhoz. További információért látogasson el a [Mi az Azure stack Edge Pro és a GPU](azure-stack-edge-gpu-overview.md). További információ a Azure Stack Edge Pro és a GPU eszköz megrendeléséről [: új erőforrás létrehozása Azure stack Edge Pro-GPU-](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)hoz.

   Ha Ön egy meglévő ügyfél, akkor is létrehozhat egy új Azure Stack Edge Pro-erőforrást, ha le kell cserélnie vagy alaphelyzetbe kell állítania a meglévő Azure Stack Edge Pro-eszközt. Útmutatásért lépjen az [Azure stack Edge Pro-eszköz rendelésének létrehozása](azure-stack-edge-deploy-prep.md#create-new-resource-for-existing-device)című részhez.

* **Azure stack Edge Pro helyi webes felhasználói felülete** – a helyi webes kezelőfelületen futtathatja a diagnosztikát, leállíthatja és újraindíthatja az Azure stack Edge Pro-eszközt, megtekintheti a másolási naplókat, és kapcsolatba léphet Microsoft ügyfélszolgálata a szolgáltatási kérések fájlba.

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    A webalapú felhasználói felület használatával kapcsolatos információkért [tekintse meg a webalapú felhasználói felület használata a Azure stack Edge Pro felügyeletéhez](azure-stack-edge-manage-access-power-connectivity-mode.md)című témakört.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Azure Stack Edge Pro fizikai eszköz, az Azure-erőforrás és a célként megadott Storage-fiók, amelybe az adatok átvitele nem feltétlenül azonos régióban kell, hogy legyen.

- **Erőforrás rendelkezésre állása** – az összes olyan régióban, ahol az Azure stack peremhálózati erőforrás elérhető, tekintse meg a régiók [által elérhető Azure-termékek](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)listáját. A Azure Stack Edge Pro a Azure Government felhőben is üzembe helyezhető. További információ: [Mi az Azure Government?](../azure-government/documentation-government-welcome.md)
    
- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők. Azokat a régiókat, amelyekben a Storage-fiókok Azure Stack Edge Pro-adataikat tárolják, közel kell lennie ahhoz, hogy az eszköz az optimális teljesítmény érdekében legyen. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható.

## <a name="next-steps"></a>Következő lépések

- Tekintse át az [Azure stack Edge Pro rendszerkövetelményeit](azure-stack-edge-system-requirements.md).
- Az [Azure stack Edge Pro korlátainak](azure-stack-edge-limits.md)megismerése.
- [Azure stack Edge Pro](azure-stack-edge-deploy-prep.md) üzembe helyezése Azure Portalban.