---
title: Értesítések Reliable Services
description: A megbízható állapot-kezelő és a megbízható szótár Service Fabric Reliable Services értesítéseinek fogalmi dokumentációja
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: f5b48cc6cca2e143c48ed7bdfc99de936be2a227
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784579"
---
# <a name="reliable-services-notifications"></a>Értesítések Reliable Services
Az értesítések lehetővé teszik, hogy az ügyfelek nyomon kövessék egy olyan objektum módosításait, amelyekre kíváncsiak. Két típusú objektum támogatja az értesítéseket: *megbízható State Manager* és *megbízható szótár*.

Az értesítések használatának gyakori okai a következők:

* A replika állapotának kiépítése, például másodlagos indexek vagy összesített szűrt nézetek készítése. Ilyen például a megbízható szótárban található összes kulcs rendezett indexe.
* Figyelési adatok küldése, például az elmúlt órában hozzáadott felhasználók száma.

Az értesítések a műveletek alkalmazásának részeként jelennek meg. Emiatt az értesítéseket a lehető leggyorsabban kell kezelni, és a szinkron események nem tartalmazhatnak költséges műveleteket.

## <a name="reliable-state-manager-notifications"></a>Megbízható állapot-kezelő értesítései
A megbízható State Manager a következő eseményekhez nyújt értesítéseket:

* Tranzakció
  * Véglegesítés
* State Manager
  * Újraépítés
  * Megbízható állapot hozzáadása
  * Megbízható állapot eltávolítása

A megbízható State Manager nyomon követi az aktuális fedélzeti tranzakciókat. A tranzakciós állapot egyetlen olyan változása, amely egy értesítés indítását eredményezi, a tranzakció véglegesítése folyamatban van.

A megbízható State Manager megbízható állapotokat (például megbízható szótárt és megbízható üzenetsor) tartalmazó gyűjteményt tart fenn. Megbízható State Manager – értesítéseket küld a gyűjtemény változásairól: a rendszer megbízható állapotot ad hozzá vagy távolít el, vagy újraépíti a teljes gyűjteményt.
A megbízható State Manager-gyűjteményt a rendszer három esetben újraépíti:

* Helyreállítás: a replika indításakor helyreállítja az előző állapotot a lemezről. A helyreállítás végén a **NotifyStateManagerChangedEventArgs** használatával olyan eseményt hoz létre, amely a helyreállított megbízható állapotok készletét tartalmazza.
* Teljes másolás: ahhoz, hogy a replika csatlakozni tudja a konfigurációs készlethez, létre kell majd állítania. Előfordulhat, hogy ehhez a megbízható állapot-kezelő állapotának teljes másolatát kell használnia az elsődleges replikáról az üresjárati másodlagos replikára való alkalmazáshoz. A megbízható State Manager a másodlagos replikán a **NotifyStateManagerChangedEventArgs** használatával olyan eseményt hoz létre, amely az elsődleges replikából beszerzett megbízható állapotokat tartalmazza.
* Visszaállítás: a vész-helyreállítási helyzetekben a replika állapota visszaállítható a **RestoreAsync**-n keresztüli biztonsági mentésből. Ilyen esetekben az elsődleges replika megbízható **NotifyStateManagerChangedEventArgs** használatával a rendszer olyan eseményt hoz létre, amely a biztonsági másolatból visszaállított megbízható állapotokat tartalmazza.

A tranzakciós értesítések és/vagy az állapot-kezelő értesítéseinek regisztrálásához regisztrálnia kell a **TransactionChanged** -vagy **StateManagerChanged** -eseményekkel a megbízható állapot kezelőjében. Ezekkel az eseménykezelőkkal regisztrálhat egy közös helyet az állapot-nyilvántartó szolgáltatás konstruktora. Ha regisztrálja a konstruktort, nem fog kihagyni egy olyan értesítést, amelyet a **IReliableStateManager** élettartama során változás okoz.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

A **TransactionChanged** eseménykezelő a **NotifyTransactionChangedEventArgs** használatával adja meg az esemény részleteit. Ez tartalmazza a művelet típusát (például **NotifyTransactionChangedAction. commit**), amely meghatározza a változást. Emellett tartalmazza a Transaction tulajdonságot is, amely a módosult tranzakcióra mutató hivatkozást tartalmaz.

> [!NOTE]
> Napjainkban a **TransactionChanged** események csak akkor jönnek létre, ha a tranzakció véglegesítve van. A művelet ekkor a **NotifyTransactionChangedAction. commit** értékkel egyenlő. A jövőben azonban előfordulhat, hogy az események más típusú tranzakciós állapot-változásokra is felmerülhetnek. Javasoljuk, hogy ellenőrizze a műveletet, és csak akkor dolgozza fel az eseményt, ha az várható.
> 
> 

A következő példa egy **TransactionChanged** eseménykezelőt mutat be.

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

A **StateManagerChanged** eseménykezelő a **NotifyStateManagerChangedEventArgs** használatával adja meg az esemény részleteit.
A **NotifyStateManagerChangedEventArgs** két alosztálya van: a **NotifyStateManagerRebuildEventArgs** és a **NotifyStateManagerSingleEntityChangedEventArgs**.
A **NotifyStateManagerChangedEventArgs** művelet tulajdonságát használja a **NotifyStateManagerChangedEventArgs** a megfelelő alosztályba való beküldéséhez:

