---
title: Tárolók és szolgáltatások erőforrás-szabályozása
description: Az Azure Service Fabric segítségével megadhatja a folyamatként vagy tárolóként futó szolgáltatások erőforrás-kérelmeit és korlátait.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: d760766870c8c2be0a2d2384f6d012b75bc92fbd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735658"
---
# <a name="resource-governance"></a>Erőforrások szabályozása

Ha több szolgáltatást futtat ugyanazon a csomóponton vagy fürtön, lehetséges, hogy az egyik szolgáltatás több erőforrást is felhasználhat, és a folyamat más szolgáltatásait is megéhezik. Ezt a problémát a "zajos szomszéd" problémának nevezzük. Az Azure Service Fabric lehetővé teszi, hogy a fejlesztő ezt a viselkedést a kérelmek és a szolgáltatási korlátok megadásával korlátozza az erőforrások használatának korlátozására.

> A cikk folytatása előtt javasoljuk, hogy ismerkedjen meg a [Service Fabric alkalmazás modelljével][application-model-link] és a [Service Fabric üzemeltetési modellel][hosting-model-link].
>

## <a name="resource-governance-metrics"></a>Erőforrás-irányítási mérőszámok

Az erőforrás-szabályozás Service Fabric a [szervizcsomaggal][application-model-link]összhangban támogatott. A szervizcsomaghoz rendelt erőforrások tovább oszthatók a csomagok között. Service Fabric támogatja a CPU-és memória-szabályozást a szervizcsomagok esetében, két beépített [metrikával](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (metrika neve `servicefabric:/_CpuCores` ): a gazdagépen elérhető logikai mag. Az összes csomóponton lévő összes mag súlyozása azonos.

* *Memória* (metrika neve `servicefabric:/_MemoryInMB` ): a memória megabájtban van kifejezve, és a számítógépen elérhető fizikai memóriára van leképezve.

Ezen két metrika esetében a [fürterőforrás-kezelő (CRM)][cluster-resource-manager-description-link] nyomon követi a fürt teljes kapacitását, a fürt egyes csomópontjainak terhelését, valamint a fürt többi erőforrását. Ez a két metrika egyenértékű a többi felhasználóval vagy egyéni metrikával. Az összes meglévő funkció használható együtt:

* A fürt a két metrika (alapértelmezett viselkedés) alapján [kiegyensúlyozott](service-fabric-cluster-resource-manager-balancing.md) lehet.
* A fürt a két metrika alapján is [feldarabolható](service-fabric-cluster-resource-manager-defragmentation-metrics.md) .
* [Fürt leírásakor][cluster-resource-manager-description-link]a rendszer pufferelt kapacitást állíthat be ehhez a két metrikához.

> [!NOTE]
> Ezeknél a metrikákban nem támogatott a [dinamikus betöltési jelentéskészítés](service-fabric-cluster-resource-manager-metrics.md) . a metrikák terhelése a létrehozáskor van meghatározva.

## <a name="resource-governance-mechanism"></a>Erőforrás-irányítási mechanizmus

Az 7,2-es verziótól kezdődően a Service Fabric futtatókörnyezet támogatja a processzor-és memória-erőforrások kéréseinek és korlátainak a meghatározását.

> [!NOTE]
> A 7,2-nál régebbi futtatókörnyezet-verziók csak olyan modelleket támogatnak, amelyekben a **kérelem** és az adott erőforrás (CPU vagy memória) **korlátja** egyaránt használható. Service Fabric Ezt a dokumentum **RequestsOnly** -specifikációja írja le.

* *Kérelmek:* A CPU-és memória-kérelmek értékei a fürterőforrás- [kezelő (CRM)][cluster-resource-manager-description-link] által a és a `servicefabric:/_CpuCores` metrikák számára használt terheléseket jelölik `servicefabric:/_MemoryInMB` . Ez azt jelenti, hogy a CRM a szolgáltatás erőforrás-felhasználását úgy tekinti, hogy megegyezzen a kérelmek értékeivel, és az elhelyezési döntések meghozatalakor ezeket az értékeket használja.

* *Korlátok:* A CPU-és a memória-határértékek azok a tényleges erőforrás-korlátok, amelyek akkor érvényesek, amikor egy folyamat vagy egy tároló aktiválva van egy csomóponton.

Service Fabric lehetővé teszi a **RequestsOnly, a LimitsOnly** és a **RequestsAndLimits** -specifikációk használatát a CPU-hoz és a memóriához.
* A RequestsOnly-specifikáció használatakor a Service Fabric korlátként is használja a kérelmek értékeit.
* A LimitsOnly-specifikáció használatakor a Service Fabric 0 értékűre tekinti a kérelmek értékeit.
* A RequestsAndLimits-specifikáció használatakor a határértékeknek a kérelmek értékeinél nagyobbnak vagy azzal egyenlőnek kell lenniük.

Az erőforrás-irányítási mechanizmus jobb megismeréséhez tekintse meg az elhelyezési forgatókönyvet a CPU-erőforrásra vonatkozó **RequestsOnly** -specifikációval (a memória szabályozásának mechanizmusa egyenértékű). Vegyünk egy olyan csomópontot, amely két CPU-magot és két szervizcsomagot is elhelyez. Az első elhelyezni kívánt szolgáltatáscsomag csak egy Container Code csomagból áll, és csak egy CPU-mag kérelmét adja meg. A második szolgáltatáscsomag, amely csak egy folyamaton alapuló kódú csomagból áll, és csak egy CPU-mag kérelmét adja meg. Mivel mindkét szervizcsomag rendelkezik RequestsOnly-specifikációval, a határértékek a kérelmek értékeire vannak beállítva.

1. Először a tároló alapú szervizcsomag, amely egy CPU Core-t kér, a csomópontra kerül. A futtatókörnyezet aktiválja a tárolót, és a CPU-korlátot egy mag értékre állítja. A tároló nem tud egynél több magját használni.

2. Ezután a folyamaton alapuló szervizcsomag egy CPU Core-t kér a csomópontra. A futtatókörnyezet aktiválja a szolgáltatás folyamatát, és beállítja a CPU-korlátot egy mag értékre.

Ezen a ponton a kérelmek összege egyenlő a csomópont kapacitásával. A CRM nem helyez további tárolókat vagy szolgáltatási folyamatokat a csomóponton található CPU-kérelmekkel. A csomóponton egy folyamat és egy tároló egyetlen alapszintű, és nem fog megbirkózni egymással a CPU-val.

Most nézzük újra a példát egy **RequestsAndLimits** -specifikációval. Ezúttal a Container-alapú szolgáltatáscsomag egy CPU-mag kérelmét határozza meg, és legfeljebb két CPU-magot. A folyamaton alapuló szervizcsomag a kérést és egy CPU-mag korlátot is meghatároz.
  1. Először a tároló-alapú szolgáltatáscsomag kerül a csomópontra. A futtatókörnyezet aktiválja a tárolót, és beállítja a CPU-korlátot két maggal. A tároló nem fogja tudni használni a kettőnél több magot.
  2. Ezután a folyamaton alapuló szervizcsomag a csomópontra kerül. A futtatókörnyezet aktiválja a szolgáltatás folyamatát, és beállítja a CPU-korlátot egy mag értékre.

  Ezen a ponton a csomóponton elhelyezett szolgáltatási csomagok CPU-kéréseinek összege megegyezik a csomópont CPU-kapacitásával. A CRM nem helyez további tárolókat vagy szolgáltatási folyamatokat a csomóponton található CPU-kérelmekkel. Azonban a csomóponton a korlátok összege (két mag a tárolóhoz + egy mag a folyamathoz) meghaladja a két mag kapacitását. Ha a tároló és a folyamat egy időben is megszakad, fennáll a valószínűsége a processzor erőforrásának. Az ilyen jellegű tartalmat a platform mögöttes operációs rendszer fogja összeállítani. Ebben a példában a tároló akár két CPU-magot is feltört, így a folyamat kérelme egy CPU-mag nem garantált.

> [!NOTE]
> Ahogy az előző példában is látható, a processzor és a memória kérelmezési értékei **nem vezetnek a csomópont erőforrásainak lefoglalásához**. Ezek az értékek a fürterőforrás-kezelő által az elhelyezési döntések meghozatalakor figyelembe vehető erőforrás-felhasználást jelölik. A határértékek azok a tényleges erőforrás-korlátok, amelyek akkor érvényesek, amikor egy folyamat vagy egy tároló aktiválva van egy csomóponton.


Vannak olyan helyzetek, amikor a CPU-ra lehet szükség. Ilyen helyzetekben a példánkban szereplő folyamat és tároló a zajos szomszéd problémát tapasztalhatja:

* *Irányított és nem szabályozott szolgáltatások és tárolók összekeverése*: Ha egy felhasználó a megadott erőforrás-szabályozás nélkül hoz létre szolgáltatást, a futtatókörnyezet nem igényel erőforrást, és elhelyezheti a példában szereplő csomóponton. Ebben az esetben ez az új folyamat hatékonyan felhasznál bizonyos CPU-t a csomóponton már futó szolgáltatások rovására. Ennek a problémának két megoldása van. Vagy ne keverje a szabályozott és nem szabályozott szolgáltatásokat ugyanazon a fürtön, vagy használjon [elhelyezési korlátozásokat](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) úgy, hogy ez a két típusú szolgáltatás ne legyen ugyanazon a csomópontokon.

* *Ha egy másik folyamat indul el a csomóponton, Service Fabricon kívül (például egy operációsrendszer-szolgáltatáson)*: ebben az esetben a folyamaton Service Fabric kívüli folyamat a meglévő szolgáltatásokkal rendelkező CPU-ra is érvényes. Ennek a problémának a megoldása a csomópont-kapacitások megfelelő beállítása az operációs rendszer terhelésének megfelelően, a következő szakaszban látható módon.

* *Ha a kérelmek nem felelnek meg a határértékeknek*: a fentiekben leírtak szerint a kérések nem vezetnek a csomóponton lévő erőforrások lefoglalásához a RequestsAndLimits. Ha egy olyan szolgáltatás, amely a kérelmeknél nagyobb korlátot helyez el egy csomóponton, akkor az erőforrás (ha elérhető) korlátozható. Ilyen esetekben előfordulhat, hogy a csomóponton lévő egyéb szolgáltatások nem tudják felhasználni az erőforrásokat a kérésük értékeire.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Fürt beállítása az erőforrás-szabályozás engedélyezéséhez

Amikor egy csomópont elindul és csatlakozik a fürthöz, Service Fabric észleli a rendelkezésre álló memória mennyiségét és a magok rendelkezésre álló számát, majd beállítja a csomópontok kapacitását a két erőforrás számára.

Ha meg szeretné hagyni az operációs rendszer pufferét, és a csomóponton futó egyéb folyamatok esetében a Service Fabric a csomóponton rendelkezésre álló erőforrások 80%-át használja. Ez a százalék konfigurálható, és módosítható a fürt jegyzékfájljában.

Íme egy példa arra, hogyan utasíthatja a Service Fabric a rendelkezésre álló CPU 50%-ában és a rendelkezésre álló memória 70%-ában:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

A legtöbb ügyfél és forgatókönyv esetében a processzor és a memória csomópont-kapacitásának automatikus észlelése az ajánlott konfiguráció (alapértelmezés szerint az automatikus észlelés be van kapcsolva). Ha azonban a csomópont-kapacitások teljes manuális beállítására van szüksége, a fürt csomópontjainak leírására szolgáló mechanizmus használatával konfigurálhatja azokat a csomópont-típusok alapján. Íme egy példa arra, hogyan állíthatja be a csomópont típusát négy maggal és 2 GB memóriával:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Ha a rendelkezésre álló erőforrások automatikus észlelése engedélyezve van, és a csomóponti kapacitások manuálisan vannak definiálva a fürt jegyzékfájljában, Service Fabric ellenőrzi, hogy a csomópont rendelkezik-e elegendő erőforrással a felhasználó által definiált kapacitás támogatásához:

* Ha a jegyzékfájlban definiált csomópont-kapacitások kisebbek vagy egyenlőek a csomópont rendelkezésre álló erőforrásaival, akkor Service Fabric a jegyzékfájlban megadott kapacitásokat használja.

* Ha a jegyzékfájlban definiált csomópont-kapacitások nagyobbak, mint a rendelkezésre álló erőforrások, Service Fabric a rendelkezésre álló erőforrásokat használja csomópont-kapacitásként.

A rendelkezésre álló erőforrások automatikus észlelése kikapcsolható, ha nincs rá szükség. A kikapcsolásához módosítsa a következő beállítást:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Az optimális teljesítmény érdekében a következő beállítást is be kell kapcsolni a fürt jegyzékfájljában:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Az 7,0-Service Fabric es verziótól kezdődően a rendszer frissítette a csomópont-erőforrás-kapacitások kiszámításának szabályát azokban az esetekben, amikor a felhasználó manuálisan megadja a csomóponti erőforrás-kapacitások értékeit. Tekintsük át a következő forgatókönyvet:
>
> * A csomóponton összesen 10 CPU-mag található.
> * Az SF úgy van konfigurálva, hogy a felhasználói szolgáltatások teljes erőforrásainak 80%-át használja (alapértelmezett beállítás), amely 20%-os puffert hagy a csomóponton futó többi szolgáltatáshoz (beleértve Service Fabric rendszerszolgáltatásokat).
> * A felhasználó úgy dönt, hogy manuálisan felülbírálja a CPU-magok metrikai csomópontjának erőforrás-kapacitását, és beállítja 5 maggal
>
> Módosítottuk azt a szabályt, hogy a Service Fabric felhasználói szolgáltatások rendelkezésre álló kapacitása a következő módon legyen kiszámítva:
>
> * A 7,0 Service Fabric előtt a felhasználói szolgáltatások rendelkezésre álló kapacitása **5 maggal** lesz kiszámítva (a 20%-os kapacitási puffer figyelmen kívül lesz hagyva)
> * A Service Fabric 7,0-től kezdődően a felhasználói szolgáltatások rendelkezésre álló kapacitása **4 mag** (a 20%-os kapacitási puffer figyelmen kívül hagyása) lesz kiszámítva.

## <a name="specify-resource-governance"></a>Erőforrás-szabályozás meghatározása

Az erőforrás-irányítási kérelmek és korlátok az Application manifest (ServiceManifestImport) szakaszban vannak megadva. Íme, néhány példa:

**1. példa: RequestsOnly-specifikáció**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

Ebben a példában az `CpuCores` attribútum az 1 CPU Core **ServicePackageA**-hez tartozó kérelem megadására szolgál. Mivel nincs megadva a CPU-korlát ( `CpuCoresLimit` attribútum), Service Fabric az 1 mag megadott kérés értékét is használja a szervizcsomaghoz tartozó CPU-korlátként.

A **ServicePackageA** csak olyan csomópontokon helyezhetők el, ahol a **csomóponton elhelyezett összes szervizcsomag CPU-kérelmei összegének** kivonása után a fennmaradó CPU-kapacitás nagyobb vagy egyenlő, mint 1 mag. A csomóponton a szervizcsomag egyetlen mag-ra lesz korlátozva. A szervizcsomag két kódot tartalmaz (**CodeA1** és **CodeA2**), és mindkettőben megadja az `CpuShares` attribútumot. A CpuShares 512:256-es arányát használjuk az egyes kódokhoz tartozó CPU-korlátok kiszámítására. Így a CodeA1 a mag kétharmadára korlátozódik, és a CodeA2 a mag egy harmadára lesz korlátozva. Ha a CpuShares nincsenek megadva az összes kódhoz, Service Fabric egyenlően osztja el a CPU-korlátot a kettő között.

Míg a CpuShares megadott érték a szervizcsomag összesített CPU-korlátjának relatív részét képviseli, a kódok memóriájának értéke abszolút értékben van megadva. Ebben a példában az `MemoryInMB` attribútum a 1024 MB memória-kérések megadására szolgál mind a CodeA1, mind a CodeA2 esetében. Mivel a memória korlátja ( `MemoryInMBLimit` attribútuma) nincs megadva, a Service Fabric a megadott kérési értékeket is használja a kód csomagjainak korlátaiként. A szervizcsomaghoz tartozó memória-kérést (és korlátot) a rendszer a memória-kérelem (és a korlát) értékeinek összege alapján számítja ki. Így a **ServicePackageA** és a korlát 2048 MB-ként lesz kiszámítva.

A **ServicePackageA** csak olyan csomópontra kerül, ahol a **csomóponton elhelyezett összes szervizcsomaghoz tartozó memória-kérelmek összegének** kivonása után az 2048 MB-nál nagyobb vagy azzal egyenlő. A csomóponton mindkét kód csomagjai 1024 MB-nyi memóriára lesznek korlátozva. A kódok (tárolók vagy folyamatok) nem képesek több memóriát lefoglalni ennél a korlátnál, és ennek megkísérlése a memóriában lévő kivételeket eredményez.

**2. példa: LimitsOnly-specifikáció**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
Ez a példa `CpuCoresLimit` azokat a és `MemoryInMBLimit` attribútumokat használja, amelyek csak a 7,2-es és újabb verziójú SF-verziókban érhetők el. A CpuCoresLimit attribútum a **ServicePackageA** 1 mag CPU-korlátjának megadására szolgál. Mivel a CPU-kérelem ( `CpuCores` attribútum) nincs megadva, a rendszer 0 értéknek számít. `MemoryInMBLimit` az attribútummal megadható a CodeA1 és a CodeA2 1024 MB-os memória-korlátja, és mivel a kérések ( `MemoryInMB` attribútumok) nincsenek megadva, a rendszer 0-ként számít. A **ServicePackageA** vonatkozó memória-kérelem és-korlát így 0 és 2048 között van kiszámítva. Mivel a **ServicePackageA** -hez tartozó CPU-és memória-kérések is 0, nem jelent terhelést a CRM számára az elhelyezés, a `servicefabric:/_CpuCores` és a `servicefabric:/_MemoryInMB` metrikák esetében. Ezért egy erőforrás-irányítási szempontból a **ServicePackageA** bármely csomópontra elhelyezhető, **a fennmaradó kapacitástól függetlenül**. A csomóponton az 1. példához hasonlóan a CodeA1 a Core és 1024 MB memória kétharmadára korlátozódik, és a CodeA2 a Core és a 1024 MB memória egyharmadát fogja korlátozni.

**3. példa: RequestsAndLimits-specifikáció**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
Ez a példa az első két példát mutatja be, és a PROCESSZORra és a memóriára vonatkozó kérelmeket és korlátozásokat egyaránt megadja. A **ServicePackageA** 1 mag és 3072 (1024 + 2048) MB méretű CPU-és memória-kérelmeket tartalmaz. Csak olyan csomópontra helyezhető el, amely legalább 1 mag (és 3072 MB) kapacitást tartalmaz, és a csomópont teljes CPU-(és memória-) kapacitásának kivonása után a csomóponton elhelyezett összes szolgáltatás összes CPU-(és memória-) kérelmének összegét kivonta. A csomóponton a CodeA1 2 maggal vagy 3072 MB memóriára korlátozódik, míg a CodeA2 a 2 maggal és a 4096 MB memóriára korlátozódik.

### <a name="using-application-parameters"></a>Alkalmazás paramétereinek használata

Az erőforrás-irányítási beállítások megadásakor az [alkalmazás paramétereinek](service-fabric-manage-multiple-environment-app-configuration.md) használatával több alkalmazás-konfiguráció is kezelhető. Az alábbi példa az alkalmazás paramétereinek használatát mutatja be:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

Ebben a példában az alapértelmezett paraméterérték az éles környezethez van beállítva, ahol az egyes szervizcsomagok 4 magot és 2 GB memóriát kapnak. Az alapértelmezett értékek módosíthatók az Application paraméter fájljaival. Ebben a példában egy paraméteres fájl használható az alkalmazás helyi tesztelésére, ha kevesebb erőforrást fog kapni, mint az éles környezetben:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> Az erőforrás-szabályozás és az alkalmazás paramétereinek megadása Service Fabric 6,1-es verziótól kezdődően érhető el.<br>
>
> Ha az alkalmazás paramétereinek használatával határozza meg az erőforrás-szabályozást, Service Fabric nem lehet visszaminősíteni az 6,1-es verzió előtti verzióra.

## <a name="enforcing-the-resource-limits-for-user-services"></a>A felhasználói szolgáltatások erőforrás-korlátainak érvényesítése

Az erőforrás-szabályozásnak a Service Fabric-szolgáltatásokra való alkalmazása során garantálható, hogy az erőforrás-szabályozott szolgáltatások nem haladhatják meg az erőforrás-kvótát, sok felhasználónak továbbra is le kell futtatnia a Service Fabric szolgáltatásait a nem szabályozott módban. A nem szabályozott Service Fabric szolgáltatások használatakor lehetséges, hogy olyan helyzetekben is futtathatók, ahol a "szökevény" nem szabályozott szolgáltatások a Service Fabric-csomópontokon elérhető összes erőforrást használják, ami a következő súlyos problémákhoz vezethet:

* A csomópontokon futó egyéb szolgáltatások (köztük a Service Fabric rendszerszolgáltatások) erőforrásainak éhezése
* Sérült állapotban végződő csomópontok
* Nem válaszol Service Fabric fürt felügyeleti API-jai

Az ilyen helyzetek elkerülése érdekében Service Fabric lehetővé teszi, hogy a *csomóponton futó összes Service Fabric felhasználói szolgáltatáshoz érvényesítse az erőforrás-korlátozásokat* (mind a szabályozott, mind a nem szabályozott), hogy a felhasználói szolgáltatások soha ne használják a megadott mennyiségű erőforrást. Ezt úgy érheti el, ha a ClusterManifest PlacementAndLoadBalancing szakaszában a EnforceUserServiceMetricCapacities konfiguráció értékét True értékre állítja. Ez a beállítás alapértelmezés szerint ki van kapcsolva.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

További megjegyzések:

* Az erőforrás-korlátozás kényszerítése csak a `servicefabric:/_CpuCores` és az `servicefabric:/_MemoryInMB` erőforrás-metrikára vonatkozik
* Az erőforrás-korlátozás kényszerítése csak akkor működik, ha az erőforrás-metrikák csomópont-kapacitása Service Fabric elérhető az automatikus észlelési mechanizmuson keresztül, vagy a felhasználók manuálisan, a csomópontok kapacitásának megadásával (ahogy azt az [erőforrás-irányítás engedélyezése](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) című szakasz ismerteti). Ha a csomópont kapacitása nincs konfigurálva, az erőforrás-korlát kényszerítési funkciója nem használható, mert Service Fabric nem tudja, hogy mennyi erőforrást kell lefoglalni a felhasználói szolgáltatásokhoz. Ha a "EnforceUserServiceMetricCapacities" érték igaz, de a csomópont kapacitása nincs konfigurálva, a Service Fabric állapot figyelmeztetést ad ki.

## <a name="other-resources-for-containers"></a>A tárolók egyéb erőforrásai

A processzor és a memória mellett más erőforrás-korlátok is megadhatók a tárolók számára. Ezek a korlátok a kód csomag szintjén vannak megadva, és a rendszer a tároló indításakor alkalmazza őket. A CPU-val és a memóriával ellentétben a fürterőforrás-kezelő nem ismeri ezeket az erőforrásokat, és nem hajtja végre a kapacitás-ellenőrzéseket és a terheléselosztást.

* *MemorySwapInMB*: a tároló által használható swap memória mennyisége.
* *MemoryReservationInMB*: a memória-szabályozáshoz szükséges, csak akkor kényszerített, ha a csomóponton a memória-tartalom észlelhető.
* *CpuPercent*: a tároló által használható CPU százalékaránya. Ha CPU-kérelmek vagy-korlátok vannak megadva a szervizcsomaghoz, ez a paraméter ténylegesen figyelmen kívül lesz hagyva.
* *MaximumIOps*: a tároló által használható maximális IOPS (olvasás és írás).
* *MaximumIOBytesps*: a tároló által használható maximális i/o-érték (bájt/s) (olvasási és írási).
* *BlockIOWeight*: az i/o súlyozásának letiltása a többi tárolóhoz viszonyítva.

Ezek az erőforrások kombinálhatók a PROCESSZORral és a memóriával. Az alábbi példa bemutatja, hogyan határozhat meg további erőforrásokat a tárolók számára:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Következő lépések

* A fürterőforrás-kezelővel kapcsolatos további információkért olvassa el [a Service Fabric fürterőforrás-kezelő bemutatása](service-fabric-cluster-resource-manager-introduction.md)című témakört.
* Ha többet szeretne megtudni az alkalmazás modelljéről, a szervizcsomagokról és a kódokról, valamint arról, hogy miként képezhetők le a replikák – olvassa el a [modell alkalmazást Service Fabricban][application-model-link].

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md
