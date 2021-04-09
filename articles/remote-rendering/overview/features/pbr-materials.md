---
title: PBR-anyagok
description: A PBR anyag típusát írja le.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: e9908c106e57801cb1b7def8b3353a983cc97de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "99591939"
---
# <a name="pbr-materials"></a>PBR-anyagok

A *pbr-anyagok* az Azure távoli renderelés egyik támogatott anyag- [típusa](../../concepts/materials.md) . Ezeket olyan [Rácsvonalak](../../concepts/meshes.md) használják, amelyeknek reális világítást kell kapniuk.

A PBR a **P** hysically **B** ased **R**-megsértését jelöli, és azt jelenti, hogy az anyag fizikailag kézenfekvő módon mutatja be egy felület vizuális tulajdonságait, így a reális eredmények az összes megvilágítás esetén lehetségesek. A legtöbb modern játék-végrehajtó eszköz támogatja a PBR-anyagokat, mivel ezek a valós idejű rendereléshez szükséges valós forgatókönyvek legjobb közelítését jelentik.

![Az ARR által megjelenített Helmet glTF-minta modell](media/helmet.png)

A PBR-anyagok nem univerzális megoldások, bár. A látószögtől függően eltérő színű anyagok szerepelnek. Például bizonyos hálók vagy autók. Az ilyen típusú anyagokat nem a szabványos PBR-modell kezeli, és az Azure távoli renderelés jelenleg nem támogatja azokat. Ez magában foglalja a PBR-bővítményeket, például a *vékony fóliát* (többrétegű felületek) és a *világos réteget* (az autó festéséhez).

## <a name="common-material-properties"></a>Gyakori anyagok tulajdonságai

Ezek a tulajdonságok az összes anyagnál közösek:

* **albedoColor:** Ez a szín más színekkel, például a *albedoMap* vagy a *:::no-loc text="vertex "::: színekkel* együtt van megszorozva. Ha az *átlátszóság* engedélyezve van egy adott anyagon, az alfa-csatorna az opacitás beállítására szolgál, amely `1` teljes mértékben átlátszatlan és `0` teljes átláthatóságot jelent. Az alapértelmezett érték fehér.

  > [!NOTE]
  > Ha a PBR-anyagok teljesen transzparensek, például tökéletesen tiszta üvegként, továbbra is tükrözik a környezetet. A reflexióban a világos foltok, például a nap még mindig láthatók. Ez különbözik a [színanyagoktól](color-materials.md).

* **albedoMap:** [2D-textúra](../../concepts/textures.md) a képpont albedó értékekhez.

* **alphaClipEnabled** és **alphaClipThreshold:** ha a *alphaClipEnabled* értéke igaz, az összes képpont, ahol a albedó alfa-értéke alacsonyabb, mint a *alphaClipThreshold* , nem lesz kirajzolva. Az Alpha-levágást még az átlátszóság engedélyezése nélkül is használhatja, és a renderelés sokkal gyorsabb. Az alfa-levágott anyagok továbbra is lassabbak, mint a teljes mértékben átlátszatlan anyagok, mégis. Alapértelmezés szerint az Alpha levágása le van tiltva.

* **textureCoordinateScale** és **textureCoordinateOffset:** a skála az UV-textúra koordinátáiba van szorozva, az eltolás hozzá lesz adva. A textúrák nyújtására és átváltására használható. Az alapértelmezett skála (1, 1) és az eltolás (0, 0).

* **useVertexColor:** Ha a háló :::no-loc text="vertex"::: színeket tartalmaz, és ez a beállítás engedélyezve van, a rácsvonalak :::no-loc text="vertex"::: színe megszorozva a *AlbedoColor* és a *albedoMap*. Alapértelmezés szerint a *useVertexColor* le van tiltva.

* **isDoubleSided:** Ha a kétoldalas megjelenítés értéke TRUE (igaz), akkor az ezzel az anyaggal rendelkező háromszögek akkor is jelennek meg, ha a kamera a háttérben néz. A PBR-anyagok megvilágítása is megfelelően van kiszámítva a vissza arcok számára. Alapértelmezés szerint ez a beállítás le van tiltva. Lásd még: [ :::no-loc text="Single-sided"::: renderelés](single-sided-rendering.md).

