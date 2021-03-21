---
title: Microsoft Translator szolgáltatás
titlesuffix: Azure Cognitive Services
description: A fordítót integrálhatja alkalmazásaiba, webhelyeire, eszközeibe és egyéb megoldásaiba a többnyelvű felhasználói élmény biztosítása érdekében.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: fordító, szöveg fordítása, gépi fordítás, fordítási szolgáltatás
ms.openlocfilehash: ec76aa7554110b7440eb825f2d5e86ae2da6baa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657722"
---
# <a name="what-is-the-translator-service"></a>Mi a Translator Service?

A Translator egy felhőalapú gépi fordítási szolgáltatás, amely az intelligens alkalmazások készítéséhez használt, a kognitív API-k [Cognitive Services](../../index.yml?panel=ai&pivot=products) családjának részét képezi. A Translator egyszerűen integrálható alkalmazásaiba, webhelyeire, eszközeibe és megoldásaiba. Lehetővé teszi, hogy a többnyelvű felhasználói élményt [90 nyelven és dialektusban](./language-support.md) adja hozzá, és bármilyen operációs rendszer használatával szöveges fordításhoz is használható legyen.

Ez a dokumentáció a következő cikk-típusokat tartalmazza:  

* [**A gyors**](quickstart-translator.md) üzembe helyezési útmutató végigvezeti Önt a szolgáltatásra irányuló kérések lépésein.  
* A [**útmutatók**](translator-how-to-signup.md) útmutatói a szolgáltatás részletesebb vagy testreszabott módokon történő használatára vonatkozó utasításokat tartalmaznak.  
* A [**fogalmak**](character-counts.md) részletesen ismertetik a szolgáltatás funkcióit és funkcióit.  
* Az [**oktatóanyagok**](tutorial-wpf-translation-csharp.md) már olyan útmutatók, amelyek bemutatják, hogyan használhatja a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.  


## <a name="about-microsoft-translator"></a>A Microsoft Translator ismertetése

A Translator számos Microsoft-terméket és-szolgáltatást felhasznál, és világszerte több ezer vállalat használja az alkalmazásaikban és munkafolyamataiban.

A fordító által működtetett beszéd fordítás az [Azure Speech Service](../speech-service/index.yml)-en keresztül is elérhető. Egyesíti a Translator Speech API és a Custom Speech Service funkcióit egy egységes és teljes mértékben testreszabható szolgáltatásban. 

## <a name="language-support"></a>Nyelvi támogatás

A fordító többnyelvű támogatást biztosít a szöveg fordításához, az íráshoz, a nyelvfelismerés és a szótárakhoz. A teljes listát a [nyelvi támogatást](language-support.md) ismertető szakaszban, vagy programozott módon a [REST API-t](./reference/v3-0-languages.md) megnyitva találja.  

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator – neurális gépi fordítás

A neurális gépi fordítás (Neural Machine Translation, NMT) az új etalon a minőségi, mesterséges intelligencián alapuló gépi fordítások területén. A korábbi statisztikai gépi fordítási (Statistical Machine Translation, SMT) technológia helyébe lép, amely a 2010-es évek közepére elérte a minősége felső korlátját.

Az NMT nem csupán a nyers fordítások pontozását tekintve biztosít jobb fordításokat az SMT-nél, hanem az általa fordított szövegek gördülékenyebbnek, illetve sokkal emberibbnek is hatnak. Ez a gördülékenység elsősorban annak köszönhető, hogy az NMT a mondat teljes kontextusát használja a szavak lefordításához. Az SMT ezzel szemben az adott szavak előtt és után álló néhány szóból álló közvetlen kontextust vette alapul.

Az NMT-modellek az API magját képezik, és a végfelhasználók számára nem láthatók. Ami észrevehető, az a fordítás minőségének javulása, különösen az olyan nyelvek esetében, mint a kínai, a japán vagy az arab.

További információ a [NMT működéséről](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>A fordítások javítása egyéni fordítóval

 A fordítói szolgáltatás [Egyéni fordítójának](customization.md)a segítségével testre szabhatja a neurális fordítási rendszerét, és javíthatja az adott terminológia és stílus fordítását.

A Custom Translator segítségével fordítási rendszereket építhet ki, amelyekkel kezelheti a saját vállalkozásában vagy iparágában használt terminológiát. A testreszabott fordítási rendszer könnyen integrálható a meglévő alkalmazásokkal, munkafolyamatokkal, webhelyekkel és eszközökkel a normál fordítón keresztül a category paraméter használatával.

## <a name="next-steps"></a>Következő lépések

- [Hozzon létre egy Translator Service](./translator-how-to-signup.md) -t a hozzáférési kulcsainak és végpontjának lekéréséhez.
- Próbálja ki [a](quickstart-translator.md) fordítói szolgáltatást gyorsan meghívó rövid útmutatóban.
- Az [API-referenciák](./reference/v3-0-reference.md) az API-k műszaki dokumentációját biztosítják.
- [Díjszabás részletei](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
