---
title: Service Fabric fürterőforrás-kezelő elhelyezési házirendjei
description: További elhelyezési házirendek és szabályok áttekintése Service Fabric szolgáltatásokhoz
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 263e45928642aa74d682fc490e424a24deeb8076
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790681"
---
# <a name="placement-policies-for-service-fabric-services"></a>A Service Fabric-szolgáltatások elhelyezési házirendjei
Az elhelyezési házirendek olyan további szabályok, amelyek segítségével szabályozható a szolgáltatások elhelyezése bizonyos konkrét, kevésbé gyakori helyzetekben. Ilyen forgatókönyvek például a következők:

- A Service Fabric-fürt földrajzi távolságokra, például több helyszíni adatközpontra vagy Azure-régióra terjed ki.
- A környezet kiterjed a geopolitikai vagy jogi szabályozás több területére, vagy olyan esetekre, amikor a szabályzatok határain belül kell kikényszeríteni
- A kommunikációs teljesítményre vagy késésre vonatkozó megfontolások nagy távolságok vagy lassabb vagy kevésbé megbízható hálózati kapcsolatok használata miatt
- Bizonyos számítási feladatokat a lehető legjobb közös elhelyezésű kell tartania, akár más számítási feladatokkal, akár az ügyfelek közelében
- Egyetlen csomóponton több állapot nélküli példánynak kell lennie egy partíción

A követelmények többsége a fürt fizikai elrendezésével van összhangban, amely a fürt tartalék tartománya. 

Az ilyen forgatókönyveket segítő speciális elhelyezési házirendek a következők:

1. Érvénytelen tartományok
2. Szükséges tartományok
3. Előnyben részesített tartományok
4. Replika csomagolásának letiltása
5. Több állapot nélküli példány engedélyezése a csomóponton

A következő vezérlők többsége konfigurálható a csomópont tulajdonságai és az elhelyezési megkötések használatával, de néhány bonyolultabb. A Service Fabric fürterőforrás-kezelője egyszerűbbé teszi a további elhelyezési házirendeket. Az elhelyezési házirendek egy névvel ellátott szolgáltatási példány alapján konfigurálhatók. Dinamikusan is frissíthetők.

## <a name="specifying-invalid-domains"></a>Érvénytelen tartományok meghatározása
A **InvalidDomain** elhelyezési házirend lehetővé teszi annak megadását, hogy egy adott tartalék tartomány egy adott szolgáltatás esetében érvénytelen. Ez a szabályzat biztosítja, hogy egy adott szolgáltatás soha ne fusson egy adott területen, például geopolitikai vagy vállalati házirend miatt. Több érvénytelen tartomány is megadható külön szabályzatok használatával.

<center>

![Érvénytelen tartományi példa][Image1]
</center>

Kód:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Szükséges tartományok megadása
A szükséges tartomány-elhelyezési házirend megköveteli, hogy a szolgáltatás csak a megadott tartományban legyen megtalálható. Több kötelező tartomány is megadható külön szabályzatok használatával.

<center>

![Kötelező tartományi példa][Image2]
</center>

Kód:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Előnyben részesített tartomány megadása egy állapot-nyilvántartó szolgáltatás elsődleges replikái számára
Az előnyben részesített elsődleges tartomány meghatározza azt a tartalék tartományt, amelybe az elsődleges helyet helyezni kívánja. Az elsődleges végpont ebben a tartományban, ha minden kifogástalan állapotú. Ha a tartomány vagy az elsődleges replika meghibásodik vagy leáll, az elsődleges áthelyezés egy másik helyre, ideális esetben ugyanabban a tartományban történik. Ha az új hely nincs az előnyben részesített tartományban, a fürterőforrás-kezelő a lehető leghamarabb áthelyezi az előnyben részesített tartományba. Ez a beállítás természetesen csak az állapot-nyilvántartó szolgáltatások esetében van értelme. Ez a szabályzat a legtöbb Azure-régióban vagy több adatközpontban átnyúló fürtök esetében hasznos, de olyan szolgáltatásokkal rendelkezik, amelyek előnyben részesítik az elhelyezést egy adott helyen. Az elsődleges felhasználók vagy más szolgáltatások közel tartása segít csökkenteni a késést, különösen az olvasások esetében, amelyeket alapértelmezés szerint az elsődlegesek kezelnek.

