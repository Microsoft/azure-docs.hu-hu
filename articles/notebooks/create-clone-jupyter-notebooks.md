---
title: Hozzon létre, és klónozza a Jupyter notebooks az Azure-ban
description: Azure-jegyzetfüzetek projektek jegyzetfüzetek és a kapcsolódó fájlokat, amelyek új létrehozása, vagy más forrásból származó klónozása gyűjteményét kezelheti.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 13615d319af600234dcc23e04f82ce46b8f97780
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970090"
---
# <a name="create-and-clone-projects"></a>Projektek létrehozása és klónozása

Az Azure-jegyzetfüzeteket a Jupyter notebookok és a kapcsolódó fájlokat rendszerezi a nevű logikai csoportokba *projektek*. Tárolójaként, először hozzon létre egy projektet, majd hozzon létre vagy klónozásához egy vagy több notebookok mellett egyéb projekt fájlokat egy mappában található. (Ezt a folyamatot mutatják be a [oktatóanyag](tutorial-create-run-jupyter-notebook.md).)

Egy projektet is fenntartják, metaadatokat és egyéb konfigurációs beállítások, amelyek hatással lennének a kiszolgálóra notebookokban mely futtatja, beleértve az egyéni beállítási lépéseket és a csomag telepítése. További információkért lásd: [kezelése és konfigurálása a projektek](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>A saját projektek irányítópult

A **saját projektek** : Irányítópult `https://notebooks.azure.com/<userID>/projects` ahol megtekintése, kezelése és hozható létre:

[@no__t 1My-projektek irányítópultja Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Mi mindent az irányítópulton attól függ, hogy jelentkezett be a fiók, amely a felhasználói azonosító tulajdonosa:

| Parancs | Elérhető: | Leírás |
| --- | --- | --- |
| **Futtatás** | Tulajdonos | A project server elindul, és a projektmappában nyílik Jupyter. (Leggyakrabban, akkor először lépjen a projektmappára, majd indítsa el egy jegyzetfüzetet innen.) |
| **Letöltés** | Mindenki számára | ZIP-fájlként tölti le a kiválasztott projekt egy példányát. |
| **Megosztás** | Mindenki számára | Megjeleníti a megosztási előugró ablak, amelyen keresztül szerezze be a kiválasztott projekt URL-címe, közösségi megosztás, e-mail küldése az URL-CÍMÉT és szerezze be a HTML- vagy a Markdown kódot "indítási notebook" jelvény különbözteti (lásd: [szerezzen be egy indítási jelvény](#obtain-a-launch-badge)) az URL-címmel. |
| **Törlés** | Tulajdonos | Törli a kiválasztott projekthez. Ez a művelet nem vonható vissza. |
| **Terminálszolgáltatások** | Tulajdonos | A project server kezdődik, majd megnyílik egy új böngészőablakot a bash, terminál, hogy a kiszolgáló. |
| **+ Új projekt** | Tulajdonos | Létrehoz egy új projektet. Lásd: [hozzon létre egy új projektet](#create-a-new-project). |
| **GitHub-adattár feltöltése** | Tulajdonos | Importál egy projektet a Githubról. [A projekt importálása a Githubról](#import-a-project-from-github). |
| **Klónozás** | Mindenki számára | Másolja át a kiválasztott projekt a saját fiókba. Kéri, ha még nem már bejelentkezhet. Lásd: [-projekt klónozása](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Szerezzen be egy indítási jelvény

Használatakor a **megosztás** parancsot, és válassza ki a **beágyazási** lapon is másolhatja, vagy a HTML-kódot, vagy a Markdown, amely létrehoz egy "indítási notebook" jelvény:

![Indítsa el a jegyzetfüzet-jelvény](https://notebooks.azure.com/launch.png)

Ha nem rendelkezik egy Azure-jegyzetfüzetek projekt, egy hivatkozás, amely klónokat hozhat létre a Githubról közvetlenül használatával a következő sablonokat, és cserélje le a megfelelő felhasználónevet és a tárház nevét:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Új projekt létrehozása

Használatakor a **+ új projekt** parancsot, az Azure-jegyzetfüzetek jeleníti meg egy **új projekt létrehozása** előugró ablak. Az előugró ablakban adja meg a következő adatokat, majd válassza ki **létrehozás**:

| Mező | Leírás |
| --- | --- |
| Projektnév | Egy rövid nevet a projekthez, amely az Azure-jegyzetfüzetek megjelenítési célokra használja. Például: "My notebook Project". |
| Projektazonosító | A projekt megosztása használhatja egy egyéni azonosítót, amely az URL-cím részévé válik (a képernyő `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Ez az azonosító csak betűket, számokat és kötőjeleket használhat, legfeljebb 30 karakterből állhat, és nem lehet [foglalt projekt-azonosító](#reserved-project-ids). Ha Ön nem tudja, hogy melyiket érdemes használni, a common konvenciónak, hogy a projekt neve kisbetűs verzióját, szóközöket, kötőjeleket, például a "my-jegyzetfüzet-projekt" (ha szükséges, hogy illeszkedjen a hosszra vonatkozó korlátot csonkolt) vannak kapcsolva. |
| Nyilvános | Ha a beállítása, lehetővé teszi, hogy bárki a hivatkozást a projekt eléréséhez. Privát projekt létrehozásakor törölje ezt a beállítást. |
| Ez a projekt és a egy információs fájl inicializálása | Ha a beállítása, létrehoz egy alapértelmezett *README.md* fájlt a projektben. A *README.md* fájl az dokumentáció adni a projekthez, ha szükséges. |

### <a name="reserved-project-ids"></a>Fenntartott projektek azonosítói

A következő fenntartott szavak önmagukban nem használhatók projekt-azonosítóként. Ezek a fenntartott szavak azonban a hosszú projekt-azonosítók részeként is használhatók.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| körülbelül | fiók | felügyeleti | api | blog | osztályteremben |
| tartalom | irányítópult | Ismerkedés | – gyakori kérdések | help | html |
| Kezdőlap | importálása | erőforrástár | felügyelet | új | notebook |
| notebookok | PDF | előzetes verzió | díjszabás | profile | keresés |
| status | támogatás | test | | | |

Ha a következő szavak egyikét próbálja meg projekt-AZONOSÍTÓként használni, az **új projekt létrehozása** és a **projekt beállításainak** előugró ablakai jelzik, hogy a "könyvtár azonosítója fenntartott azonosító".

Mivel a projekt-azonosító egy projekt URL-címéhez is tartozik, az ad blocker szoftver blokkolhatja bizonyos kulcsszavak használatát, például a "hirdetést". Ilyen esetekben használjon egy másik szót a projekt-AZONOSÍTÓban.

## <a name="import-a-project-from-github"></a>A projekt importálása a Githubról

Egy teljes nyilvános GitHub-adattár segítségével egyszerűen importálhatja a olyan projekt, beleértve az adatokat és *README.md* fájlokat. Használja a **GitHub-adattár feltöltése** parancsot, adja meg a következő adatokat, az előugró ablakban, majd válassza ki **importálás**:

| Mező | Leírás |
| --- | --- |
| GitHub-adattár | A github.com tárházban neve. Például az Azure Cognitive Services, a Jupyter notebookok klónozása [ https://github.com/Microsoft/cognitive-services-notebooks ](https://github.com/Microsoft/cognitive-services-notebooks), adja meg a "Microsoft/cognitive-services-notebookok".  |
| Klónozza a rekurzív módon | GitHub-adattárak több gyermek-tárházakat is tartalmazhat. Állítsa be ezt a beállítást, ha azt szeretné, a szülő-tárházat és az összes gyermekre a klónozásához. Szeretné, hogy számos gyermek-tárházhoz lehetőség, mert hagyja törölje ezt a beállítást, ha tudja, szüksége lesz rá. |
| Projektnév | Egy rövid nevet a projekthez, amely az Azure-jegyzetfüzetek megjelenítési célokra használja. |
| Projektazonosító | A projekt megosztása használhatja egy egyéni azonosítót, amely az URL-cím részévé válik (a képernyő `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Ez az azonosító csak betűket, számokat és kötőjeleket használhat, legfeljebb 30 karakterből állhat, és nem lehet [foglalt projekt-azonosító](#reserved-project-ids). Ha Ön nem tudja, hogy melyiket érdemes használni, a common konvenciónak, hogy a projekt neve kisbetűs verzióját, szóközöket, kötőjeleket, például a "my-jegyzetfüzet-projekt" (ha szükséges, hogy illeszkedjen a hosszra vonatkozó korlátot csonkolt) vannak kapcsolva. |
| Nyilvános | Ha a beállítása, lehetővé teszi, hogy bárki a hivatkozást a projekt eléréséhez. Privát projekt létrehozásakor törölje ezt a beállítást. |

Az előzményeket is importálja egy tárház GitHub importálja. A terminálból a normál Git-parancsok használatával véglegesítse a módosításokat, kérje le a módosításokat a Githubról, és így tovább.

## <a name="clone-a-project"></a>Egy projekt klónozása

Egy meglévő projekt egy példányát a saját fiókját, ahol ezután futtassa, és minden olyan jegyzetfüzet vagy más fájlt a projektben módosítsa a Klónozás hoz létre. Használhatja a Klónozás példányát a saját projektek szükség lehet kísérletfuttatásért és más munkahelyi anélkül, hogy megzavarná az eredeti projektet.

A projekt klónozása:

1. Az a **saját projektek** irányítópultján kattintson a jobb gombbal a kívánt projektre, és válassza ki **Klónozás** (billentyűparancs: c).

    ![Klónozási parancs a projekt helyi menüjében](media/clone-command.png)

1. Az a **Klónozás projekt** előugró ablakban adja meg a klón nevét és Azonosítóját, és adja meg, hogy a klónozott nyilvános. Ezek a beállítások ugyanazok, mint a egy [új projekt](#create-a-new-project).

    ![Klónozás projekt helyi menü](media/clone-project.png)

1. Kiválasztása után a **Klónozás** gombra, az Azure-jegyzetfüzetek közvetlenül a másolat navigál.

## <a name="next-steps"></a>További lépések

- [Ismerkedés a mintafüzetek](azure-notebooks-samples.md)
- [Útmutató: Projektek konfigurálása és kezelése @ no__t-0
- [Útmutató: Csomagok telepítése jegyzetfüzetből @ no__t-0
- [Útmutató: Bemutató megjelenítése @ no__t-0
- [Útmutató: Az adatfájlok használata @ no__t-0
- [Útmutató: Hozzáférés az adaterőforrásokhoz @ no__t-0
- [Útmutató: Azure Machine Learning Services @ no__t-0 használata
