---
title: Service Fabric fürterőforrás-kezelő – affinitás
description: Az Azure Service Fabric szolgáltatásainak szolgáltatás-affinitásának áttekintése és útmutatás a szolgáltatás affinitásának konfigurálásához.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c141cd96877fd140b858d0aaed9197f2de80eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89005734"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Szolgáltatás-affinitás konfigurálása és használata Service Fabric
Az affinitás egy olyan vezérlő, amely elsősorban a nagyobb monolit alkalmazások Felhőbeli és a Services világba való átállásának megkönnyítéséhez nyújt segítséget. A szolgáltatások teljesítményének javítására szolgáló optimalizálásként is használatos, bár ennek mellékhatásai lehetnek.

Tegyük fel, hogy egy nagyobb alkalmazást hoz létre, vagy az egyiket, hogy csak a nem a szolgáltatásokkal, hanem a Service Fabricával (vagy bármely elosztott környezettel) tervezték. Az ilyen típusú áttérés gyakori. Először emelje ki a teljes alkalmazást a környezetbe, csomagolja ki, és gondoskodjon arról, hogy zökkenőmentesen fusson. Ezután megkezdheti a lebontást különböző kisebb szolgáltatásokra, amelyek mindegyike kommunikál egymással.

Végül előfordulhat, hogy az alkalmazás néhány problémát észlelt. A problémák általában az alábbi kategóriák valamelyikébe tartoznak:

1. A monolit alkalmazás egyes X-összetevőjének nem dokumentált függősége volt az Y összetevőtől, és ezeket az összetevőket külön szolgáltatásokra kapcsolta. Mivel ezek a szolgáltatások mostantól a fürt különböző csomópontjain futnak, megszakadnak.
2. Ezek az összetevők a következőn keresztül kommunikálnak: (helyi Nevesített csövek | megosztott memória | fájlok a lemezen), és a megfelelő teljesítmény érdekében szükségük van arra, hogy egy megosztott helyi erőforrásba tudják írni őket. A rendszer később eltávolítja a kemény függőséget.
3. Minden rendben van, de kiderül, hogy ez a két összetevő valójában a beszéd/teljesítmény érzékeny. Amikor áthelyezték őket külön szolgáltatásba, a teljes alkalmazás-teljesítmény kiosztott vagy késéssel bővült. Ennek eredményeképpen az általános alkalmazás nem felel meg az elvárásoknak.

Ezekben az esetekben nem szeretnénk elveszíteni az újrabontási munkát, és nem szeretnék visszalépni a monolithbe. Az utolsó feltétel akár egyszerű optimalizálásként is kívánatos lehet. Addig azonban, amíg át nem tervezzük az összetevőket úgy, hogy a szolgáltatások (vagy amíg meg nem oldhatók a teljesítményre vonatkozó elvárások) a lehető legtöbbnek kell lenniük.

Mi a teendő? Jól megpróbálkozhat az affinitás bekapcsolásával.

## <a name="how-to-configure-affinity"></a>Affinitás konfigurálása
Az affinitás beállításához két különböző szolgáltatás közötti affinitási kapcsolatot kell meghatároznia. Azt is megteheti, hogy az affinitást egy szolgáltatásként "mutat", és "Ez a szolgáltatás csak akkor fut le, ha a szolgáltatás fut." Előfordul, hogy szülő/gyermek kapcsolatként hivatkozunk az affinitásra (ahol a gyermek a szülőre mutat). Az affinitás biztosítja, hogy az egyik szolgáltatás replikái vagy példányai ugyanarra a csomópontra kerüljenek, mint egy másik szolgáltatásra.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Egy alárendelt szolgáltatás csak egyetlen affinitási kapcsolatban tud részt venni. Ha azt szeretné, hogy a gyermek két szülő szolgáltatáshoz legyen összekapcsolva, ha van pár lehetősége:
> - A kapcsolatok sztornírozása (a parentService1 és a parentService2 pont az aktuális gyermek szolgáltatásban), vagy
> - Jelölje ki az egyik szülőt központilag az egyezmény szerint, és az összes szolgáltatási pontot a szolgáltatásban. 
>
> A fürtben létrejövő elhelyezési viselkedésnek azonosnak kell lennie.
>

## <a name="different-affinity-options"></a>Eltérő affinitási beállítások
Az affinitás a több korrelációs séma egyikén keresztül képviselteti magát, és két különböző módban van. Az affinitás leggyakoribb módja a NonAlignedAffinity hívása. A NonAlignedAffinity-ben a különböző szolgáltatások replikái vagy példányai ugyanarra a csomópontra kerülnek. A másik mód a AlignedAffinity. Az igazított affinitás csak állapot-nyilvántartó szolgáltatásokkal használható. Ha két állapot-nyilvántartó szolgáltatást konfigurál úgy, hogy az összhangban legyen egymással, gondoskodjon arról, hogy a szolgáltatások elsődlegesei ugyanazon csomópontokon legyenek elhelyezve, mint a többi. Ez azt is okozhatja, hogy az egyes szolgáltatások egyes formátumú másodlagos zónák ugyanazon csomópontokra kerüljenek. Az állapot-nyilvántartó szolgáltatások NonAlignedAffinity konfigurálása is lehetséges (bár kevésbé gyakori). A NonAlignedAffinity esetében a két állapot-nyilvántartó szolgáltatás különböző replikái ugyanazon a csomóponton futnak, de az elsődlegesek a különböző csomópontokon is futhatnak.

