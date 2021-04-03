---
title: 'Metaadatok szerkesztése: modulok leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning metaadatok szerkesztése modulját az adatkészlet oszlopaihoz társított metaadatok módosításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/10/2020
ms.openlocfilehash: e279a7c9f6810ece482b043284f0be0719a3dafe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90908033"
---
# <a name="edit-metadata-module"></a>Metaadat-modul szerkesztése

Ez a cikk a Azure Machine Learning Designer részét képező modult ismerteti.

A metaadatok szerkesztése modul használatával módosíthatja az adatkészlet oszlopaihoz társított metaadatokat. Az adatkészlet értéke és adattípusa a metaadatok szerkesztése modul használata után módosul.

A metaadatok tipikus változásai a következők lehetnek:
  
+ Logikai vagy numerikus oszlopok kategorikus értékként való kezelése.
  
+ Azt jelzi, hogy melyik oszlop tartalmazza a **osztály** címkéjét, vagy tartalmazza a kategorizálni vagy megjósolni kívánt értékeket.
  
+ Oszlopok megjelölése szolgáltatásként.
  
+ Dátum-és időértékek numerikus értékekre való módosítása vagy fordítva.
  
+ Oszlopok átnevezése.
  
 A metaadatok szerkesztése lehetőséggel bármikor módosíthatja az oszlopok definícióját, jellemzően egy alsóbb rétegbeli modul követelményeinek megfelelően. Egyes modulok például csak bizonyos adattípusokkal működnek, vagy jelölőket igényelnek az oszlopokon, például `IsFeature` vagy `IsCategorical` .  
  
 A szükséges művelet elvégzése után visszaállíthatja a metaadatokat az eredeti állapotába.
  
## <a name="configure-edit-metadata"></a>Metaadatok szerkesztésének konfigurálása
  
1. A Azure Machine Learning Designerben adja hozzá a metaadatok szerkesztése modult a folyamathoz, és kapcsolódjon a frissíteni kívánt adatkészlethez. A modult az **Adatátalakítási** kategóriában találja.
  
1. A modul jobb oldali paneljén kattintson az **oszlop szerkesztése** elemre, és válassza ki az oszlopot vagy az oszlopok készletét, amelyekkel dolgozni szeretne. Az oszlopokat egyenként is kiválaszthatja név vagy index alapján, vagy az oszlopok csoportját típus szerint is kiválaszthatja.  
  
1. Válassza az **adattípus** lehetőséget, ha egy másik adattípust kell hozzárendelni a kijelölt oszlopokhoz. Előfordulhat, hogy módosítania kell az adattípust bizonyos műveletekhez. Ha például a forrás-adatkészlet szövegként kezelt számokat tartalmaz, a matematikai műveletek használata előtt módosítania kell őket egy numerikus adattípusra.

    + A támogatott adattípusok a következők: **String**, **Integer**, **Double**, **Boolean** és **datetime**.

    + Ha több oszlopot választ ki, a metaadatok módosításait *minden* kijelölt oszlopra alkalmaznia kell. Tegyük fel például, hogy két vagy három numerikus oszlopot választ. Az összeset megváltoztathatja egy sztring típusú adattípusra, és átnevezheti őket egyetlen műveletben. Azonban az egyik oszlop nem módosítható karakterlánc adattípusra, és egy lebegőpontos oszlop egy egész számra.
  
    + Ha nem ad meg új adattípust, az oszlop metaadatai változatlanok maradnak.

    + Az oszlop típusa és értékei a metaadatok szerkesztése művelet végrehajtása után változnak. Az oszlop adattípusának alaphelyzetbe állításához a metaadatok szerkesztése lehetőséggel bármikor helyreállíthatja az eredeti adattípust.  

    > [!NOTE]
    > A **datetime formátum** a [Python beépített datetime formátumát](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior)követi.  
    > Ha bármilyen típusú számot módosít a **datetime** típusra, hagyja üresen a **datetime formátum** mezőt. Jelenleg nem lehet megadnia a célként megadott adatformátumot.

