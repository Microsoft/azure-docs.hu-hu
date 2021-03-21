---
title: 'Gyors útmutató: az alkalmazásban használandó objektum-horgonyok modell létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy objektum-összekötési modellt 3D-modellből.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 69d23b9d02eb176a2e42985ef5c3673e83d9bb7e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102607900"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>Rövid útmutató: objektum-horgonyok modell létrehozása 3D-modellből

Az Azure Object-horgonyok egy felügyelt felhőalapú szolgáltatás, amely átalakítja a 3D modelleket olyan mesterséges intelligencia-modellekre, amelyek lehetővé teszik az objektum-Aware vegyes valóságot a HoloLens. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy objektum-összehorgonyt a 3D modellből a C# kódon Core SDK használatával.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Objektum-horgonyok fiókjának létrehozása
> * 3D modell konvertálása objektum-horgony modell létrehozásához

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

* Egy Windows rendszerű gép a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>-mel.
* <a href="https://git-scm.com" target="_blank">Git for Windows</a>.
* A <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">.net Core 3,1 SDK</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-object-anchors-account"></a>Objektum-horgonyok fiókjának létrehozása

Először létre kell hoznia egy fiókot az objektum-horgonyok szolgáltatással.

1. Lépjen a [Azure Portalra](https://portal.azure.com/) , és válassza az **erőforrás létrehozása** lehetőséget.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="Új erőforrás létrehozása":::

2. Keresse meg az **objektum-horgonyok** erőforrást.

   Keressen rá az "objektum-horgonyok" kifejezésre.

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="Az objektumhoz tartozó horgonyok erőforrásának kiválasztása":::

   A keresési eredmények között az **objektumra vonatkozó horgonyok** erőforrásban válassza a **Létrehozás – > objektum-horgonyok** lehetőséget.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Objektum-horgonyok erőforrásának létrehozása":::

3. Az **objektum-horgonyok fiók** párbeszédpanelen:
    * Adjon meg egy egyedi erőforrás-nevet.
    * Válassza ki azt az előfizetést, amelyhez csatlakoztatni kívánja az erőforrást.
    * Meglévő erőforráscsoport létrehozása vagy használata.
    * Válassza ki azt a régiót, amelyre az erőforrást be szeretné léptetni.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Adja meg az objektum-horgonyok erőforrás-fiókjának adatait":::

    Válassza a **Létrehozás** lehetőséget az erőforrás létrehozásának megkezdéséhez.

4. Ha az erőforrás már létre lett hozva, válassza az **Erőforrás megnyitása** lehetőséget.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="Erőforrás megnyitása":::

5. Az Áttekintés oldalon:

   Jegyezze fel a **fiók tartományát**. Erre később még szüksége lesz.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Az objektum-horgony erőforráshoz tartozó fiók tartományának másolása":::

   Jegyezze fel a **fiók azonosítóját**. Erre később még szüksége lesz.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="Az objektum-horgony erőforráshoz tartozó fiók AZONOSÍTÓjának másolása":::

   Nyissa meg a **hozzáférési kulcsok** lapot, és jegyezze fel az **elsődleges kulcsot**. Erre később még szüksége lesz.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="Az objektum-horgony erőforráshoz tartozó fiók kulcsának másolása":::

## <a name="get-the-sample-project"></a>A minta projekt beszerzése

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>3D modell konvertálása

Most pedig átalakíthatja a 3D modellt.

1. Megnyitás `quickstarts/conversion/Conversion.sln` a Visual Studióban. Ez a megoldás C# konzolos projektet tartalmaz.

2. Nyissa meg a `Configuration.cs` projekt gyökerében található fájlt, és cserélje le az `set-me` értékeket a következő mezőkre:

   | Mező         | Leírás                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | A fent létrehozott objektumazonosító-fiókhoz tartozó **fiók tartománya** . |
   | AccountId     | A fent létrehozott objektum-horgonyok fiókjának **azonosítója** .     |
   | AccountKey    | A fent létrehozott objektum-horgonyok **elsődleges kulcsa**     |

   Négy további mezőt kell ellenőrizni:

    | Mező                    | Leírás                       |
    | ---                      | ---                               |
    | InputAssetPath           | A 3D modell abszolút elérési útja a helyi gépen. Támogatott fájlformátumok:,,, `fbx` `ply` `obj` `glb` és `gltf` . |
    | AssetDimensionUnit       | A 3D modell mértékegysége. Az összes támogatott mértékegység a számbavétel használatával érhető el `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` . |
    | Gravitációs                  | A 3D modell gravitációs vektorának iránya. Ez a 3D vektor lefelé irányítja a modell koordináta-rendszerébe. Ha például a negatív `y` érték a modell 3D-területének lefelé irányuló irányát jelöli, ez az érték a következő lesz: `Vector3(0.0f, -1.0f, 0.0f)` . |

3. Hozza létre és futtassa a projektet a 3D-modell feltöltéséhez, regisztráljon egy új átalakítási feladatot a szolgáltatással, és várjon, amíg be nem fejeződik. A feladatok befejezése után az objektum-horgonyok modell a alkalmazásban megadott fájl mellett lesz letöltve `InputAssetPath` . A következő konzol kimenetéhez hasonlóan kell megjelennie:

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Jegyezze fel a **feladatok azonosítóját** a jövőbeli referenciához. Hibakeresés vagy hibaelhárítás esetén hasznos lehet.

4. Miután a feladatot sikeresen elvégezte, meg kell jelennie egy, a megadott kimeneti helyen található formátumú fájlnak `<Model-Filename-Without-Extension>_<JobID>.ou` . Ha például a 3D-modell fájlneve, `chair.ply` és a feladatainak neve, `00000000-0000-0000-0000-000000000000` akkor a szolgáltatás kimenetének fájlneve lesz `chair_00000000-0000-0000-0000-000000000000.ou` .

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta az objektum-horgonyok fiókot, és átalakította a 3D modellt egy objektum-horgony modell létrehozásához. Ha szeretné megtudni, hogyan integrálhatja ezt a modellt az Object Anchors SDK-val a vegyes valóság alkalmazásban, folytassa a következő cikkek bármelyikével:

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens és MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)
