---
title: DTDL-modellek kezelése
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan hozhat létre, szerkeszthet és törölhet modelleket az Azure digitális Twins-n belül.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 75911e91bb61b84d2e9315421f794739f2b5088b
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953697"
---
# <a name="manage-azure-digital-twins-models"></a>Azure digitális Twins-modellek kezelése

A [**DigitalTwinModels API**](/rest/api/digital-twins/dataplane/models)-k, a [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)vagy az [Azure Digital Twins CLI](how-to-use-cli.md)használatával felügyelheti a [modelleket](concepts-models.md) , amelyeket az Azure digitális Twins-példánya tud. 

A kezelési műveletek közé tartozik a modellek feltöltése, ellenőrzése, beolvasása és törlése. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-models"></a>Modellek kezelésének módjai

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-models"></a>Modellek létrehozása

Az Azure Digital Twins modelljei a DTDL-ben íródnak, és *. JSON* fájlként lesznek mentve. Létezik egy DTDL- [bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) is a [Visual Studio Code](https://code.visualstudio.com/)-hoz, amely szintaxis-ellenőrzést és egyéb funkciókat biztosít a DTDL dokumentumok írásához.

Vegyünk például egy olyan példát, amelyben a kórház a saját szobáit szeretné digitálisan kijelölni. Minden helyiség tartalmaz egy intelligens SOAP-adagolót, amely figyeli a kézmosást, és érzékelőkkel figyeli a forgalmat a helyiségen keresztül.

A megoldás első lépéseként létre kell hoznia a kórházi aspektusait képviselő modelleket. Ebben a forgatókönyvben az alábbihoz hasonló kórházi helyiségeket lehet leírta:

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> Ez egy minta törzs egy. JSON-fájlhoz, amelyben a modell definiálva és mentve lett, az ügyfél-projekt részeként való feltöltéshez. A REST API hívást a másik oldalon a modell definícióinak egy tömbjét veszi igénybe (amely a `IEnumerable<string>` .net SDK-ban van leképezve). Ha ezt a modellt közvetlenül a REST API szeretné használni, szögletes zárójelekkel.

Ez a modell definiálja a beteg helyiség nevét és egyedi AZONOSÍTÓját, valamint a látogatók és a kézmosás állapotát jelölő tulajdonságokat (ezek a számlálók frissülnek a mozgásérzékelők és az intelligens SOAP-adagolók között, és együtt használhatók a *kézmosás százalékos* tulajdonságainak kiszámításához). A modell egy kapcsolati *hasDevices* is definiál, amely az adott *szobatípus* [alapján a](concepts-twins-graph.md) tényleges eszközökre való kapcsolódáshoz használható.

Ezt a módszert követve megadhatja a kórházi részleg, a zónák vagy a kórház modelljeit.

### <a name="validate-syntax"></a>Szintaxis ellenőrzése

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>Modellek feltöltése

A modellek létrehozása után feltöltheti őket az Azure Digital Twins-példányba.

Ha készen áll a modell feltöltésére, használja a következő kódrészletet:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Figyelje meg, hogy a `CreateModels` metódus több fájlt is elfogad egyetlen tranzakcióban. Az alábbi példa szemlélteti a következőket:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

A modell fájljai több modellt is tartalmazhatnak. Ebben az esetben a modelleket JSON-tömbbe kell helyezni. Például:

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

A feltöltéskor a modell fájljait a szolgáltatás ellenőrzi.

## <a name="retrieve-models"></a>Modellek beolvasása

Az Azure Digital Twins-példányon tárolt modelleket listázhatja és lekérheti. 

A következő lehetőségek közül választhat:
* Egyetlen modell lekérése
* Az összes modell lekérése
* A modellek metaadatainak és függőségeinek beolvasása

Íme néhány példa a hívásokra:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Az API-hívások lekérik a modelleket az összes visszaadott `DigitalTwinsModelData` objektumra vonatkozóan. `DigitalTwinsModelData` Az Azure Digital Twins-példányban tárolt modellel kapcsolatos metaadatokat tartalmaz, például a név, a DTMI és a modell létrehozási dátuma. Az `DigitalTwinsModelData` objektum opcionálisan magába foglalja a modellt is. A paraméterektől függően a hívások lekérése paranccsal lekérheti a csak metaadatokat (ez olyan esetekben hasznos, amikor az elérhető eszközök felhasználói felületi listáját szeretné megjeleníteni, például) vagy a teljes modellt.

A `RetrieveModelWithDependencies` hívás nem csak a kért modellt adja vissza, hanem az összes olyan modellt is, amelytől a kért modell függ.

A modelleket nem feltétlenül adja vissza pontosan abban a dokumentum űrlapon, amelyet feltöltöttek. Az Azure Digital Twins csak azt garantálja, hogy a visszatérési űrlap szemantikailag egyenértékű lesz. 

## <a name="update-models"></a>Modellek frissítése

Miután feltöltötte a modellt az Azure Digital Twins-példányba, a teljes modell felülete nem változtatható meg. Ez azt jelenti, hogy nincs a modellek hagyományos "szerkesztése". Az Azure Digital Twins szintén nem teszi lehetővé ugyanannak a modellnek az újbóli feltöltését.

Ehelyett, ha módosítani szeretné a modelleket – például a frissítést `displayName` vagy a módszert –, akkor `description` a modell **újabb verziójának** feltöltését kell végrehajtania. 

### <a name="model-versioning"></a>Modell verziószámozása

Meglévő modell új verziójának létrehozásához kezdje az eredeti modell DTDL. Frissítse, adja hozzá vagy távolítsa el a módosítani kívánt mezőket.

Ezután a modell mezőjét frissítve a modell újabb verziójára kell megjelölnie `id` . A modell AZONOSÍTÓjának utolsó szakasza, amely után a `;` , a modell számát jelöli. Annak jelzéséhez, hogy ez a modell most már egy frissített verziója, növelje az érték végén lévő számot az `id` aktuális verziószámnál nagyobb számra.

Ha például az előző modell azonosítója így néz ki:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

a modell 2. verziója így néz ki:

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

Ezután töltse fel a modell új verzióját a példányra. 

A modell ezen verziója ezután elérhető lesz a példányban a digitális ikrek számára. **Nem** írja felül a modell korábbi verzióit, így a modell több verziója is egyszerre fog létezni a példányban, amíg [el nem távolítja őket](#remove-models).

### <a name="impact-on-twins"></a>Az ikrek hatása

Új Twin létrehozásakor, mivel az új modell verziója és a régi modell verziója együttes létezik, az új Twin a modell új verzióját vagy a régebbi verziót is használhatja.

Ez azt is jelenti, hogy egy modell új verziójának feltöltése nem befolyásolja automatikusan a meglévő ikreket. A meglévő ikrek egyszerűen megmaradnak a modell korábbi verziójának példányain.

Ezeket a meglévő ikreket frissítheti az új modell verziójára úgy, hogy kijavítja azokat a következő témakörben ismertetett módon: a [*Digital Twin modell frissítése*](how-to-manage-twin.md#update-a-digital-twins-model) című rész, *útmutató: digitális ikrek kezelése*. Ugyanezen a javításon belül frissítenie kell a **modell azonosítóját** (az új verzióra) és **minden olyan mezőt, amelyet meg kell változtatni a twinon, hogy az megfeleljen az új modellnek**.

## <a name="remove-models"></a>Modellek eltávolítása

A modelleket a szolgáltatásból is el lehet távolítani a következő két módszer egyikével:
* **Leszerelés** : a modell leszerelése után már nem használhatja új digitális Twins létrehozásához. A modellt már használó meglévő digitális ikrek nem érintettek, így továbbra is frissítheti azokat olyan dolgokkal, mint a tulajdonságok módosítása, valamint kapcsolatok hozzáadása vagy törlése.
* **Törlés** : ezzel teljesen eltávolítja a modellt a megoldásból. Az ezt a modellt használó ikrek már nem tartoznak egyetlen érvényes modellel sem, így azok úgy vannak kezelve, mintha egyáltalán nem rendelkeznek modellel. Ezeket az ikreket továbbra is elolvashatja, de nem fog tudni frissítéseket készíteni, amíg hozzá nem rendeli őket egy másik modellhez.

Ezek különálló funkciók, és nem érintik egymást, bár ezek együtt használhatók a modellek fokozatos eltávolítására. 

### <a name="decommissioning"></a>Leszerelési

Itt látható a modell leszerelésének kódja:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

A modell leszerelési állapota a `ModelData` modell lekérési API-k által visszaadott rekordokban szerepel.

### <a name="deletion"></a>Törlés

A példány összes modelljét egyszerre is törölheti, vagy megteheti azt egyéni alapon.

Az összes modell törlésére példaként töltse le az oktatóanyagban használt minta alkalmazást [*: Fedezze fel az alapokat egy minta ügyfélalkalmazás*](tutorial-command-line-app.md)használatával. A *CommandLoop. cs* fájl ezt egy `CommandDeleteAllModels` függvényben végzi el.

A szakasz további részében lebonthatja a modell törlésének részleteit, és bemutatja, hogyan végezheti el azt egy adott modell esetében.

#### <a name="before-deletion-deletion-requirements"></a>Törlés előtt: törlési követelmények

A modelleket általában bármikor törölni lehet.

Kivételt képeznek a más modellektől függő modellek, vagy egy `extends` kapcsolattal vagy egy összetevővel. Ha például egy *ConferenceRoom* modell kibővít egy *helyiség* modellt, és egy *ACUnit* modellt tartalmaz összetevőként, akkor nem törölheti a *szobát* vagy a *ACUnit* , amíg a *ConferenceRoom* el nem távolítja a vonatkozó hivatkozásokat. 

Ezt úgy teheti meg, hogy a függő modell frissítésével eltávolítja a függőségeket, vagy teljesen törli a függő modellt.

#### <a name="during-deletion-deletion-process"></a>Törlés közben: törlési folyamat

Még ha a modell megfelel a azonnal törölni kívánt követelményeknek, érdemes lehet néhány lépést megtennie, hogy elkerülje a nem szándékolt következményeket az ikrek mögött. Íme néhány lépés, amely segíthet a folyamat kezelésében:
1. Első lépésként szerelje le a modellt
2. Várjon néhány percet, és győződjön meg arról, hogy a szolgáltatás feldolgozta a leszerelés előtt küldött, Last minute kettős létrehozási kérelmeket.
3. Az ikrek lekérdezése modell szerint az összes olyan ikrek megjelenítéséhez, amely a most leszerelt modellt használja
4. Ha már nincs szüksége rájuk, törölje az ikreket, vagy ha szükséges, egy új modellre. Dönthet úgy is, hogy egyedül hagyja őket, ebben az esetben a modell törlése után az ikrek nélkül válnak elérhetővé. Tekintse meg a következő szakaszt ezen állapot következményeiről.
5. Várjon még néhány percet, hogy ellenőrizze, hogy a módosítások főzött-e
6. A modell törlése 

A modell törléséhez használja a következő hívást:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

#### <a name="after-deletion-twins-without-models"></a>Törlés után: ikrek modellek nélkül

A modell törlését követően a modellt használó digitális ikrek már nem modellnek minősülnek. Vegye figyelembe, hogy nincs olyan lekérdezés, amely az összes ikrek listáját megadja ebben az állapotban, bár a törölt modellből *továbbra is* lekérdezheti az ikreket, hogy megtudja, milyen hatással van az ikrekre.

Az alábbiakban áttekintheti, hogy mit tehet és nem tud olyan ikrekkel dolgozni, amelyek nem rendelkeznek modellel.

**Elvégezhető műveletek:**
* A Twin lekérdezése
* Tulajdonságok olvasása
* Kimenő kapcsolatok olvasása
* Bejövő kapcsolatok hozzáadása és törlése (a verzióban a más ikrek továbbra is *létrehozhatnak kapcsolatokat ehhez a Twin* )
  - A `target` kapcsolat definíciója továbbra is tükrözheti a törölt modell DTMI. Itt is működhet egy meghatározott céllal nem rendelkező kapcsolat.      
* Kapcsolatok törlése
* A Twin törlése

Nem **hajthatók** végre a következők:
* Kimenő kapcsolatok szerkesztése (a-ben, *a Twin* és más ikrek közötti kapcsolatok)
* Tulajdonságok szerkesztése

#### <a name="after-deletion-re-uploading-a-model"></a>Törlés után: modell ismételt feltöltése

A modell törlését követően később dönthet úgy, hogy új modellt tölt fel ugyanazzal az AZONOSÍTÓval, amelyet a törölt. Ebben az esetben ez történik.
* A megoldás áruházának szemszögéből ez ugyanaz, mint egy teljesen új modell feltöltése. A szolgáltatás soha nem emlékszik a régire.   
* Ha a gráf a törölt modellre hivatkozó további ikreket tartalmaz, azok már nem elárvultak. Ez a modell-azonosító ismét érvényes az új definícióval. Ha azonban a modell új definíciója eltér a törölt modellel, akkor ezek az ikrek olyan tulajdonságokkal és kapcsolatokkal rendelkezhetnek, amelyek megfelelnek a törölt definíciónak, és nem érvényesek az újat.

Az Azure digitális Twins nem akadályozza ezt az állapotot, ezért ügyeljen arra, hogy az ikrek megfelelő javításával győződjön meg arról, hogy azok érvényesek maradnak a modell definíciós kapcsolóján keresztül.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan hozhat létre és kezelhet digitális ikreket a modelljei alapján:
* [*Útmutató: digitális ikrek kezelése*](how-to-manage-twin.md)