---
title: 'Az alkalmazás frissítése: adatszerializálás'
description: Ajánlott eljárások az adatszerializáláshoz, valamint a működés közbeni alkalmazások frissítésének módjáról.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 9474a0bf8041ac541389f782f60bf1220d690cde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575635"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Az adatszerializálás hatása az alkalmazás frissítésére
Egy [működés közbeni alkalmazás frissítése](service-fabric-application-upgrade.md)esetén a frissítés a csomópontok egy részhalmazára lesz alkalmazva, egyszerre egy frissítési tartomány. A folyamat során egyes frissítési tartományok az alkalmazás újabb verziójára vonatkoznak, és néhány frissítési tartomány az alkalmazás régebbi verziójára mutat. A bevezetés során az alkalmazás új verziójának képesnek kell lennie az adatai régi verziójának olvasására, és az alkalmazás régi verziójának képesnek kell lennie az adatai új verziójának olvasására. Ha az adatformátum nem továbbítható, és visszafelé nem kompatibilis, a frissítés meghiúsulhat, vagy rosszabb lehet, az adat elveszhet vagy sérült lehet. Ez a cikk azt ismerteti, hogy mit jelent az adatformátuma, és ajánlott eljárásokat biztosít az adattovábbítás és a visszamenőleges kompatibilitás biztosításához.

## <a name="what-makes-up-your-data-format"></a>Mi teszi az adatformátumot?
Az Azure Service Fabricban a megőrzött és replikált adatok a C#-osztályokból származnak. A [megbízható gyűjteményeket](service-fabric-reliable-services-reliable-collections.md)használó alkalmazások esetében ez az érték a megbízható szótárakban és várólistákban lévő objektumok. A [Reliable Actorst](service-fabric-reliable-actors-introduction.md)használó alkalmazások esetében ez a biztonsági másolati állapot. Ezeket a C# osztályokat szerializálni kell a megőrzéshez és a replikáláshoz. Ezért az adatformátumot a szerializált mezők és tulajdonságok határozzák meg, valamint a szerializált módon. Például az `IReliableDictionary<int, MyClass>` adatsorok szerializálva `int` és szerializálva vannak `MyClass` .

### <a name="code-changes-that-result-in-a-data-format-change"></a>Adatformátumot eredményező kód módosítása
Mivel az adatformátumot C# osztályok határozzák meg, az osztályok módosításai az adatformátum megváltozását okozhatják. Ügyelni kell arra, hogy a működés közbeni frissítés képes legyen az adatformátum módosítására. Az adatformátum változását okozó példák:

* Mezők vagy tulajdonságok hozzáadása vagy eltávolítása
* Mezők vagy tulajdonságok átnevezése
* A mezők vagy tulajdonságok típusának módosítása
* Az osztály nevének vagy névterének módosítása

### <a name="data-contract-as-the-default-serializer"></a>Adategyezmény alapértelmezett szerializáló
A szerializáló általában az adat olvasására és a jelenlegi verzióra való deszerializálására szolgál, még akkor is, ha az adat régebbi vagy *újabb* verziójú. Az alapértelmezett szerializáló az [adategyezmény-szerializáló](/dotnet/framework/wcf/feature-details/using-data-contracts), amely jól definiált verziószámozási szabályokkal rendelkezik. A megbízható gyűjtemények lehetővé teszik a szerializáló felülbírálását, de a Reliable Actors jelenleg nem. Az adatszerializáló fontos szerepet játszik a működés közbeni frissítések engedélyezésében. Az adategyezmény-szerializáló a Service Fabric alkalmazásokhoz javasolt szerializáló.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Az adatformátum hatása a működés közbeni frissítésre
A működés közbeni frissítés során két fő forgatókönyv jelenhet meg, amelyekben a szerializáló az adatok egy régebbi vagy *újabb* verzióját észlelheti:

1. Miután frissített egy csomópontot, és elindítja a biztonsági mentést, az új szerializáló betölti azokat az adatlemezeket, amelyeket a régi verzióban tárolt.
2. A működés közbeni frissítés során a fürt a kód régi és új verzióinak kombinációját fogja tartalmazni. Mivel a replikák különböző frissítési tartományokban helyezhetők el, és a replikák egymásba küldik az adatait, a szerializáló új és/vagy régi verziója is felmerülhet.

> [!NOTE]
> Az "új verzió" és a "régi verzió" itt a futtatott kód verziójára hivatkozik. Az "új szerializáló" az alkalmazás új verziójában futtatott szerializáló kódját jelöli. Az "új adatok" kifejezés az alkalmazás új verziójának szerializált C# osztályára hivatkozik.
> 
> 

A kód és az adatformátum két verziójának a továbbítás és a visszamenőlegesen kompatibilisnek kell lennie. Ha nem kompatibilisek, előfordulhat, hogy a működés közbeni frissítés meghiúsul, vagy előfordulhat, hogy az adat elvész. Előfordulhat, hogy a működés közbeni frissítés meghiúsul, mert a kód vagy a szerializáló kivételeket vagy hibát okozhat, ha a másik verziót találkozik. Az adatvesztés történhet, ha például új tulajdonság lett hozzáadva, de a régi szerializáló a deszerializálás során elveti azt.

Az adategyezmény a javasolt megoldás annak biztosítására, hogy az adatai kompatibilisek legyenek. Jól definiált verziószámozási szabályokkal rendelkezik a mezők hozzáadásához, eltávolításához és módosításához. Emellett támogatja az ismeretlen mezők kezelését, a szerializálási és a deszerializálási folyamat összekötését, valamint az osztály öröklődésének kezelését. További információ: az [adategyezmény használata](/dotnet/framework/wcf/feature-details/using-data-contracts).

## <a name="next-steps"></a>Következő lépések
[Az alkalmazás a Visual Studióval történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítésén a Visual Studióval.

[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti az alkalmazás frissítésén a PowerShell használatával.

Annak szabályozása, hogy az alkalmazás hogyan legyen [frissítve a frissítési paraméterek](service-fabric-application-upgrade-parameters.md)használatával.

Ismerje meg, hogyan használhatja a speciális funkciókat az alkalmazás frissítéséhez a [speciális témakörökre](service-fabric-application-upgrade-advanced.md)való hivatkozással.

Az alkalmazások frissítéseinek [hibaelhárításával](service-fabric-application-upgrade-troubleshooting.md)kapcsolatos gyakori problémák elhárítása.
