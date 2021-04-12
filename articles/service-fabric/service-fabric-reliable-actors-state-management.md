---
title: Reliable Actors állapot kezelése
description: Leírja, hogy Reliable Actors állapot felügyelt, megőrzött és replikálható a magas rendelkezésre állás érdekében.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: badfc490f26b71881e7970c2c0be3472abfec25a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575601"
---
# <a name="reliable-actors-state-management"></a>Reliable Actors állapot kezelése
A Reliable Actors egyszálas objektumok, amelyek logika és állapot beágyazására is képesek. Mivel a szereplők Reliable Services futnak, megbízhatóan kezelhetik az állapotot ugyanazon megőrzési és replikációs mechanizmusok használatával. Így a szereplők nem veszítik el az állapotukat a meghibásodások után, az újraaktiválást követően, vagy a fürt csomópontjai között, erőforrás-kiegyensúlyozás vagy-verziófrissítés miatt.

## <a name="state-persistence-and-replication"></a>Állapot-megőrzés és replikálás
Minden Reliable Actors állapot- *nyilvántartónak* minősül, mivel minden egyes Actor-példány egy egyedi azonosítóra mutat. Ez azt jelenti, hogy az ugyanahhoz a színész-AZONOSÍTÓhoz intézett ismétlődő hívások ugyanahhoz a színészi példányhoz vannak irányítva. Az állapot nélküli rendszerekben az ügyfél-hívások nem garantáltak úgy, hogy minden alkalommal ugyanarra a kiszolgálóra legyenek irányítva. Emiatt a Actor Services mindig állapot-nyilvántartó szolgáltatás.

Bár a szereplők állapot-nyilvántartónak minősülnek, ez nem jelenti azt, hogy megbízhatóan kell tárolniuk az állapotot. A szereplők az adattárolási követelmények alapján választhatják ki az állapot megőrzése és a replikáció szintjét:

* Megőrzött **állapot**: a rendszer megőrzi az állapotot a lemezen, és három vagy több replikára replikálja. A megőrzött állapot a legtartós állapotú tárolási lehetőség, ahol az állapot a teljes fürt kimaradásán keresztül maradhat.
* **Illékony állapot**: az állapot három vagy több replikára replikálódik, és csak a memóriában marad. A felejtő állapot rugalmasságot biztosít a csomópont-meghibásodások és a színészi hibák, valamint a frissítések és az erőforrások kiegyensúlyozása során. Azonban az állapot nem marad a lemezen. Tehát ha az összes replika elvész egyszerre, az állapot is elvész.
* **Nincs** megőrzött állapot: az állapot nem replikálódik, vagy nincs lemezre írva, csak azokra a résztvevőkre használja, amelyeknek nem kell megbízható állapotot fenntartaniuk.

Az adatmegőrzés minden szintje egyszerűen egy másik *szolgáltató* és a szolgáltatás *replikációs* konfigurációja. Azt határozza meg, hogy az állapot a lemezre van-e írva, az állami szolgáltatótól függ – ez az összetevő egy megbízható szolgáltatás, amely az állapotot tárolja. A replikáció attól függ, hogy a szolgáltatás hány replikát telepít. A Reliable Serviceshoz hasonlóan az állami szolgáltató és a replika száma is könnyen beállítható manuálisan. A Actors Framework olyan attribútumot biztosít, amely egy szereplő esetében automatikusan kiválasztja az alapértelmezett állapot-szolgáltatót, és automatikusan létrehozza a replikák számának beállításait, hogy az a következő három adatmegőrzési beállítás egyikét érjen el. A származtatott osztály nem örökli a StatePersistence attribútumot, minden egyes Actor típusnak meg kell adnia a StatePersistence szintjét.

### <a name="persisted-state"></a>Megőrzött állapot
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Ez a beállítás egy olyan szolgáltatót használ, amely lemezen tárolja az adattárolást, és automatikusan beállítja a szolgáltatás replikáinak értékét 3-ra.

### <a name="volatile-state"></a>Illékony állapot
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Ez a beállítás csak a memóriában tárolt állapot-szolgáltatót használja, és a replika darabszámát 3-ra állítja.

### <a name="no-persisted-state"></a>Nincs megőrzött állapot
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Ez a beállítás a memóriában tárolt állapot-szolgáltatót használja, és a replika darabszámát 1-re állítja.

### <a name="defaults-and-generated-settings"></a>Alapértékek és generált beállítások
Az attribútum használatakor a `StatePersistence` rendszer automatikusan kijelöli az állami szolgáltatót a futtatáskor, amikor a Actor szolgáltatás elindul. A replika száma azonban a Visual Studio Actor Build eszközeinek fordítási ideje szerint van beállítva. A build-eszközök automatikusan létrehozzák a Actors szolgáltatás *alapértelmezett szolgáltatását* ApplicationManifest.xmlban. A paramétereket a rendszer a **minimális replika méretének** és a **célként megadott replika méretének** beállítására hozza létre.

