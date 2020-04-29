---
title: Mi a Translator Text API? – Translator Text API
titlesuffix: Azure Cognitive Services
description: A Translator Text API az alkalmazásaiba, webhelyeire, eszközeibe és egyéb megoldásaiba való integrálásával többnyelvű felhasználói felületeket hozhat létre.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 12/09/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: f6b55163440b25bf69c76e9b1217c439b0884d5d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78331042"
---
# <a name="what-is-the-translator-text-api"></a>Mi a Translator Text API?

A Translator Text API könnyedén integrálható az alkalmazásaiba, webhelyeire, eszközeibe és megoldásaiba. Lehetővé teszi többnyelvű felhasználói felületek hozzáadását [több mint 60 nyelven](languages.md), és bármely hardverplatformon és bármely operációs rendszerrel használható szöveg-szöveg vektorú nyelvi fordítások végrehajtásához.

A Translator Text API a felhőalapú gépi tanulási és mesterséges intelligenciára épülő algoritmusokat biztosító, a fejlesztési projektekben készen használható Azure [Cognitive Services API](https://docs.microsoft.com/azure/?pivot=products&panel=ai)-gyűjtemény részét képezi.

## <a name="about-microsoft-translator"></a>A Microsoft Translator ismertetése

A Microsoft Translator egy felhőalapú gépi fordítási szolgáltatás. A fő szolgáltatás a Translator Text API, amely a Microsoft számos termékét és szolgáltatását szolgálja ki, és amelyeket világszerte vállalkozások ezrei használnak alkalmazásaikban és munkafolyamataikban, hogy tartalmaikat a globális közönség elé tárják.

A Translator Text API által kiszolgált beszédfordítás a [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/) szolgáltatáson keresztül is elérhető. Egyesíti a Translator Speech API és a Custom Speech Service funkcióit egy egységes és teljes mértékben testreszabható szolgáltatásban.A Speech Service leváltja a Translator Speech API-t, amely 2019. október 15-től kezdve visszavonunk.

## <a name="language-support"></a>Nyelvi támogatás

A Microsoft Translator többnyelvű támogatást nyújt a fordításhoz, az átíráshoz, a nyelvfelismeréshez és a szótárakhoz. A teljes listát a [nyelvi támogatást](language-support.md) ismertető szakaszban, vagy programozott módon a [REST API-t](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) megnyitva találja.  

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator – neurális gépi fordítás

A neurális gépi fordítás (Neural Machine Translation, NMT) az új etalon a minőségi, mesterséges intelligencián alapuló gépi fordítások területén. A korábbi statisztikai gépi fordítási (Statistical Machine Translation, SMT) technológia helyébe lép, amely a 2010-es évek közepére elérte a minősége felső korlátját.

Az NMT nem csupán a nyers fordítások pontozását tekintve biztosít jobb fordításokat az SMT-nél, hanem az általa fordított szövegek gördülékenyebbnek, illetve sokkal emberibbnek is hatnak. Ez a gördülékenység elsősorban annak köszönhető, hogy az NMT a mondat teljes kontextusát használja a szavak lefordításához. Az SMT ezzel szemben az adott szavak előtt és után álló néhány szóból álló közvetlen kontextust vette alapul.

Az NMT-modellek az API magját képezik, és a végfelhasználók számára nem láthatók. Ami észrevehető, az a fordítás minőségének javulása, különösen az olyan nyelvek esetében, mint a kínai, a japán vagy az arab.

További információ [az NMT működéséről](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="language-customization"></a>Nyelvi testreszabás

Az alap Microsoft Translator szolgáltatás kiterjesztéseként a Custom Translator a Translator Text API-val együtt alkalmazva segít testre szabni a neurális fordítási rendszert, illetve feljavítani a fordításokat a fejlesztő egyedi terminológiájával és stílusával.

A Custom Translator használatával olyan fordítási rendszereket állíthat össze, amelyek kezelik a vállalkozásában vagy az iparágban alkalmazott terminológiát. A testre szabott fordítási rendszer ezután könnyen integrálható a meglévő alkalmazásaiba, munkafolyamataiba és webhelyeibe több eszköztípuson is, a rendes Microsoft Translator Text API-n keresztül, a „category” (kategória) paraméter használatával.

További információk [a nyelvi testreszabásról](customization.md).

## <a name="next-steps"></a>További lépések

- [Regisztráljon](translator-text-how-to-signup.md) egy hozzáférési kulcsra.
- Az [API-referenciák](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) az API-k műszaki dokumentációját biztosítják.
- [Díjszabás részletei](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