* **NotifyStateManagerChangedAction. Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction. Add** és **NotifyStateManagerChangedAction. remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

A következő példa egy **StateManagerChanged** -értesítő kezelőt mutat be.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Megbízható szótárak értesítései
A megbízható szótár a következő eseményekhez nyújt értesítéseket:

* Újraépítés: akkor kell meghívni, amikor a **ReliableDictionary** helyreállítja az állapotát egy helyreállított vagy másolt helyi állapotból vagy biztonsági másolatból.
* Clear (Törlés): hívás, ha a **ReliableDictionary** állapota törölve lett a **ClearAsync** metódussal.
* Hozzáadás: hívás, ha egy elem hozzá lett adva a **ReliableDictionary**-hoz.
* Frissítés: hívás, ha a **IReliableDictionary** egy eleme frissült.
* Eltávolítás: hívás, ha a **IReliableDictionary** egy eleme törölve lett.

A megbízható szótári értesítések beszerzéséhez regisztrálnia kell a **DictionaryChanged** -eseménykezelőt a **IReliableDictionary**-on. Ezekkel az eseménykezelőkkal regisztrálhat egy közös helyet a **ReliableStateManager. StateManagerChanged** értesítés hozzáadásakor.
Regisztráció, ha a **IReliableDictionary** hozzáadása a **IReliableStateManager** szolgáltatáshoz funkció biztosítja, hogy az értesítések ne legyenek kimaradva.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> A **ProcessStateManagerSingleEntityNotification** az a minta módszer, amelyet az előző **OnStateManagerChangedHandler** példa hív meg.
> 
> 

Az előző kód beállítja a **IReliableNotificationAsyncCallback** felületet, valamint a **DictionaryChanged**. Mivel a **NotifyDictionaryRebuildEventArgs** tartalmaz egy **IAsyncEnumerable** felületet – amelyet aszinkron módon kell enumerálni – az Újraépítés után az értesítéseket a **OnDictionaryChangedHandler** helyett **RebuildNotificationAsyncCallback** a rendszer.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> Az előző kódban az Újraépítés értesítésének feldolgozásának részeként először törlődik a karbantartott összesített állapot. Mivel a megbízható gyűjteményt új állapotba építették újra, az összes korábbi értesítés irreleváns.
> 
> 

A **DictionaryChanged** eseménykezelő a **NotifyDictionaryChangedEventArgs** használatával adja meg az esemény részleteit.
A **NotifyDictionaryChangedEventArgs** öt alosztálya van. Használja a **NotifyDictionaryChangedEventArgs** művelet tulajdonságát a **NotifyDictionaryChangedEventArgs** a megfelelő alosztályba való beküldéséhez:

* **NotifyDictionaryChangedAction. Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction. Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction. Add**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction. Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction. remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Javaslatok
* A lehető leggyorsabban *fejezze be* az értesítési eseményeket.
* *Ne hajtson végre költséges* műveleteket (például I/O-műveletek) a szinkron események részeként.
* Az esemény feldolgozása *előtt tekintse* meg a művelet típusát. Később új tevékenységtípusok is hozzáadhatók.

A következő szempontokat érdemes figyelembe venni:

* Az értesítések a művelet végrehajtásának részeként jelennek meg. Egy visszaállítási értesítés például a visszaállítási művelet utolsó lépése. A visszaállítás nem fejeződik be, amíg az értesítési esemény fel nem dolgozva.
* Mivel az értesítések az alkalmazási műveletek részeként jelennek meg, az ügyfelek csak a helyileg véglegesített műveletekre vonatkozó értesítéseket látják. Mivel a műveletek csak a helyileg véglegesített (más szóval naplózott), esetleg nem vonhatók vissza a jövőben.
* Az Ismétlési útvonalon minden alkalmazott művelethez egyetlen értesítés készül. Ez azt jelenti, hogy ha a T1 tranzakció tartalmazza a Create (X), a Delete (X) és a Create (x), egy értesítést fog kapni az X, az egyik a törléshez, egyet pedig a létrehozáshoz, ebben a sorrendben.
* A több műveletet tartalmazó tranzakciók esetében a rendszer az elsődleges replikán a felhasználótól kapott sorrendben alkalmazza a műveleteket.
* A hamis folyamat feldolgozásának részeként előfordulhat, hogy egyes műveletek visszavonhatók. A rendszer értesítést küld az ilyen visszavonási műveletekről, és Visszagörgeti a replika állapotát egy stabil pontra. A visszavonási értesítések egyik fontos különbsége az, hogy az ismétlődő kulcsokkal rendelkező események összesítve lesznek. Ha például a T1 tranzakciót visszavonja, egyetlen értesítést fog látni a törléshez (X).

## <a name="next-steps"></a>Következő lépések
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services – első lépések](service-fabric-reliable-services-quick-start.md)
* [Biztonsági mentés és visszaállítás Reliable Services (vész-helyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [Fejlesztői referenciák megbízható gyűjteményekhez](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
