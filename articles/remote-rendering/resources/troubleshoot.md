---
title: Hibaelhárítás
description: Az Azure távoli rendereléssel kapcsolatos hibaelhárítási információk
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4990f0d0a10709f2c1c5a17806020cd685f999fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593333"
---
# <a name="troubleshoot"></a>Hibaelhárítás

Ezen a lapon az Azure távoli renderelést akadályozó gyakori problémák és a megoldási módszerek találhatók.

## <a name="cant-link-storage-account-to-arr-account"></a>A tárfiókot nem lehet csatlakoztatni az ARR-fiókhoz

Néha a [Storage-fiók összekapcsolása](../how-tos/create-an-account.md#link-storage-accounts) során a távoli renderelési fiók nem szerepel a listáján. A probléma megoldásához nyissa meg a Azure Portal ARR-fiókját, és válassza az **identitás** elemet a bal oldali **Beállítások** csoportban. Győződjön **meg** arról, hogy az **állapot** beállítása be értékre van állítva.
![Unity frame Debugger](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Az ügyfél nem tud csatlakozni a kiszolgálóhoz

Győződjön meg arról, hogy a tűzfalak (az eszközön belül, az útválasztók stb.) nem blokkolja a [rendszerkövetelményekben](../overview/system-requirements.md#network-firewall)említett portokat.

## <a name="error-disconnected-videoformatnotavailable"></a>Hiba: " `Disconnected: VideoFormatNotAvailable` "

Győződjön meg arról, hogy a GPU támogatja a hardveres videó-dekódolást. Lásd: [fejlesztői számítógép](../overview/system-requirements.md#development-pc).

Ha két GPU-val rendelkező laptopon dolgozik, akkor előfordulhat, hogy az alapértelmezés szerint futó GPU nem biztosít hardveres videó-dekódolási funkciót. Ha igen, próbálja meg kényszeríteni az alkalmazást, hogy használja a többi GPU-t. Ez gyakran lehetséges a GPU illesztőprogram-beállításaiban.

## <a name="retrieve-sessionconversion-status-fails"></a>A munkamenet/átalakítás állapotának lekérése sikertelen

Ha túl gyakran küld REST API parancsokat, a kiszolgáló leszabályozását és a hibák visszaadását fogja eredményezni. A szabályozási esetben a http-állapotkód 429 ("túl sok kérés"). Szabályként a **következő hívások között 5-10 másodperces** késleltetésnek kell lennie.

Vegye figyelembe, hogy ez a korlát nem csupán a REST API hívásokra van hatással, amikor a hívás közvetlenül, de C#/C + +, például,, `Session.GetPropertiesAsync` `Session.RenewAsync` vagy `Frontend.GetAssetConversionStatusAsync` .

Ha kiszolgálóoldali szabályozást tapasztal, módosítsa a kódot úgy, hogy ritkábban hajtsa végre a hívásokat. A kiszolgáló percenként alaphelyzetbe állítja a szabályozási állapotot, így biztonságos a kód egy perc elteltével újra lefutni.

## <a name="h265-codec-not-available"></a>A H265-kodek nem érhető el

Két oka lehet annak, hogy a kiszolgáló elutasítja a hibával való kapcsolódást `codec not available` .

**Nincs telepítve a H265-kodek:**

Először győződjön meg arról, hogy a rendszerkövetelmények között a [szoftver](../overview/system-requirements.md#software) szakaszban említettek szerint telepíti a **HEVC video Extensions** szolgáltatást.

Ha továbbra is problémákba ütközik, győződjön meg arról, hogy a videokártya támogatja a H265, és hogy telepítve van a legújabb grafikus illesztőprogram. Tekintse meg a gyártó által megadott információk rendszerkövetelményeit a [fejlesztői számítógépekről](../overview/system-requirements.md#development-pc) szóló szakaszban.

**A kodek telepítve van, de nem használható:**

A probléma oka a DLL-ek helytelen biztonsági beállítása. Ez a probléma nem jelenik meg a H265-mel kódolt videók megtekintésére tett kísérlet során. A kodek újratelepítése nem oldja meg a problémát. Ehelyett hajtsa végre a következő lépéseket:

1. Nyisson meg egy **rendszergazdai jogosultságokkal rendelkező PowerShellt** , és futtassa a parancsot

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    A parancsnak a következőhöz hasonló módon kell kiadnia a `InstallLocation` kodeket:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. A mappa megnyitása a Windows Intézőben
1. Egy **x86** -os és egy **x64** -es almappanek kell lennie. Kattintson a jobb gombbal az egyik mappára, és válassza a **Tulajdonságok** lehetőséget.
    1. Válassza a **Biztonság** fület, és kattintson a **speciális** beállítások gombra.
    1. Kattintson a **tulajdonos** **módosítása** elemre.
    1. Írja be a **rendszergazdákat** a szövegmezőbe
    1. Kattintson **a Névellenőrzés** elemre, majd **az OK gombra** .
1. Ismételje meg a fenti lépéseket a másik mappára vonatkozóan
1. Ismételje meg a fenti lépéseket is az egyes DLL-fájlokon mindkét mappában. Összesen négy dll-fájlnak kell lennie.

Annak ellenőrzéséhez, hogy a beállítások helyesek-e, tegye a következőket a négy dll-fájlhoz:

1. Válassza a **tulajdonságok > biztonsági > szerkesztés** lehetőséget
1. Lépjen végig az összes **csoport/felhasználó** listáján, és ellenőrizze, hogy mindegyik rendelkezik-e az **olvasási & végrehajtásának** megfelelő készlettel (az **Engedélyezés** oszlopban jelölje be a jelölőnégyzetet)

## <a name="low-video-quality"></a>Alacsony minőségű videó

A videó minősége a hálózati minőség vagy a hiányzó H265-videó kodekkel is feltörhető.

* Tekintse meg a [hálózati problémák azonosításához](#unstable-holograms)szükséges lépéseket.
* Tekintse meg a legújabb grafikus illesztőprogram telepítéséhez [szükséges rendszerkövetelményeket](../overview/system-requirements.md#development-pc) .

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>A MRC-mel rögzített videó nem tükrözi az élő élmény minőségét

A HoloLens [vegyes valóság-rögzítéssel (MRC)](/windows/mixed-reality/mixed-reality-capture-for-developers)rögzíthet videót. Az eredményül kapott videó azonban rosszabb minőségben működik, mint az élő élmény, két okból:
* A videó frameráta 30 Hz-re van korlátozva, szemben a 60 Hz-es értékkel.
* A videó-lemezképek nem haladnak át a [késői szakasz újravetítésének](../overview/features/late-stage-reprojection.md) feldolgozásának lépésén, így a videó choppier válik.

Mindkettő a rögzítési technikában rejlő korlátozásokat is magában foglalhat.

## <a name="black-screen-after-successful-model-loading"></a>A sikeres modell betöltését követő fekete képernyő

Ha a renderelési futtatókörnyezethez csatlakozik, és sikeresen betöltötte a modellt, de ezt követően csak fekete képernyő jelenik meg, akkor ez néhány különböző okot is tartalmazhat.

Javasoljuk, hogy a részletes elemzés előtt tesztelje a következő dolgokat:

* Telepítve van a H265-kodek? Bár a H264 kodeknek tartaléknak kell lennie, láttuk azokat az eseteket, amelyekben ez a tartalék nem működött megfelelően. Tekintse meg a legújabb grafikus illesztőprogram telepítéséhez [szükséges rendszerkövetelményeket](../overview/system-requirements.md#development-pc) .
* Ha Unity-projektet használ, zárjuk be az egységet, törölje az ideiglenes *könyvtárat* és az *obj* -mappákat a projekt könyvtárában, majd töltse be/hozza létre újra a projektet. Bizonyos esetekben a gyorsítótárazott adat miatt a minta nem működik megfelelően a nyilvánvaló ok nélkül.

Ha ez a két lépés nem segített, meg kell állapítani, hogy az ügyfél fogad-e képkockákat. Ez programozott módon kérdezhető le a [kiszolgálóoldali teljesítmény-lekérdezések](../overview/features/performance-queries.md) című fejezetben leírtak szerint. A `FrameStatistics struct` tartalmaz egy tagot, amely azt jelzi, hogy hány képkockát fogadtak. Ha ez a szám nagyobb, mint 0, és az idő múlásával növekszik, az ügyfél megkapja a tényleges képkockákat a kiszolgálóról. Ennek következtében az ügyfél oldalán problémának kell lennie.

### <a name="common-client-side-issues"></a>Gyakori ügyféloldali problémák

**A modell meghaladja a kiválasztott virtuális gép korlátait, pontosabban a sokszögek maximális számát:**

Megtekintheti az adott [kiszolgáló méretének korlátozásait](../reference/limits.md#overall-number-of-polygons).

**A modell nem a kamera csonkakúpot belül található:**

Sok esetben a modell helyesen jelenik meg, de a kamera csonkakúpot kívül található. Ennek gyakori oka az, hogy a modellt egy távoli középpontú kimutatással exportálták, így azt a kamera távoli nyírási síkja vágja le. Segít lekérdezni a modell határoló mezőjét programozott módon, és megjelenítheti a négyzetet az egységgel, vagy kinyomtathatja az értékeket a hibakeresési naplóba.

Továbbá a konverziós folyamat létrehoz egy [kimeneti JSON-fájlt](../how-tos/conversion/get-information.md) a konvertált modellel együtt. A modell pozicionálási problémáinak hibakereséséhez érdemes megtekinteni a `boundingBox` [outputStatistics szakaszban](../how-tos/conversion/get-information.md#the-outputstatistics-section)található bejegyzést:

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

A határolókeret a következőként van leírva: a `min` és a `max` pozíciója 3D térben, méterben. Így a 1000,0-es koordináta azt jelenti, hogy a forrás 1 kilométer távolságra van.

Ennek a határolókeretnak két problémája lehet, amely láthatatlan geometriát eredményez:
* **A mező távolról is elvégezhető**, így az objektum teljes kivágása a sík kivágása miatt történik. Ebben az esetben a következőhöz `boundingBox` hasonló értékek jelennek meg: az `min = [-2000, -5,-5], max = [-1990, 5,5]` x tengely nagy eltolásának használata példaként. Az ilyen típusú probléma megoldásához engedélyezze a `recenterToOrigin` [modell átalakítási konfigurációjában](../how-tos/conversion/configure-model-conversion.md)a beállítást.
* **A Box lehet középre igazított, de a nagyságrenddel túl nagy**. Ez azt jelenti, hogy bár a kamera a modell közepén indul el, a geometriája minden irányban kikerül. Ebben az esetben a tipikus `boundingBox` értékek a következőképpen néznek ki: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . Az ilyen típusú probléma oka általában az egység méretezési eltérése. A kiegyenlítéshez az átalakítás során meg kell adnia egy [skálázási értéket](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) , vagy a megfelelő egységekkel kell megadnia a forrás modellt. A skálázás a legfelső szintű csomópontra is alkalmazható a modell futásidőben történő betöltésekor.

**Az Unity Render folyamat nem tartalmazza a renderelési horgokat:**

Az Azure Remote rendering összekapcsolja az egység renderelési folyamatát, hogy elvégezze a képkockák összeállítását a videóval, és hogy elvégezze az újravetítést. Ha szeretné ellenőrizni, hogy ezek a hookok léteznek-e, nyissa meg a menüt *:::no-loc text="Window > Analysis > Frame debugger":::* . Engedélyezze, és győződjön meg arról, hogy a folyamat két bejegyzéssel rendelkezik `HolographicRemotingCallbackPass` :

![Egység renderelési folyamata](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>A Pepita minta a modell betöltését követően jelenik meg

Ha a megjelenített rendszerkép így néz ki: a ![ képernyőképen a fekete-fehér négyzetek rácsa látható az eszközök menüvel.](../reference/media/checkerboard.png)
Ezután a megjelenítő [megkeresi a szabványos konfiguráció méretére vonatkozó sokszög-korlátokat](../reference/vm-sizes.md). A mérsékléshez váltson a **prémium** szintű konfiguráció méretére, vagy csökkentse a látható sokszögek számát.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>A megjelenített rendszerkép (Unity)

Ügyeljen arra, hogy kövesse az [Unity oktatóanyagot: a távoli modellek pontosan megtekinthetők](../tutorials/unity/view-remote-models/view-remote-models.md) . A lefelé irányuló kép azt jelzi, hogy az egységnek a képernyőn kívüli megjelenítési cél létrehozásához kell tartoznia. Ez a viselkedés jelenleg nem támogatott, és óriási teljesítménybeli hatást eredményez a 2. HoloLens.

A probléma oka a MSAA, a HDR vagy a post Processing engedélyezése lehet. Győződjön meg arról, hogy az alacsony színvonalú profil van kiválasztva, és alapértelmezettként van beállítva az egységben. Ehhez lépjen a *> projekt beállításainak szerkesztése... > minőség* gombra.

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>A távoli renderelési API-t használó Unity kód nem fordítható le

### <a name="use-debug-when-compiling-for-unity-editor"></a>Az Unity Editor fordításakor használja a hibakeresést

Állítsa át az Unity megoldás *Build típusát* a **hibakereséshez**. A Unity Editorban az ARR tesztelésekor a define parancs `UNITY_EDITOR` csak a "debug" buildek esetében érhető el. Vegye figyelembe, hogy ez nem kapcsolódik a [központilag telepített alkalmazásokhoz](../quickstarts/deploy-to-hololens.md)használt Build típushoz, ahol a "Release" buildeket érdemes előnyben részesíteni.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Hibák fordítása a 2. HoloLens Unity-minták fordításakor

Hamis hibák történtek, amikor az egységbeli mintákat (gyors üzembe helyezési útmutató, ShowCaseApp,..) próbálta lefordítani a 2. HoloLens. A Visual Studio arra panaszkodik, hogy nem tud fájlokat másolni, noha ott vannak. Ha a probléma a következő:
* Távolítsa el az összes ideiglenes Unity-fájlt a projektből, és próbálkozzon újra. Az egység bezárásához törölje az ideiglenes *könyvtárat* és az *obj* -mappákat a projekt könyvtárában, majd töltse be vagy hozza létre újra a projektet.
* Győződjön meg arról, hogy a projektek a lemez egy olyan könyvtárában találhatók, amely ésszerűen rövid elérési úttal rendelkezik, mert a másolási lépés néha úgy tűnik, hogy hosszú fájlnevekkel problémákba ütköznek.
* Ha ez nem segít, előfordulhat, hogy az MS Sense ütközik a másolási lépéssel. Kivétel beállításához futtassa ezt a beállításjegyzék-parancsot a parancssorból (rendszergazdai jogosultságok szükségesek):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>Az Unity-projektek Arm64-buildei sikertelenek, mert AudioPluginMsHRTF.dll hiányzik

A `AudioPluginMsHRTF.dll` for Arm64 hozzá lett adva a *Windows vegyes valósághoz* készült csomagjához *(com. Unity. XR. windowsmr. Metro)* a 3.0.1-es verzióban. Győződjön meg arról, hogy a Unity csomagkezelő segítségével telepítette az 3.0.1-es vagy újabb verziót. A Unity menüsávban navigáljon az *ablak > csomagkezelő* elemre, és keresse meg a *Windows vegyes valóság* csomagot.

## <a name="native-c-based-application-does-not-compile"></a>A natív C++-alapú alkalmazás nem fordítható le

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>"A könyvtár nem található" hiba a UWP-alkalmazás vagy-DLL esetében

A C++ NuGet csomagon belül van egy fájl, amely `microsoft.azure.remoterendering.Cpp.targets` meghatározza, hogy a bináris íz milyen típusú legyen. Az azonosításához a `UWP` fájlban található feltételeket kell megkeresni `ApplicationType == 'Windows Store'` . Ezért biztosítani kell, hogy ez a típus be legyen állítva a projektben. Ennek a helyzetnek kell lennie, amikor UWP-alkalmazást vagy DLL-t hoz létre a Visual Studio Project varázslójával.

## <a name="unstable-holograms"></a>Instabil Hologramok

Ha úgy tűnik, hogy a megjelenített objektumok a fej mozgásával együtt mozognak, előfordulhat, hogy a *késői fázis-újravetítéssel* (LSR) kapcsolatos problémák merülhetnek fel. Az ilyen helyzetek megközelítésével kapcsolatos útmutatásért tekintse meg a [késői fázisok újravetítésének](../overview/features/late-stage-reprojection.md) szakaszát.

Az instabil Hologramok (ingadozó, hajlítási, vibrálás vagy jumping Hologramok) egy másik oka lehet gyenge hálózati kapcsolat, különösen a nem megfelelő hálózati sávszélesség vagy túl nagy késés. A hálózati kapcsolatok minőségének jó mutatója a [teljesítmény statisztika](../overview/features/performance-queries.md) értéke `ServiceStatistics.VideoFramesReused` . Az újrafelhasznált keretek olyan helyzeteket jeleznek, amikor egy régi videó-keretet újra fel kell használni az ügyféloldali oldalon, mert nem volt elérhető új videó keret – például a csomagok elvesztése miatt vagy a hálózati késésben lévő változatok miatt. Ha a `ServiceStatistics.VideoFramesReused` értéke gyakran nagyobb nullánál, ez hálózati problémát jelez.

Egy másik érték a következő: `ServiceStatistics.LatencyPoseToReceiveAvg` . Következetesen 100 MS alá kell esnie. A nagyobb értékek megjelenítésével jelezheti, hogy egy túl távol lévő adatközponthoz csatlakozik.

A lehetséges enyhítések listáját a [hálózati kapcsolatra vonatkozó irányelvek](../reference/network-requirements.md#guidelines-for-network-connectivity)című részben tekintheti meg.

## <a name="z-fighting"></a>Z – küzdelem

Míg az ARR a [z-elleni küzdelemre szolgáló funkciók enyhítését](../overview/features/z-fighting-mitigation.md)kínálja, a z-harcok továbbra is megjelennek a jelenetben. Ez az útmutató a fennmaradó problémák elhárítását célozza meg.

### <a name="recommended-steps"></a>Javasolt lépések

A következő munkafolyamat használatával csökkentheti a z-elleni küzdelmet:

1. Tesztelje a jelenetet az alapértelmezett beállításokkal az ARR (z-Fighting mérséklés)

1. A z-harcok enyhítésének letiltása az [API](../overview/features/z-fighting-mitigation.md) -n keresztül 

1. A kamera közelében és távolabbi sík közötti váltás egy szorosabb tartományba

1. A jelenet hibáinak megoldása a következő szakasz használatával

### <a name="investigating-remaining-z-fighting"></a>A fennmaradó z-harcok kivizsgálása

Ha a fenti lépések kimerültek, és a fennmaradó z-harcok elfogadhatatlanok, a z-harcok mögöttes okot meg kell vizsgálni. Ahogy az a [z-Fighting enyhítő funkció oldalán](../overview/features/z-fighting-mitigation.md)is látható, két fő oka van a z-harcok esetében: mélységi pontosság a mélységi tartomány végén, valamint az egymást keresztező felületek. A mélységi pontosság elvesztése matematikai eshetőségre, és csak a fenti 3. lépéssel enyhíthető. Az egyhelyes felületek a forrás-eszköz hibáját jelzik, és a forrásadatok jobb rögzítését mutatják.

Az ARR tartalmaz egy funkciót, amely meghatározza, hogy a felületek megadhatják-e a z-Fight: [Pepita kiemelés](../overview/features/z-fighting-mitigation.md). Azt is megteheti, hogy vizuálisan mi okozza a z-harcok megjelenését. A következő első animáció egy példát mutat be a távolság pontosságára, a második pedig egy példát mutat be a közel álló felületek közül:

![Az animáció egy példát mutat be a távolság pontosságának csökkenésére.](./media/depth-precision-z-fighting.gif)  ![Az animáció egy példát mutat be közel álló felületek számára.](./media/coplanar-z-fighting.gif)

Hasonlítsa össze ezeket a példákat a z-küzdelemmel az ok megállapításához, vagy opcionálisan kövesse ezt a lépésenkénti munkafolyamatot:

1. Helyezze a kamerát a z-harci felületek fölé úgy, hogy közvetlenül a felületre nézzen.
1. Lassan helyezze át a kamerát visszafelé, a felületektől távolabb.
1. Ha a z-harcok egész idő alatt láthatók, a felületek tökéletesen összetartoznak. 
1. Ha a z-harcok az idő nagy részében láthatók, a felületek közel vannak egymáshoz.
1. Ha a z-harcok csak messze láthatók, az ok a mélységi pontosság hiánya.

Az egymáshoz tartozó felületek számos különböző oka lehet:

* Egy objektumot duplikált egy hiba vagy eltérő munkafolyamat-megközelítés miatt az exportálási alkalmazás.

    Ezeket a problémákat a megfelelő alkalmazás-és alkalmazás-támogatással vizsgálja meg.

* A felületek duplikálva vannak, és úgy lettek tükrözve, hogy kétoldalas megjelenítéssel jelenjenek meg.

    Az Importálás a modell [átalakításán](../how-tos/conversion/model-conversion.md) keresztül meghatározza a modell fő oldalát. A kettős oldalú érték alapértelmezettként lesz feltételezve. A felületet vékony falként jeleníti meg a rendszer, és mindkét oldalról fizikailag helyes megvilágítás is van. Az egyoldalas kifejezéseket a forrásként szolgáló jelzők, vagy kifejezetten a [modell konvertálása](../how-tos/conversion/model-conversion.md)során lehet kényszeríteni. Emellett, de opcionálisan az [egyoldalas mód](../overview/features/single-sided-rendering.md) is beállítható a "NORMAL" értékre.

* Az objektumok a forrás eszközein metszik egymást.

     Az átalakított objektumok a felületek egy részének átfedésben vannak. A jelenet faszerkezetének az ARR-ben importált jelenetében lévő részeinek átalakításával is létrehozhatja ezt a problémát.

* A felületek célirányosan megtalálhatók, például matricák vagy szövegek a falakon.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Multi-pass sztereó renderelést használó grafikus összetevők natív C++-alkalmazásokban

Bizonyos esetekben a [**BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image) meghívása után olyan egyéni natív C++-alkalmazások, amelyek többcsatornás sztereó renderelési módot használnak a helyi tartalomhoz (a bal és a jobb oldali megjelenítésre külön-külön haladnak), az illesztőprogram hibájának meghívása után. A hiba a nem determinisztikus raszterizálási-hibákat eredményezi, ami a helyi tartalom egyes háromszögeit vagy háromszögeit véletlenszerűen eltűnnek. A teljesítménnyel kapcsolatos okokból ajánlott a helyi tartalom megjelenítése egy modern, Egylépéses sztereó renderelési technikával, például **SV_RenderTargetArrayIndex** használatával.

## <a name="next-steps"></a>Következő lépések

* [Rendszerkövetelmények](../overview/system-requirements.md)
* [A hálózatra vonatkozó követelmények](../reference/network-requirements.md)