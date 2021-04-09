---
title: A Reliable Collections használata
description: Ismerje meg a megbízható gyűjtemények Azure Service Fabric alkalmazáson belüli használatának ajánlott eljárásait.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 908f24cc22c969a34a513ff3fd3ceaa788420620
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98787875"
---
# <a name="working-with-reliable-collections"></a>A Reliable Collections használata
Service Fabric egy megbízható gyűjtemények használatával a .NET-fejlesztők számára elérhető állapot-nyilvántartó programozási modellt kínál. Pontosabban, Service Fabric megbízható szótárt és megbízható üzenetsor-osztályokat biztosít. Ha ezeket az osztályokat használja, az állapota particionálva van (a méretezhetőség érdekében), a replikált (rendelkezésre álláshoz), és egy partíción belül (a savas szemantika esetében). Nézzük meg egy megbízható szótár tipikus használatát, és lássuk, mit csinál valójában.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
   await Task.Delay(100);
}
```

A megbízható szótárak objektumainak összes művelete (kivéve a ClearAsync, amely nem vonható vissza) ITransaction objektumot igényel. Ez az objektum társítva van minden olyan módosításhoz, amelyet egy adott partíción belüli megbízható szótárban és/vagy megbízható üzenetsor-objektumokon próbál végrehajtani. A ITransaction objektum beolvasásához hívja meg a partíció StateManager's CreateTransaction metódusát.

A fenti kódban a ITransaction objektum egy megbízható szótár AddAsync metódusának lesz átadva. Belsőleg az olyan szótári metódusok, amelyek elfogadnak egy kulcsot, a kulcshoz tartozó olvasó/író zárolást vesznek fel. Ha a metódus módosítja a kulcs értékét, a metódus írási zárolást hajt a kulcshoz, és ha a metódus csak a kulcs értékét olvassa be, akkor a kulcson olvasási zárolást kell végrehajtani. Mivel a AddAsync módosítja a kulcs értékét az új, átadott értékre, a rendszer a kulcs írási zárolását veszi fel. Tehát ha a két (vagy több) szál ugyanazon a kulccsal kísérli meg az értékek hozzáadását, az egyik szál beolvassa az írási zárolást, és a többi szál blokkolva lesz. Alapértelmezés szerint a metódusok legfeljebb 4 másodpercig blokkolják a zárolást. 4 másodperc elteltével a metódusok eldobják a Timeoutexception osztályról. A metódus túlterhelése lehetővé teszi, hogy explicit időtúllépési értéket adjon át, ha szeretné.

A kódot általában úgy kell megírnia, hogy reagáljon a Timeoutexception osztályról, és megpróbálja megpróbálkozni a teljes művelettel (a fenti kódban látható módon). Ebben az egyszerű kódban csak a feladatát hívja meg. az 100 milliszekundumos késleltetést minden alkalommal. A valóságban azonban érdemes lehet jobban kipróbálni valamilyen exponenciális visszalépési késleltetést.

A zárolás beszerzését követően a AddAsync hozzáadja a kulcs-érték objektumot a ITransaction objektumhoz társított belső ideiglenes szótárhoz. Ezt úgy teheti meg, hogy a saját írási szemantikai adatait adja meg. Ez azt eredményezi, hogy a AddAsync meghívása után a TryGetValueAsync egy későbbi meghívása ugyanazzal a ITransaction-objektummal akkor is visszaküldi az értéket, ha még nem véglegesítette a tranzakciót.

> [!NOTE]
> A TryGetValueAsync új tranzakcióval való meghívása az utolsó véglegesített értékre mutató hivatkozást ad vissza. Ne módosítsa a hivatkozást közvetlenül, mivel a megkerüli a változtatások megőrzésére és replikálására szolgáló mechanizmust. Azt javasoljuk, hogy az értékek csak olvashatók legyenek, hogy a kulcsok értékének egyetlen módja a megbízható szótár API-k használatával legyen módosítva.

Ezután a AddAsync szerializálja a kulcs-érték objektumokat és a bájtos tömböket, és hozzáfűzi ezeket a bájtos tömböket a helyi csomóponton található naplófájlhoz. Végül a AddAsync elküldi a bájtos tömböket az összes másodlagos replikára, hogy ugyanazokat a kulcs/érték adatokat használják. Annak ellenére, hogy a kulcs/érték információ egy naplófájlba lett írva, az információ nem tekintendő a szótárnak, amíg a hozzá társított tranzakció véglegesítve lett.

A fenti kódban a CommitAsync hívása véglegesíti a tranzakció összes műveletét. Pontosabban hozzáfűzi a naplófájlt a helyi csomóponton, és a véglegesítő rekordot is elküldi az összes másodlagos replikának. Ha a replikák kvóruma (többsége) válaszolt, az összes adatváltozás állandónak minősül, és a ITransaction objektumon keresztül manipulált kulcsokhoz társított zárolások fel lesznek szabadítva, így más szálak/tranzakciók is kezelhetik ugyanazokat a kulcsokat és azok értékeit.

Ha a CommitAsync nincs meghívva (általában egy kivétel miatt), akkor a ITransaction objektum el lesz távolítva. Egy nem véglegesített ITransaction objektum ártalmatlanításakor Service Fabric hozzáfűzi a megszakítási adatokat a helyi csomópont naplófájljában, és semmit sem kell elküldeni a másodlagos replikák egyikéhez sem. Ezután a rendszer a tranzakción keresztül manipulált kulcsokhoz társított zárolásokat is felszabadítja.

## <a name="volatile-reliable-collections"></a>Felejtő megbízható gyűjtemények 
Bizonyos számítási feladatokban, például egy replikált gyorsítótárban, az alkalmi adatvesztés tolerálható. Az adatok lemezre való megőrzésének elkerülése lehetővé teszi a jobb késést és adatátvitelt a megbízható szótárakba való írás során. Az adatmegőrzés hiánya miatti kompromisszum azt eredményezi, hogy a kvórum elvesztése esetén a teljes adatvesztés történik. Mivel a kvórum elvesztése ritkán fordul elő, a megnövekedett teljesítmény érdemes lehet a munkaterhelések adatvesztésének ritka lehetősége.

Jelenleg a felejtő támogatás csak megbízható szótárak és megbízható várólisták esetén érhető el, és nem ReliableConcurrentQueues. Tekintse meg a [kikötések](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) listáját, hogy tájékoztassa döntését arról, hogy az illékony gyűjtemények használatát kívánja-e használni.

A szolgáltatásban az illékony támogatás engedélyezéséhez állítsa be a ```HasPersistedState``` jelzőt a szolgáltatás típusa deklarációban a következőhöz ```false``` hasonlóan:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>A meglévő megőrzött szolgáltatások nem hozhatók létre illékonyak, és fordítva. Ha ezt szeretné tenni, törölnie kell a meglévő szolgáltatást, majd telepítenie kell a szolgáltatást a frissített jelzővel. Ez azt jelenti, hogy ha módosítani szeretné a jelzőt, készen kell állnia a teljes adatvesztésre ```HasPersistedState``` . 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Gyakori buktatók és azok elkerülésének módjai
Most, hogy megértette, hogy a megbízható gyűjtemények hogyan működnek belsőleg, vessünk egy pillantást a gyakori félreértésekre. Lásd az alábbi kódot:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Ha normál .NET-szótárral dolgozik, hozzáadhat egy kulcsot/értéket a szótárhoz, majd módosíthatja egy tulajdonság értékét (például LastLogin). Azonban ez a kód nem fog megfelelően működni a megbízható szótárral. Ne feledje, hogy a korábbi vitafórumban a AddAsync-hívás a kulcs/érték objektumokat bájt tömbökre szerializálja, majd a tömböket egy helyi fájlba menti, és a másodlagos replikákat is elküldi. Ha később módosít egy tulajdonságot, ez a tulajdonság értékét csak a memóriában módosítja; Ez nem befolyásolja a helyi fájlt vagy a replikák számára továbbított adatvesztést. Ha a folyamat összeomlik, a memóriában lévő memória eldobása folyamatban van. Ha egy új folyamat elindul, vagy ha egy másik replika válik elsődlegesnek, akkor a régi tulajdonság értéke az, ami elérhető.

Nem tudom eléggé hangsúlyozni, hogy milyen egyszerű a fent látható hiba. A hiba esetén pedig csak akkor fog megjelenni, ha a folyamat leáll. A kód írásához a helyes módszer a két sor megfordítása:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Az alábbi példa egy gyakori hibát mutat be:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

A normál .NET-szótárakkal együtt a fenti kód jól működik, és egy gyakori minta: a fejlesztő egy kulcs használatával keres egy értéket. Ha az érték létezik, a fejlesztő megváltoztatja a tulajdonság értékét. A megbízható gyűjtemények esetében azonban ez a kód ugyanazt a problémát mutatja be, mint a már tárgyalt: **nem kell módosítania az objektumokat, ha azt egy megbízható gyűjteményhez adta.**

Egy megbízható gyűjteményben lévő érték módosításának helyes módja, ha a meglévő értékre mutató hivatkozást kap, és figyelembe veszi a jelen hivatkozás által hivatkozott objektumot. Ezután hozzon létre egy új objektumot, amely az eredeti objektum pontos másolata. Most módosíthatja az új objektum állapotát, és megírhatja az új objektumot a gyűjteménybe, hogy az a bájtos tömbökbe legyen szerializálva, és a helyi fájlhoz legyen hozzáfűzve, és a replikák számára legyen elküldve. Miután elvégezte a módosítás (oka) t, a memóriában lévő objektumokat, a helyi fájlt és az összes replikát pontosan megegyező állapotban van. Minden jó!

Az alábbi kód azt mutatja be, hogyan frissíthető egy érték egy megbízható gyűjteményben:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Megváltoztathatatlan adattípusok definiálása a programozói hiba megelőzésére
Ideális esetben azt szeretnénk, ha a fordító hibát jelez, amikor véletlenül olyan kódot hoz létre, amely egy olyan objektum állapotát vizsgálja, amelyet érdemes lehet átgondolni. A C# fordító azonban nem képes erre. A lehetséges programozói hibák elkerülése érdekében javasoljuk, hogy a megbízható gyűjteményekkel használt típusokat a nem módosítható típusokként határozza meg. Ez azt jelenti, hogy az alapértékek típusaihoz (például számok [Int32, UInt64 stb.], DateTime, GUID, TimeSpan és hasonlók) kell ragaszkodnia. Karakterláncot is használhat. Érdemes elkerülni, hogy a gyűjtemény tulajdonságai szerializálás és deszerializálás közben is gyakran sérültek a teljesítményt. Ha azonban a gyűjtemény tulajdonságait szeretné használni, javasoljuk, hogy használja az használatát. A NET megváltoztathatatlan gyűjtemények könyvtára ([System. Collections. változtathatatlan](https://www.nuget.org/packages/System.Collections.Immutable/)). Ez a könyvtár letölthető innen: https://nuget.org . Javasoljuk továbbá az osztályok lezárását és a mezők olvasását, amikor csak lehetséges.

Az alábbi UserInfo azt mutatja be, hogyan határozhat meg egy nem módosítható típust a fenti javaslatok kihasználása érdekében.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

A elemazonosító típusa egy nem módosítható típus is, ahogy az itt látható:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Séma verziószámozása (frissítések)
Belsőleg a megbízható gyűjtemények a használatával szerializálják az objektumokat. NET DataContractSerializer. A szerializált objektumok megmaradnak az elsődleges replika helyi lemezén, és a másodlagos replikák is továbbítva lesznek. A szolgáltatás futása során valószínű, hogy módosítani szeretné a szolgáltatás által igényelt adattípust (sémát). Rendkívül körültekintően kezelheti adatait. Az első és legfontosabb, hogy mindig képes legyen deszerializálni a régieket. Pontosabban, ez azt jelenti, hogy a deszerializálási kódnak végtelen visszafelé kompatibilisnek kell lennie: a szolgáltatási kód 333-es verziójának képesnek kell lennie arra, hogy az 5 évvel ezelőtti szolgáltatási kód 1. verziójával megbízható gyűjteménybe helyezett adatain működjön.

Emellett a szolgáltatási kód egyszerre egy frissítési tartományt frissít. Tehát a frissítés során a szolgáltatási kód két különböző verziója fut egyszerre. El kell kerülnie, hogy a szolgáltatási kód új verziója ne használja az új sémát, mert a szolgáltatási kód régi verziói nem tudják kezelni az új sémát. Ha lehetséges, úgy tervezze meg a szolgáltatás egyes verzióit, hogy azok kompatibilisek legyenek egy adott verzióval. Pontosabban, ez azt jelenti, hogy a szolgáltatási kód v1-nek képesnek kell lennie arra, hogy figyelmen kívül hagyja azokat a séma-elemeket, amelyeket nem kifejezetten kezel. Azonban képesnek kell lennie arra, hogy bármilyen olyan információt mentsen, amely nem ismeri a explicit módon, és nem írja vissza a szótár kulcsának vagy értékének frissítésekor.

> [!WARNING]
> A kulcsok sémájának módosítását követően biztosítania kell, hogy a kulcs kivonatoló kódja és az egyenlő algoritmusok stabilak legyenek. Ha megváltoztatja, hogy ezek az algoritmusok hogyan működnek, a megbízható szótárban még soha nem fogja tudni megkeresni a kulcsot.
> A .NET-karakterláncok használhatók kulcsként, de a karakterláncot is használhatja kulcsként – ne használja a string. GetHashCode eredményét kulcsként.

Azt is megteheti, hogy a szokásos módon két frissítést hajt végre. A kétfázisú frissítéssel a Service-t a v1-ről v2-re, a v2-re pedig az új séma módosításával, de ez a kód nem hajtható végre. Ha a v2 kód beolvassa a v1-es adatokat, akkor az működik rajta, és v1-adatokat ír. Ezt követően a frissítés befejezését követően az összes frissítési tartományon megadhatja a frissítés befejezését jelző futó v2-példányokat. (Az egyik lehetőség, hogy ezt a konfigurációt egy konfigurációs frissítés kiépítésére állítja be. Ez a kétfázisú frissítés.) A v2-példányok a v1-es adatelemzést, a v2-re való átalakítást, a működést, valamint a v2-adatként való kiírását teszik elérhetővé. Ha más példányok is beolvassák a v2-et, akkor nem kell azokat konvertálniuk, csak azokon működnek, és ki kell írni a v2-es adattípust.

## <a name="next-steps"></a>Következő lépések
A továbbítással kompatibilis adategyezmények létrehozásával kapcsolatos további információkért lásd: [továbbítással kompatibilis adategyezmények](/dotnet/framework/wcf/feature-details/forward-compatible-data-contracts)

Az adategyezmények verziószámozásával kapcsolatos ajánlott eljárásokért tekintse meg az [adategyezmény verziószámozása](/dotnet/framework/wcf/feature-details/data-contract-versioning) című témakört.

A verzió-toleráns adategyezmények megvalósításának megismeréséhez tekintse meg a [Version-toleráns szerializálási visszahívások](/dotnet/framework/wcf/feature-details/version-tolerant-serialization-callbacks) című témakört.

Ha szeretné megtudni, hogyan biztosíthat olyan adatstruktúrát, amely több verzióban is képes együttműködni, tekintse meg a következőt: [IExtensibleDataObject](/dotnet/api/system.runtime.serialization.iextensibledataobject)
