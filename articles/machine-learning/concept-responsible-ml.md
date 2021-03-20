---
title: Mi a felelős gépi tanulás (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, milyen felelősségteljes gépi tanulásra van mód, és hogyan használható a Azure Machine Learning a modellek megismerésére, az adatvédelemre és a modell életciklusának szabályozására.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 02/25/2021
ms.custom: responsible-ml
ms.openlocfilehash: f849968c16d2c3d8940a71da0e0cda536882d9c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692062"
---
# <a name="what-is-responsible-machine-learning-preview"></a>Mi a felelős gépi tanulás? (előzetes verzió)

Ebből a cikkből megtudhatja, milyen felelősségteljes gépi tanulásra (ML) van szükség, és hogyan helyezheti üzembe azt Azure Machine Learning használatával.

## <a name="responsible-machine-learning-principles"></a>A felelősségteljes gépi tanulás alapelvei

Az AI-rendszerek fejlesztése és használata során a megbízhatóságnak a legfontosabbnak kell lennie. Megbízhatóság a platformon, a folyamaton és a modelleken. A Microsoftnál a felelős gépi tanulás a következő értékeket és alapelveket foglalja magában:

- A gépi tanulási modellek ismertetése
  - A modell viselkedésének értelmezése és magyarázata
  - A modell kiértékelése és enyhítése
- A személyek és az adataik védelme
  - A különbözeti adatokkal való kitettség megakadályozása
  - Titkosított adatkezelés a homomorphic titkosítás használatával
- A végpontok közötti gépi tanulási folyamat szabályozása
  - A gépi tanulási életciklus dokumentálása az adatlapokkal

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Felelős ML pillérek – értelmező, differenciált adatvédelem, homomorphic titkosítás, naplózási nyomvonal – Azure Machine Learning":::

Mivel a mesterséges intelligencia és az autonóm rendszerek egyre inkább integrálják a társadalmat a társadalomba, fontos, hogy proaktív módon végezze el az ilyen technológiák nem szándékolt következményeinek megelőzését és enyhítését.

## <a name="interpret-and-explain-model-behavior"></a>A modell viselkedésének értelmezése és magyarázata

A nehezen érthető vagy átlátszatlan rendszerek problémái lehetnek, mert az érintett felek, például a rendszerfejlesztők, a szabályozók, a felhasználók és az üzleti döntéshozók megtudhatják, hogy a rendszer miért tesz bizonyos döntéseket. Néhány AI-rendszer jobban megmagyarázható, mint a többinél, és néha egy kompromisszumos rendszer, amely nagyobb pontossággal rendelkezik, és az egyik még magyarázható.

A értelmezhető AI-rendszerek létrehozásához használja a [InterpretML](https://github.com/interpretml/interpret)-t, amely a Microsoft által készített nyílt forráskódú csomag. A InterpretML-csomag számos értelmező módszert támogat, például a SHapley-adalékanyag magyarázatait (SHAP), a magyarázatot és a permutációs funkciók fontosságát (PFI).  A InterpretML a gépi tanulási modellek, például az [automatizált gépi tanulási modellek](how-to-machine-learning-interpretability-automl.md) [értelmezése és magyarázata](how-to-machine-learning-interpretability-aml.md) [Azure Machine learning belül is használható](how-to-machine-learning-interpretability.md) .

## <a name="mitigate-fairness-in-machine-learning-models"></a>A gépi tanulási modellek tisztaságának enyhítése

Mivel a mesterséges intelligencia-rendszerek egyre nagyobb szerepet játszanak a társadalom mindennapos döntéshozatalában, rendkívül fontos, hogy ezek a rendszerek jól működjenek a tisztességes eredmények mindenki számára való biztosítása terén.

A mesterséges intelligencia-rendszerekben a következő nem kívánt következményekkel járhat:

- Lehetőségek, erőforrások és információk visszatartása magánszemélyek számára.
- A torzítások és a sztereotípiák megerősítése.

A méltányosság számos aspektusa nem rögzíthető és nem jeleníthető meg mérőszámok alapján. Vannak olyan eszközök és gyakorlatok, amelyek az AI-rendszerek kialakításában és fejlesztésében is javítják a méltányosság hatékonyságát.

Az AI-rendszerekben a tisztességtelenség csökkentésének két fő lépése az értékelés és a mérséklés. Javasoljuk, hogy [FairLearn](https://github.com/fairlearn/fairlearn)egy nyílt forráskódú csomagot, amely képes felmérni és elhárítani a mesterséges intelligencia-rendszerek potenciális méltánytalan képességét. Ha többet szeretne megtudni a méltányosság és a FairLearn csomagról, tekintse meg a [méltányosság ml-ben című cikket](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>A különbözeti adatokkal való kitettség megakadályozása

Az adatok elemzésre való használatakor fontos, hogy az adatok a használat során magánjellegűek és bizalmasak maradnak. A differenciált adatvédelem olyan rendszerek és eljárások összessége, amelyek segítenek megőrizni a magánszemélyek adatait.

A hagyományos helyzetekben a nyers adatok tárolása a fájlokban és az adatbázisokban történik. Amikor a felhasználók elemeznek az adatelemzést, általában a nyers adattípust használják. Ez aggodalomra ad okot, mert sértheti az egyén adatvédelmét. A differenciált adatvédelem a "zaj" vagy az adatok véletlenszerű törlésével próbálkozik a probléma megoldásával, így a felhasználók nem azonosíthatják az egyes adatpontokat.

A differentially privát rendszereinek megvalósítása nehéz feladat. A [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core) egy nyílt forráskódú projekt, amely különböző összetevőket tartalmaz a globális differentially-alapú privát rendszerek létrehozásához. Ha többet szeretne megtudni a differenciált adatvédelemről és a SmartNoise projektről, tekintse meg az adatvédelem [megőrzése a különbözeti adatvédelem és a SmartNoise című cikkben leírtakat](./concept-differential-privacy.md) .

## <a name="work-on-encrypted-data-with-homomorphic-encryption"></a>Titkosított adatmennyiség homomorphic titkosítással

A hagyományos Felhőbeli tárolási és számítási megoldások esetében a felhőnek titkosítatlan hozzáférésre van szüksége az ügyféladatok számára a számítási feladatokhoz. Ez a hozzáférés a Felhőbeli operátorok számára teszi elérhetővé az adatvédelmet. Az adatvédelem a felhő által megvalósított hozzáférés-vezérlési házirendekre támaszkodik, és az ügyfél megbízhatónak tartja.

A homomorphic titkosítás lehetővé teszi a számítások elvégzését a titkosított adatokon anélkül, hogy hozzá kellene férnie egy titkos (visszafejtési) kulcshoz. A számítások eredményei titkosítottak, és csak a titkos kulcs tulajdonosa által láthatók. A homomorphic titkosítás használata esetén a Felhőbeli operátorok soha nem titkosítják a tárolt és a számítástechnikai adataikat. A számításokat a rendszer közvetlenül a titkosított adatokon hajtja végre. Az adatok védelme a legkorszerűbb titkosításra támaszkodik, az adatok tulajdonosa pedig az összes információ kiadását vezérli. A Microsoft homomorphic-titkosításával kapcsolatos további információkért lásd: [Microsoft Research](https://www.microsoft.com/research/project/homomorphic-encryption/).

A Azure Machine Learning homomorphic-titkosítás használatának megkezdéséhez használja a [Microsoft Seal](https://github.com/microsoft/SEAL) [titkosított-következtetések Python-](https://pypi.org/project/encrypted-inference/) kötéseit. A Microsoft SEAL egy nyílt forráskódú homomorphic titkosítási függvénytár, amely lehetővé teszi, hogy a hozzáadások és a szorzások titkosított egész számokon vagy valós számokon legyenek elvégezve. A Microsoft SEAL szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Azure Architecture Center](/azure/architecture/solution-ideas/articles/homomorphic-encryption-seal) vagy a [Microsoft kutatási projektjét ismertető oldalt](https://www.microsoft.com/research/project/microsoft-seal/).

A következő példában megtudhatja, [Hogyan helyezhet üzembe egy titkosított, Azure Machine learning-alapú webszolgáltatást](how-to-homomorphic-encryption-seal.md).

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>A gépi tanulási életciklus dokumentálása az adatlapokkal

A megfelelő információk dokumentálása a gépi tanulási folyamat során kulcsfontosságú a felelős döntések meghozatalához az egyes fázisokban. Az adatlapokon dokumentálhatja a gépi tanulási életciklus részeként használt és létrehozott gépi tanulási eszközöket.

A modellek általában úgy gondolják, hogy "átlátszatlan dobozok", és gyakran kevés információ áll rendelkezésre róluk. Mivel a gépi tanulási rendszerek egyre átfogóbbak, és a döntéshozatalhoz használják őket, az adatlapok használata egy lépés a felelősségteljesebb gépi tanulási rendszerek fejlesztése felé.

Néhány modell-információ, amelyet egy adatlap részeként szeretne dokumentálni:

- Rendeltetésszerű használat
- Modell architektúrája
- Felhasznált betanítási adatgyűjtés
- Felhasznált kiértékelési adatszolgáltatások
- Képzési modell teljesítmény-mérőszámai
- A méltányosság adatai.

Tekintse meg az alábbi mintát, amelyből megtudhatja, hogyan hozhatja létre a [modellek adatlapjait](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb)a Azure Machine learning SDK használatával.

## <a name="additional-resources"></a>További források

- További információkért tekintse meg a [felelős innovációs eszközkészletet](/azure/architecture/guide/responsible-innovation/) az ajánlott eljárások megismeréséhez.
- További információ a Machine learning-rendszer dokumentációjának a [ml](https://www.partnershiponai.org/about-ml/) -készletéről.