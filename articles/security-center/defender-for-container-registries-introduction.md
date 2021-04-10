---
title: Azure Defender a Container nyilvántartók számára – az előnyök és funkciók
description: Ismerje meg az Azure Defender a Container-jegyzékek előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 04/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 4840fc27133b1d92cb8aaad80921f9d21901569d
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010692"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>A tárolóregisztrációs adatbázisokhoz készült Azure Defender bemutatása

A Azure Container Registry (ACR) egy felügyelt, privát Docker beállításjegyzék-szolgáltatás, amely egy központi beállításjegyzékben tárolja és kezeli az Azure-beli központi telepítésekhez tartozó tároló lemezképeit. Ez a nyílt forráskódú Docker beállításjegyzék 2,0-es adatbázisán alapul.

Az előfizetéshez tartozó összes Azure Resource Manager-alapú nyilvántartó védelem érdekében engedélyezze az **Azure Defender számára a tároló** -beállításjegyzéket az előfizetés szintjén. A Security Center ezután beolvassa a beállításjegyzékbe beküldött, a beállításjegyzékbe importált képeket vagy az elmúlt 30 napban lekért képeket. Ez a funkció rendszerkép alapján van felszámítva.

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Milyen előnyökkel jár az Azure Defender a Container-jegyzékek esetében?

Security Center az előfizetésében Azure Resource Manager alapú ACR-jegyzékeket azonosít, és zökkenőmentesen biztosítja az Azure-natív sebezhetőségi felmérést és a beállításjegyzék lemezképének kezelését.

Az **Azure Defender for Container** -beállításjegyzékek egy sebezhetőségi ellenőrzőeszközt tartalmaznak a Azure Resource Manager-alapú Azure Container Registry-jegyzékekben található rendszerképek vizsgálatához, és mélyebb láthatóságot biztosítanak a lemezképek biztonsági rései számára. Az integrált képolvasót a Qualys, az iparág vezető sebezhetőségének vizsgálatára szolgáló gyártó látja el.

