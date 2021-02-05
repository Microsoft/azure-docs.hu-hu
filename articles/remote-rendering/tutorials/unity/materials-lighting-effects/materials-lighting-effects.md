---
title: Az anyagok, a megvilágítás és az effektusok finomítása
description: Modell anyagok és megvilágítás módosítása. További effektusok hozzáadása, például Tagolás és kivágott síkok.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 8eb73fcfde7e294896a12289486ff71794a00ae6
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591718"
---
# <a name="tutorial-refining-materials-lighting-and-effects"></a>Oktatóanyag: az anyagok, a világítás és a hatások finomítása

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Kiemelés és vázlatos modellek és modell-összetevők
> * Különböző anyagok alkalmazása modellekre
> * Szeletek kivágott síkokkal rendelkező modelleken keresztül
> * Egyszerű animációk hozzáadása a távolról megjelenített objektumokhoz

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag a [modellek manipulálására szolgáló oktatóanyagra](..\manipulate-models\manipulate-models.md)épül.

## <a name="highlighting-and-outlining"></a>Kiemelés és tagolás

A felhasználók számára a vizuális visszajelzések fontos részét képezik a felhasználói élménynek bármely alkalmazásban. Az Azure Remote rendering a [hierarchikus állapotú felülbírálások](../../../overview/features/override-hierarchical-state.md)révén vizuális visszajelzési mechanizmusokat biztosít. A hierarchikus állapot felülbírálásai a modellek helyi példányaihoz csatolt összetevőkkel vannak implementálva. Megtanultuk, hogyan hozhatja létre ezeket a helyi példányokat a [távoli objektum gráfjának az Unity-hierarchiába való szinkronizálásához](../manipulate-models/manipulate-models.md#synchronizing-the-remote-object-graph-into-the-unity-hierarchy).

Először hozzunk létre egy burkolót a [**HierarchicalStateOverrideComponent**](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent) összetevő köré. A **HierarchicalStateOverrideComponent** a helyi parancsfájl, amely a távoli entitás felülbírálásait vezérli. Az [**oktatóanyag eszközei**](../custom-models/custom-models.md#import-assets-used-by-this-tutorial) közé tartozik egy **BaseEntityOverrideController** nevű absztrakt alaposztály, amelyet a burkoló létrehozásához fogunk kiterjeszteni.

1. Hozzon létre egy **EntityOverrideController** nevű új parancsfájlt, és cserélje le a tartalmát a következő kódra:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class EntityOverrideController : BaseEntityOverrideController
    {
        public override event Action<HierarchicalStates> FeatureOverrideChange;

        private ARRHierarchicalStateOverrideComponent localOverride;
        public override ARRHierarchicalStateOverrideComponent LocalOverride
        {
            get
            {
                if (localOverride == null)
                {
                    localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();
                    if (localOverride == null)
                    {
                        localOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
                    }

                    var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                    if (remoteStateOverride == null)
                    {
                        // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                        localOverride.Create(RemoteManagerUnity.CurrentSession);
                    }
                    else
                    {
                        // otherwise, bind our local stateOverride component to the remote component
                        localOverride.Bind(remoteStateOverride);

                    }
                }
                return localOverride;
            }
        }

        private RemoteEntitySyncObject targetEntity;
        public override RemoteEntitySyncObject TargetEntity
        {
            get
            {
                if (targetEntity == null)
                    targetEntity = gameObject.GetComponent<RemoteEntitySyncObject>();
                return targetEntity;
            }
        }

        private HierarchicalEnableState ToggleState(HierarchicalStates feature)
        {
            HierarchicalEnableState setToState = HierarchicalEnableState.InheritFromParent;
            switch (LocalOverride.RemoteComponent.GetState(feature))
            {
                case HierarchicalEnableState.ForceOff:
                case HierarchicalEnableState.InheritFromParent:
                    setToState = HierarchicalEnableState.ForceOn;
                    break;
                case HierarchicalEnableState.ForceOn:
                    setToState = HierarchicalEnableState.InheritFromParent;
                    break;
            }

            return SetState(feature, setToState);
        }

        private HierarchicalEnableState SetState(HierarchicalStates feature, HierarchicalEnableState enableState)
        {
            if (GetState(feature) != enableState) //if this is actually different from the current state, act on it
            {
                LocalOverride.RemoteComponent.SetState(feature, enableState);
                FeatureOverrideChange?.Invoke(feature);
            }

            return enableState;
        }

        public override HierarchicalEnableState GetState(HierarchicalStates feature) => LocalOverride.RemoteComponent.GetState(feature);

        public override void ToggleHidden() => ToggleState(HierarchicalStates.Hidden);

        public override void ToggleSelect() => ToggleState(HierarchicalStates.Selected);

        public override void ToggleSeeThrough() => ToggleState(HierarchicalStates.SeeThrough);

        public override void ToggleTint(Color tintColor = default)
        {
            if (tintColor != default) LocalOverride.RemoteComponent.TintColor = tintColor.toRemote();
            ToggleState(HierarchicalStates.UseTintColor);
        }

        public override void ToggleDisabledCollision() => ToggleState(HierarchicalStates.DisableCollision);

        public override void RemoveOverride()
        {
            var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();
            if (remoteStateOverride != null)
            {
                remoteStateOverride.Destroy();
            }

            if (localOverride == null)
                localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();

            if (localOverride != null)
            {
                Destroy(localOverride);
            }
        }
    }
    ```

A **LocalOverride** fő feladata, hogy kapcsolatot hozzon létre a saját maga és a között `RemoteComponent` . A **LocalOverride** ezután lehetővé teszi, hogy a helyi összetevőn állítson be állapotjelzőket, amelyek a távoli entitáshoz vannak kötve. A felülbírálásokat és azok [állapotát a hierarchikus állapot felülbírálásai](../../../overview/features/override-hierarchical-state.md) lapon találja. 

Ez a megvalósítás csak egy állapotot vált ki egyszerre. Azonban teljesen lehetséges több felülbírálást egyesíteni egyetlen entitáson, és a hierarchiában különböző szinteken hozhat létre kombinációkat. Például a `Selected` és `SeeThrough` az egyetlen összetevővel való egyesítése egy vázlatot is biztosít, miközben az áttetszővé válik. Vagy ha a gyökérszintű entitás `Hidden` felülbírálását úgy állítja be, hogy a `ForceOn` gyermek entitás `Hidden` felülbírálását úgy állítsa be, hogy a `ForceOff` felülbírált gyermek kivételével mindent elrejtse.

Az állapotok entitásokra való alkalmazásához a korábban létrehozott **RemoteEntityHelper** is módosíthatjuk.

1. Módosítsa a **RemoteEntityHelper** osztályt a **BaseRemoteEntityHelper** absztrakt osztályának megvalósításához. Ez a módosítás lehetővé teszi az **oktatóanyag eszközeiben** megadott View vezérlő használatát. A módosításkor a következőhöz hasonlóan kell kinéznie:

    ```cs
    public class RemoteEntityHelper : BaseRemoteEntityHelper
    ```

2. Bírálja felül az absztrakt metódusokat a következő kód használatával:

    ```cs
    public override BaseEntityOverrideController EnsureOverrideComponent(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent == null)
            overrideComponent = entityGameObject.AddComponent<EntityOverrideController>();
        return overrideComponent;
    }

    public override HierarchicalEnableState GetState(Entity entity, HierarchicalStates feature)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        return overrideComponent.GetState(feature);
    }

    public override void ToggleHidden(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void ToggleSelect(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSelect();
    }

    public override void ToggleSeeThrough(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSeeThrough();
    }

    public Color TintColor = new Color(0.0f, 1.0f, 0.0f, 0.1f);
    public override void ToggleTint(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleTint(TintColor);
    }

    public override void ToggleDisableCollision(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void RemoveOverrides(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent != null)
        {
            overrideComponent.RemoveOverride();
            Destroy(overrideComponent);
        }
    }
    ```

Ez a kód biztosítja a **EntityOverrideController** összetevő hozzáadását a célként megadott entitáshoz, majd meghívja az egyik váltási módszert. Ha kívánja, a **TestModel** -GameObject hívja meg ezeket a segítő metódusokat úgy, hogy a **RemoteEntityHelper** visszahívásként adja hozzá a `OnRemoteEntityClicked` **RemoteRayCastPointerHandler** -összetevő eseményéhez.

![Mutató visszahívásai](./media/pointer-event-callbacks.png)

Most, hogy ezek a parancsfájlok hozzá lettek adva a modellhez, a futtatókörnyezethez való csatlakozás után a **AppMenu** -vezérlőnek további csatolókkal kell rendelkeznie a **EntityOverrideController** -szkripttel való interakcióhoz. Tekintse meg a **modell eszközei** menüt a kinyitott nézet-vezérlők megtekintéséhez.

Ezen a ponton a **TestModel** GameObject összetevőinek ehhez hasonlóan kell kinéznie:

![Modell tesztelése további parancsfájlokkal](./media/test-model-updated.png)

Íme egy példa arra, hogy a felülbírálások egyetlen entitáson legyenek halmozva. A következőt használtuk `Select` `Tint` :

![Modell színárnyalatának kiválasztása](./media/select-tint-test-model.png)

## <a name="cut-planes"></a>Síkok kivágása

A [kivágott síkok](../../../overview/features/cut-planes.md) olyan szolgáltatások, amelyek bármely távoli entitáshoz hozzáadhatók. Leggyakrabban olyan új távoli entitást hoz létre, amely nincs társítva a rácsvonalak adataihoz a kivágott sík összetevő tárolásához. A kivágási sík pozícióját és tájolását annak a távoli entitásnak a pozíciója és tájolása határozza meg, amelyhez csatolva van.

Létrehozunk egy parancsfájlt, amely automatikusan létrehoz egy távoli entitást, hozzáadja a kivágási sík összetevőt, és szinkronizálja a helyi objektumok átalakítását a kivágott sík entitással. Ezt követően a **CutPlaneViewController** használatával becsomagolhatja a kivágási síkot egy olyan felületen, amely lehetővé teszi számunkra a manipulálására.

1. Hozzon létre egy **RemoteCutPlane** nevű új parancsfájlt, és cserélje le a kódját az alábbi kódra:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class RemoteCutPlane : BaseRemoteCutPlane
    {
        public Color SliceColor = new Color(0.5f, 0f, 0f, .5f);
        public float FadeLength = 0.01f;
        public Axis SliceNormal = Axis.Y_Neg;

        public bool AutomaticallyCreate = true;

        private CutPlaneComponent remoteCutPlaneComponent;
        private bool cutPlaneReady = false;

        public override bool CutPlaneReady 
        { 
            get => cutPlaneReady;
            set 
            { 
                cutPlaneReady = value;
                CutPlaneReadyChanged?.Invoke(cutPlaneReady);
            }
        }

        public override event Action<bool> CutPlaneReadyChanged;

        public UnityBoolEvent OnCutPlaneReadyChanged = new UnityBoolEvent();

        public void Start()
        {
            // Hook up the event to the Unity event
            CutPlaneReadyChanged += (ready) => OnCutPlaneReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateCutPlane();
                    break;
                default:
                    DestroyCutPlane();
                    break;
            }
        }

        public override void CreateCutPlane()
        {
            //Implement me
        }

        public override void DestroyCutPlane()
        {
            //Implement me
        }
    }
    ```

    Ez a kód kiterjeszti a **BaseRemoteCutPlane** osztályt az **oktatóanyag eszközei** között. A távolról renderelt modellhez hasonlóan ez a szkript csatolja és figyeli a `RemoteRenderingState` távoli koordinátor változásait. Ha a koordinátor eléri az `RuntimeConnected` állapotot, a rendszer megpróbál automatikusan csatlakozni, ha az a feltételezett. Van egy változó is, `CutPlaneComponent` amelyet nyomon követünk. Ez az Azure távoli renderelési összetevő, amely a távoli munkamenetben lévő kivágási síkon szinkronizál. Vessünk egy pillantást a kivágott sík létrehozásához szükséges műveletekre.

2. Cserélje le a `CreateCutPlane()` metódust az alábbi befejezett verzióra:

    ```cs
    public override void CreateCutPlane()
    {
        if (remoteCutPlaneComponent != null)
            return; //Nothing to do!

        //Create a root object for the cut plane
        var cutEntity = RemoteRenderingCoordinator.CurrentSession.Connection.CreateEntity();

        //Bind the remote entity to this game object
        cutEntity.BindToUnityGameObject(this.gameObject);

        //Sync the transform of this object so we can move the cut plane
        var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
        syncComponent.SyncEveryFrame = true;

        //Add a cut plane to the entity
        remoteCutPlaneComponent = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.CutPlaneComponent, cutEntity) as CutPlaneComponent;

        //Configure the cut plane
        remoteCutPlaneComponent.Normal = SliceNormal;
        remoteCutPlaneComponent.FadeColor = SliceColor.toRemote();
        remoteCutPlaneComponent.FadeLength = FadeLength;
        CutPlaneReady = true;
    }
    ```

    Itt hozunk létre egy távoli entitást, és azt egy helyi GameObject köti. Győződjön meg arról, hogy a távoli entitás átalakítja az átalakítását a helyi átalakításra a beállításával `SyncEveryFrame` `true` . Ezt követően a hívást a `CreateComponent` távoli objektum hozzáadására használjuk `CutPlaneComponent` . Végezetül konfigurálja a kivágási síkot a MonoBehaviour felső részén megadott beállításokkal. Lássuk, mire van szüksége a kivágott sík tisztításához a metódus megvalósításával `DestroyCutPlane()` .

3. Cserélje le a `DestroyCutPlane()` metódust az alábbi befejezett verzióra:

    ```cs
    public override void DestroyCutPlane()
    {
        if (remoteCutPlaneComponent == null)
            return; //Nothing to do!

        remoteCutPlaneComponent.Owner.Destroy();
        remoteCutPlaneComponent = null;
        CutPlaneReady = false;
    }
    ```

Mivel a távoli objektum meglehetősen egyszerű, és csak a távoli végpontot (és a helyi objektum megtartását) törli, egyszerűen csak hívja `Destroy` meg a távoli objektumot, és törölje a hivatkozást.

A **AppMenu** tartalmaz egy nézet vezérlőt, amely automatikusan csatolva lesz a kivágási síkon, és lehetővé teszi, hogy együttműködjön vele. Nem szükséges, hogy a **AppMenu** vagy a megtekintési vezérlőket használja, de a jobb felhasználói élmény érdekében. Most tesztelje a kivágott síkot és a nézet vezérlőjét.

1. Hozzon létre egy új, üres GameObject a jelenetben, és nevezze el a **CutPlane**.
1. Adja hozzá a **RemoteCutPlane** összetevőt a **CutPlane** -GameObject.

   ![Sík összetevő-konfigurációjának kivágása](./media/cut-plane-config.png)

1. Kattintson a Play (lejátszás) gombra az Unity Editorban egy távoli munkamenet betöltéséhez és a kapcsolódáshoz.
1. A MRTK 's Hand szimulálása, a fogd és a forgatás (a CTRL billentyűt nyomva tartva) a CutPlane helyezheti át a jelenetet. A belső összetevők megjelenítéséhez tekintse meg a szeletet a **TestModel** .

![Példa a sík kivágására](./media/cut-plane-example-engine.png)

## <a name="configuring-the-remote-lighting"></a>A távoli megvilágítás konfigurálása

A távoli renderelési munkamenet a [világítási lehetőségek](../../../overview/features/lights.md)teljes spektrumát támogatja. Parancsfájlokat hozunk létre az [égbolt textúrához](../../../overview/features/sky.md) , valamint egy egyszerű térképet, amely két Unity típusú, távoli rendereléssel használható.

### <a name="sky-texture"></a>Égbolt textúrája

Számos beépített Cubemaps közül választhat az égbolt textúrájának módosításakor. Ezeket a rendszer betölti a munkamenetbe, és alkalmazza őket az égbolt textúrára. Az is lehetséges, hogy a [saját textúrákat is betöltheti](../../../concepts/textures.md) a felhőbe.

Létrehozunk egy **RemoteSky** -szkriptet, amely tartalmazza a betöltési paraméterek formájában elérhető beépített Cubemaps listáját. Ezután a felhasználó kiválaszthatja és betöltheti az egyik lehetőséget.

1. Hozzon létre egy **RemoteSky** nevű új parancsfájlt, és cserélje le a teljes tartalmát az alábbi kódra:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using System;
    using System.Collections.Generic;
    using UnityEngine;

    public class RemoteSky : BaseRemoteSky
    {
        public override Dictionary<string, LoadTextureFromSasOptions> AvailableCubemaps => builtInTextures;

        private bool canSetSky;
        public override bool CanSetSky
        {
            get => canSetSky;
            set
            {
                canSetSky = value;
                CanSetSkyChanged?.Invoke(canSetSky);
            }
        }

        private string currentSky = "DefaultSky";
        public override string CurrentSky
        {
            get => currentSky;
            protected set
            {
                currentSky = value;
                SkyChanged?.Invoke(value);
            }
        }

        private Dictionary<string, LoadTextureFromSasOptions> builtInTextures = new Dictionary<string, LoadTextureFromSasOptions>()
        {
            {"Autoshop",new LoadTextureFromSasOptions("builtin://Autoshop", TextureType.CubeMap)},
            {"BoilerRoom",new LoadTextureFromSasOptions("builtin://BoilerRoom", TextureType.CubeMap)},
            {"ColorfulStudio",new LoadTextureFromSasOptions("builtin://ColorfulStudio", TextureType.CubeMap)},
            {"Hangar",new LoadTextureFromSasOptions("builtin://Hangar", TextureType.CubeMap)},
            {"IndustrialPipeAndValve",new LoadTextureFromSasOptions("builtin://IndustrialPipeAndValve", TextureType.CubeMap)},
            {"Lebombo",new LoadTextureFromSasOptions("builtin://Lebombo", TextureType.CubeMap)},
            {"SataraNight",new LoadTextureFromSasOptions("builtin://SataraNight", TextureType.CubeMap)},
            {"SunnyVondelpark",new LoadTextureFromSasOptions("builtin://SunnyVondelpark", TextureType.CubeMap)},
            {"Syferfontein",new LoadTextureFromSasOptions("builtin://Syferfontein", TextureType.CubeMap)},
            {"TearsOfSteelBridge",new LoadTextureFromSasOptions("builtin://TearsOfSteelBridge", TextureType.CubeMap)},
            {"VeniceSunset",new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap)},
            {"WhippleCreekRegionalPark",new LoadTextureFromSasOptions("builtin://WhippleCreekRegionalPark", TextureType.CubeMap)},
            {"WinterRiver",new LoadTextureFromSasOptions("builtin://WinterRiver", TextureType.CubeMap)},
            {"DefaultSky",new LoadTextureFromSasOptions("builtin://DefaultSky", TextureType.CubeMap)}
        };

        public UnityBoolEvent OnCanSetSkyChanged;
        public override event Action<bool> CanSetSkyChanged;

        public UnityStringEvent OnSkyChanged;
        public override event Action<string> SkyChanged;

        public void Start()
        {
            // Hook up the event to the Unity event
            CanSetSkyChanged += (canSet) => OnCanSetSkyChanged?.Invoke(canSet);
            SkyChanged += (key) => OnSkyChanged?.Invoke(key);

            RemoteRenderingCoordinator.CoordinatorStateChange += ApplyStateToView;
            ApplyStateToView(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void ApplyStateToView(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CanSetSky = true;
                    break;
                default:
                    CanSetSky = false;
                    break;
            }
        }

        public override async void SetSky(string skyKey)
        {
            if (!CanSetSky)
            {
                Debug.Log("Unable to set sky right now");
                return;
            }

            if (AvailableCubemaps.ContainsKey(skyKey))
            {
                Debug.Log("Setting sky to " + skyKey);
                //Load the texture into the session
                var texture = await RemoteRenderingCoordinator.CurrentSession.Connection.LoadTextureFromSasAsync(AvailableCubemaps[skyKey]);

                //Apply the texture to the SkyReflectionSettings
                RemoteRenderingCoordinator.CurrentSession.Connection.SkyReflectionSettings.SkyReflectionTexture = texture;
                SkyChanged?.Invoke(skyKey);
            }
            else
            {
                Debug.Log("Invalid sky key");
            }
        }
    }
    ```

    A kód legfontosabb része csupán néhány sor:

    ```cs
    //Load the texture into the session
    var texture = await RemoteRenderingCoordinator.CurrentSession.Connection.LoadTextureFromSasAsync(AvailableCubemaps[skyKey]);

    //Apply the texture to the SkyReflectionSettings
    RemoteRenderingCoordinator.CurrentSession.Connection.SkyReflectionSettings.SkyReflectionTexture = texture;
    ```

    Itt egy hivatkozással láthatjuk el a textúrát, amelyet a beépített blob-tárolóból a munkamenetbe való betöltéssel lehet használni. Ezt követően csak ezt a textúrát kell hozzárendelni a munkamenethez, `SkyReflectionTexture` hogy alkalmazza azt.

1. Hozzon létre egy üres GameObject a jelenetben, és nevezze el a **tetőablaknak**.

1. Adja hozzá a **RemoteSky** -szkriptet a **tetőablak** -GameObject.

    Az ég fények közötti váltás a `SetSky` ben definiált karakterlánc-kulcsok egyikének meghívásával végezhető el `AvailableCubemaps` . A  **AppMenu** beépített View vezérlő automatikusan létrehozza a gombokat, és összekapcsolja az eseményeket, hogy meghívja a `SetSky` megfelelő kulcsot.
1. Nyomja le a play gombot az Unity Editorban, és engedélyezze a kapcsolatokat.
1. Miután csatlakoztatta a helyi futtatókörnyezetet egy távoli munkamenethez, navigáljon a **AppMenu-> munkamenet-eszközök-> távoli égbolton** a különböző Sky-beállítások megismeréséhez, és nézze meg, hogyan érintik a **TestModel**.

### <a name="scene-lights"></a>Jelenet jelzőfények

A távoli jelenetekhez tartozó fények a következők: pont, hely és irány. A fent létrehozott kivágási síkon hasonlóan ezek a jelenet-fények távoli entitások, amelyekhez hozzá vannak rendelve összetevők. Fontos figyelembe venni, hogy a távoli jelenet megvilágításakor a helyi jelenet megvilágításának megfelelően próbálkozik. Ez a stratégia nem mindig lehetséges, mert a 2. HoloLens számos Unity alkalmazása nem használ fizikailag alapú renderelést a helyileg megjelenített objektumokhoz. Bizonyos szintekhez azonban szimulálhatja az egység egyszerűbb alapértelmezett megvilágítását.

1. Hozzon létre egy **RemoteLight** nevű új parancsfájlt, és cserélje le a kódját az alábbi kódra:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(Light))]
    public class RemoteLight : BaseRemoteLight
    {
        public bool AutomaticallyCreate = true;

        private bool lightReady = false;
        public override bool LightReady 
        {
            get => lightReady;
            set
            {
                lightReady = value;
                LightReadyChanged?.Invoke(lightReady);
            }
        }

        private ObjectType remoteLightType = ObjectType.Invalid;
        public override ObjectType RemoteLightType => remoteLightType;

        public UnityBoolEvent OnLightReadyChanged;

        public override event Action<bool> LightReadyChanged;

        private Light localLight; //Unity Light

        private Entity lightEntity;
        private LightComponentBase remoteLightComponent; //Remote Rendering Light

        private void Awake()
        {
            localLight = GetComponent<Light>();
            switch (localLight.type)
            {
                case LightType.Directional:
                    remoteLightType = ObjectType.DirectionalLightComponent;
                    break;
                case LightType.Point:
                    remoteLightType = ObjectType.PointLightComponent;
                    break;
                case LightType.Spot:
                case LightType.Area:
                    //Not supported in tutorial
                case LightType.Disc:
                    // No direct analog in remote rendering
                    remoteLightType = ObjectType.Invalid;
                    break;
            }
        }

        public void Start()
        {
            // Hook up the event to the Unity event
            LightReadyChanged += (ready) => OnLightReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        public void OnDestroy()
        {
            lightEntity?.Destroy();
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateLight();
                    break;
                default:
                    DestroyLight();
                    break;
            }
        }

        public override void CreateLight()
        {
            if (remoteLightComponent != null)
                return; //Nothing to do!

            //Create a root object for the light
            if(lightEntity == null)
                lightEntity = RemoteRenderingCoordinator.CurrentSession.Connection.CreateEntity();

            //Bind the remote entity to this game object
            lightEntity.BindToUnityGameObject(this.gameObject);

            //Sync the transform of this object so we can move the light
            var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
            syncComponent.SyncEveryFrame = true;

            //Add a light to the entity
            switch (RemoteLightType)
            {
                case ObjectType.DirectionalLightComponent:
                    var remoteDirectional = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.DirectionalLightComponent, lightEntity) as DirectionalLightComponent;
                    //No additional properties
                    remoteLightComponent = remoteDirectional;
                    break;

                case ObjectType.PointLightComponent:
                    var remotePoint = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.PointLightComponent, lightEntity) as PointLightComponent;
                    remotePoint.Radius = 0;
                    remotePoint.Length = localLight.range;
                    //remotePoint.AttenuationCutoff = //No direct analog in Unity legacy lights
                    //remotePoint.ProjectedCubeMap = //No direct analog in Unity legacy lights

                    remoteLightComponent = remotePoint;
                    break;
                default:
                    LightReady = false;
                    return;
            }

            // Set the common values for all light types
            UpdateRemoteLightSettings();

            LightReady = true;
        }

        public override void UpdateRemoteLightSettings()
        {
            remoteLightComponent.Color = localLight.color.toRemote();
            remoteLightComponent.Intensity = localLight.intensity;
        }

        public override void DestroyLight()
        {
            if (remoteLightComponent == null)
                return; //Nothing to do!

            remoteLightComponent.Destroy();
            remoteLightComponent = null;
            LightReady = false;
        }

        [ContextMenu("Sync Remote Light Configuration")]
        public override void RecreateLight()
        {
            DestroyLight();
            CreateLight();
        }

        public override void SetIntensity(float intensity)
        {
            localLight.intensity = Mathf.Clamp(intensity, 0, 1);
            UpdateRemoteLightSettings();
        }

        public override void SetColor(Color color)
        {
            localLight.color = color;
            UpdateRemoteLightSettings();
        }
    }
    ```

    Ez a szkript különböző típusú távoli jelzõfényeket hoz létre attól függően, hogy milyen típusú helyi egységre van csatolva a parancsfájl. A távoli fény megismétli a helyi fényt: pozíció, forgás, szín és intenzitás. Ha lehetséges, a távoli fény további konfigurálást is beállíthat. Ez nem tökéletes egyezés, mert az Unity-fények nem a PBR-fények.

1. Keresse meg a **DirectionalLight** GameObject a saját jelenetében. Ha eltávolította az alapértelmezett **DirectionalLight** a jelenetből: a felső menüsorban válassza a *GameObject-> Light-> DirectionalLight* lehetőséget, hogy új fényt hozzon létre a jelenetben.

1. Válassza ki a **DirectionalLight** GameObject, és az **összetevő hozzáadása** gombbal adja hozzá a **RemoteLight** -szkriptet.

1. Mivel ez a parancsfájl az alaposztályt valósítja `BaseRemoteLight` meg, a megadott **AppMenu** -vezérlő használatával használhatja a távoli fényt. Navigáljon a **AppMenu->-munkamenet eszközeire – > irányt jelző fény**.

    > [!NOTE]
    > A **AppMenu** felhasználói felülete az egyszerűség kedvéért egyetlen, irányt jelző fényre korlátozódik. Azonban továbbra is lehetséges, és azt javasoljuk, hogy vegyen fel pontokat, és csatolja hozzájuk a **RemoteLight** szkriptet. Ezek a további fények a szerkesztőben az egységességi fény tulajdonságainak szerkesztésével módosíthatók. A távoli fény helyi módosításait manuálisan kell szinkronizálnia a **RemoteLight** helyi menüjének használatával.
    >
    > ![Távoli fény manuális szinkronizálása](./media/sync-remote-light.png)

1. Nyomja le a play gombot az Unity Editorban, és engedélyezze a kapcsolatokat.

1. Miután csatlakoztatta a futásidejű modult egy távoli munkamenethez, helyezze el és célozza meg a kamerát (a WASD és a jobb gombbal kattintson az + egér mozgatása lehetőségre), hogy a nézet legyen az irányított nézet vezérlője. 
1. A fény tulajdonságainak módosításához használja a Remote Light View vezérlőt. A MRTK-szimuláció, a fogd és a forgatás (a CTRL billentyűt lenyomva tartva) megtekintheti az irányt a jelenet megvilágításának hatásával.

    ![Irányított fény](./media/directional-light-test.png)

## <a name="editing-materials"></a>Anyagok szerkesztése

A távolról renderelt [anyagok](../../../concepts/materials.md) módosíthatók további vizuális hatások biztosításához, a renderelt modellek vizualizációinak finomhangolásához, illetve a felhasználókra vonatkozó további visszajelzések megadásához. Számos módszer és számos ok van az anyagok módosítására. Itt megtudhatja, hogyan módosíthatja az anyagok albedó színét, és módosíthatja a PBR-anyagok durvaságát és fémeket.

> [!NOTE]
> Sok esetben, ha egy funkció vagy hatás egy **HierarchicalStateOverrideComponent** használatával valósítható meg, ideális megoldás az anyag módosítása helyett.

Létrehozunk egy olyan parancsfájlt, amely elfogadja a célként megadott entitást, és néhány `OverrideMaterialProperty` objektumot konfigurál, hogy megváltoztassa a célként megadott entitás anyagának tulajdonságait. Kezdjük a cél entitás [**MeshComponent**](../../../concepts/meshes.md#meshcomponent)beszerzésével, amely a Hálón használt anyagok listáját tartalmazza. Az egyszerűség kedvéért csak az első anyagot fogjuk használni. Ez a naiv stratégia nagyon egyszerűen meghiúsulhat attól függően, hogy a tartalom hogyan lett létrehozva, ezért érdemes lehet összetettebb megközelítést választani a megfelelő anyagok kiválasztásához.

Az anyagból elérheti a közös értékeket, például a albedó. Először az anyagokat a megfelelő típus szerint kell feldolgozni, `PbrMaterial` vagy az `ColorMaterial` értékeket a **GetMaterialColor** metódusban látható módon kell lekérni. Ha már van egy hivatkozás a kívánt anyagra, csak állítsa be az értékeket, és az ARR kezeli a helyi anyagok tulajdonságai és a távoli anyagok közötti szinkronizálást.

1. Hozzon létre egy **EntityMaterialController** nevű szkriptet, és cserélje le a tartalmát a következő kódra:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using System.Linq;
    using UnityEngine;
    // to prevent namespace conflicts
    using ARRMaterial = Microsoft.Azure.RemoteRendering.Material;

    public class EntityMaterialController : BaseEntityMaterialController
    {
        public override bool RevertOnEntityChange { get; set; } = true;

        public override OverrideMaterialProperty<Color> ColorOverride { get; set; }
        public override OverrideMaterialProperty<float> RoughnessOverride { get; set; }
        public override OverrideMaterialProperty<float> MetalnessOverride { get; set; }

        private Entity targetEntity;
        public override Entity TargetEntity
        {
            get => targetEntity;
            set
            {
                if (targetEntity != value)
                {
                    if (targetEntity != null && RevertOnEntityChange)
                    {
                        Revert();
                    }

                    targetEntity = value;
                    ConfigureTargetEntity();
                    TargetEntityChanged?.Invoke(value);
                }
            }
        }

        private ARRMaterial targetMaterial;
        private ARRMeshComponent meshComponent;

        public override event Action<Entity> TargetEntityChanged;
        public UnityRemoteEntityEvent OnTargetEntityChanged;

        public void Start()
        {
            // Forward events to Unity events
            TargetEntityChanged += (entity) => OnTargetEntityChanged?.Invoke(entity);

            // If there happens to be a remote RayCaster on this object, assume we should listen for events from it
            if (GetComponent<BaseRemoteRayCastPointerHandler>() != null)
                GetComponent<BaseRemoteRayCastPointerHandler>().RemoteEntityClicked += (entity) => TargetEntity = entity;
        }

        protected override void ConfigureTargetEntity()
        {
            //Get the Unity object, to get the sync object, to get the mesh component, to get the material.
            var targetEntityGameObject = TargetEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

            var localSyncObject = targetEntityGameObject.GetComponent<RemoteEntitySyncObject>();
            meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
            if (meshComponent == null)
            {
                var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
                if (mesh != null)
                {
                    targetEntityGameObject.BindArrComponent<ARRMeshComponent>(mesh);
                    meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
                }
            }

            meshComponent.enabled = true;

            targetMaterial = meshComponent.RemoteComponent.Mesh.Materials.FirstOrDefault();
            if (targetMaterial == default)
            {
                return;
            }

            ColorOverride = new OverrideMaterialProperty<Color>(
                GetMaterialColor(targetMaterial), //The original value
                targetMaterial, //The target material
                ApplyMaterialColor); //The action to take to apply the override

            //If the material is a PBR material, we can override some additional values
            if (targetMaterial.MaterialSubType == MaterialType.Pbr)
            {
                var firstPBRMaterial = (PbrMaterial)targetMaterial;

                RoughnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Roughness, //The original value
                    targetMaterial, //The target material
                    ApplyRoughnessValue); //The action to take to apply the override

                MetalnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Metalness, //The original value
                    targetMaterial, //The target material
                    ApplyMetalnessValue); //The action to take to apply the override
            }
            else //otherwise, ensure the overrides are cleared out from any previous entity
            {
                RoughnessOverride = null;
                MetalnessOverride = null;
            }
        }

        public override void Revert()
        {
            if (ColorOverride != null)
                ColorOverride.OverrideActive = false;

            if (RoughnessOverride != null)
                RoughnessOverride.OverrideActive = false;

            if (MetalnessOverride != null)
                MetalnessOverride.OverrideActive = false;
        }

        private Color GetMaterialColor(ARRMaterial material)
        {
            if (material == null)
                return default;

            if (material.MaterialSubType == MaterialType.Color)
                return ((ColorMaterial)material).AlbedoColor.toUnity();
            else
                return ((PbrMaterial)material).AlbedoColor.toUnity();
        }

        private void ApplyMaterialColor(ARRMaterial material, Color color)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Color)
                ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
            else
                ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
        }

        private void ApplyRoughnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Roughness
                ((PbrMaterial)material).Roughness = value;
        }

        private void ApplyMetalnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Metalness
                ((PbrMaterial)material).Metalness = value;
        }
    }
    ```

A `OverrideMaterialProperty` típusnak elég rugalmasnak kell lennie ahhoz, hogy a további lényeges értékeket meg lehessen változtatni, ha szükséges. A `OverrideMaterialProperty` típus nyomon követi egy felülbírálás állapotát, megtartja a régi és az új értéket, és egy delegált használatával állítja be a felülbírálást. Példaként tekintse meg a következőt `ColorOverride` :

```cs
ColorOverride = new OverrideMaterialProperty<Color>(
    GetMaterialColor(targetMaterial), //The original value
    targetMaterial, //The target material
    ApplyMaterialColor); //The action to take to apply the override
```

Ez létrehoz egy új `OverrideMaterialProperty` értéket, amelyben a felülbírálás becsomagolja a típust `Color` . A felülbírálás létrejöttének időpontjában a jelenlegi vagy az eredeti színt adja meg. Egy ARR-anyagot is biztosítunk. Végezetül meg kell adni egy delegált, amely alkalmazza a felülbírálást. A delegált egy olyan metódus, amely az ARR-anyagokat fogadja el, és írja be a felülbírálási sortöréseket. Ez a módszer a legfontosabb része annak megértésében, hogy az ARR hogyan módosítja az anyagi értékeket.

A a módszert használja a munka elvégzésére `ColorOverride` `ApplyMaterialColor` :

```cs
private void ApplyMaterialColor(ARRMaterial material, Color color)
{
    if (material.MaterialSubType == MaterialType.Color)
        ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
    else
        ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
}
```

Ez a kód egy anyagot és egy színt fogad el. Ellenőrzi, hogy milyen típusú anyagokat tartalmaz, majd a szín alkalmazásához az anyagot öntötte.

A `RoughnessOverride` és a `MetalnessOverride` hasonlóan működik – a `ApplyRoughnessValue` és a módszerek használata a `ApplyMetalnessValue` munkájuk elvégzéséhez.

Ezután tesztelje az anyag vezérlőt.

1. Adja hozzá a **EntityMaterialController** parancsfájlt a **TestModel** -GameObject.
1. Nyomja meg az egységben a lejátszás gombot a jelenet elindításához és az ARR-hez való kapcsolódáshoz.
1. Miután csatlakoztatta a futtatókörnyezetet egy távoli munkamenethez, és betölti a modellt, navigáljon a **AppMenu-> Model Tools-> anyagok szerkesztése** elemre.
1. Válasszon ki egy entitást a modellből a szimulált kezek használatával, és kattintson a **TestModel**.
1. Győződjön meg arról, hogy az anyag nézet vezérlő (**AppMenu->Model Tools->Edit Material**) frissítve lett a célként kijelölt entitásra.
1. Az anyag nézet vezérlővel állíthatja be az anyagot a célként kijelölt entitáson.

Mivel csak a háló első anyagát módosítjuk, előfordulhat, hogy nem változik az anyag. A hierarchikus felülbírálás **átnézet** használatával ellenőrizze, hogy a módosított anyag a Hálón belül van-e.

![Anyag szerkesztése példa](./media/material-edit-example.png)

## <a name="next-steps"></a>Következő lépések

Gratulálunk! Ezzel végrehajtotta az Azure távoli renderelés összes alapvető funkcióját. A következő fejezetben megismerheti az Azure-beli távoli renderelés és a blob-tároló biztonságossá tételét. Ezek az első lépések az Azure távoli renderelést használó kereskedelmi alkalmazások kiadásához.

> [!div class="nextstepaction"]
> [Tovább: az Azure távoli renderelés és a modell tárterületének védelme](../security/security.md)