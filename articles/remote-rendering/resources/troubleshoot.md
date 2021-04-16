---
title: Hibaelhárítás
description: Hibaelhárítási információk a Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 8f0fb9ab5c53c3fd1bfb32ac7b112a116301cba7
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575343"
---
# <a name="troubleshoot"></a>Hibaelhárítás

Ez az oldal felsorolja az adatbázissal kapcsolatos gyakori Azure Remote Rendering és a megoldásuk módjait.

## <a name="cant-link-storage-account-to-arr-account"></a>A tárfiókot nem lehet csatlakoztatni az ARR-fiókhoz

Előfordulhat, [hogy a tárfiókok összekapcsolása](../how-tos/create-an-account.md#link-storage-accounts) Remote Rendering a fiók nem szerepel a listán. A probléma megoldásához a bal oldalon, a Beállítások  csoportban  Azure Portal ARR-fiókot. Ellenőrizze, **hogy az Állapot** be van-e **állítva.**
![Unity-képkocka hibakereső](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Az ügyfél nem tud csatlakozni a kiszolgálóhoz

Győződjön meg arról, hogy a tűzfalak (az eszközön, útválasztón belül stb.) nem blokkolják a [rendszerkövetelmények között említett portokat.](../overview/system-requirements.md#network-firewall)

## <a name="error-disconnected-videoformatnotavailable"></a>Hiba: `Disconnected: VideoFormatNotAvailable` '

Ellenőrizze, hogy a GPU támogatja-e a hardveres videó dekódolást. Lásd: [Fejlesztői számítógép.](../overview/system-requirements.md#development-pc)

Ha két GPU-val működő laptopon dolgozik, akkor lehetséges, hogy az alapértelmezés szerint futtatott GPU nem biztosít hardveres videokódolási funkciót. Ha igen, próbálja meg kényszeríteni az alkalmazást a másik GPU használatára. Ez gyakran lehetséges a GPU-illesztőprogram beállításaiban.

## <a name="retrieve-sessionconversion-status-fails"></a>A munkamenet/átalakítási állapot lekérése sikertelen

Ha REST API parancsokat túl gyakran küld, akkor a kiszolgáló végül el fogja tudni fogja tudni tudni kordani a kiszolgálót, és hibát fog visszaadni. A szabályozási esetben a HTTP-állapotkód 429 ("túl sok kérelem"). Általános szabályként a következő hívások között **5–10 másodperces késleltetésnek kell lennie.**

Vegye figyelembe, hogy ez a korlátozás nem csak a közvetlen hívásokat REST API, hanem a C#/C++ megfelelőiket is, például a `Session.GetPropertiesAsync` , `Session.RenewAsync` vagy a hívását `Frontend.GetAssetConversionStatusAsync` is.

Ha kiszolgálóoldali szabályozást tapasztal, módosítsa a kódot úgy, hogy ritkábban használja a hívásokat. A kiszolgáló percenként alaphelyzetbe állítja a szabályozási állapotot, így egy perc után biztonságosan újrafuttathatja a kódot.

## <a name="h265-codec-not-available"></a>A H265 codec nem érhető el

Két oka lehet annak, hogy a kiszolgáló megtagadja a kapcsolódást egy `codec not available` hibával.

**A H265 kodek nincs telepítve:**

Először telepítse a  HEVC-videobővítmények a rendszerkövetelmények [](../overview/system-requirements.md#software) Szoftver szakaszában említettek szerint.

Ha továbbra is problémákba ütközik, ellenőrizze, hogy a grafikus kártya támogatja-e a H265-öt, és hogy telepítve van-e a legújabb grafikus illesztőprogram. A [gyártóspecifikus információkért tekintse](../overview/system-requirements.md#development-pc) meg a rendszerkövetelmények Fejlesztői számítógép című szakaszát.

**A kodek telepítve van, de nem használható:**

A probléma oka a DLL-ek helytelen biztonsági beállítása. Ez a probléma nem jelentkezik a H265 kódolású videók megtekintésekor. A kodek újratelepítése sem oldja meg a problémát. Ehelyett hajtsa végre a következő lépéseket:

1. Nyisson meg egy **PowerShellt rendszergazdai jogosultságokkal,** és futtassa a parancsot

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Ennek a parancsnak a következő `InstallLocation` képhez hasonló kimenetet kell kihozni a kodekből:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Nyissa meg ezt a mappát a Windows Explorer
1. Egy **x86 és** egy **x64 almappának** kell lennie. Kattintson a jobb gombbal az egyik mappára, és válassza a Tulajdonságok **lehetőséget**
    1. Válassza a **Biztonság lapot,** majd kattintson a **Speciális beállítások** gombra
    1. Kattintson **a Tulajdonos** módosítása **lehetőségre**
    1. Írja be **a Rendszergazdák** szöveget a szövegmezőbe
    1. Kattintson **a Névellenőrzés és** az OK **gombra.**
1. Ismételje meg a fenti lépéseket a másik mappával is
1. Ismételje meg a fenti lépéseket mindkét mappában található DLL-fájlokon is. Összesen négy DLL-nek kell lennie.

Annak ellenőrzéséhez, hogy a beállítások helyesek-e, tegye ezt a következő négy DLL mindegyikéhez:

1. A **Biztonság > és > kiválasztása**
1. Tekintse át az összes **csoport/felhasználó** listáját, és győződjön meg arról, hogy mindegyik rendelkezik  a Read **& Execute** (Végrehajtás) jobb oldali beállításokkal (az engedélyező oszlopban lévő pipának pipának kell lennie)

## <a name="low-video-quality"></a>Alacsony videóminőség

A videóminőséget a hálózati minőség vagy a hiányzó H265 videokodekek veszélyeztethetik.

* Lásd a hálózati problémák [azonosításának lépéseit.](#unstable-holograms)
* Tekintse meg [a legújabb](../overview/system-requirements.md#development-pc) grafikus illesztőprogram telepítésére vonatkozó rendszerkövetelményeket.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Az MRC-val rögzített videó nem tükrözi az élő élmény minőségét

A HoloLensen a videó a Vegyes valóság rögzítése [(MRC) segítségével rögzítható.](/windows/mixed-reality/mixed-reality-capture-for-developers) Az eredményül kapott videó minősége azonban két okból rosszabb, mint az élő élmény:
* A videó képkocka-rátának felső határa 30 Hz, nem pedig 60 Hz.
* A videóképek nem jelennek meg a reprodukálás utolsó fázisának feldolgozási lépésén, ezért a videó elégedettnek tűnik. [](../overview/features/late-stage-reprojection.md)

Mindkettő a rögzítési technika eredendő korlátozásai.

## <a name="black-screen-after-successful-model-loading"></a>Fekete képernyő a sikeres modellbetöltés után

Ha csatlakozik a renderelési futtatáshoz, és sikeresen betöltött egy modellt, de utána csak fekete képernyő jelenik meg, akkor ennek több különböző oka lehet.

Javasoljuk, hogy a részletes elemzés előtt tesztelni kell a következőket:

* Telepítve van a H265-kodek? Bár a H264-kodek tartalékként is működik, olyan eseteket is láttunk, amelyekben ez a tartalék nem működött megfelelően. Tekintse meg [a legújabb](../overview/system-requirements.md#development-pc) grafikus illesztőprogram telepítésére vonatkozó rendszerkövetelményeket.
* Unity-projekt használata esetén zárja be  a Unityt, törölje az ideiglenes kódtárat és *az obj* mappát a projektkönyvtárból, és töltse be/buildje újra a projektet. Bizonyos esetekben a gyorsítótárazott adatok miatt a minta nyilvánvaló ok nélkül nem működik megfelelően.

Ha ez a két lépés nem segített, meg kell tudni, hogy az ügyfél megkapta-e a képkockákat. Ez programozott módon lekérdezhető a kiszolgálóoldali [teljesítménylekérdezésekkel kapcsolatos fejezetben leírtak](../overview/features/performance-queries.md) szerint. A `FrameStatistics struct` rendelkezik egy tagokkal, amely jelzi, hogy hány képkockát kapott a videó. Ha ez a szám nagyobb, mint 0, és idővel növekszik, az ügyfél tényleges videókereteket kap a kiszolgálótól. Ennek következtében problémát kell jelentenünk az ügyféloldalon.

### <a name="common-client-side-issues"></a>Gyakori ügyféloldali problémák

**A modell túllépi a kiválasztott virtuális gép korlátait, pontosabban a sokszögek maximális számát:**

Tekintse meg a [kiszolgálók méretére vonatkozó korlátozásokat.](../reference/limits.md#overall-number-of-polygons)

**A modell nincs a kamera frustumában:**

Sok esetben a modell helyesen jelenik meg, de a kamera frustumja kívül helyezkedik el. Ennek gyakori oka, hogy a modellt egy középen túli eltolással exportálták, így a kamera távoli vágósíkja levágja. Segít programozott módon lekérdezni a modell határolókeretét, és a Unityt vonalmezőként ábrázolni, vagy kinyomtatni az értékeit a hibakeresési naplóba.

Emellett az átalakítási folyamat létrehoz egy kimeneti [JSON-fájlt](../how-tos/conversion/get-information.md) a konvertált modellel együtt. A modellpozícióval kapcsolatos problémák hibakeresése érdekében érdemes megnézni az `boundingBox` [outputStatistics szakasz bejegyzését:](../how-tos/conversion/get-information.md#the-outputstatistics-section)

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

A határolókeretet a és a pozíciót `min` `max` 3D térben, méterben írva írják le. Tehát a 1000,0 koordináta azt jelenti, hogy 1 km-re van a forrástól.

Ez a határolókeret két olyan problémát okozhat, amelyek láthatatlan geometriához vezetnek:
* **A mező lehet távol a** középponttól, így az objektum teljesen ki van levágva a távolról kivágás miatt. Az értékek ebben az esetben a következőek: , amely egy nagy eltolást használ az `boundingBox` `min = [-2000, -5,-5], max = [-1990, 5,5]` x tengelyen példaként. Az ilyen típusú probléma megoldásához engedélyezze a beállítást `recenterToOrigin` a modellkonverzió [konfigurációjában.](../how-tos/conversion/configure-model-conversion.md)
* **A mező középre lehet ható, de túl nagy nagyságrendű.** Ez azt jelenti, hogy bár a kamera a modell közepén kezdődik, a geometriájának minden irányban ciklikusnak kell lenni. A `boundingBox` tipikus értékek ebben az esetben így néznek ki: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . Az ilyen típusú probléma oka általában az egységskála eltérése. A kompenzálás érdekében adjon meg egy [méretezési](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) értéket az átalakítás során, vagy jelölje meg a forrásmodellt a megfelelő egységekkel. A skálázás a modell futásidőben való betöltésekor is alkalmazható a gyökércsomópontra.

**A Unity renderelési folyamat nem tartalmazza a renderelési hookokat:**

Azure Remote Rendering a Unity renderelési folyamathoz, hogy a képkocka-összeállítást a videóval és a reprodukálás során el tudja látni. A hookok létezésének ellenőrzéséhez nyissa meg a *:::no-loc text="Window > Analysis > Frame debugger":::* menüt. Engedélyezze, és győződjön meg arról, hogy a folyamatban két bejegyzés `HolographicRemotingCallbackPass` van:

![Unity renderelési folyamat](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>Az ellenőrzőtábla minta a modell betöltése után jelenik meg

Ha a megjelenített kép így néz ki: Képernyőkép a fekete-fehér négyzetek rácsával és ![ egy Eszközök menüvel.](../reference/media/checkerboard.png)
ezután a renderelő eléri a standard konfigurációs méret [sokszögkorlátját.](../reference/vm-sizes.md) A probléma enyhítése  érdekében váltson prémium szintű konfigurációra, vagy csökkentse a látható sokszögek számát.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>A Unityben renderelt kép lefelé lefelé van fordítva

Ügyeljen arra, hogy pontosan kövesse a [Unity oktatóanyagát: Távoli modellek megtekintése.](../tutorials/unity/view-remote-models/view-remote-models.md) Egy lefelé lefelé irányuló, lefelé irányuló kép azt jelzi, hogy a Unity egy képernyőn belüli renderelési cél létrehozásához szükséges. Ez a viselkedés jelenleg nem támogatott, és jelentős hatással van a HoloLens 2 teljesítményére.

Ennek a problémának az okai lehetnek az MSAA, a FOG vagy a utófeldolgozás engedélyezése. Győződjön meg arról, hogy az alacsony minőségű profil van kiválasztva, és alapértelmezettként van beállítva a Unityben. Ezt a Projektbeállítások *szerkesztése >... > gombra.*

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>A Remote Rendering API-t használó Unity-kód nem fordítja le

### <a name="use-debug-when-compiling-for-unity-editor"></a>Hibakeresés használata a Unity Editorhoz való fordításkor

*Váltsa a Unity-megoldás* buildtípusát **Hibakeresésre.** Amikor az ARR-t teszteli a Unity-szerkesztőben, a define csak hibakeresési buildek esetén `UNITY_EDITOR` érhető el. Vegye figyelembe, hogy ez nem kapcsolódik [](../quickstarts/deploy-to-hololens.md)az üzembe helyezett alkalmazásokhoz használt buildtípushoz, ahol a "Kiadás" buildeket érdemes előnyben részesíteni.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Unity-minták Fordítási hibák a HoloLens 2-hez való fordítása során

A HoloLens 2 Unity-mintái (rövid útmutató, ShowCaseApp, ..) fordítása során helytelen hibákat láthattunk. Visual Studio arra panaszkodik, hogy egyes fájlokat nem tud másolni, bár ott vannak. Ha ezzel a problémával ütközik:
* Távolítsa el az összes ideiglenes Unity-fájlt a projektből, és próbálkozzon újra. Ez azt jelenti, hogy zárja be a Unityt, törölje az *ideiglenes* kódtárat és *az obj* mappát a projektkönyvtárból, és töltse be/buildje újra a projektet.
* Győződjön meg arról, hogy a projektek egy viszonylag rövid útvonalú lemezen található könyvtárban találhatók, mivel a másolási lépés olykor problémákba fog belefutni a hosszú fájlnevekkel kapcsolatban.
* Ha ez nem segít, akkor lehet, hogy az MS Sense akadályozza a másolási lépést. Kivétel beállításához futtassa ezt a beállításjegyzék-parancsot a parancssorból (rendszergazdai jogosultság szükséges):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>A Unity-projektek arm64-buildei meghiúsulnak, mert AudioPluginMsHRTF.dll hiányzik

Az `AudioPluginMsHRTF.dll` Arm64-hez elérhető a *Windows Mixed Reality-csomaghoz* *(com.unity.xr.windowsmr.miami)* a 3.0.1-es verzióban. Győződjön meg arról, hogy a Unity-alkalmazáson keresztül telepítette a 3.0.1-es vagy újabb Csomagkezelő. A Unity menüsávjában lépjen a *Window > Csomagkezelő,* és keresse meg a *Windows Mixed Reality* csomagot.

## <a name="native-c-based-application-does-not-compile"></a>A natív C++ alapú alkalmazás nem fordítja le

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>"A kódtár nem található" hiba UWP-alkalmazáshoz vagy DLL-hez

A C++ NuGet-csomagban található egy fájl, amely meghatározza a használni kívánt `microsoft.azure.remoterendering.Cpp.targets` bináris ízt. A `UWP` azonosításához a fájlban található feltételek a következőt ellenőrzik: `ApplicationType == 'Windows Store'` . Ezért gondoskodni kell arról, hogy ez a típus be legyen állítva a projektben. Ez történik egy UWP-alkalmazás vagy dll létrehozásakor az Visual Studio projektvarázslójával.

## <a name="unstable-holograms"></a>Instabil hologramok

Ha úgy tűnik, hogy a renderelt objektumok a fejmozgással együtt mozognak, problémákba ütközhet a késői szakasz-reprodukálás (Late *Stage Reprojection,* LSR) során. Az ilyen helyzet megközelítésére vonatkozó útmutatásért tekintse meg a [késői](../overview/features/late-stage-reprojection.md) szakasz reprodukálás című szakaszát.

Az instabil hologramok (búsbolás, elkeserítés, jitter vagy ugró hologramok) másik oka lehet a gyenge hálózati kapcsolat, különösen az elégtelen hálózati sávszélesség vagy a túl nagy késés. A hálózati kapcsolat minőségének jó jelzője a [teljesítménystatisztika](../overview/features/performance-queries.md) `ServiceStatistics.VideoFramesReused` értéke. Az újrafelhasznált képkockák olyan helyzeteket jeleznek, amikor egy régi videókeretet újra kellett használni az ügyféloldalon, mert nem volt elérhető új videokeret – például csomagvesztés vagy a hálózati késés változása miatt. Ha `ServiceStatistics.VideoFramesReused` a gyakran nagyobb nullánál, az hálózati problémára utal.

Egy másik fontos érték a `ServiceStatistics.LatencyPoseToReceiveAvg` . Konzisztensen 100 ms alatt kell lennie. Ha magasabb értékeket lát, az azt jelezheti, hogy egy túl távoli adatközponthoz csatlakozik.

A lehetséges megoldások listájáért tekintse meg a hálózati [kapcsolatokra vonatkozó irányelveket.](../reference/network-requirements.md#guidelines-for-network-connectivity)

## <a name="z-fighting"></a>Z-éneklök

Bár az ARR [z-megoldási](../overview/features/z-fighting-mitigation.md)megoldási funkciót is kínál, a z-mitigation továbbra is használhatja a jelenetben. Ez az útmutató a fennmaradó problémák elhárítását tűzi ki célul.

### <a name="recommended-steps"></a>Javasolt lépések

Használja a következő munkafolyamatot a z-hiba elhárításához:

1. A jelenet tesztelése az ARR alapértelmezett beállításával (z-mitigation mitigation on)

1. A z-mitigation megoldás letiltása az [API-val](../overview/features/z-fighting-mitigation.md) 

1. A kamera közeli és távoli sík módosítása közelebbi tartományra

1. A jelenet hibaelhárítása a következő szakaszban

### <a name="investigating-remaining-z-fighting"></a>A z-fel való további vizsgálat vizsgálata

Ha a fenti lépések kimerültek, és a fennmaradó z-ökonomáliák nem fogadhatóak el, meg kell vizsgálni a z-feleset kiváltó okokat. Ahogy a [z-mitigation](../overview/features/z-fighting-mitigation.md)mitigation funkcióoldalán is említettük, a z-megoldásnak két fő oka van: a mélységi pontosság elvesztése a mélységi tartomány túlsó végén, és a felületei, amelyek koplaniárisan metszik egymást. A mélységi pontosságú veszteség matematikai esemény, és csak a fenti 3. lépéssel enyhíthető. Az egyenlútú felületek a forráseszköz hibájára utalnak, és jobban ki vannak javítva a forrásadatokban.

Az ARR rendelkezik egy olyan funkcióval, amely meghatározza, hogy a felületek elesnek-e: [Checkerboard highlighting](../overview/features/z-fighting-mitigation.md). Vizuálisan is meghatározhatja, hogy mi okozza a z-egyezést. Az alábbi első animáció egy példát mutat be a távolság mélységi pontosságú veszteségére, a második pedig szinte egysokos felületekre:

![Animáció a távolság mélységi pontosságú veszteségére.](./media/depth-precision-z-fighting.gif)  ![Az animáció egy közel egyenlikus felületre mutat példát.](./media/coplanar-z-fighting.gif)

Hasonlítsa össze ezeket a példákat a z-parancsokkal az ok meghatározásához, vagy ha szükséges, kövesse ezt a részletes munkafolyamatot:

1. Helyezze a kamerát a z-tengelyfelszín fölé, hogy közvetlenül a felszínre nézzen.
1. Lassan mozgatja a kamerát visszafelé, a felszíntől távolodva.
1. Ha a z-eloterdő mindig látható, a felületek tökéletesen egyenlök. 
1. Ha a z-anyag az idő nagy része látható, a felületek szinte teljesen el vannak különülve.
1. Ha a mélységi vizsgálat csak távolról látható, a mélységi pontosság hiánya az oka.

A koplani felületeknek számos különböző oka lehet:

* Egy objektumot az exportálási alkalmazás egy hiba vagy más munkafolyamat-megközelítés miatt duplikált.

    Ellenőrizze ezeket a problémákat a megfelelő alkalmazás- és alkalmazástámogatással.

* A felületek duplikálva vannak, és tükrözve jelennek meg az előlapi vagy a fordított arcot cullingot használó renderelőkben.

    A modell [átalakítással](../how-tos/conversion/model-conversion.md) történő importálása határozza meg a modell elsődleges oldalságát. A rendszer a kétoldalasságot használja alapértelmezettként. A felület vékony falként jelenik meg, mindkét oldalról fizikailag megfelelő megvilágítással. Az egyoldalasság utalhat a forráseszközben található jelzőkre, vagy explicit módon kényszerítheti a [modellátalakítás során.](../how-tos/conversion/model-conversion.md) Emellett, de opcionálisan az [egyoldalas](../overview/features/single-sided-rendering.md) mód beállítható "normál" üzemmódra.

* Az objektumok a forráseszközökben metszik egymást.

     Azok az objektumok, amelyek úgy vannak átalakítva, hogy egyes felületek átfedésben legyenek egymással, szintén z-elkedő objektumokat hoznak létre. Az ARR-be importált jelenet jelenetfája részeinek átalakítása is létrehozhatja ezt a problémát.

* A felületeket szándékosan úgy ékesítik, hogy érintéssel érje el őket, például a faliakon található szövegekkel vagy más szövegekkel.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Grafikus összetevők több pass-pass sztereotikus renderelés használatával natív C++ alkalmazásokban

Bizonyos esetekben a [**BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image) hívása után egyéni natív C++ alkalmazások aktiválhatnak illesztőprogramhibát, ha a helyi tartalmakhoz több passzos sztereotikus renderelési módot használ (a renderelést a bal és jobb szem részére külön-külön haladva). A hiba nem determinisztikus raszterizálási hibákat okoz, így a helyi tartalom egyes háromszögei vagy háromszögrészei véletlenszerűen eltűnnek. A teljesítmény javítása érdekében javasoljuk, hogy a helyi tartalmat modernebb, egy pass-beli sztereotikus renderelési technikával renderelheti, **például** a SV_RenderTargetArrayIndex.

## <a name="conversion-file-download-errors"></a>Átalakítási fájlletöltési hibák

Az átalakítási szolgáltatás hibákba ütközhet, amikor fájlokat tölt le a Blob Storage-ból a Windows és a szolgáltatás által előírt elérésiút-hosszkorlátok miatt. A blobtárolóban lévő fájl elérési útjai és fájlnevei nem haladhatják meg a 178 karaktert. Például egy `blobPrefix` `models/Assets` 13 karakterből áll:

`models/Assets/<any file or folder path greater than 164 characters will fail the conversion>`

Az átalakítási szolgáltatás a alatt megadott összes fájlt letölti, nem csak az `blobPrefix` átalakításhoz használt fájlokat. Ezekben az esetekben a problémákat okozó fájlok/mappák kevésbé nyilvánvalóak, ezért fontos ellenőrizni a tárfiókban található összes adatokat a `blobPrefix` alatt. A letöltött fájlokat az alábbi példabemenetek között láthatja.
``` json
{
  "settings": {
    "inputLocation": {
      "storageContainerUri": "https://contosostorage01.blob.core.windows.net/arrInput",
      "blobPrefix": "models/Assets",
      "relativeInputAssetPath": "myAsset.fbx"
    ...
  }
}
```

```
models
├───Assets
│   │   myAsset.fbx                 <- Asset
│   │
│   └───Textures
│   |       myTexture.png           <- Used in conversion
│   |
|   └───MyFiles
|          myOtherFile.txt          <- File also downloaded under blobPrefix      
|           
└───OtherFiles
        myReallyLongFileName.txt    <- Ignores files not under blobPrefix             
```
## <a name="next-steps"></a>Következő lépések

* [Rendszerkövetelmények](../overview/system-requirements.md)
* [A hálózatra vonatkozó követelmények](../reference/network-requirements.md)