---
title: Modellek módosítása
description: Távolról renderelt modellek manipulálása áthelyezéssel, méretezéssel és egyebekkel
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: cec97134173cfc7879baf1d914d8f224a0736430
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593044"
---
# <a name="tutorial-manipulating-models"></a>Oktatóanyag: modellek módosítása

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Vizuális és manipulációs kötések hozzáadása távolról renderelt modellek köré
> * Áthelyezés, forgatás és méretezés
> * Raycast térbeli lekérdezésekkel
> * Egyszerű animációk hozzáadása a távolról megjelenített objektumokhoz

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag a következő [oktatóanyagra épül: felületek és egyéni modellek](../custom-models/custom-models.md).

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>Távoli objektum határainak lekérdezése és alkalmazása a helyi korlátokra

A távoli objektumokkal való kommunikációhoz szükség van egy helyi képviseletre, amely az elsővel kommunikál. Az [objektumok határai](../../../concepts/object-bounds.md) a távoli objektumok gyors kezeléséhez hasznosak. A távoli határokat az ARR-ből lehet lekérdezni, a helyi entitást pedig hivatkozásként használva. A rendszer lekérdezi a határokat, miután betöltötte a modellt a távoli munkamenetbe.

A modell határait a teljes modellt tartalmazó mező határozza meg – akárcsak az Unity [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html), amely az x, y, z tengelyekhez definiált központtal és mérettel rendelkezik. Valójában az egység **BoxCollider** fogja használni a távoli modell határainak ábrázolására.

1. Hozzon létre egy új parancsfájlt ugyanabban a könyvtárban, mint a **RemoteRenderedModel** , és nevezze el **RemoteBounds**.
1. Cserélje le a parancsfájl tartalmát a következő kódra:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create an async query using the model entity
        async private void QueryBounds()
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > Ha a Visual Studio-jogcím *"X" funkciója nem érhető el a C# 6-ban, akkor hibaüzenet jelenik meg. Használja a 7,0-es vagy újabb verziójú nyelvi verziót*, így a hiba nyugodtan figyelmen kívül hagyható. Ez az egység megoldásával és a projekt létrehozásával kapcsolatos.

    Ezt a szkriptet hozzá kell adni ugyanahhoz a GameObject, mint a  **BaseRemoteRenderedModel**-t megvalósító szkriptet. Ebben az esetben ez azt jelenti, hogy a **RemoteRenderedModel**. Az előző szkriptekhez hasonlóan ez a kezdeti kód fogja kezelni a távoli korlátokkal kapcsolatos összes állapotot, eseményt és adatmennyiséget.

    Csak egyetlen metódus van hátra a megvalósításhoz: **QueryBounds**. A **QueryBounds** aszinkron módon kéri le a határokat, végrehajtja a lekérdezés eredményét, és alkalmazza azt a helyi **BoxCollider**.

    A **QueryBounds** metódus egyszerű: lekérdezés küldése a távoli renderelési munkamenetnek, és várakozás az eredményre.

