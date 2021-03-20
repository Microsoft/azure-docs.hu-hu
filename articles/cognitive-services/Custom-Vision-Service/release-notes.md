---
title: Kibocsátási megjegyzések – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Szerezze be a legújabb információkat a Custom Vision csapat új kiadásairól.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: 3724a7d515197c1f969bb051fc201b82bee64c42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97813496"
---
# <a name="custom-vision-service-release-notes"></a>Custom Vision Service kibocsátási megjegyzések

## <a name="may-2-2019-and-may-10-2019"></a>Május 2., 2019 és május 10., 2019

- Hibajavítások és háttérbeli javítások

## <a name="may-23-2019"></a>2019. május 23.

- Továbbfejlesztett portál UX-élmény az Azure-előfizetésekkel kapcsolatban, így könnyebben kiválaszthatja az Azure-címtárakat.

## <a name="april-18-2019"></a>Április 18., 2019 

- Objektum-észlelési exportálás hozzáadva a jövőkép AI fejlesztői csomaghoz.
- Felhasználói felületi csípések, beleértve a projekt keresését.

## <a name="april-3-2019"></a>2019. április 3.

- Megnövelt számú korlát a képkeretek száma alapján a 200-es méretre. 
- Hibajavítások, beleértve a TensorFlow exportált modellek jelentős teljesítményének frissítését. 

## <a name="march-26-2019"></a>Március 26., 2019