* **TransparencyWritesDepth:** Ha az TransparencyWritesDepth jelző be van állítva az anyagra, és az anyag átlátszó, akkor az ezt az anyagot használó objektumok is hozzájárulnak a végső mélységi pufferhez. A következő szakaszban tekintse meg a PBR Material Flag *transzparensjét* . A funkció engedélyezése akkor ajánlott, ha a használati esetnek a teljes transzparens jelenetek [kivetítésének valószínűbb késői fázisára](late-stage-reprojection.md) van szüksége. Vegyes átlátszatlan/transzparens jelenetek esetén ez a beállítás nem valószínűsíthető újravetítési viselkedést vagy újravetítési összetevőket vezethet be. Ezért az általános használati eset alapértelmezett és ajánlott beállítása a jelző letiltása. A rendszer a kamerához legközelebb eső objektum képpont mélységi rétegében található írásos mélységi értékeket veszi figyelembe.

* **FresnelEffect:** Az anyag jelzője lehetővé teszi, hogy az adalékanyag [Fresnel hatással](../../overview/features/fresnel-effect.md) legyen a megfelelő anyagra. A hatás megjelenését a következő Fresnel paraméterek szabályozzák. 

* **FresnelEffectColor:** Az anyaghoz használt Fresnel-szín. Csak akkor fontos, ha az Fresnel-effektus bit be van állítva ezen az anyagon (lásd fent). Ez a tulajdonság a Fresnel ragyogás alapszínét szabályozza (lásd a [Fresnel-effektust](../../overview/features/fresnel-effect.md) a teljes magyarázathoz). Jelenleg csak az RGB-csatorna értékei fontosak, és az alfa-érték figyelmen kívül lesz hagyva.

* **FresnelEffectExponent:** Az anyaghoz használt Fresnel kitevő. Csak akkor fontos, ha az Fresnel-effektus bit be van állítva ezen az anyagon (lásd fent). Ez a tulajdonság határozza meg a Fresnel ragyogásának eloszlását. A 0,01-as minimális érték a teljes objektumon belüli oldalpárt okoz. A 10,0-as maximális érték a ragyogást csak a leginkább kipróbálható élekre korlátozza.

## <a name="pbr-material-properties"></a>PBR-anyagok tulajdonságai