Ha problémák merülnek fel, a Qualys vagy a Security Center – értesítést kap a Security Center irányítópulton. Security Center a biztonsági rések esetében gyakorlati ajánlásokat és súlyossági besorolást biztosít, valamint útmutatást nyújt a probléma megoldásához. A Security Center a tárolókkal kapcsolatos javaslatairól a [javaslatok hivatkozási listájában](recommendations-reference.md#recs-compute)talál további információt.

Security Center szűrők és a vizsgálati eredmények osztályozása. Ha egy rendszerkép kifogástalan állapotú, Security Center megjelöli. A Security Center csak olyan rendszerképekhez hoz létre biztonsági javaslatokat, amelyeknek feloldhatók a problémák. Security Center részletesen ismerteti az egyes jelentett biztonsági réseket és a súlyossági besorolást. Emellett útmutatást nyújt az egyes rendszerképeken talált biztonsági rések szervizeléséhez.

Ha csak akkor értesíti, ha problémák merülnek fel, Security Center csökkenti a nemkívánatos tájékoztatási riasztások lehetséges lehetőségét.


> [!TIP]
> Ha többet szeretne megtudni a Security Center tárolójának biztonsági funkcióiról, tekintse meg a következő témakört:
>
> - [Azure Security Center és tárolók biztonsága](container-security.md)
> - [Bevezetés az Azure Defender for Kubernetes használatába](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>Mikor vannak beolvasva a képek?

A képvizsgálat három eseményindítót tartalmaz:

- **Leküldés** esetén – amikor egy rendszerképet küld a beállításjegyzékbe, Security Center automatikusan megvizsgálja a képet. A rendszerkép vizsgálatának elindításához küldje le az adattárba.

- **Nemrégiben leállt** – mivel az új biztonsági réseket minden nap felderítik, az **Azure Defender a Container-jegyzékek esetében** hetente megkeresi az elmúlt 30 napban meghúzott képeket is. Ezek az újraellenőrzések nem díjkötelesek. a fentiekben leírtaknak megfelelően havonta egyszer kell fizetni.

- **Az import** -Azure Container Registry eszközön olyan eszközök importálhatók, amelyekkel a Docker hub, a Microsoft Container Registry vagy más Azure Container Registry használatával képeket hozhat a beállításjegyzékbe. Az **Azure Defender a Container nyilvántartói számára** az importált támogatott lemezképeket vizsgálja. További információ: [Container images importálása egy tároló-beállításjegyzékbe](../container-registry/container-registry-import-images.md).
 
A vizsgálat általában 2 percen belül befejeződik, de akár 15 percet is igénybe vehet. A megállapítások elérhetők Security Center javaslatokként, például a következő módon:

[![Példa Azure Security Center a Azure Container Registry (ACR) tárolt rendszerképben felderített biztonsági rések észlelésére](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Hogyan működik Security Center a Azure Container Registry

Az alábbiakban áttekintheti az összetevők és a Security Center a kibocsátásiegység-forgalmi jegyzékek védelmének előnyeit.

![Azure Security Center és Azure Container Registry (ACR) – magas szintű áttekintés](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Gyakori kérdések Azure Container Registry rendszerképek vizsgálatához

### <a name="how-does-security-center-scan-an-image"></a>Hogyan vizsgálja Security Center a rendszerképet?
Security Center lekéri a rendszerképet a beállításjegyzékből, és egy elkülönített homokozóban futtatja a Qualys-olvasóval. A képolvasó kibontja az ismert biztonsági rések listáját.

Security Center szűrők és a vizsgálati eredmények osztályozása. Ha egy rendszerkép kifogástalan állapotú, Security Center megjelöli. A Security Center csak olyan rendszerképekhez hoz létre biztonsági javaslatokat, amelyeknek feloldhatók a problémák. A csak akkor értesíti Önt, ha problémák merülnek fel, Security Center csökkenti a nemkívánatos tájékoztatási riasztások lehetséges lehetőségét.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Lekérhetem a vizsgálat eredményeit REST APIon keresztül?
Igen. Az eredmények az [Alértékelések Rest API](/rest/api/securitycenter/subassessments/list/) szakaszban találhatók. Emellett használhatja az Azure Resource Graph (ARG), a Kusto API-t az összes erőforráshoz: a lekérdezés egy adott vizsgálatot tud beolvasni.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Milyen beállításjegyzék-típusok vannak beolvasva? Milyen típusú számlázást kell fizetni?
Az Azure Defender által a Container nyilvántartók számára támogatott tároló-nyilvántartási típusok listáját a [rendelkezésre állás](#availability)című részben tekintheti meg.

Ha nem támogatott beállításjegyzékeket csatlakozik az Azure-előfizetéséhez, az Azure Defender nem ellenőrzi őket, és nem számláz rájuk.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>Testreszabhatók a sebezhetőségi képolvasóból származó eredmények?
Igen. Ha a szervezetnek figyelmen kívül kell hagynia egy megállapítást, és nem javítja azt, akkor letilthatja. A letiltott eredmények nem befolyásolják a biztonságos pontszámot, vagy nem eredményeznek nemkívánatos zajt.

[Ismerje meg, hogyan hozhat létre szabályokat az eredményeknek a beépített sebezhetőségi felmérési eszközből való letiltásához](defender-for-container-registries-usage.md#disable-specific-findings-preview).

### <a name="why-is-security-center-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>Miért Security Center riasztást a beállításjegyzékben nem szereplő rendszerképekkel kapcsolatos biztonsági réseket illetően?
A Security Center biztonsági réseket biztosít a beállításjegyzékben leküldett vagy lehúzott összes rendszerképhez. Egyes képek a már beolvasott képekből is felhasználhatnak címkéket. Előfordulhat például, hogy a "legutóbbi" címkét újra hozzá kell rendelnie, amikor egy képet vesz fel egy kivonatba. Ilyen esetekben a régi rendszerkép továbbra is létezik a beállításjegyzékben, és a kivonata továbbra is lekérhető. Ha a rendszerkép biztonsági megállapításokkal rendelkezik, és leállt, biztonsági rések jelennek meg.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Biztonsági rések ellenőrzése a lemezképekben](defender-for-container-registries-usage.md)