<center>

![Affinitási módok és azok hatásai][Image1]
</center>

### <a name="best-effort-desired-state"></a>Legjobb erőfeszítést kívánt állapot
Az affinitási kapcsolat a legjobb megoldás. Nem biztosít ugyanazokat a garanciákat, mint a létrehozásakor közös elhelyezés vagy a megbízhatóság, amelyek ugyanabban a végrehajtható folyamatban futnak. Az affinitási kapcsolatban lévő szolgáltatások alapvetően különböző entitások, amelyek sikertelenek lehetnek, és egymástól függetlenül helyezhetők át. Az affinitási kapcsolat is megszakadhat, bár ezek a megszakítások ideiglenesek. A kapacitás korlátozásai például azt jelenthetik, hogy az affinitási kapcsolaton belül csak néhány szolgáltatási objektum fér el egy adott csomóponton. Ezekben az esetekben annak ellenére, hogy fennáll az affinitási kapcsolat, a többi megkötés miatt nem kényszeríthető ki. Ha ezt megteheti, a szabálysértést a rendszer később automatikusan kijavítja.

### <a name="chains-vs-stars"></a>Láncok és csillagok
A fürterőforrás-kezelő jelenleg nem tudja modellezni az affinitási kapcsolatok láncait. Ez azt jelenti, hogy egy olyan szolgáltatás, amely egy affinitási kapcsolatban gyermek, nem lehet szülő egy másik affinitási kapcsolatban. Ha ezt a típusú kapcsolatot szeretné modellezni, akkor a lánc helyett gyakorlatilag csillagként kell modellezni. Ha egy láncból egy csillagra szeretne áttérni, a legalsó gyermeke az első gyermek szülője lesz. A szolgáltatások elrendezésének függvényében előfordulhat, hogy ezt többször kell végrehajtania. Ha nincs természetes fölérendelt szolgáltatás, előfordulhat, hogy létre kell hoznia egyet, amely helyőrzőként szolgál. A követelményektől függően előfordulhat, hogy az [alkalmazás-csoportokat](service-fabric-cluster-resource-manager-application-groups.md)is érdemes megtekinteni.

<center>

![Láncok és csillagok az affinitási kapcsolatok kontextusában][Image2]
</center>

Még ma is érdemes megjegyezni, hogy az affinitási kapcsolatok alapértelmezés szerint irányt képeznek. Ez azt jelenti, hogy az affinitási szabály csak azt kényszeríti, hogy a gyermek a szülővel lett ellátva. Nem biztosítja, hogy a szülő a gyermekkel együtt legyen. Ezért, ha van affinitási szabálysértés, és valamilyen okból kifolyólag nem lehetséges, hogy a gyermeket a szülő csomópontján helyezze át, akkor is, ha a szülőt a gyermek csomópontjának áthelyezte volna, a szülő nem lesz áthelyezve a gyermek csomópontjához. Ha a konfiguráció [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) igaz értékre állítja, akkor a írásirányát törlődni fog. Azt is fontos megjegyezni, hogy az affinitási kapcsolat nem lehet tökéletes vagy azonnal kényszerítve, mert a különböző szolgáltatások eltérő életciklusokkal rendelkeznek, és a meghibásodása és egymástól függetlenül is elhelyezhetők. Tegyük fel például, hogy a szülő hirtelen feladatátvételt hajt végre egy másik csomóponton, mert összeomlott. Először a fürterőforrás-kezelő és a Feladatátvételi felügyelő kezeli a feladatátvételt, mivel a szolgáltatások megtartása, konzisztens és elérhetővé tétele a prioritás. A feladatátvétel befejeződése után az affinitási kapcsolat megszakad, de a fürterőforrás-kezelő úgy gondolja, hogy minden rendben van, amíg nem észleli, hogy a gyermek nem a szülővel rendelkezik. Az ilyen típusú ellenőrzések rendszeres időközönként történnek. További információ arról, hogy a fürterőforrás-kezelő hogyan értékeli a korlátozásokat ebben a [cikkben](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), és [Ez](service-fabric-cluster-resource-manager-balancing.md) többet is megtudhat arról, hogyan konfigurálhatja azt a ritmust, amelyen a megkötések kiértékelése megtörténik.   


### <a name="partitioning-support"></a>Particionálás támogatása
Az affinitással kapcsolatos utolsó dolog, hogy az affinitási kapcsolatok nem támogatottak, ahol a szülő particionálva van. A particionált szülői szolgáltatások esetlegesen támogatottak, de jelenleg nem engedélyezettek.

## <a name="next-steps"></a>Következő lépések
- A szolgáltatások konfigurálásával kapcsolatos további információkért [tekintse meg a szolgáltatások konfigurálását](service-fabric-cluster-resource-manager-configure-services.md) ismertető témakört.
- Ha korlátozni szeretné a szolgáltatásokat a gépek kis csoportjára vagy a szolgáltatások terhelésének összesítésére, használja az [alkalmazáscsoport](service-fabric-cluster-resource-manager-application-groups.md) használatát

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png