Ezeket a paramétereket manuálisan is módosíthatja. Az `StatePersistence` attribútum minden módosításakor azonban a paraméterek a kiválasztott attribútum alapértelmezett replikakészlet-méret értékeire vannak beállítva `StatePersistence` , felülbírálva a korábbi értékeket. Más szóval a ServiceManifest.xmlban megadott értékek *csak* a létrehozáskor vannak felülbírálva, amikor megváltoztatja az `StatePersistence` attribútumérték értékét.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>State Manager
Minden színészi példány saját State Managerrel rendelkezik: egy olyan szótárhoz hasonló adatstruktúra, amely megbízhatóan tárolja a kulcs/érték párokat. A State Manager egy állami szolgáltató körüli burkoló. Az adatmegőrzési beállítástól függetlenül is tárolhatja az adattárolást. Nem biztosít garanciát arra, hogy a futó Actors szolgáltatás a működés közbeni frissítéssel megőrzött állapotra vonatkozó beállítással módosítható legyen az adatok megőrzése mellett. Azonban lehetséges, hogy egy futó szolgáltatás replikáinak száma módosítható.

Az állapot-kezelő kulcsai csak karakterláncok lehetnek. Az értékek általánosak, és bármilyen típus lehet, beleértve az egyéni típusokat is. Az állapot-kezelőben tárolt értékeknek szerializálható adategyezménynek kell lenniük, mert előfordulhat, hogy a hálózaton keresztül továbbítják őket más csomópontoknak a replikálás során, és előfordulhat, hogy a szereplő állapotának megőrzési beállításától függően lemezre íródnak.

A State Manager a megbízható szótárban találhatóhoz hasonló általános szótári módszereket tesz elérhetővé az állapot kezeléséhez.

Példák a Actors állapotának kezelésére, olvasási [hozzáférés, mentés és eltávolítás Reliable Actors állapot](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Ajánlott eljárások
Íme néhány ajánlott eljárás és hibaelhárítási tipp a színészi állapot kezeléséhez.

### <a name="make-the-actor-state-as-granular-as-possible"></a>A szereplő állapotának beállítása a lehető legrészletesebben
Ez az alkalmazás teljesítmény-és erőforrás-használatának szempontjából kritikus fontosságú. Amikor bármilyen írás/frissítés szerepel a szereplő "nevesített állapotában", a "nevesített állapotnak" megfelelő teljes érték szerializálva van, és a hálózaton keresztül küldi el a másodlagos replikákat.  A formátumú másodlagos zónák írja a helyi lemezre, és válaszoljon vissza az elsődleges replikára. Amikor az elsődleges visszaigazolást kap a másodlagos replikák kvórumáról, a rendszer az állapotot a helyi lemezre írja. Tegyük fel például, hogy az érték egy olyan osztály, amely 20 taggal és 1 MB méretű. Még ha csak az 1. méretű osztály tagjainak egyikét módosította, akkor a teljes 1 MB-ra kifizeti a szerializálási és hálózati és lemez-írási költségeket. Hasonlóképpen, ha az érték egy gyűjtemény (például egy lista, tömb vagy szótár), akkor is a teljes gyűjtemény díját kell megfizetnie, még akkor is, ha módosítja az egyik tagot. A Actors osztály StateManager felülete a szótárhoz hasonló. Mindig modellezheti a szereplő állapotot jelképező adatstruktúrát a szótár tetején.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>A színész életciklusának megfelelő kezelése
Egyértelmű szabályzattal kell rendelkeznie az állapot méretének kezeléséhez egy Actor szolgáltatás minden partícióján. A Actor szolgáltatásnak rögzített számú szereplőkkel kell rendelkeznie, és a lehető legnagyobb mértékben újra fel kell őket használni. Ha folyamatosan új szereplőkkel hoz létre, akkor azokat a munkájuk után törölnie kell. A Actors keretrendszer néhány metaadatot tárol a létező szereplőkről. Egy színész összes állapotának törlése nem távolítja el a szereplő metaadatait. Törölnie kell a szereplőt (lásd: a [szereplők törlése és állapota](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) a rendszeren tárolt összes információ eltávolításához. További ellenőrzésként le kell kérdezni a Actors szolgáltatást (lásd: [szereplők enumerálása](service-fabric-reliable-actors-enumerate.md)) egy ideje, hogy megbizonyosodjon róla, hogy a szereplők száma a várt tartományon belül van-e.
 
Ha azt tapasztalja, hogy egy Actor szolgáltatás adatbázis-fájljának mérete meghaladja a várt méretet, ügyeljen arra, hogy a fenti irányelveket követi. Ha követi ezeket az irányelveket, és továbbra is az adatbázis fájlméretével kapcsolatos problémákat tapasztal, [egy támogatási jegyet kell megnyitnia](service-fabric-support.md) a termék csapatával, hogy segítséget kapjon.

## <a name="next-steps"></a>Következő lépések

A Reliable Actorsban tárolt állapotot szerializálni kell a lemezre írás előtt, és a magas rendelkezésre állás érdekében replikálni kell őket. További információ a [Actor típusú szerializálásról](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Következő lépésként Ismerkedjen meg a [Actor Diagnostics szolgáltatással és a teljesítmény monitorozásával](service-fabric-reliable-actors-diagnostics.md).
