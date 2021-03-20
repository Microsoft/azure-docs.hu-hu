---
title: Azure Media Player beállítások
description: A Azure Media Player beágyazási kód egyszerűen egy HTML5-videó címkéje, ezért számos lehetőség esetében használhatja a standard címke attribútumokat a beállítások megadásához.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 392ecff2ab120e713a07d130493a4a8339dae5ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366870"
---
# <a name="options"></a>Beállítások #

## <a name="setting-options"></a>Beállítás lehetőségei ##

A Azure Media Player beágyazási kód egyszerűen egy HTML5-videó címkéje, ezért számos lehetőség esetében használhatja a standard címke attribútumokat a beállítások megadásához.

`<video controls autoplay ...>`

Azt is megteheti, hogy az adattelepítő attribútumot használja a [JSON](http://json.org/example.html) formátumú beállítások megadására. Azt is megteheti, hogy olyan beállításokat állít be, amelyek nem standard szintűek a videó címkéjére.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Végül, ha nem az adattelepítési attribútumot használja a lejátszó beállításának elindításához, akkor a JavaScript Setup függvény második argumentuma átadhat egy objektumot a lejátszó beállításaival.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> A konstruktorban lévő beállítások csak az első inicializálásra vannak beállítva a forrás beállítása előtt.  Ha módosítani szeretné a beállításokat ugyanazon a inicializált Azure Media Player elemen, a forrás módosítása előtt frissítenie kell a beállításokat. A JavaScriptben található beállításokat a használatával frissítheti `myPlayer.options({/*updated options*/});` . Vegye figyelembe, hogy a rendszer csak a megváltozott beállításokat érinti, az összes többi korábban beállított beállítás is megmarad.

## <a name="individual-options"></a>Egyéni beállítások ##

> [!NOTE]
>A videó címke attribútumai csak TRUE vagy FALSE (Boolean) lehet, egyszerűen belefoglalhatja az attribútumot (nincs egyenlő előjel) a bekapcsolásához, vagy kizárhatja azt a kikapcsoláshoz. Ha például a vezérlők bekapcsolásához nem megfelelő `<video controls="true" ...>` , `<video controls ...>` akkor a legnagyobb probléma, hogy a rendszer a legtöbbet futtatja, és false értékkel állítja be ezeket az értékeket (pl. Controls = "false"), amely valójában az ellenkezőjét adja meg, és az értéket igaz értékre állítja, mert az attribútum továbbra is szerepel.

### <a name="controls"></a>vezérlők ###

A vezérlők beállítás megadja, hogy a lejátszó rendelkezik-e olyan vezérlőkkel, amelyekkel a felhasználó együttműködhet. Az ellenőrzés nélkül az egyetlen módszer a videó lejátszásának elindítására az Autoplay attribútummal vagy az API-n keresztül.

`<video controls ...>` vagy `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Ha az automatikus lejátszás értéke TRUE (igaz), a videó a lap betöltése után azonnal elindul (a felhasználó beavatkozása nélkül).

> [!NOTE]
> Ez a beállítás nem támogatott a mobileszközök, például az Windows Phone-telefon, az Apple iOS és az Android esetében. A mobileszközök letiltják az automatikus lejátszás funkciót, amely megakadályozza a fogyasztó havi adatcsomagjainak (gyakran költséges) használatát. Ebben az esetben a videó elindításához a felhasználó érintése/kattintás szükséges.

`<video autoplay ...>`vagy `{ "autoplay": true }`

### <a name="poster"></a>poszter ###
A poszter attribútum azt a képet állítja be, amely a videó megkezdése előtt megjelenik. Ez gyakran a videó kerete vagy egy egyéni cím képernyő. Amint a felhasználó rákattint a lejátszás gombra, a rendszer elindul.

`<video poster="myPoster.jpg" ...>` vagy `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>szélesség ###

A Width (szélesség) attribútum beállítja a videó megjelenítési szélességét.

`<video width="640" ...>` vagy `{ "width": 640 }`

### <a name="height"></a>magasság ###

A magasság attribútum beállítja a videó megjelenítési magasságát.

`<video height="480" ...>` vagy `{ "height": 480 }`

### <a name="plugins"></a>dugó ###

A plugins JSON meghatározza, hogy mely beépülő modulok tölthetők be az adott példánnyal, lehetővé teszi, hogy a beépülő modul esetlegesen milyen beállításokat konfiguráljon.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

További információ a beépülő modulok fejlesztéséről és használatáról: [plugins írása](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>egyéb beállítások ###

A címkén további beállítások is megadhatók a JSON-t `<video>` használó `data-setup` paraméter használatával.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Ez explicit módon false (hamis) értékre van állítva. A hamis értékre állításával lehetővé teszi, hogy a Azure Media Player-bőr ugyanazokat a platformokat jelenítse meg.  Továbbá a névvel ellentétben a Touch továbbra is engedélyezve lesz.

### <a name="fluid"></a>numerikus ###

Ha ezt a beállítást True video elemre állítja, a rendszer a szülő tároló teljes szélességét és magasságát fogja módosítani úgy, hogy a videóhoz a szabványos 16:9 méretarányt használja.

`<video ... data-setup='{"fluid": true}'>`

`fluid` a beállítás felülbírálja `width` a explicit és a `height` beállításokat. Ez a beállítás csak Azure Media Player vagy újabb verzióban érhető el `2.0.0` .

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` a beállítás szabályozza a felhasználó számára elérhető playbackSpeed-szabályozást és a lejátszási sebesség beállítását. `playbackSpeed` egy objektumot vesz fel. Ha engedélyezni szeretné a lejátszás sebességének vezérlését a menüsávon, `enabled` az objektum tulajdonságát True értékre kell állítani. Példa a lejátszási sebesség engedélyezésére a kódban:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


A beállítás egyéb tulajdonságait a `playbackSpeed` [PlaybackSpeedOptions](/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) objektum adja meg.

Példa a lejátszási sebesség beállításainak beállítására a JavaScriptben:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Ez a beállítás csak Azure Media Player 2.0.0 és újabb verziókban érhető el.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

A `staleDataTimeLimitInSec` lehetőség egy olyan optimalizálás, amely lehetővé teszi, hogy a mediaSource-pufferekben a megtartani kívánt elavult adatmennyiséget hány másodpercig szeretné beállítani. A beállítás alapértelmezés szerint le van tiltva.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

A True beállítás lehetővé teszi az élő CEA-feliratok élő streamekben és élő archívumokban való megjelenítését. A Label attribútum megadása nem kötelező, ha a lejátszó nem tartalmaz egy alapértelmezett címkét.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Ez a beállítás csak Azure Media Player 2.1.1-es és újabb verzióiban érhető el.

## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)
