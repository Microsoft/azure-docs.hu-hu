---
title: Újravetítés késői fázisban
description: Információk a késői fázisok újravetítéséről és használatáról.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 8d42087008f1812bc3713456025ed3be351d0917
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84022180"
---
# <a name="late-stage-reprojection"></a>Újravetítés késői fázisban

A *késői fázisok újravetítése* (LSR) egy hardveres szolgáltatás, amely segít a Hologramok stabilizálásában a felhasználó mozgatása során.

A statikus modellek várhatóan megőrzik a helyüket, amikor mozgatják őket. Ha instabilnak tűnik, ez a viselkedés LSR problémákra utalhat. Vegye figyelembe, hogy további dinamikus átalakítások, például animációk vagy alábontási nézetek is felfedik ezt a viselkedést.

Két különböző LSR mód közül választhat, azaz a **SÍKBELI LSR** vagy a **mélységi LSR**is. Azt határozza meg, hogy az ügyfélalkalmazás egy mélységi puffert küld-e el.

Mindkét LSR mód javítja a hologramos stabilitást, bár ezek eltérő korlátozásokkal rendelkeznek. Első lépésként próbálja ki a mélységi LSR, mivel ez vitathatatlanul jobb eredményeket biztosít a legtöbb esetben.

## <a name="choose-lsr-mode-in-unity"></a>LSR mód kiválasztása az egységben

A Unity Editorban nyissa meg a következőt: *:::no-loc text="File > Build Settings":::* . Válassza ki *:::no-loc text="Player Settings":::* a bal alsó sarokban, majd jelölje be, *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* hogy be van-e **:::no-loc text="Enable Depth Buffer Sharing":::** jelölve:

![Mélységi puffer megosztása engedélyezve jelző](./media/unity-depth-buffer-sharing-enabled.png)

Ha igen, az alkalmazás a mélységi LSR fogja használni, ellenkező esetben a síkbeli LSR fogja használni.

## <a name="depth-lsr"></a>Részletes LSR

Ahhoz, hogy a részletes LSR működjön, az ügyfélalkalmazás érvényes mélységi puffert kell megadnia, amely tartalmazza az összes releváns geometriát, amelyet a LSR során figyelembe kell venni.

A mélységi LSR megkísérli a képkeret stabilizálását a megadott mélységi puffer tartalma alapján. Ennek következményeként a nem a számára megjelenített tartalmak, például az átlátszó objektumok nem módosíthatók LSR, és instabilitási és újravetítési összetevőket is tartalmazhatnak.

## <a name="planar-lsr"></a>Síkbeli LSR

A síkbeli LSR nem rendelkezik képpont mélységi információval, mert a mélységi LSR. Ehelyett az összes tartalmat egy olyan sík alapján újratervezi, amelyet az egyes kereteknek meg kell adnia.

A síkbeli LSR újratervezi azokat az objektumokat, amelyek a megadott síkon közelítenek. Minél távolabb egy objektum, annál instabillá válik. Míg a mélységi LSR jobbak az objektumok különböző mélységekben történő újravetítéséhez, a síkbeli LSR jobban működhetnek, ha a tartalom jól illeszkedik egy síkkal.

### <a name="configure-planar-lsr-in-unity"></a>Síkbeli LSR konfigurálása az egységben

A sík paramétereit egy úgynevezett *fókuszpontból*származtatják, amelynek minden keretét meg kell adnia `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` . A részletekért tekintse meg az [Unity Focus Point API](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) -t. Ha nem állít be fókuszt, a rendszer tartalékot választ Önnek. Az automatikus tartalék azonban gyakran az optimálisnál rosszabb eredményeket eredményez.

Saját maga is kiszámíthatja a fókuszt, bár érdemes lehet a távoli renderelési gazdagép alapján kiszámítani. Meghívásával szerezze be a következőt: `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` . A rendszer arra kéri, hogy adjon meg egy koordináta-keretet, amelyben kifejezni szeretné a fókuszt. A legtöbb esetben csak az eredményt szeretné megadni `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` .

Általában mind az ügyfél, mind a gazdagép olyan tartalmat jelenít meg, amelyet a másik oldal nem tud, például az ügyfél felhasználói felületi elemeit. Ezért érdemes lehet a távoli fókuszt egy helyileg számított eggyel kombinálni.

A két egymást követő keretben számított fókusz pontok meglehetősen eltérőek lehetnek. Egyszerűen használhatja őket a-ként úgy, hogy a kiugróan megjelenő Hologramok. Ennek a viselkedésnek a megakadályozásához az előző és az aktuális fókusz pontok közötti interpoláció javasolt.

## <a name="next-steps"></a>További lépések

* [Kiszolgálóoldali teljesítménylekérdezések](performance-queries.md)