1. Válassza a **kategorikus** lehetőséget annak megadásához, hogy a kijelölt oszlopokban szereplő értékeket kategóriákként kell kezelni.

    Előfordulhat például, hogy olyan oszlopa van, amely a 0, 1 és 2 számot tartalmazza, de tudja, hogy a számok valójában "dohányos", "nem dohányzó" és "ismeretlen" értéket tartalmaznak. Ebben az esetben úgy, hogy az oszlopot kategorikusként jelöli meg, így az értékek csak az adatok csoportosítására szolgálnak, nem numerikus számításokban.
  
1. Ha módosítani szeretné a modellben található adatAzure Machine Learning, használja a **mezők** beállítást.

    + **Szolgáltatás**: ezzel a beállítással az oszlopokat szolgáltatásként jelölheti meg a modulokban, amelyek csak a szolgáltatás oszlopaiban működnek. Alapértelmezés szerint a rendszer az összes oszlopot szolgáltatásként kezeli.  
  
    + **Címke**: ezzel a beállítással jelölheti meg a címkét, amely a kiszámítható attribútum vagy a célként megadott változó néven is ismert. Számos modulhoz szükség van az adatkészlet pontosan egy felirat oszlopának megjelölésére.

        Sok esetben Azure Machine Learning következtetni, hogy egy oszlop tartalmaz egy osztály címkét. A metaadatok beállításával gondoskodhat arról, hogy az oszlop helyesen legyen azonosítva. Ez a beállítás nem változtatja meg az adatértékeket. Csak úgy változik, hogy egyes gépi tanulási algoritmusok kezelik az adatmennyiséget.
  
    > [!TIP]
    > Vannak olyan adatkészletei, amelyek nem illeszkednek a kategóriákba? Előfordulhat például, hogy az adatkészlet olyan értékeket tartalmaz, mint például az egyedi azonosítók, amelyek nem hasznosak változókként. Időnként előfordulhat, hogy az ilyen azonosítók problémákat okozhatnak a modellben való használat során.
    >
    > Szerencsére Azure Machine Learning megtartja az összes adatát, így nem kell törölnie ezeket az oszlopokat az adatkészletből. Ha műveleteket kell végrehajtania egy bizonyos speciális oszlopon, csak távolítsa el az összes többi oszlopot ideiglenesen az [adatkészlet kiválasztása](select-columns-in-dataset.md) modulban. Később az oszlopok [hozzáadása](add-columns.md) modullal egyesítheti az oszlopokat az adatkészletbe.  
  
1. Az alábbi beállításokkal törölheti a korábbi beállításokat, és visszaállíthatja a metaadatokat az alapértelmezett értékekre.  
  
    + **Funkció törlése**: ezzel a beállítással távolíthatja el a funkció jelzőjét.  
  
         Az összes oszlopot kezdetben szolgáltatásként kezeli a rendszer. A matematikai műveleteket végző modulok esetén ezt a beállítást kell használni ahhoz, hogy megakadályozza a numerikus oszlopok változóként való kezelését.
  
    + **Címke törlése**: ezzel a beállítással távolíthatja el a **címke** metaadatait a megadott oszlopból.  
  
    + **Pontszám törlése**: ezzel a beállítással távolíthatja el a **pontszám** metaadatait a megadott oszlopból.  
  
         Jelenleg nem lehet explicit módon megjelölni egy oszlopot a Azure Machine Learning pontszáma alapján. Egyes műveletek azonban egy oszlopként jelennek meg, amely a pontszám belső jelölését eredményezi. Emellett az egyéni R-modulok is kihasználhatják a pontszám értékeit.

1. Az **új oszlopnevek** mezőben adja meg a kijelölt oszlop vagy oszlopok új nevét.  
  
    + Az oszlopnevek csak az UTF-8 kódolás által támogatott karaktereket használhatják. Üres karakterláncok, nullák vagy kizárólag szóközökből álló nevek nem engedélyezettek.  
  
    + Több oszlop átnevezéséhez adja meg a neveket vesszővel tagolt listaként az oszlop indexek sorrendjében.  
  
    + Az összes kijelölt oszlopot át kell jelölni. Az oszlopokat nem lehet kihagyni vagy kihagyni.  
  
1. A folyamat elküldése.  

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .
