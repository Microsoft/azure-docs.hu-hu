---
title: Felületek és egyéni modellek
description: A View Controllers hozzáadása és az Azure távoli renderelés által megjelenített egyéni modellek betöltése
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a278b6e725488d6107e6b0819e002d1dafe4774
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591663"
---
# <a name="tutorial-interfaces-and-custom-models"></a>Oktatóanyag: felületek és egyéni modellek

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Vegyes valóság eszközkészlet hozzáadása a projekthez
> * Modell állapotának kezelése
> * Azure-Blob Storage konfigurálása a modell betöltéséhez
> * Modellek feltöltése és feldolgozása rendereléshez

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag az [oktatóanyagra épül: távolról renderelt modell megtekintése](../view-remote-models/view-remote-models.md).

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>Ismerkedés a Mixed Reality Toolkit (MRTK) használatával

A Mixed Reality Toolkit (MRTK) egy többplatformos eszközkészlet, amely vegyes valóságú tapasztalatokat épít ki. A MRTK 2.5.1-t fogjuk használni az interakciós és vizualizációs funkciókhoz.

MRTK hozzáadásához kövesse a [MRTK telepítési útmutatójában](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html)felsorolt [szükséges lépéseket](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#required) .

Ezek a lépések a következők:
 - [A legújabb MRTK Unity-csomagok beszerzése](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#1-get-the-latest-mrtk-unity-packages)
     - Annak ellenére, hogy a "legutóbbi" kifejezést adja meg, használja a 2.5.1-es verziót a MRTK kiadási oldaláról.
     - Ebben az oktatóanyagban csak az *Foundation* -csomagot használjuk. A *bővítmények*, *eszközök* és *példák* nem szükségesek.
 - [MRTK-csomagok importálása az Unity-projektbe](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#2-import-mrtk-packages-into-your-unity-project)
 - [Az Unity-projekt átváltása a célként megadott platformra](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#3-switch-your-unity-project-to-the-target-platform)
     - Ezt a lépést már az első fejezetben kell megtennie, de most már jó idő van a kettős vizsgálatra!
 - [MRTK hozzáadása új jelenethez vagy új projekthez](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#4-add-and-configure-mrtk-with-a-new-scene)
     - Hozzáadhat MRTK új jelenetekhez, és újból hozzáadhatja a koordinátort és a Model Objects/szkripteket, vagy hozzáadhat MRTK a meglévő jelenethez a *vegyes valóság Toolkit – > Hozzáadás a jelenethez és* a menü konfigurálása parancs használatával.

## <a name="import-assets-used-by-this-tutorial"></a>Az oktatóanyag által használt eszközök importálása

Ebből a fejezetből megtudhatja, hogyan valósítható meg egy egyszerű [modell – vezérlő minta](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) az érintett anyagok nagy része számára. A *minta része az* Azure Remote rendering speciális kódja, valamint az Azure-beli távoli rendereléshez kapcsolódó állami felügyelet. A minta *nézete* és *vezérlő* részei a MRTK-eszközök és néhány egyéni szkript használatával valósíthatók meg. Ebben az oktatóanyagban az itt ismertetett *megtekintési vezérlő* nélkül is használhatja a *modellt* . Ez az elkülönítés lehetővé teszi, hogy egyszerűen integrálja az oktatóanyagban található kódot a saját alkalmazásba, ahol átveszi a tervezési minta *nézet – vezérlő* részét.

A MRTK bevezetésével számos olyan parancsfájlt, panelt és eszközt használhat, amely most már hozzáadható a projekthez az interakciók és a vizuális visszajelzések támogatásához. Ezek az eszközök, mint az **oktatóanyagi eszközök**, egy [Unity Asset-csomagba](https://docs.unity3d.com/Manual/AssetPackages.html)vannak csomagolva, amely a "\Unity\TutorialAssets\TutorialAssets.unitypackage" [Azure távoli renderelési GitHub](https://github.com/Azure/azure-remote-rendering) részét képezi.

1. A git-tárház [Azure távoli renderelésének](https://github.com/Azure/azure-remote-rendering)klónozása vagy letöltése, ha a letöltés kibontja a zip-fájlt egy ismert helyre.
1. Az Unity-projektben válassza az *eszközök – > csomag importálása – > egyéni csomag* elemet.
1. A Fájlkezelőben keresse meg azt a könyvtárat, ahová klónozott vagy kicsomagolta az Azure távoli renderelési tárházát, majd válassza ki az **Unity-> TutorialAssets-> TutorialAssets. unitypackage** mappában található. unitypackage
1. Válassza az **Importálás** gombot a csomag tartalmának a projektbe való importálásához.
1. Az Unity Editorban válassza a *vegyes valóság eszközkészlet – > segédprogramok – > a MRTK Standard shader frissítése a Lightweight renderelési folyamathoz* lehetőséget a felső menüsorban, és kövesse az utasításokat a shader frissítéséhez.

A MRTK és az oktatóanyag-eszközök projektbe való felvételét követően a MRTK-profilt az oktatóanyaghoz alkalmasabbnak fogjuk váltani.

1. Válassza ki a **MixedRealityToolkit** GameObject a jelenet hierarchiában.
1. Az Ellenőrben a **MixedRealityToolkit** összetevő alatt állítsa át a konfigurációs profilt a *ARRMixedRealityToolkitConfigurationProfile* értékre.
1. Nyomja le a *CTRL + S* billentyűkombinációt a módosítások mentéséhez.

Ezzel a beállítással a MRTK-t, elsősorban az alapértelmezett HoloLens 2 profillal konfigurálja. A megadott profilok a következő módokon vannak előre konfigurálva:
 - Kapcsolja ki a Profilert (a 9-es gomb megnyomásával be-vagy kikapcsolhatja a "Profiler megjelenítése/elrejtése" eszközt).
 - Kapcsolja ki a szem tekintetét mutató kurzort.
 - Az Unity egérkattintásának engedélyezése, így a szimulált kéz helyett az egérrel a MRTK FELHASZNÁLÓIFELÜLET-elemek elemre kattinthat.

## <a name="add-the-app-menu"></a>Az alkalmazás menü hozzáadása

Az oktatóanyagban szereplő vezérlők többsége a konkrét osztályok helyett absztrakt alaposztályokon működik. Ez a minta nagyobb rugalmasságot biztosít, és lehetővé teszi számunkra, hogy megismerje a vezérlők megtekintését, miközben továbbra is segítséget nyújt az Azure távoli renderelési kódjának megismeréséhez. Az egyszerűség kedvéért a **RemoteRenderingCoordinator** osztályhoz nincs megadva absztrakt osztály, és a nézet vezérlője közvetlenül a konkrét osztályon működik.

Most hozzáadhatja a panel **AppMenu** a jelenethez az aktuális munkamenet-állapot vizuális visszajelzéséhez. Ez a nézet-vezérlő "feloldja" a további almenü-vezérlőket, ahogy Implementáljuk és további ARR-funkciókat integrálunk a jelenetbe. Egyelőre a **AppMenu** az ARR állapotot vizuálisan jelzi, és bemutatja a modális panelt, amelyet a felhasználó használ, hogy engedélyezze az alkalmazásnak az ARR-hez való kapcsolódást.

1. Keresse meg a **AppMenu** panelt az *eszközök/RemoteRenderingTutorial/előregyártott/AppMenu*
1. Húzza a **AppMenu** panelt a jelenetbe.
1. Valószínűleg megjelenik egy párbeszédpanel a tmp- **importőr** számára, mivel ez az első alkalom, hogy a jelenetben a *text Mesh Pro* -eszközök is szerepelnek. Kövesse az utasításokat a **tmp Essentials importálásához**. Ezután az importáló párbeszédpanel bezárásához a példák és az extrák nem szükségesek.
1. A **AppMenu** úgy van konfigurálva, hogy automatikusan összekapcsoljon, és biztosítson egy munkamenethez való csatlakozáshoz szükséges modális lehetőséget, így törölheti a korábban megkerülő megkerülő beállítást. A **RemoteRenderingCoordinator** -GameObject távolítsa el a korábban megvalósított engedélyezés mellőzését az **engedélyezést kérő** esemény "-" gombjának megnyomásával.
 ![A Mellőzés eltávolítása ](./media/remove-bypass-event.png) .
1. Tesztelje a nézet vezérlőjét a **Play (lejátszás** ) megnyomásával az Unity Editorban.
1. A szerkesztőben most, hogy a MRTK konfigurálva van, a WASD billentyűk használatával módosíthatja a nézet pozícióját, és a jobb oldali egérgomb + az egér mozgatásával módosíthatja a nézet irányát. Próbálja ki a "vezetést" a jelenet körül egy kicsit, hogy kedve legyen a vezérlők számára.
1. Az eszközön felveheti a tenyerét, hogy meghívja a **AppMenu**, az Unity Editorban használja a gyorsbillentyűt.
1. Ha elvesztette a menüt, nyomja le az 'M billentyűt a menü megidézéséhez. Az egyszerű interakcióhoz a menü a kamera közelében lesz elhelyezve.
1. Az engedélyezés mostantól a **AppMenu** jobb oldalán megjelenő kérelemként fog megjelenni, ezzel engedélyezi az alkalmazás számára a távoli renderelési munkamenetek kezelését.
 ![Felhasználói felület engedélyezése](./media/authorize-request-ui.png)
1. Állítsa le az egységet a lejátszásból, és folytassa az oktatóanyagot.

## <a name="manage-model-state"></a>Modell állapotának kezelése

Most egy új szkriptet fogunk megvalósítani, amely a **RemoteRenderedModel** , az eseményekre, az égetési eseményekre és a konfigurációra reagál. A **RemoteRenderedModel** lényegében a modellben tárolt adattípusok távoli elérési útját tárolja `modelPath` . A **RemoteRenderingCoordinator** megfigyelheti az állapotváltozás állapotát, hogy ellenőrizze, automatikusan be kell-e töltenie vagy eltávolítania az általa definiált modellt. A **RemoteRenderedModel** csatolt GameObject a távoli tartalom helyi szülője lesz.

Figyelje meg, hogy a **RemoteRenderedModel** -szkript implementálja az **oktatóanyag eszközeinek** részét képező **BaseRemoteRenderedModel**. Ez lehetővé teszi, hogy a távoli modell nézet vezérlője kötést hozzon létre a parancsfájlhoz.

1. Hozzon létre egy **RemoteRenderedModel** nevű új parancsfájlt ugyanabban a mappában, mint a **RemoteRenderingCoordinator**. Cserélje le a teljes tartalmat a következő kódra:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

A **RemoteRenderedModel** a legalapvetőbb feltételek szerint tárolja a modell betöltéséhez szükséges adatok (ebben az esetben az SAS vagy a *Builtin://* URI), és nyomon követi a távoli modell állapotát. Amikor betöltődik a betöltés ideje, a `LoadModel` metódust a rendszer **RemoteRenderingCoordinator** hívja meg, és a modellt tartalmazó entitást adja vissza referenciának és eltávolításnak.

## <a name="load-the-test-model"></a>A teszt modell betöltése

Tesztelje az új szkriptet a test Model ismételt betöltésével. Hozzunk létre egy Game objektumot, amely tartalmazza a szkriptet, és szülőnek kell lennie a tesztelési modellnek.

1. Hozzon létre egy új, üres játék objektumot a jelenetben, és nevezze el **TestModel**.
1. Adja hozzá a *RemoteRenderedModel* parancsfájlt a **TestModel**.
![RemoteRenderedModel-összetevő hozzáadása](./media/add-remote-rendered-model-script.png)
1. Töltse ki a `Model Display Name` és a kifejezést az `Model Path` "*TestModel*" és a "*Builtin://Engine*" értékkel.
![Modell részleteinek megadása](./media/add-model-script.png)
1. Helyezze a **TestModel** objektumot a kamera elé, az **x = 0, y = 0, z = 3** pozícióban.
![Objektum elhelyezése](./media/test-model-position.png)
1. Győződjön meg arról, hogy a **AutomaticallyLoad** be van kapcsolva.
1. Az alkalmazás teszteléséhez kattintson a **Play (lejátszás** ) gombra az Unity Editorban.
1. Engedélyezze az engedélyezést a *Kapcsolódás* gombra kattintva, hogy az alkalmazás hozzon létre egy munkamenetet, és hogy egy munkamenethez kapcsolódjon, és automatikusan töltse be a modellt.

Tekintse meg a konzolt, amikor az alkalmazás állapota az állapotokon halad. Ne feledje, hogy egyes állapotok eltarthat egy ideig, és nem fog megjelenni az előrehaladás. Végül a modell betöltését fogja látni a naplókból, majd a teszt modellt a jelenetben fogja megjeleníteni.

Próbálja meg áthelyezni és elforgatni a **TestModel** GameObject az ellenőrben vagy a jelenet nézetben. Ekkor megjelenik a modell mozgatása és elforgatása a játék nézetben.

![Unity-napló](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Blob Storage kiépítése az Azure-ban és az egyéni modellek betöltése

Most megpróbáljuk betölteni a saját modelljét. Ehhez konfigurálnia kell Blob Storage és az Azure-ban, fel kell töltenie és konvertálnia kell egy modellt, majd be kell töltenie a modellt a **RemoteRenderedModel** -szkript használatával. Az egyéni modell betöltése lépéseit nyugodtan kihagyhatja, ha nincs saját modellje betölteni.

Kövesse a rövid útmutatóban megadott lépéseket [: modell átalakítása renderelésre](../../../quickstarts/convert-model.md). Ugorja **át az új modell beszúrása a gyors üzembe helyezési minta alkalmazásba** szakaszt az oktatóanyag céljára. Miután megtörtént a betöltött modell *megosztott hozzáférési aláírása (SAS)* URI-ja, folytassa a következő lépéssel.

## <a name="load-and-rendering-a-custom-model"></a>Egyéni modell betöltése és megjelenítése

1. Hozzon létre egy új üres GameObject a jelenetben, és nevezze el az egyéni modellhez hasonló nevet.
1. Adja hozzá a *RemoteRenderedModel* parancsfájlt az újonnan létrehozott GameObject.
 ![RemoteRenderedModel-összetevő hozzáadása](./media/add-remote-rendered-model-script.png)
1. Töltse ki a `Model Display Name` megfelelő nevet a modellnek.
1. Töltse ki az `Model Path` elemet a modellben a fenti betöltési lépések során létrehozott *megosztott hozzáférés-aláírási (SAS)* URI-val.
1. Helyezze a GameObject a kamera elé, az **x = 0, y = 0, z = 3** pozíciónál.
1. Győződjön meg arról, hogy a **AutomaticallyLoad** be van kapcsolva.
1. Az alkalmazás teszteléséhez kattintson a **Play (lejátszás** ) gombra az Unity Editorban.

    Látni fogja, hogy a konzol megkezdi az aktuális állapot feltöltését, és végül a modell betöltésének folyamatát. Az egyéni modell ezután betöltődik a jelenetbe.

1. Távolítsa el az egyéni modell objektumot a jelenetből. Ennek az oktatóanyagnak a legjobb tapasztalata a tesztelési modell használata. Noha az ARR-ben több modell is támogatott, ez az oktatóanyag a lehető legjobb támogatás egyetlen távoli modellhez.

## <a name="next-steps"></a>Következő lépések

Most már betöltheti saját modelljeit az Azure Remote rendering szolgáltatásba, és megtekintheti őket az alkalmazásban. Ezután végigvezeti Önt a modelljeinek kezelésén.

> [!div class="nextstepaction"]
> [Következő: modellek módosítása](../manipulate-models/manipulate-models.md)
