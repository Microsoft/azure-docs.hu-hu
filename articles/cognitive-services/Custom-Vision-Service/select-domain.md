---
title: Tartomány kiválasztása Custom Vision projekthez – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan választhat tartományokat a projekthez a Custom Vision Service.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 0b1358f67e2b10078e24168bdb779cd708ac2403
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095663"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Tartomány kiválasztása Custom Vision projekthez

A Custom Vision projekt beállítások lapján választhat egy tartományt a projekthez. Válassza ki a forgatókönyvhöz legközelebb eső tartományt. Ha ügyféloldali kódtár vagy REST API használatával fér hozzá Custom Visionhoz, meg kell adnia egy tartományi azonosítót a projekt létrehozásakor. Lekérheti a tartományi azonosítók listáját a [lekérési tartományokkal](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab), vagy használhatja az alábbi táblázatot.

## <a name="image-classification"></a>Képbesorolás

|Tartomány|Cél|
|---|---|
|__Általános__| A képbesorolási feladatok széles körére optimalizált. Ha a többi tartomány egyike sem megfelelő, vagy ha nem biztos abban, hogy melyik tartományt kell választania, válassza az általános tartományt. ID `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__Általános [a1]__| A jobb pontosság érdekében az általános tartományhoz hasonló következtetési idő van optimalizálva. Nagyobb adatkészletekhez vagy nehezebb felhasználói forgatókönyvekhez ajánlott. Ehhez a tartományhoz több betanítási idő szükséges. ID `a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__Élelmiszer__|Az ételek fényképeihez optimalizált, ahogy azt egy étterem menüjében láthatja. Ha egyéni gyümölcsökből vagy zöldségekből származó fényképeket szeretne osztályozni, használja az élelmiszer-tartományt. ID `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Arcrészek__|Felismerhető tereptárgyak számára optimalizált, természetes és mesterséges is. Ez a tartomány akkor működik a legjobban, ha a tereptárgy jól látható a fényképben. Ez a tartomány akkor is működik, ha a tereptárgyat az előttük lévő személyek kis mértékben akadályozzák. ID `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Retail__|A vásárlási katalógusban vagy a vásárlási webhelyen található rendszerképekre optimalizált. Ha magas pontosságú osztályozást szeretne a ruhák, nadrágok és ingek között, használja ezt a tartományt. ID `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Kompakt tartományok__| Az Edge-eszközök valós idejű besorolásának korlátaira optimalizált.|

## <a name="object-detection"></a>Objektumészlelés

|Tartomány|Cél|
|---|---|
|__Általános__| Az objektum-észlelési feladatok széles körére optimalizált. Ha a többi tartomány egyike sem megfelelő, vagy nem biztos benne, hogy melyik tartományt szeretné kiválasztani, válassza az általános tartományt. ID `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__Általános [a1]__| A jobb pontosság érdekében az általános tartományhoz hasonló következtetési idő van optimalizálva. A régió pontos elhelyezésére, nagyobb adatkészletekre vagy bonyolultabb felhasználói forgatókönyvekre ajánlott. Ehhez a tartományhoz több betanítási idő szükséges, és az eredmények nem determinisztikus: a rendszer a megadott betanítási adatokkal egy +-1% átlagos pontossági (mAP) különbözetet vár. ID `9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__Embléma__|A képeken található márka emblémák keresésére optimalizált. ID `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Termékek a polcokon__|A polcokon található termékek észlelésére és besorolására optimalizált. ID `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Kompakt tartományok__| Az Edge-eszközök valós idejű objektum-észlelésének korlátaira optimalizált.|

## <a name="compact-domains"></a>Kompakt tartományok

A kompakt tartományok által generált modellek helyileg is futtathatók. A Custom Vision 3,4 nyilvános előzetes verzió API-ban a GetDomains API meghívásával lekérheti a kompakt tartományokhoz tartozó exportálható platformok listáját.

A modell teljesítménye a kiválasztott tartománytól függ. Az alábbi táblázatban bejelentjük a modell méretét és következtetéseit az Intel Desktop CPU-ban és az NVidia GPU \[ 1-ben \] . Ezek a számok nem tartalmazzák az előfeldolgozást és a utófeldolgozó időt.

|Feladat|Tartomány|ID (Azonosító)|Modell mérete|CPU-következtetési idő|GPU-következtetési idő|
|---|---|---|---|---|---|
|Besorolás|General (compact) (Általános (kompakt))|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10 MS|5 MS|
|Besorolás|Általános (Compact) [S1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 MB|50 MS|5 MS|
|Objektumészlelés|General (compact) (Általános (kompakt))|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 MS|5 MS|
|Objektumészlelés|Általános (Compact) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 MS|7 MS|

>[!NOTE]
>Az objektumok észleléséhez szükséges __általános (Compact)__ tartomány speciális utófeldolgozó logikát igényel. A részletekért tekintse meg az exportált zip-csomagban található példaként szolgáló parancsfájlt. Ha a utófeldolgozó logikát nem használó modellre van szüksége, használja az __általános (Compact) [S1]__.

>[!IMPORTANT]
>Nincs garancia arra, hogy az exportált modellek pontosan ugyanazt eredményezik, mint az előrejelzési API a felhőben. A futó platform enyhe eltérése vagy az előfeldolgozás megvalósítása nagyobb különbséget eredményezhet a modell kimenetében. Az előfeldolgozási logika részleteiért tekintse meg ezt a [dokumentumot](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5 – 2690 CPU és NVIDIA Tesla M60
