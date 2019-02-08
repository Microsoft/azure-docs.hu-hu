---
title: Kibocsátási megjegyzések – Custom Vision Service
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: anroth
ms.openlocfilehash: b712f47fe9272e0ae6ccb9ab9847462729434698
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894411"
---
# <a name="custom-vision-service-release-notes"></a>Custom Vision Service kibocsátási megjegyzései

## <a name="january-22-2019"></a>2019. január 22.
- Támogatás hozzáadva az új Azure-régió: 2. nyugati RÉGIÓJA, USA keleti RÉGIÓJA, USA 2. keleti régiója, Nyugat-Európa, Észak-Európa, Délkelet-Ázsia, Kelet-Ausztrália, közép-India, Egyesült Királyság déli régiója, kelet-japán és USA északi középső RÉGIÓJA. Támogatási továbbra is fennáll, az USA déli középső Régiójában. 

## <a name="december-12-2018"></a>2018. december 12.
- Exportálás támogatása Objektumfelismerés modellek (bevezetett objektum észlelési kompakt tartomány).
- Rögzített számos továbbfejlesztett képernyőolvasó és billentyűzet navigációs támogatási problémákat. 
- UX frissítéseket képmegtekintő és a továbbfejlesztett címkézés gyorsabb címkézésre élmény objektumfelismerés.  
- Frissített alapszintű modell objektum észlelési tartomány jobb minőségű objektum észleléséhez. 
- Bugfixes.

## <a name="november-6-2018"></a>2018. november 6.
- Támogatás hozzáadva a embléma tartományt az Objektumfelismerés.

## <a name="october-9-2018"></a>2018. október 9.
- Objektum észlelési fizetett előzetes verzióként kerül. Mostantól létrehozhat Objektumfelismerés projektek és a egy Azure-erőforrás.
- A hozzáadott "Áthelyezése az Azure-bA" funkció webhelyre, hogy egyszerűbb legyen a korlátozott próbaverzió projektek az Azure-frissítése. erőforrás társított project (F0 vagy S0.) Megtalálhatja a beállítások lapon a termékhez.  
- ONNX 1.2 Windows 2018. október frissítés verzióját, a Windows ML hozzáadott exportálás.
Bugfixes, beleértve az ONNX-exportálás a különleges karakterek. 

## <a name="august-14-2018"></a>2018. augusztus 14.
- A hozzáadott "Első lépések" widget végigvezetik a felhasználókat szóló customvision.ai helyet. 
- A machine learning-folyamat számára, hogy a multilabel projektek (új adatveszteség réteg) kapcsolatos további fejlesztések.

## <a name="june-28-2018"></a>2018. június 28.
- Bugfixes & háttérrendszer fejlesztései.
- Engedélyezett Többosztályos osztályozási projektek, ahol a képek pontosan egy címkével rendelkezik. Az előrejelzések multiclass módhoz, valószínűségek fog összeg egy (az összes rendszerkép sorolják be a megadott címkék között).

## <a name="june-13-2018"></a>2018. június 13.
- UX frissítés, a könnyű használat és a kisegítő lehetőségek összpontosít. 
- A machine learning-folyamat számára, hogy a címkék nagy számú multilabel projektek fejlesztései.
- Kijavítva a hiba, a TensorFlow-exportálás. Engedélyezve van a exportált modell verziószámozás, így az ismétlések egynél többször is exportálható. 

## <a name="may-7-2018"></a>2018. május 7.
- Előzetes verziójú objektumészlelési szolgáltatás bevezetése korlátozott próbaverziójú projektekhez.
- Frissítés 2.0 API-kra
- Az S0 szint kibővítve akár 250 címkére és 50 000 képre. 
- Jelentős háttérbeli fejlesztések a képosztályozási projektek gépi tanulási folyamataiban. A 2018. április 27. után betanított projektek már kihasználják ezen frissítések előnyeit.
- Hozzáadott modellexportálás az ONNX-hez, Windows ML-el való használathoz.
- Hozzáadott modellexportálás a Dockerfile-hoz. Ez lehetővé teszi az összetevők, például a DockerFile, a TensorFlow-modell és a szolgáltatáskód letöltését saját Windows- vagy Linux-tárolók létrehozásához. 
- Az újonnan betanított modellek az általános (CD) és tereptárgyak (CD) tartományokban, TensorFlow exportált [értékek jelenti azt, hogy immár (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), az összes projekt belüli konzisztencia biztosításához. 

## <a name="march-1-2018"></a>2018. március 1.
- A fizetős előzetes verzió elérhetővé válik az Azure Portalon. Mostantól csatolhatók projektek az F0 (Ingyenes) vagy S0 (Standard) szintű Azure-erőforrásokhoz. S0 szintű projektek bevezetve, amelyek akár 100 címkét és 25 000 képet is engedélyeznek. 
- Háttérbeli módosítások a gép tanulási folyamatban/normalizálási paraméterben. Ezzel a felhasználók jobban kezelhetik a pontosság-visszahívási előnyöket és hátrányokat a valószínűségi küszöbérték beállításakor. A módosítások részeként az alapértelmezett valószínűségi küszöbérték a CustomVision.ai portálon 50%-ra lett állítva.

## <a name="december-19-2017"></a>2017. december 19.

- Hozzáadott exportálás Androidba (TensorFlow), a korábban kiadott, iOS-be történő (CoreML) exportálási lehetőség mellett. Ez lehetővé teszi, hogy egy betanított kompakt modell exportálása offline fusson egy alkalmazásban.
- Kiskereskedelem és Tereptárgy „kompakt” tartományok hozzáadva, így elérhetővé válik bennük a modellexportálás.
- Az [1.2 Training API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) és az [1.1 Prediction API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164) kiadva. A frissített API-k támogatják a modellexportálást, illetve az új előrejelzési műveletet, amely nem menti a képeket az előrejelzések közé. Bevezetett kötegelt műveletek a Training API-ban.
- UX-finomhangolások, beleértve azt a lehetőséget, amellyel megtekinthető, hogy melyik tartomány használatával lett betanítva az ismétlés.
- Frissült a [C# SDK és minta](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

