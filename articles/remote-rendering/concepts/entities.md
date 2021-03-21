---
title: Entitások
description: Az Azure távoli renderelési API hatókörében lévő entitások definíciója
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 29952353b8c3452d95bcced163fafa81fe158f64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593401"
---
# <a name="entities"></a>Entitások

Az *entitások* a térben lévő ingó objektumokat jelölik, és a távolról renderelt tartalom alapvető építőeleme.

## <a name="entity-properties"></a>Entitás tulajdonságai

Az entitások a pozíció, a rotáció és a skála alapján definiált átalakítóval rendelkeznek. Önmagában az entitások nem rendelkeznek megfigyelhető funkciókkal. Ehelyett a rendszer a viselkedést az entitásokhoz csatolt összetevőkön keresztül adja hozzá. Egy [CutPlaneComponent](../overview/features/cut-planes.md)  csatolása például létrehoz egy kivágási síkot az entitás pozíciójában.

Maga az entitás legfontosabb aspektusa a hierarchia és a létrejövő hierarchikus transzformáció. Ha például több entitás gyermekként van csatolva egy megosztott szülő entitáshoz, akkor az összes ilyen entitás áthelyezhető, elforgatható, és méretezhető úgy, hogy megváltoztatja a szülő entitás átalakítását. Emellett az entitás `enabled` állapotával kikapcsolhatja a megtekinthető és a kikapcsolási válaszokat a-hierarchiában lévő teljes algráfra.

Az entitások egyedi tulajdonosa a szülője, ami azt jelenti, hogy amikor a szülő megsemmisíti a `Entity.Destroy()` -t, a gyermekei és az összes csatlakoztatott [összetevő](components.md). Így a modell eltávolításával a rendszer meghívja a `Destroy` modellt, amelyet a vagy az SAS- `RenderingSession.Connection.LoadModelAsync()` variánsa ad vissza `RenderingSession.Connection.LoadModelFromSasAsync()` .

Az entitások akkor jönnek létre, amikor a kiszolgáló tartalmat tölt be, vagy amikor a felhasználó hozzá szeretne adni egy objektumot a jelenethez. Ha például egy felhasználó egy kivágási síkot szeretne felvenni egy rácsvonal belsejének megjelenítéséhez, akkor a felhasználó létrehozhat egy entitást, ahol a sík léteznie kell, majd hozzá kell adnia a kivágási sík összetevőt.

## <a name="create-an-entity"></a>Entitás létrehozása

Ha új entitást szeretne felvenni a színtérbe, például a modellek betöltéséhez, illetve összetevők csatolásához, használja a következő kódot:

```cs
Entity CreateNewEntity(RenderingSession session)
{
    Entity entity = session.Connection.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<RenderingSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Connection()->CreateEntity())
    {
        entity = entityRes.value();
        entity->SetPosition(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>Lekérdezési függvények

Az entitásokon két típusú lekérdezési funkció létezik: szinkron és aszinkron hívás. A szinkron lekérdezések csak az ügyfélen található, és nem sok számítási művelettel rendelkező adatmennyiségek esetében használhatók. Ilyenek például az összetevők, a relatív objektum-átalakítások vagy a szülő-gyermek kapcsolatok lekérdezése. Az aszinkron lekérdezések olyan adathoz használatosak, amelyek csak a kiszolgálón találhatók, vagy olyan további számításokat tartalmaznak, amelyek túl költségesek lesznek az ügyfélen való futtatáshoz. Ilyenek például a térbeli kötések vagy a metaadat-lekérdezések.

### <a name="querying-components"></a>Összetevők lekérdezése

Egy adott típusú összetevő megkereséséhez használja a következőt `FindComponentOfType` :

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Átalakítások lekérdezése

Az átalakítási lekérdezések szinkron hívásokat mutatnak az objektumon. Fontos megjegyezni, hogy az API-n keresztül lekérdezett átalakítások a helyi tér-átalakítások, az objektum szülőhöz képest. A kivételek a főobjektumok, amelyek esetében a helyi terület és a lemezterület azonos.

> [!NOTE]
> Nincs dedikált API a tetszőleges objektumok lemezterület-átalakításának lekérdezéséhez.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = entity->GetPosition();
Quaternion rotation = entity->GetRotation();
```

### <a name="querying-spatial-bounds"></a>Térbeli korlátok lekérdezése

A kötött lekérdezések olyan aszinkron hívások, amelyek teljes objektum-hierarchián működnek, és egy entitást használnak gyökérként. Tekintse meg a dedikált fejezetet az [objektumok határaival](object-bounds.md)kapcsolatban.

### <a name="querying-metadata"></a>Metaadatok lekérdezése

A metaadatok az objektumokon tárolt további adatok, amelyeket a kiszolgáló figyelmen kívül hagy. Az objektum metaadatai lényegében a (név, érték) párok halmaza, ahol az _érték_ numerikus, logikai vagy karakterlánc típusú lehet. A metaadatok exportálhatók a modell használatával.

A metaadat-lekérdezések aszinkron hívások egy adott entitáson. A lekérdezés csak egyetlen entitás metaadatait adja vissza, nem az algráf egyesített információit.

```cs
Task<ObjectMetadata> metaDataQuery = entity.QueryMetadataAsync();
ObjectMetadata metaData = await metaDataQuery;
ObjectMetadataEntry entry = metaData.GetMetadataByName("MyInt64Value");
System.Int64 intValue = entry.AsInt64;
// ...
```

```cpp
entity->QueryMetadataAsync([](Status status, ApiHandle<ObjectMetadata> metaData) 
{
    if (status == Status::OK)
    {
        ApiHandle<ObjectMetadataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
        int64_t intValue = *entry->GetAsInt64();

        // ...
    }
});
```

A lekérdezés akkor is sikeres lesz, ha az objektum nem rendelkezik metaadatokkal.

## <a name="api-documentation"></a>API-dokumentáció

* [C# entitás osztálya](/dotnet/api/microsoft.azure.remoterendering.entity)
* [C# RenderingConnection. CreateEntity ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createentity)
* [C++ entitás osztálya](/cpp/api/remote-rendering/entity)
* [C++ RenderingConnection:: CreateEntity ()](/cpp/api/remote-rendering/renderingconnection#createentity)

## <a name="next-steps"></a>Következő lépések

* [Összetevők](components.md)
* [Objektumhatárok](object-bounds.md)