A fizikailag vezérelt renderelés lényege, hogy a *BaseColor*, a *fémesség* és a *érdesség* tulajdonságait használja a valós anyagok széles skálájának emulálása érdekében. A PBR részletes leírása meghaladja a jelen cikk hatókörét. További információ a PBR-ról: [egyéb források](http://www.pbr-book.org). A következő tulajdonságok a PBR-anyagokra vonatkoznak:

* **baseColor:** A PBR-anyagokban a *albedó színét* az *alapszínnek* nevezzük. Az Azure távoli renderelés során a *albedó Color* tulajdonság már szerepel a közös anyag tulajdonságai között, így nincs további alapszín tulajdonság.

* **érdesség** és **roughnessMap:** a durvaség meghatározza, hogy a felület milyen durva vagy egyenletes legyen. A durva felületek a sima felületeknél több irányba szórják a fényt, így a reflexiók elmosódottak, nem pedig élesek. Az érték tartományból származik `0.0` `1.0` . Ha `roughness` egyenlő `0.0` , a reflexiók élesek lesznek. Ha `roughness` egyenlő `0.5` , a reflexiók elmosódottak lesznek.

  Ha a rendszer a durva és a durva leképezést is megadja, a végső érték a kettő szorzata lesz.

* **fémmegmunkálás** és **metalnessMap:** a fizikában ez a tulajdonság azt jelzi, hogy egy felület vezetőképes vagy dielektromos állapotú-e. A vezetőképes anyagok eltérő fényvisszaverő tulajdonságokkal rendelkeznek, és a albedó szín nélkül általában nem tükröznek. A PBR-anyagokban ez a tulajdonság befolyásolja, hogy a felület mekkora mértékben tükrözi a környező környezetet. Az értékek a és a közötti tartományba esnek `0.0` `1.0` . A fémesség esetén `0.0` a albedó színe teljesen látható, az anyag pedig műanyag-vagy kerámiákhoz hasonlít. Ha a fémesség `0.5` , úgy tűnik, hogy a festett fém. A fémesség esetén `1.0` a felület majdnem teljesen elveszti a albedó színét, és csak a környéket tükrözi. Például, ha `metalness` az, `1.0` és `roughness` `0.0` egy olyan felület, mint a valós tükrözés.

  Ha a rendszer egy fémes értéket és egy fémes térképet is biztosít, akkor a végső érték a kettő szorzata lesz.

  ![Különböző fémekkel és érdes értékekkel megjelenített gömbök](./media/metalness-roughness.png)

  A fenti képen a jobb alsó sarokban lévő gömb úgy néz ki, mint egy valódi fém, a bal alsó rész a kerámia vagy a műanyag elemre hasonlít. A albedó színe a fizikai tulajdonságok alapján is változik. A növekvő durvasággal az anyag elveszíti a tükröződés élességét.

* **normalMap:** A részletes részletességi adatok szimulálása érdekében egy [normál leképezést](https://en.wikipedia.org/wiki/Normal_mapping) is meg lehet adni.

* **occlusionMap** és **aoScale:** a [környezeti elzáródások](https://en.wikipedia.org/wiki/Ambient_occlusion) olyan objektumokat tesznek elérhetővé, amelyekkel az árnyékok a bezárt területekhez hozzáadhatók. Az értéknek a-tól a- `0.0` ig történő elzáródása `1.0` , ahol a `0.0` sötétség (bezárt) és az `1.0` azt jelenti, hogy nincsenek elzáródások. Ha egy 2D-textúra elzáródási térképként van megadva, a hatás engedélyezve van, és a *aoScale* szorzóként működik.

  ![Környezeti elzáródással és anélkül megjelenített objektum](./media/boom-box-ao2.gif)

* **transzparens:** A PBR-anyagok esetében csak egyetlen áttetszőségi beállítás van: engedélyezve van vagy nem. Az opacitást a albedó színe alfa-csatornája határozza meg. Ha engedélyezve van, a rendszer összetettebb renderelési folyamatot hív meg félig átlátszó felületek rajzolásához. Az Azure-alapú távoli renderelés igaz [sorrendű, független átlátszóságot](https://en.wikipedia.org/wiki/Order-independent_transparency) (OIT) valósít meg.

  Az átlátszó geometria költséges a megjelenítéshez. Ha csak a felületen lévő lyukakra van szüksége, például egy fa leveleinél, érdemes inkább alfa-nyírást használni.

  ![A fenti képen a nullával megjelenített elemek teljes átláthatóságára vonatkozó figyelmeztetést jelenítenek meg ](./media/transparency.png) , a jobb szélső gömb pedig teljesen átlátszó, de a reflexió továbbra is látható.

  > [!IMPORTANT]
  > Ha bármilyen anyagot át kell váltani átlátszatlanról áttetszőre futásidőben, a megjelenítőnek a *TileBasedComposition* [renderelési módot](../../concepts/rendering-modes.md)kell használnia. Ez a korlátozás nem vonatkozik azokra az anyagokra, amelyeket transzparens anyagként konvertálnak a kezdéshez.

## <a name="technical-details"></a>Technikai részletek

Az Azure Remote rendering a GGX NDF, a Schlick Fresnel és a GGX Smith korrelált láthatósági kifejezéssel rendelkező BRDF használja Cook-Torrance a Lambert Diffúz kifejezéssel. Ez a modell jelenleg a de facto iparági szabvány. További részletes részletekért tekintse meg a következő cikket: [fizikailag alapú renderelés – Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 A *ragyogás* pbr modell az Azure Remote rendering-ben használt *fémmentes pbr-* modell alternatívája. Ez a modell az anyagok szélesebb körét reprezentálhatja. Azonban ez drágább, és általában nem működik megfelelően a valós idejű esetekben.
Nem mindig lehetséges a *ragyogás* -ről a *fémekre* váltani, mivel olyan *(diffúziós, visszaverődési)* érték párokat, amelyek nem alakíthatók át *(BaseColor, fémes)*. A másik irányba történő átalakítás egyszerűbb és pontosabb, mivel az összes *(BaseColor, fémes)* párok jól meghatározott *(diffúz, fényvisszaverődési)* pároknak felelnek meg.

## <a name="api-documentation"></a>API-dokumentáció

* [C# PbrMaterial osztály](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection. CreateMaterial ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ PbrMaterial osztály](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection:: CreateMaterial ()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Következő lépések

* [Színes anyagok](color-materials.md)
* [Textúrák](../../concepts/textures.md)
* [Hálók](../../concepts/meshes.md)