1. Cserélje le a **QueryBounds** metódust a következő befejezett metódusra:

    ```cs
    // Create a query using the model entity
    async private void QueryBounds()
    {
        remoteBoundsQuery = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        await remoteBounds;

        if (remoteBounds.IsCompleted)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

    A lekérdezés eredményét fogjuk megtekinteni, hogy sikeres volt-e. Ha igen, alakítsa át és alkalmazza a visszaadott határokat olyan formátumban, amelyet a **BoxCollider** el tud fogadni.

Ha a **RemoteBounds** parancsfájlt a **RemoteRenderedModel** azonos game objektumhoz adja hozzá, akkor szükség esetén egy **BoxCollider** lesz hozzáadva, és amikor a modell eléri az `Loaded` állapotát, a rendszer automatikusan lekérdezi és alkalmazza a határokat a **BoxCollider**.

1. A korábban létrehozott **TestModel** -GameObject hozzáadásával adja hozzá a **RemoteBounds** összetevőt.
1. Erősítse meg a parancsfájl hozzáadását.

     ![RemoteBounds-összetevő hozzáadása](./media/remote-bounds-script.png)

1. Futtassa ismét az alkalmazást. Röviddel a modell betöltése után a távoli objektum határai láthatók. Az alábbi értékekhez hasonlóan fog megjelenni:

     ![A távoli objektumhoz tartozó határokat bemutató képernyőkép.](./media/updated-bounds.png)

Most már van egy helyi **BoxCollider** , amely pontos határokkal van konfigurálva az Unity objektumon. A korlátok lehetővé teszik a vizualizációk és a interakciók használatát ugyanazokkal a stratégiákkal, amelyeket egy helyileg megjelenített objektumhoz használunk. Például az átalakítást, a fizikát és egyebeket megváltoztató parancsfájlok.

## <a name="move-rotate-and-scale"></a>Áthelyezés, forgatás és méretezés  

A távolról megjelenített objektumok áthelyezése, elforgatása és skálázása ugyanúgy működik, mint bármely más Unity objektum. A **RemoteRenderingCoordinator** a `LateUpdate` metódusában a `Update` jelenleg aktív munkamenetet hívja meg. A mi a `Update` szinkronizálása a helyi modellel, a távoli ügyfelekkel való átalakítás része. Távolról renderelt modell áthelyezéséhez, forgatásához vagy méretezéséhez csak a távoli modellt jelképező GameObject kell áthelyeznie, elforgatni vagy méreteznie. Itt módosítani fogjuk annak a szülő-GameObject az átalakítását, amelyhez a **RemoteRenderedModel** -szkript csatolva van.

Ez az oktatóanyag az MRTK for Object interakciót használja. Az egyes objektumok áthelyezésére, elforgatására és skálázására szolgáló MRTK nagy része az oktatóanyag hatókörén kívül esik. Az **AppMenu**-ben előre konfigurált modell-nézet vezérlő a **modell eszközei** menüben található.

1. Győződjön meg arról, hogy a korábban létrehozott **TestModel** -GameObject a jelenetben van.
1. Győződjön meg arról, hogy a **AppMenu** panel a jelenetben van.
1. Nyomja meg az egység lejátszás gombját a jelenet lejátszásához, majd nyissa meg a **modell eszközei** menüt a **AppMenu** belül.
![Vezérlő megtekintése](./media/model-with-view-controller.png)

A **AppMenu** olyan almenü- **modell eszközöket** tartalmaz, amelyek a modellhez kötést biztosító nézet-vezérlőt implementálnak. Ha a GameObject tartalmaz egy **RemoteBounds** -összetevőt, akkor a vezérlő egy [**BoundingBox**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html) -összetevőt ad hozzá, amely egy MRTK-összetevő, amely egy **BoxCollider** rendelkező objektum körüli határoló mezőt jelenít meg. Egy [**ObjectManipulator**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/api/Microsoft.MixedReality.Toolkit.UI.ObjectManipulator.html), amely a kéz-interakcióért felelős. Ezek a szkriptek együttesen lehetővé teszik a távolról renderelt modell áthelyezését, elforgatását és méretezését.

1. Vigye az egérmutatót a játék panelre, és kattintson rá, hogy a fókuszt adja.
1. A [MRTK 's Hand szimulációjának](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation)használatával nyomja le és tartsa nyomva a bal oldali SHIFT billentyűt.
1. Kormányozza a szimulált kezet, hogy a Hand Ray a teszt modelljére mutasson.

    ![Hegyes Hand Ray](./media/handray-engine.png)

1. Tartsa lenyomva a bal oldali egérgombot, és húzza át a modellt.

Meg kell jelennie, hogy a távolról megjelenített tartalom a határolókeret mellett mozog. Előfordulhat, hogy némi késést vagy késést tapasztal a határolókeret és a távoli tartalom között. Ez a késleltetés az internetes késéstől és a sávszélességtől függ.

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>Ray Cast-és térbeli lekérdezések a távoli modellekről

A modelleket tartalmazó Box-ütközések a teljes modellel való interakcióra alkalmasak, de nem eléggé részletesek a modell egyes részeinek működéséhez. Ennek megoldásához használhatja a [távoli Ray castingt](../../../overview/features/spatial-queries.md#ray-casts). A távoli Ray casting egy olyan API, amelyet az Azure távoli renderelése biztosít a sugaraknak a távoli színtérbe való küldéséhez, és a találatok helyi visszatérését. Ezzel a technikával kiválaszthatja egy nagyméretű modell alárendelt entitásait, vagy elérheti a találati eredmények információit, például a pozíciót, a felszíni normál és a távolságot.

A test Model több alentitást is tartalmaz, amelyek lekérdezhető és kiválaszthatók. Egyelőre a kijelölés megjeleníti a kiválasztott entitás nevét az Unity-konzolon. A kiválasztott entitás kiemeléséhez jelölje be az [anyagok, a világítás és a hatások](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining) című fejezetet.

Először hozzon létre egy statikus burkolót a távoli Ray Cast-lekérdezések köré. Ez a szkript fogadja az egység területének pozícióját és irányát, átalakítja a távoli Ray által elfogadott adattípusokra, és visszaadja az eredményeket. A szkript az API-t fogja használni `RayCastQueryAsync` .

1. Hozzon létre egy új, **RemoteRayCaster** nevű szkriptet, és cserélje le a tartalmát a következő kódra:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                var result = await RemoteRenderingCoordinator.CurrentSession.Connection.RayCastQueryAsync(rayCast);
                return result.Hits;
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > Az Unity egy [**RaycastHit**](https://docs.unity3d.com/ScriptReference/RaycastHit.html)nevű osztályt tartalmaz, az Azure távoli renderelés pedig egy [**RaycastHit**](/dotnet/api/microsoft.azure.remoterendering.raycasthit)nevű osztályt tartalmaz. A **C** betűs szám a hibák fordításának elkerülése érdekében fontos különbség.

    A **RemoteRayCaster** általános hozzáférési pontot biztosít a távoli sugaraknak az aktuális munkamenetbe való öntéséhez. Ahhoz, hogy pontosabbak legyenek, a következő lépés egy MRTK mutató-kezelő implementálása. A szkript végrehajtja a `IMixedRealityPointerHandler` felületet, amely közli a MRTK, hogy ezt a szkriptet szeretnénk figyelni a [vegyes valóságot mutató](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html) eseményekre.

1. Hozzon létre egy új, **RemoteRayCastPointerHandler** nevű szkriptet, és cserélje le a kódot a következő kódra:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

A RemoteRayCastPointerHandler `OnPointerClicked` metódusát a MRTK hívja meg, ha egy ütközőn, például a Box-összeütközik. Ezt követően a `PointerDataToRemoteRayCast` rendszer meghívja a mutató eredményét egy pontra és irányra. A rendszer ezt a pontot és irányt használja egy távoli fénysugár elküldéséhez a távoli munkamenetben.

![Frissített korlátok](./media/raycast-local-remote.png)

A Ray Casting-re vonatkozó kérelmek küldése a Click-ben hatékony stratégia a távoli objektumok lekérdezéséhez. Azonban ez nem ideális felhasználói élmény, mert a kurzor ütközik a Box ütközővel, nem maga a modell.

Létrehozhat egy új MRTK mutatót is, amely gyakrabban viszi át a sugarait a távoli munkamenetben. Bár ez egy összetettebb megközelítés, a felhasználói élmény jobb lenne. Ez a stratégia kívül esik ennek az oktatóanyagnak a hatókörén, de erre a megközelítésre példa látható a kirakat alkalmazásban, amely az [ARR Samples repositoryban](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/Showcase)található.

Ha a **RemoteRayCastPointerHandler** sikeresen elvégezte a Ray-castot, a rendszer `Entity` kibocsátja a találatot a `OnRemoteEntityClicked` Unity eseményből. Ahhoz, hogy válaszoljon erre az eseményre, létrehozunk egy segítő parancsfájlt, amely elfogadja a parancsot, `Entity` és végrehajt egy műveletet. Kezdjük azzal, hogy beolvassa a szkriptet, hogy kinyomtassa a nevét a `Entity` hibakeresési naplóba.

1. Hozzon létre egy **RemoteEntityHelper** nevű új parancsfájlt, és cserélje le a tartalmát az alábbira:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. A korábban létrehozott **TestModel** -GameObject adja hozzá a **RemoteRayCastPointerHandler** összetevőt és a **RemoteEntityHelper** összetevőt is.
1. Rendelje hozzá a `EntityToDebugLog` metódust az `OnRemoteEntityClicked` eseményhez. Ha az esemény kimeneti típusa és típusa megegyezik, használhatjuk az egység dinamikus esemény-összekapcsolása értéket, amely automatikusan átadja az esemény értékét a metódusnak.
    1. Új visszahívási mező létrehozása ![ visszahívás hozzáadása](./media/add-callback-remote-entity-clicked.png)
    1. Húzza a **távoli entitás segítő** összetevőt az objektum mezőbe, hogy a szülő GameObject- ![ hozzárendelési objektumra hivatkozzon.](./media/assign-object.png)
    1. A `EntityToDebugLog` visszahívás ![ hozzárendelésének visszahívása](./media/remote-entity-event.png)
1. A jelenet elindításához nyomja le a play gombot az Unity Editorban, és kapcsolódjon egy távoli munkamenethez, és töltse be a teszt modellt.
1. A MRTK Hand szimulációjának használatával nyomja le és tartsa nyomva a bal oldali SHIFT billentyűt.
1. Kormányozza a szimulált kezet, hogy a Hand Ray a teszt modelljére mutasson.
1. Hosszú kattintással szimulálhatja a levegőt, és végrehajthatja az `OnPointerClicked` eseményt.
1. Figyelje meg, hogy az Unity-konzolon be van jelölve a alárendelt entitás neve. Példa: ![ alárendelt entitás – példa](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>A távoli objektum gráfjának szinkronizálása az Unity-hierarchiába

Eddig csak egyetlen helyi GameObject láttunk, amely a teljes modellt jelképezi. Ez jól működik a teljes modell megjelenítéséhez és kezeléséhez. Ha azonban effektusokat kíván alkalmazni, vagy bizonyos alentitásokat szeretne manipulálni, helyi GameObjects kell létrehoznia az entitások ábrázolásához. Először is vizsgáljuk meg manuálisan a tesztelési modellt.

1. Indítsa el a jelenetet, és töltse be a teszt modellt.
1. Bontsa ki a **TestModel** GameObject gyermekeit az egység hierarchiájában, és válassza ki a **TestModel_Entity** GameObject.
1. Az Ellenőrben kattintson a *gyermekek megjelenítése* gombra.
![Gyermekek megjelenítése](./media/show-remote-children.png)
1. Folytassa a hierarchiában lévő gyermekek kibontását, és kattintson a *gyermekek megjelenítése* gombra, amíg meg nem jelenik a gyerekek nagy listája.
![Minden gyermek](./media/test-model-children.png)

A több tucat entitások listája most már feltölti a hierarchiát. Ha kiválasztja az egyiket, a rendszer megjeleníti a `Transform` és az `RemoteEntitySyncObject` összetevőket az ellenőrben. Alapértelmezés szerint az egyes entitások nem szinkronizálják automatikusan az összes keretet, így a helyi módosítások `Transform` nem szinkronizálhatók a kiszolgálóval. Megtekintheti az *összes képkockát* , majd áthelyezheti, méretezheti vagy elforgathatja az átalakítást a jelenet nézetben, nem fogja látni a megjelenített modellt a jelenet nézetben

Ugyanez a folyamat programozott módon is elvégezhető, és az adott távoli entitások módosításának első lépése.

1. Módosítsa a **RemoteEntityHelper** parancsfájlt úgy, hogy az a következő metódust is tartalmazza:

    ```cs
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. Adjon hozzá egy további visszahívást a **RemoteRayCastPointerHandler** eseményhez `OnRemoteEntityClicked` , és állítsa be a következőre: `MakeSyncedGameObject` .
![További visszahívás](./media/additional-callback.png)
1. A MRTK Hand szimulációjának használatával nyomja le és tartsa nyomva a bal oldali SHIFT billentyűt.
1. Kormányozza a szimulált kezet, hogy a Hand Ray a teszt modelljére mutasson.
1. Hosszú kattintással szimulálhatja a levegőt, és végrehajthatja az `OnPointerClicked` eseményt.
1. Ellenőrizze és bontsa ki a hierarchiát egy új gyermekobjektum megjelenítéséhez, amely a rákattintott entitást jelképezi.
![GameObject-ábrázolás](./media/gameobject-representing-entity.png)
1. A tesztelést követően távolítsa el a visszahívását `MakeSyncedGameObject` , mivel később más effektusok részeként is beépítjük ezt.

> [!NOTE]
> Minden keret szinkronizálása csak akkor szükséges, ha szinkronizálni szeretné az átalakítási adatkészletet. Az átalakítások szinkronizálása némi terhet jelent, ezért takarékosan kell használni.

Egy helyi példány létrehozása és az automatikus szinkronizálás beállítása az alentitások manipulálása első lépése. Ugyanazokat a technikákat használjuk, amelyekkel a modell teljes egészében kezelhető az alentitásokon is. Például egy entitás szinkronizált helyi példányának létrehozása után lekérdezheti a határait, és felveheti a manipulációs kezelőket, hogy az a felhasználó által áthelyezhető legyen.

## <a name="next-steps"></a>Következő lépések

Most már kezelhet és kezelhet távolról renderelt modelleket! A következő oktatóanyagban az anyagok módosítására, a világítás módosítására és a hatások a távolról renderelt modellekre való alkalmazására fogunk vonatkozni.

> [!div class="nextstepaction"]
> [Következő: az anyagok, a világítás és a hatások finomítása](../materials-lighting-effects/materials-lighting-effects.md)