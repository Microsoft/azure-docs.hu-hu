---
title: Megbízható gyűjtemények objektumainak szerializálása
description: Ismerkedjen meg az Azure Service Fabric megbízható gyűjtemények objektumainak szerializálásával, beleértve az alapértelmezett stratégiát és az egyéni szerializálás definiálásának módját.
ms.topic: conceptual
ms.date: 5/8/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 29bb9a2dfb028d223d63559b35735e78d7e6bcf8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784359"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Megbízható gyűjteményi objektumok szerializálása az Azure-ban Service Fabric
A megbízható gyűjtemények replikálják és megőrzik az elemeiket, így biztosítva, hogy a számítógépek meghibásodása és az áramkimaradások között tartósak legyenek.
Mind a replikáláshoz, mind az elemek megőrzéséhez a megbízható gyűjteményeknek szerializálni kell őket.

Megbízható gyűjtemények – a megfelelő szerializáló beszerzése egy adott típushoz a megbízható állapot-kezelőből.
A megbízható állapot-kezelő beépített szerializálók használatával teszi lehetővé az egyéni szerializálók regisztrálását egy adott típushoz.

## <a name="built-in-serializers"></a>Beépített Szerializálók

A megbízható State Manager tartalmaz beépített szerializáló néhány gyakori típushoz, így alapértelmezés szerint hatékonyan szerializálható. Más típusok esetén a megbízható állapot-kezelő visszatér a [dataContractSerializer](/dotnet/api/system.runtime.serialization.datacontractserializer)használatára.
A beépített szerializálók hatékonyabbak, mert tudják, hogy a típusok nem változnak, és nem kell tartalmazniuk a típushoz hasonló információkat.

A megbízható State Manager a következő típusokhoz tartalmaz beépített szerializáló: 
- Guid
- logikai
- byte
- sbyte érték
- bájt []
- char
- sztring
- tizedes tört
- double
- float
- int
- uint
- hosszú
- ulong
- rövid
- ushort

## <a name="custom-serialization"></a>Egyéni szerializálás

Az egyéni szerializálók általában a teljesítmény növeléséhez, illetve a vezetékes és a lemezeken tárolt adattitkosításhoz használatosak. Többek között az egyéni szerializálók általában hatékonyabbak, mint az általános szerializáló, mivel nem kell a típussal kapcsolatos információkat szerializálni. 

A [IReliableStateManager. \<T> TryAddStateSerializer](/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) az adott típushoz tartozó egyéni szerializáló regisztrálására szolgál. Ennek a regisztrációnak a StatefulServiceBase szerkezetében kell történnie annak biztosítása érdekében, hogy a helyreállítás megkezdése előtt minden megbízható gyűjtemény hozzáférhessen a megfelelő szerializáló adataihoz, hogy beolvassa a megőrzött adatokat.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Az egyéni szerializálók elsőbbséget élveznek a beépített szerializálók felett. Ha például az int-hez tartozó egyéni szerializáló regisztrálva van, akkor a rendszer az egész számok szerializálását használja az int beépített szerializáló helyett.

### <a name="how-to-implement-a-custom-serializer"></a>Egyéni szerializáló implementálása

A [IStateSerializer \<T> ](/dotnet/api/microsoft.servicefabric.data.istateserializer-1) felületének megvalósításához egyéni szerializáló szükséges.

> [!NOTE]
> A IStateSerializer \<T> olyan írási és olvasási túlterhelést tartalmaz, amely egy további T nevű alapértéket vesz igénybe. Ez az API a differenciális szerializáláshoz szükséges. A jelenleg különbözeti szerializálási funkció nincs kitéve. Ezért a rendszer nem hívja meg ezt a két túlterhelést, amíg a különbözeti szerializálás fel nem áll és engedélyezve van.

A következő példa egy OrderKey nevű egyéni típust tartalmaz, amely négy tulajdonságot tartalmaz

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

A következő példa a IStateSerializer implementációját szemlélteti \<OrderKey> .
Vegye figyelembe, hogy a baseValue elvégzéséhez szükséges túlterhelések olvashatók és írhatók, a továbbítások kompatibilitásának meghívása a megfelelő túlterhelésre.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Bővíthetőség
Egy [működés közbeni alkalmazás frissítése](service-fabric-application-upgrade.md)esetén a frissítés a csomópontok egy részhalmazára lesz alkalmazva, egyszerre egy frissítési tartomány. A folyamat során egyes frissítési tartományok az alkalmazás újabb verziójára kerülnek, és néhány frissítési tartomány az alkalmazás régebbi verziójára fog támaszkodni. A bevezetés során az alkalmazás új verziójának képesnek kell lennie az adatai régi verziójának olvasására, és az alkalmazás régi verziójának képesnek kell lennie az adatai új verziójának olvasására. Ha az adatformátum nem továbbítható, és visszafelé nem kompatibilis, a frissítés meghiúsulhat, vagy rosszabb lehet, az adat elveszhet vagy sérült lehet.

Ha beépített szerializáló használ, nem kell aggódnia a kompatibilitás miatt.
Ha azonban egyéni szerializáló vagy dataContractSerializer használ, az adatfeldolgozásnak végtelen visszafelé kell lennie, és kompatibilisnek kell lennie.
Más szóval a szerializáló minden verziójának képesnek kell lennie a típus bármely verziójának szerializálására és deszerializálására.

Az adategyezményt használó felhasználóknak a mezők hozzáadására, eltávolítására és módosítására vonatkozó, jól definiált verziószámozási szabályokat kell követniük. Az adategyezmény Emellett támogatja az ismeretlen mezők kezelését, a szerializálási és a deszerializálási folyamat összekötését, valamint az osztályok öröklését. További információ: az [adategyezmény használata](/dotnet/framework/wcf/feature-details/using-data-contracts).

Az egyéni szerializálási felhasználóknak meg kell felelniük az általa használt szerializáló irányelveinek, hogy a visszafelé és a továbbítás kompatibilis legyen.
Az összes verzió támogatásának gyakori módja a méretre vonatkozó információk hozzáadása az elején, és csak opcionális tulajdonságok hozzáadása.
Így az egyes verziók ugyanúgy olvashatók, mint a stream hátralévő része.

## <a name="next-steps"></a>Következő lépések
  * [Szerializálás és frissítés](service-fabric-application-upgrade-data-serialization.md)
  * [Fejlesztői referenciák megbízható gyűjteményekhez](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  * [Az alkalmazás a Visual Studióval történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítésén a Visual Studióval.
  * [Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti az alkalmazás frissítésén a PowerShell használatával.
  * Annak szabályozása, hogy az alkalmazás hogyan legyen [frissítve a frissítési paraméterek](service-fabric-application-upgrade-parameters.md)használatával.
  * Ismerje meg, hogyan használhatja a speciális funkciókat az alkalmazás frissítéséhez a [speciális témakörökre](service-fabric-application-upgrade-advanced.md)való hivatkozással.
  * Az alkalmazások frissítéseinek [hibaelhárításával](service-fabric-application-upgrade-troubleshooting.md)kapcsolatos gyakori problémák elhárítása.
