---
title: Színes anyagok
description: A színanyag típusát írja le.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: fb26a669229ac140aba262032f8ce84ef9f37727
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593383"
---
# <a name="color-materials"></a>Színes anyagok

A *színanyagok* az Azure távoli renderelés egyik támogatott [anyag-típusa](../../concepts/materials.md) . Ezeket a [hálókat olyan rácsvonalak](../../concepts/meshes.md) használják, amelyek nem kapnak semmiféle megvilágítást, hanem minden esetben teljes fényerővel rendelkeznek. Ez lehet a "ragyogó" anyagok, például az autós irányítópultok, a villanykörték, vagy a már meglévő, statikus megvilágítás, például a [photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry)-on keresztül beszerzett modellek.

A színanyagok hatékonyabbak, mint a [pbr-anyagok](pbr-materials.md) , az egyszerűbb árnyékolási modelljük miatt. Különböző átlátszósági módokat is támogatnak.

## <a name="common-material-properties"></a>Gyakori anyagok tulajdonságai

Ezek a tulajdonságok az összes anyagnál közösek:

* **albedoColor:** Ez a szín más színekkel, például a *albedoMap* vagy a *:::no-loc text="vertex"::: színekkel* együtt van megszorozva. Ha az *átlátszóság* engedélyezve van egy adott anyagon, az alfa-csatorna az opacitás beállítására szolgál, amely `1` teljes mértékben átlátszatlan és `0` teljes átláthatóságot jelent. Az alapértelmezett érték fehér.

  > [!NOTE]
  > Mivel a színanyagok nem tükrözik a környezetet, a teljes átlátszó színanyag láthatatlanná válik. Ez a pbr- [anyagok](pbr-materials.md)esetében különbözik.

* **albedoMap:** [2D-textúra](../../concepts/textures.md) a képpont albedó értékekhez.

* **alphaClipEnabled** és **alphaClipThreshold:** ha a *alphaClipEnabled* értéke igaz, az összes képpont, ahol a albedó alfa-értéke alacsonyabb, mint a *alphaClipThreshold* , nem lesz kirajzolva. Az Alpha-levágást még az átlátszóság engedélyezése nélkül is használhatja, és a renderelés sokkal gyorsabb. Az alfa-levágott anyagok továbbra is lassabbak, mint a teljes mértékben átlátszatlan anyagok, mégis. Alapértelmezés szerint az Alpha levágása le van tiltva.

* **textureCoordinateScale** és **textureCoordinateOffset:** a skála az UV-textúra koordinátáiba van szorozva, az eltolás hozzá lesz adva. A textúrák nyújtására és átváltására használható. Az alapértelmezett skála (1, 1) és az eltolás (0, 0).

* **useVertexColor:** Ha a háló :::no-loc text="vertex"::: színeket tartalmaz, és ez a beállítás engedélyezve van, a rácsvonalak :::no-loc text="vertex"::: színe megszorozva a *AlbedoColor* és a *albedoMap*. Alapértelmezés szerint a *useVertexColor* le van tiltva.

* **isDoubleSided:** Ha a kétoldalas megjelenítés értéke TRUE (igaz), akkor az ezzel az anyaggal rendelkező háromszögek akkor is jelennek meg, ha a kamera a háttérben néz. Alapértelmezés szerint ez a beállítás le van tiltva. Lásd még: [ :::no-loc text="Single-sided"::: renderelés](single-sided-rendering.md).

* **TransparencyWritesDepth:** Ha az TransparencyWritesDepth jelző be van állítva az anyagra, és az anyag átlátszó, akkor az ezt az anyagot használó objektumok is hozzájárulnak a végső mélységi pufferhez. A következő szakaszban tekintse meg a Color Material tulajdonság *transparencyMode* . A funkció engedélyezése akkor ajánlott, ha a használati esetnek a teljes transzparens jelenetek [kivetítésének valószínűbb késői fázisára](late-stage-reprojection.md) van szüksége. Vegyes átlátszatlan/transzparens jelenetek esetén ez a beállítás nem valószínűsíthető újravetítési viselkedést vagy újravetítési összetevőket vezethet be. Ezért az általános használati eset alapértelmezett és ajánlott beállítása a jelző letiltása. A rendszer a kamerához legközelebb eső objektum képpont mélységi rétegében található írásos mélységi értékeket veszi figyelembe.

* **FresnelEffect:** Az anyag jelzője lehetővé teszi, hogy az adalékanyag [Fresnel hatással](../../overview/features/fresnel-effect.md) legyen a megfelelő anyagra. A hatás megjelenését a következő Fresnel paraméterek szabályozzák. 

* **FresnelEffectColor:** Az anyaghoz használt Fresnel-szín. Csak akkor fontos, ha az Fresnel-effektus bit be van állítva ezen az anyagon (lásd fent). Ez a tulajdonság a Fresnel ragyogás alapszínét szabályozza (lásd a [Fresnel-effektust](../../overview/features/fresnel-effect.md) a teljes magyarázathoz). Jelenleg csak az RGB-csatorna értékei fontosak, és az alfa-érték figyelmen kívül lesz hagyva.

* **FresnelEffectExponent:** Az anyaghoz használt Fresnel kitevő. Csak akkor fontos, ha az Fresnel-effektus bit be van állítva ezen az anyagon (lásd fent). Ez a tulajdonság határozza meg a Fresnel ragyogásának eloszlását. A 0,01-as minimális érték a teljes objektumon belüli oldalpárt okoz. A 10,0-as maximális érték a ragyogást csak a leginkább kipróbálható élekre korlátozza.

## <a name="color-material-properties"></a>Színanyag tulajdonságai

A következő tulajdonságok a színanyagokra jellemzőek:

* **vertexMix:** Ez az érték `0` és `1` annak megadása, hogy a :::no-loc text="vertex"::: [Rácsvonalak](../../concepts/meshes.md) színe milyen mértékben járul hozzá a végső színhez. Az alapértelmezett 1 értéknél a szín a :::no-loc text="vertex"::: albedó teljes színének szorzata. A 0 értékkel a :::no-loc text="vertex"::: színek figyelmen kívül lesznek hagyva.

* **transparencyMode:** A [pbr-anyagokkal](pbr-materials.md)ellentétben a színanyagok különbséget tesznek a különböző áttetszőségi módok között:

  1. **Átlátszatlan:** Az alapértelmezett mód letiltja az átlátszóságot. Az Alpha-levágás továbbra is lehetséges, de érdemes előnyben részesíteni, ha szükséges.
  
  1. **AlphaBlended:** Ez a mód hasonló a PBR-anyagok átlátszósági módjához. A rendszer az olyan áttekinthető anyagokhoz használható, mint például az üveg.

  1. **Adalékanyag:** Ez a legegyszerűbb és leghatékonyabb átlátszósági mód. Az anyag hozzájárulása hozzáadódik a megjelenített képhez. Ezzel a móddal szimulálhatja a fénylő (de még transzparens) objektumokat, például a fontos objektumok kiemeléséhez használt jelölőket.

## <a name="api-documentation"></a>API-dokumentáció

* [C# ColorMaterial osztály](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C# RenderingConnection. CreateMaterial ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ ColorMaterial osztály](/cpp/api/remote-rendering/colormaterial)
* [C++ RenderingConnection:: CreateMaterial ()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Következő lépések

* [PBR-anyagok](pbr-materials.md)
* [Textúrák](../../concepts/textures.md)
* [Hálók](../../concepts/meshes.md)