<center>

![Elsődleges tartományok és feladatátvétel][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Replika terjesztésének és a csomagolás letiltásának megkövetelése
A replikák _általában_ a hibák és a frissítési tartományok között oszlanak el, ha a fürt kifogástalan állapotban van. Vannak azonban olyan esetek, amikor egy adott partíció több replikája átmenetileg becsomagolható egyetlen tartományba. Tegyük fel például, hogy a fürt kilenc csomóponttal rendelkezik három tartalék tartományban, az FD:/0, az FD:/1 és az FD:/2. Azt is tegyük fel, hogy a szolgáltatás három replikával rendelkezik. Tegyük fel, hogy azok a csomópontok, amelyek az FD:/1 és az FD:/2-beli replikák esetében voltak használatban, leálltak. Általában a fürterőforrás-kezelő előnyben részesített más csomópontokat ugyanezen tartalék tartományokban. Ebben az esetben tegyük fel, hogy a kapacitási problémák miatt a tartományok más csomópontjai sem érvényesek. Ha a fürterőforrás-kezelő ezeket a replikákat építi fel, akkor a csomópontok közül kell kiválasztania a következőt: FD:/0. Ezzel azonban olyan _helyzetet hoz létre, amelyben_ a tartalék tartomány megkötése meg lett sértve. A csomagolási replikák növelik azt az esélyt, hogy a teljes replika leáll vagy elvész. 

> [!NOTE]
> A korlátozásokkal és a korlátozási prioritásokkal kapcsolatos további információkért tekintse meg [ezt a témakört](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Ha már látott egy egészségügyi üzenetet (például " `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain` "), akkor ezt a feltételt találta, vagy valami hasonló. Általában csak egy vagy két replika van csomagolva, ideiglenesen együtt. Ha az adott tartományban kevesebb, mint a replikák kvóruma, akkor biztonságban van. A csomagolás ritka, de előfordulhat, hogy ezek a helyzetek általában átmenetiek, mivel a csomópontok visszatérnek. Ha a csomópontok lemaradnak, és a fürterőforrás-kezelőnek ki kell építenie a cseréket, általában az ideális tartalék tartományokban más csomópontok is elérhetők.

Bizonyos munkaterhelések előnyben részesítették a replikák megcélzott számát, még akkor is, ha azok kevesebb tartományba vannak csomagolva. Ezek a számítási feladatok a teljes egyidejű állandó tartományi hibákra, és általában helyi állapot helyreállítására szolgálnak. Más munkaterhelések inkább az állásidőt vennék igénybe, mint a kockázat helyességét vagy elvesztését. A legtöbb éles számítási feladat több mint három replikával fut, több mint három tartalék tartománnyal és számos érvényes csomóponttal. Emiatt az alapértelmezett viselkedés lehetővé teszi a tartomány becsomagolását alapértelmezés szerint. Az alapértelmezett viselkedés lehetővé teszi a normál elosztást és feladatátvételt a szélsőséges esetek kezelésére, még akkor is, ha az ideiglenes tartományi csomagolást jelent.

Ha egy adott munkaterhelés esetében le szeretné tiltani az ilyen csomagolást, megadhatja a `RequireDomainDistribution` szabályzatot a szolgáltatásban. Ha ez a szabályzat be van állítva, a fürterőforrás-kezelő nem biztosít két replikát ugyanahhoz a partíciós vagy frissítési tartományhoz.

Kód:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Mostantól lehetséges lenne ezeket a konfigurációkat használni a fürt olyan szolgáltatásaihoz, amelyek földrajzilag nem voltak átnyúlva? Lehetséges, hogy ez nem egy nagyszerű ok. A szükséges, érvénytelen és előnyben részesített tartományi konfigurációkat el kell kerülni, ha a forgatókönyvek nem igénylik. Nincs értelme arra, hogy egy adott munkaterhelést egyetlen állványon futtasson, vagy a helyi fürt egyes szegmenseit egy másikra szeretné kipróbálni. A különböző hardveres konfigurációkat a tartalék tartományok között kell elosztani, és a normál elhelyezési korlátozásokkal és a csomópont tulajdonságaival kell kezelni őket.

## <a name="placement-of-multiple-stateless-instances-of-a-partition-on-single-node"></a>Egy partíció több állapot nélküli példányának elhelyezése egyetlen csomóponton
A **AllowMultipleStatelessInstancesOnNode** elhelyezési házirend lehetővé teszi egy partíció több állapot nélküli példányának elhelyezését egyetlen csomóponton. Alapértelmezés szerint egyetlen partíció több példánya nem helyezhető el egy csomóponton. Még az a-1 szolgáltatással sem lehet a fürtben lévő csomópontok számán túl méretezni a példányok számát az adott elnevezett szolgáltatás esetében. Ez az elhelyezési házirend eltávolítja ezt a korlátozást, és lehetővé teszi, hogy a InstanceCount nagyobb legyen, mint a csomópontok száma.

Ha már látott egy egészségügyi üzenetet (például " `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: ReplicaExclusion` "), akkor ezt a feltételt találta, vagy valami hasonló. 

A szabályzatnak a szolgáltatásban való megadásával a `AllowMultipleStatelessInstancesOnNode` InstanceCount a fürtben lévő csomópontok száma után is beállítható.

Kód:

```csharp
ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription allowMultipleInstances = new ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription();
serviceDescription.PlacementPolicies.Add(allowMultipleInstances);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless –PartitionSchemeSingleton –PlacementPolicy @(“AllowMultipleStatelessInstancesOnNode”) -InstanceCount 10 -ServicePackageActivationMode ExclusiveProcess 
```

> [!NOTE]
> Az elhelyezési házirend jelenleg előzetes verzióban érhető el, és a `EnableUnsupportedPreviewFeatures` fürt beállításai mögött van. Mivel ez a szolgáltatás előzetes verzióként érhető el, az előnézet konfigurációjának beállítása megakadályozza, hogy a fürt a vagy a rendszerre legyen frissítve. Más szóval létre kell hoznia egy új fürtöt, hogy kipróbálja a szolgáltatást.
>

> [!NOTE]
> Jelenleg a szabályzat csak olyan állapot nélküli szolgáltatások esetében támogatott, amelyeknél a ExclusiveProcess [Service Package aktiválási módja](/dotnet/api/system.fabric.description.servicepackageactivationmode)van.
>

> [!WARNING]
> A házirend nem támogatott statikus portok végpontokkal való használata esetén. A mindkét esetben a nem megfelelő állapotú fürtöket az ugyanazon a csomóponton található több példánya is megpróbálhatja ugyanahhoz a porthoz kötni, és nem tud elindulni. 
>

> [!NOTE]
> Ha nagy értékű [MinInstanceCount](/dotnet/api/system.fabric.description.statelessservicedescription.mininstancecount) használ ezzel az elhelyezési házirenddel, akkor az alkalmazások frissítései megakadnak. Ha például öt csomópontos fürttel rendelkezik, és a InstanceCount = 10 érték van beállítva, akkor minden csomóponton két példány fog szerepelni. Ha a MinInstanceCount = 9-et állítja be, a megkísérelt alkalmazások frissítése megakad; a MinInstanceCount = 8 használatával elkerülhető.
>

## <a name="next-steps"></a>Következő lépések
- A szolgáltatások konfigurálásával kapcsolatos további információkért [tekintse meg a szolgáltatások konfigurálását](service-fabric-cluster-resource-manager-configure-services.md) ismertető témakört.

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
