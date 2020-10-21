---
title: Azure Defender a Container nyilvántartók számára – az előnyök és funkciók
description: Ismerje meg az Azure Defender a Container-jegyzékek előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 0c504ebdec524390d9deb22088979199f80e4f13
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275960"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Bevezetés az Azure Defender for Container-jegyzékbe

A Azure Container Registry (ACR) egy felügyelt, privát Docker beállításjegyzék-szolgáltatás, amely egy központi beállításjegyzékben tárolja és kezeli az Azure-beli központi telepítésekhez tartozó tároló lemezképeit. Ez a nyílt forráskódú Docker beállításjegyzék 2,0-es adatbázisán alapul.

Az előfizetéshez tartozó összes Azure Resource Manager-alapú nyilvántartó védelem érdekében engedélyezze az **Azure Defender számára a tároló** -beállításjegyzéket az előfizetés szintjén. A Security Center ezután beolvassa a beállításjegyzékbe beküldött, a beállításjegyzékbe importált képeket vagy az elmúlt 30 napban lekért képeket. Ez a funkció rendszerkép alapján van felszámítva.

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Milyen előnyökkel jár az Azure Defender a Container-jegyzékek esetében?

Security Center az előfizetésében Azure Resource Manager alapú ACR-jegyzékeket azonosít, és zökkenőmentesen biztosítja az Azure-natív sebezhetőségi felmérést és a beállításjegyzék lemezképének kezelését.

Az **Azure Defender for Container** -beállításjegyzékek egy sebezhetőségi ellenőrzőeszközt tartalmaznak a Azure Resource Manager-alapú Azure Container Registry-jegyzékekben található rendszerképek vizsgálatához, és mélyebb láthatóságot biztosítanak a lemezképek biztonsági rései számára. Az integrált képolvasót a Qualys, az iparág vezető sebezhetőségének vizsgálatára szolgáló gyártó látja el.

Ha problémák merülnek fel, a Qualys vagy a Security Center – értesítést kap a Security Center irányítópulton. Security Center a biztonsági rések esetében gyakorlati ajánlásokat és súlyossági besorolást biztosít, valamint útmutatást nyújt a probléma megoldásához. A Security Center a tárolókkal kapcsolatos javaslatairól a [javaslatok hivatkozási listájában](recommendations-reference.md#recs-containers)talál további információt.

Security Center szűrők és a vizsgálati eredmények osztályozása. Ha egy rendszerkép kifogástalan állapotú, Security Center megjelöli. A Security Center csak olyan rendszerképekhez hoz létre biztonsági javaslatokat, amelyeknek feloldhatók a problémák. Security Center részletesen ismerteti az egyes jelentett biztonsági réseket és a súlyossági besorolást. Emellett útmutatást nyújt az egyes rendszerképeken talált biztonsági rések szervizeléséhez.

Ha csak akkor értesíti, ha problémák merülnek fel, Security Center csökkenti a nemkívánatos tájékoztatási riasztások lehetséges lehetőségét.


## <a name="when-are-images-scanned"></a>Mikor vannak beolvasva a képek?

A képvizsgálat három eseményindítót tartalmaz:

- **Leküldés** esetén – amikor egy rendszerképet küld a beállításjegyzékbe, Security Center automatikusan megvizsgálja a képet. A rendszerkép vizsgálatának elindításához küldje le az adattárba.

- **Nemrégiben leállt** – mivel az új biztonsági réseket minden nap felderítik, az **Azure Defender a Container-jegyzékek esetében** az elmúlt 30 napban meghúzott képeket is megvizsgálja. Az újraellenőrzéshez nem számítunk fel további díjat; a fentiekben leírtaknak megfelelően havonta egyszer kell fizetni.

- **Az import** -Azure Container Registry eszközön olyan eszközök importálhatók, amelyekkel a Docker hub, a Microsoft Container Registry vagy más Azure Container Registry használatával képeket hozhat a beállításjegyzékbe. Az **Azure Defender a Container nyilvántartói számára** az importált támogatott lemezképeket vizsgálja. További információ: [Container images importálása egy tároló-beállításjegyzékbe](../container-registry/container-registry-import-images.md).
 
A vizsgálat általában 2 percen belül befejeződik, de akár 15 percet is igénybe vehet. A megállapítások elérhetők Security Center javaslatokként, például a következő módon:

[![Példa Azure Security Center a Azure Container Registry (ACR) tárolt rendszerképben felderített biztonsági rések észlelésére](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Hogyan működik Security Center a Azure Container Registry

Az alábbiakban áttekintheti az összetevők és a Security Center a kibocsátásiegység-forgalmi jegyzékek védelmének előnyeit.

![Azure Security Center és Azure Container Registry (ACR) – magas szintű áttekintés](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Gyakori kérdések Azure Container Registry rendszerképek vizsgálatához

### <a name="how-does-security-center-scan-an-image"></a>Hogyan vizsgálja Security Center a rendszerképet?
A rendszerkép a beállításjegyzékből lett kihúzva. Ezután egy elkülönített homokozóban fut a Qualys-olvasóval, amely kibontja az ismert sebezhetőségek listáját.

Security Center szűrők és a vizsgálati eredmények osztályozása. Ha egy rendszerkép kifogástalan állapotú, Security Center megjelöli. A Security Center csak olyan rendszerképekhez hoz létre biztonsági javaslatokat, amelyeknek feloldhatók a problémák. Ha csak akkor értesíti, ha problémák merülnek fel, Security Center csökkenti a nemkívánatos tájékoztatási riasztások lehetséges lehetőségét.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Lekérhetem a vizsgálat eredményeit REST APIon keresztül?
Igen. Az eredmények az [alárendelt értékelések REST API](/rest/api/securitycenter/subassessments/list/)-ban találhatók. Emellett használhatja az Azure Resource Graph (ARG), a Kusto API-t az összes erőforráshoz: a lekérdezés egy adott vizsgálatot tud beolvasni.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Milyen beállításjegyzék-típusok vannak beolvasva? Milyen típusú számlázást kell fizetni?
Az Azure Defender által a Container nyilvántartók számára támogatott tároló-nyilvántartási típusok listáját a [rendelkezésre állás](defender-for-container-registries-usage.md#availability)című részben tekintheti meg.

Ha nem támogatott beállításjegyzéket csatlakozik az Azure-előfizetéséhez, a rendszer nem ellenőrzi őket, és nem lesz rájuk kiszámlázva.


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Security Center tárolójának biztonsági funkcióiról, tekintse meg a következő témakört:

- [Azure Security Center és tárolók biztonsága](container-security.md)

- [Bevezetés az Azure Defender for Kubernetes használatába](defender-for-kubernetes-introduction.md)