- A Custom Vision Service általánosan elérhetővé tette az Azure-ban!
- Fejlett betanítási funkció új gépi tanulási háttérrel a jobb teljesítmény érdekében, különösen a kihívást jelentő adatkészletek és a részletes besorolás tekintetében. A speciális betanítással megadhatja a betanítási és a Custom Vision számítási idő költségvetést, amely a legjobb képzési és bővítési beállítások kipróbálását is lehetővé teszi. A gyors iterációk esetében továbbra is használhatja a meglévő gyors képzést.
- 3,0 API-k bevezetése. Az előzetes verziójú 3,0 API-k az 2019-es október 1-én elavultak. Az első lépésekhez [a dokumentációban](./quickstarts/image-classification.md) talál példákat.
- Az "alapértelmezett iterációk" helyébe a 3,0 API-k közzétételi/visszavonási lehetőségeit cserélték.
- Új modell-exportálási célok lettek hozzáadva. A Docker export frissítve lett a következőhöz: málna PI 3. Az exportálási támogatás hozzá lett adva a [jövőkép AI fejlesztői csomaghoz.](https://visionaidevkit.com/)
- Megnövelt számú címke/projekt – 500 a S0 szinten. Nagyobb számú lemezkép projektenként és 100 000 a S0 szinten.
- A felnőtt tartomány eltávolítva. Ehelyett az általános tartomány ajánlott.
- Az általános elérhetőség [díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) bejelentve.  

## <a name="february-25-2019"></a>Február 25., 2019

- Bejelentette a korlátozott próbaverziós projektek (az Azure-erőforrásokhoz nem társított projektek) végét, mivel Custom Vision az Azure nyilvános előzetes verziójára való áttérés befejezése mellett. 2019. március 25-én a CustomVision.ai-hely csak az Azure-erőforrásokhoz társított projektek megtekintését támogatja, például az ingyenes Custom Vision erőforrást. 2019. október 1-jén a meglévő korlátozott próbaverziós projekteket a Custom Vision API-kon keresztül érheti el. Ezzel időt biztosít a Custom Vision-ban megírt alkalmazások API-kulcsainak frissítésére. A 2019. október 1. után az Azure-ba nem áthelyezett korlátozott próbaidőszakú projekteket törli a rendszer.

## <a name="january-22-2019"></a>2019. január 22.

- Támogatás az új Azure-régiókhoz: USA 2. nyugati régiója, USA keleti régiója, USA 2. keleti régiója, Nyugat-Európa, Észak-Európa, Délkelet-Ázsia, Kelet-Ausztrália, Közép-India, Egyesült Királyság déli régiója, Kelet-japán és Észak-Közép-USA. A támogatás továbbra is az USA déli középső régiójában működik.

## <a name="december-12-2018"></a>2018. december 12.

- Az objektum-észlelési modellek exportálásának támogatása (az objektum-észlelési kompakt tartomány bevezetve).
- Javítva lett a kisegítő lehetőségek száma a jobb képernyőolvasó és a billentyűzet navigációs támogatása terén.
- UX-frissítések a képnézegető és a továbbfejlesztett objektum-észlelés címkézési felületének gyorsabb címkézése érdekében.  
- Frissített alapmodell az objektum-észlelési tartományhoz a jobb minőségű objektumok észlelése érdekében.
- Hibajavítások.

## <a name="november-6-2018"></a>November 6., 2018

- A logo tartomány támogatása az objektum észlelésében.

## <a name="october-9-2018"></a>2018. október 9.

- Az objektum észlelése fizetős előzetes verzióba kerül. Mostantól létrehozhat egy Azure-erőforrással rendelkező objektum-észlelési projekteket is.
- Az "áttérés az Azure-ba" funkció a webhelyre való hozzáadásával megkönnyíti a korlátozott próbaverziós projektek frissítését az Azure-ra való hivatkozáshoz. erőforráshoz csatolt projekt (F0 vagy S0) Ezt a termék beállítások oldalán találja.  
- Exportálás a ONNX 1,2-be, hogy támogassa a 2018 Windows október, a Windows ML Update verzióját.
Hibajavítások, beleértve a speciális karaktereket tartalmazó ONNX exportálását is.

## <a name="august-14-2018"></a>Augusztus 14., 2018

- Az "első lépések" widget hozzáadva a customvision.ai a felhasználóknak a projekt betanításával.
- A gépi tanulási folyamat további továbbfejlesztése a többcímkés projektek (új veszteségi réteg) kihasználása érdekében.

## <a name="june-28-2018"></a>Június 28., 2018

- Hibajavítások & háttérbeli javításokat.
- Többosztályos besorolás engedélyezése olyan projektekhez, ahol a képek pontosan egy címkével rendelkeznek. A többosztályos mód előrejelzései esetén a valószínűségek összege eggyel (az összes rendszerkép a megadott címkék közé sorolható).

## <a name="june-13-2018"></a>2018. június 13.

- UX-frissítés, a könnyű használat és a hozzáférhetőség szempontjából.
- A Machine learning-folyamat fejlesztései nagy számú címkével rendelkező többcímkés projektek kihasználása érdekében.
- Rögzített hiba a TensorFlow-exportálásban. Az exportált modell verziószámozásának engedélyezése lehetővé teszi, hogy az ismétlések többször is exportálhatók.

## <a name="may-7-2018"></a>2018. május 7.

- Előzetes verziójú objektumészlelési szolgáltatás bevezetése korlátozott próbaverziójú projektekhez.
- Frissítés 2.0 API-kra
- Az S0 szint kibővítve akár 250 címkére és 50 000 képre.
- Jelentős háttérbeli fejlesztések a képosztályozási projektek gépi tanulási folyamataiban. A 2018. április 27. után betanított projektek már kihasználják ezen frissítések előnyeit.
- Hozzáadott modellexportálás az ONNX-hez, Windows ML-el való használathoz.
- Hozzáadott modellexportálás a Dockerfile-hoz. Ez lehetővé teszi az összetevők, például a DockerFile, a TensorFlow-modell és a szolgáltatáskód letöltését saját Windows- vagy Linux-tárolók létrehozásához.
- Az olyan újonnan betanított modellek esetében, amelyek az általános (Compact) és a Landmark (Compact) tartományok TensorFlow lettek exportálva, a [középértékek mostantól (0, 0, 0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), az összes projekt konzisztenciája érdekében.

## <a name="march-1-2018"></a>2018. március 1.

- A fizetős előzetes verzió bekerült a Azure Portalba. Mostantól csatolhatók projektek az F0 (Ingyenes) vagy S0 (Standard) szintű Azure-erőforrásokhoz. S0 szintű projektek bevezetve, amelyek akár 100 címkét és 25 000 képet is engedélyeznek.
- Háttérbeli módosítások a gép tanulási folyamatban/normalizálási paraméterben. Ezzel a felhasználók jobban kezelhetik a pontosság-visszahívási előnyöket és hátrányokat a valószínűségi küszöbérték beállításakor. A módosítások részeként az alapértelmezett valószínűségi küszöbérték a CustomVision.ai portálon 50%-ra lett állítva.

## <a name="december-19-2017"></a>2017. december 19.

- Exportálás az Androidba (TensorFlow) a korábban kiadott, iOS-re (CoreML.) való exportálás mellett Ez lehetővé teszi egy betanított kompakt modell exportálását offline állapotba egy alkalmazásban.
- Kiskereskedelem és Tereptárgy „kompakt” tartományok hozzáadva, így elérhetővé válik bennük a modellexportálás.
- Az [1.2 Training API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) és az [1.1 Prediction API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164) kiadva. A frissített API-k támogatják a modellexportálást, illetve az új előrejelzési műveletet, amely nem menti a képeket az előrejelzések közé. Bevezetett kötegelt műveletek a Training API-ban.
- UX-finomhangolások, beleértve azt a lehetőséget, amellyel megtekinthető, hogy melyik tartomány használatával lett betanítva az ismétlés.
- Frissült a [C# SDK és minta](https://github.com/Microsoft/Cognitive-CustomVision-Windows).