---
title: Az Azure Service Fabric Actors életciklusának áttekintése
description: Ismerteti Service Fabric megbízható szereplők életciklusát, a szemétek gyűjtését, valamint a résztvevők és állapotuk manuális törlését
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 88db4bb2376cbc418d6954e274a18a6c18a280d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576043"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Színészi életciklus, automatikus Garbage-gyűjtés és manuális törlés
A színész akkor aktiválódik, amikor az első alkalommal hívást kezdeményeztek valamelyik metódusára. Egy szereplő inaktiválva van (a Actors Runtime által összegyűjtött szemetet), ha nem használják konfigurálható időtartamra. Egy színész és az állapota manuálisan is törölhető bármikor.

## <a name="actor-activation"></a>Színész aktiválása
Egy szereplő aktiválása esetén a következők történnek:

* Ha egy szereplő hívása egy szereplőhöz tartozik, és az egyik még nem aktív, egy új színész jön létre.
* A színész állapota akkor töltődik be, ha az állapota karbantartás alatt áll.
* A `OnActivateAsync` (C#) vagy `onActivateAsync` (Java) metódus (amely felülbírálható a színész implementációjában).
* A színész most aktívnak minősül.

## <a name="actor-deactivation"></a>Színész inaktiválása
Ha egy szereplő inaktiválva van, a következők történnek:

* Ha egy résztvevőt nem használ valamilyen ideig, a rendszer eltávolítja az aktív Actors táblából.
* A `OnDeactivateAsync` (C#) vagy `onDeactivateAsync` (Java) metódus (amely felülbírálható a színész implementációjában). Ezzel törli a színész összes időzítőjét. A színészi műveletek, például az állapotadatok nem hívhatók meg ebből a metódusból.

> [!TIP]
> A Fabric Actors futtatókörnyezet néhány [, a színész aktiválásával és inaktiválásával kapcsolatos eseményt bocsát ki](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Ezek a diagnosztika és a teljesítmény monitorozása során hasznosak.
>
>

### <a name="actor-garbage-collection"></a>Színészi Garbage-gyűjtemény
Ha egy szereplő inaktiválva van, a Actor objektumra mutató hivatkozások jelennek meg, és az általában a közös nyelvi futtatókörnyezet (CLR) vagy a Java virtuális gép (JVM) adatgyűjtési gyűjtője által összegyűjtött szemetet is felhasználhatja. A Garbage Collection csak a Actor objektumot törli. **nem** távolítja el a szereplő State Managerben tárolt állapotot. A színész következő aktiválása után létrejön egy új Actor objektum, és visszaállítja az állapotát.

Mi számít "használatnak" a Deaktiválás és a szemét gyűjtése céljából?

* Hívás fogadása
* `IRemindable.ReceiveReminderAsync` a metódus meghívása (csak akkor érvényes, ha a színész emlékeztetőket használ)

> [!NOTE]
> Ha a színész időzítőt használ, és meghívja az időzítő visszahívását, a rendszer **nem** a "használat" értékre számít.
>
>

Az Inaktiválás részleteinek megkezdése előtt fontos a következő feltételek meghatározása:

* *Ellenőrzési időköz*. Ez az az időtartam, amikor a szereplők futtatókörnyezete megkeresi az aktív Actors táblát a deaktiválható és az összegyűjtött szemetet. Az alapértelmezett érték 1 perc.
* *Üresjárat időkorlátja*. Ez az az időtartam, ameddig egy szereplőnek fel kell használnia a használaton kívüli állapotot (inaktív), és a begyűjtött szemetet fel kell venni. Ennek alapértelmezett értéke 60 perc.

Általában nem kell módosítania ezeket az alapértelmezett értékeket. Ha azonban szükséges, ezek az intervallumok `ActorServiceSettings` a [Actor szolgáltatás](service-fabric-reliable-actors-platform.md)regisztrálásakor változhatnak:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
A Actor Runtime minden aktív színésznél nyomon követi, hogy mennyi ideig tart a tétlenség (azaz nincs használatban). A Actor Runtime ellenőrzi, hogy az egyes szereplők képesek-e `ScanIntervalInSeconds` begyűjteni a szemetet, és jelzi, ha a szolgáltatás tétlen volt `IdleTimeoutInSeconds` .

Bármikor, ha a színész használatban van, a tétlenségi ideje 0-ra lesz állítva. Ezt követően a színész csak akkor gyűjthet szemetet, ha ismét tétlen marad `IdleTimeoutInSeconds` . Ne felejtse el, hogy egy szereplőt akkor kell használni, ha egy Actor Interface metódus vagy egy színészi emlékeztető visszahívása van végrehajtva. A szereplőt **nem** tekinti a rendszer, ha az időzítő visszahívását futtatja.

Az alábbi ábrán egy színész életciklusa látható, amely szemlélteti ezeket a fogalmakat.

![Tétlenségi idő – példa][1]

A példa bemutatja a színészi metódusok, emlékeztetők és időzítők hatását a színész élettartamán. A példával kapcsolatban a következő szempontokat érdemes megemlíteni:

* A ScanInterval és a IdleTimeout értéke 5 és 10. (Az egységek itt nem számítanak, mivel a célunk csak a koncepció szemléltetése.)
* A begyűjtött szemetet végző szereplők vizsgálata a következő vizsgálati intervallumban megadott módon történik: T = 0, 5, 10, 15, 20, 25.
* Az időszakos időzítő a T = 4, 8, 12, 16, 20, 24 és a visszahívási végrehajtást hajtja végre. Nem befolyásolja a színész üresjárati idejét.
* A T = 7 színészi metódus hívása visszaállítja a tétlenségi időt 0 értékre, és késlelteti a szereplő szemét-gyűjtését.
* A színészi emlékeztető visszahívása T = 14-kor hajtja végre, és tovább késlelteti a szereplő Garbage gyűjteményét.
* A szemetet a T = 25 helyen végzett vizsgálat során a színész tétlenségi ideje végül meghaladja a 10 üresjárati időkorlátot, és a szereplő szemetet gyűjt.

Egy színész soha nem kerül begyűjtésre, amíg az egyik módszert futtatja, függetlenül attól, hogy mennyi idő telik el a metódus végrehajtása során. Ahogy azt korábban említettük, a színészi felületi módszerek és az emlékeztető visszahívások végrehajtása megakadályozza a szemetet, ha alaphelyzetbe állítja a szereplő tétlenségi idejét 0-ra. Az időzítő visszahívások végrehajtása nem állítja vissza a tétlenségi időt 0-ra. Azonban a szereplő szemét-gyűjteménye Elhalasztva lesz, amíg az időzítő visszahívása befejezte a végrehajtást.

## <a name="manually-deleting-actors-and-their-state"></a>A szereplők és állapotuk manuális törlése
A deaktivált szereplők szemét-gyűjteménye csak a Actor objektumot törli, de nem távolítja el a szereplő állapot-kezelőjében tárolt adatmennyiséget. Ha egy szereplő újra aktiválódik, a rendszer ismét elérhetővé teszi az adatforrást az állami kezelőn keresztül. Azokban az esetekben, amikor a szereplők az állapot-kezelőben tárolják az adattárolást, és inaktiválva vannak, de soha nem aktiválják őket, szükség lehet az Adattisztításra.  Példák a szereplők törlésére, a [szereplők törlésére és azok állapotára](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Következő lépések
* [Színészi időzítők és emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Színészi események](service-fabric-reliable-actors-events.md)
* [Actor újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
* [Színészi diagnosztika és Teljesítményfigyelés](service-fabric-reliable-actors-diagnostics.md)
* [A Actor API-referenciájának dokumentációja](/previous-versions/azure/dn971626(v=azure.100))
* [C# mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
