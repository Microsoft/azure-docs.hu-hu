---
title: Mi a Custom Translator?
titleSuffix: Azure Cognitive Services
description: A Custom Translator hasonló funkciókat kínál a Microsoft Translator hub számára a statisztikai gépi fordításhoz (SMT), de kizárólag neurális gépi fordítási (NMT) rendszerekhez.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: lajanuar
ms.topic: overview
ms.openlocfilehash: 89ab25f45cd35a86d2ff4416ceb851ae6cd5e115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657161"
---
# <a name="what-is-custom-translator"></a>Mi a Custom Translator?

Az [Egyéni Translator](https://portal.customtranslator.azure.ai) a Microsoft Translator Service egyik funkciója, amely lehetővé teszi a fordító vállalatok, az alkalmazások fejlesztői és a nyelvi szolgáltatók számára a testreszabott neurális gépi fordítási (NMT-) rendszerek készítését. A testreszabott fordítási rendszerek zökkenőmentesen integrálva vannak a meglévő alkalmazásokkal, munkafolyamatokkal és webhelyekkel.

Az [Egyéni fordítóval](https://portal.customtranslator.azure.ai) létrehozott fordítási rendszerek ugyanazon a felhőalapú, [biztonságos](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), nagy teljesítményű, rugalmasan MÉRETEZHETŐ Microsoft Translator [text API v3](../reference/v3-0-translate.md?tabs=curl)-as verzión keresztül érhetők el, amely naponta több milliárd fordítást biztosít.

Az egyéni Translator több mint három tucat nyelvet támogat, és közvetlenül a NMT elérhető nyelvekhez nyújt térképeket. A teljes listát lásd:  [Microsoft Translator languages](../language-support.md#customization).

Ez a dokumentáció a következő cikk-típusokat tartalmazza:

* [**A gyors**](quickstart-build-deploy-custom-model.md) üzembe helyezési útmutató végigvezeti Önt a szolgáltatásra irányuló kérések lépésein.  
* A [**útmutatók**](how-to-create-project.md) útmutatásai a szolgáltatás részletesebb vagy testreszabott módokon történő használatára vonatkozó utasításokat tartalmaznak.  
* A [**fogalmak**](workspace-and-project.md) részletesen ismertetik a szolgáltatás funkcióit.  


## <a name="features"></a>Funkciók

Az egyéni Translator különböző funkciókat kínál az egyéni fordítási rendszerek létrehozásához és a későbbi hozzáféréshez.

|Szolgáltatás  |Leírás  |
|---------|---------|
|[Neurális gépi fordítási technológia alkalmazása](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Az egyéni fordító által biztosított neurális gépi fordítás (NMT) alkalmazásával javíthatja a fordítást.       |
|[Az üzleti terminológiát ismerő rendszerek létrehozása](what-are-parallel-documents.md)     |  A párhuzamos dokumentumok segítségével testre szabhatja és felépítheti a fordítási rendszereket, és megismerheti a saját üzleti és iparági terminológiáját.       |
|[Modellek készítése szótár használatával](what-is-dictionary.md)     |   Ha nem rendelkezik betanítási adatkészlettel, a modelleket csak a szótárakból álló adattal lehet betanítani.       |
|[Együttműködés másokkal](how-to-manage-settings.md#share-your-workspace)     |   Együttműködik a csapatával, és megoszthatja a munkáját a különböző személyekkel.     |
|[Hozzáférés az egyéni fordítási modellhez](../reference/v3-0-translate.md?tabs=curl)     |  Az egyéni fordítási modellt a meglévő alkalmazások/programok a Microsoft Translator Text API v3 segítségével érhetik el.       |

## <a name="get-better-translations"></a>Jobb fordítások

A Microsoft Translator 2016-ben kiadott egy [neurális gépi fordítást (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) . A NMT az iparági szabványnak megfelelő [statisztikai gépi fordítási (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) technológia keretében jelentős előrelépéseket biztosított a fordítási minőségben. Mivel a NMT jobban rögzíti a teljes mondatok kontextusát, mielőtt lefordítja őket, jobb minőségű, több emberi hanghatású és több Fluent fordítást biztosít. A [Custom Translator](https://portal.customtranslator.azure.ai) a jobb fordítási minőséget eredményező egyéni modellekhez biztosít NMT.

A korábban lefordított dokumentumok használatával fordítási rendszer hozható létre. Ezek a dokumentumok a tartományra jellemző terminológiát és stílust foglalják magukban, mint a szabványos fordítási rendszerek. A felhasználók az igazítás, a PDF, a LCL, a HTML, a HTM, a XLF, a TMX, a XLIFF, a TXT, a DOCX és az XLSX dokumentumokat tölthetik fel.

Az egyéni fordító a dokumentum szintjén párhuzamosan is elfogadja az adatgyűjtés és az előkészítés hatékonyságát. Ha a felhasználók több nyelven, de külön dokumentumokban is hozzáférnek ugyanahhoz a tartalomhoz, akkor az egyéni fordító automatikusan egyezteti a mondatokat a dokumentumok között.

Ha a megfelelő típusú és betanítási adatmennyiséget adja meg, nem ritka, hogy a [Bleu pontszám](what-is-bleu-score.md) nyeresége 5 és 10 pont közötti, egyéni fordító használatával.

## <a name="be-productive-and-cost-effective"></a>Hatékony és költséghatékony

Az [Egyéni fordítók](https://portal.customtranslator.azure.ai), az egyéni rendszerek betanítása és üzembe helyezése nem igényel programozási ismereteket.

A biztonságos [Egyéni Translator](https://portal.customtranslator.azure.ai) portál használatával a felhasználók az intuitív felhasználói felületen keresztül tölthetik fel a betanítási adatok, a rendszerek és a rendszerek tesztelésére szolgáló rendszereket, és üzembe helyezhetik őket éles környezetben. A rendszer ezután néhány órán belül elérhetővé válik a skálán (a tényleges idő a betanítási adatok méretétől függ).

Az [Egyéni fordító](https://portal.customtranslator.azure.ai) programozott módon is elérhető egy dedikált API-n keresztül (jelenleg előzetes [verzióban](https://custom-api.cognitive.microsofttranslator.com/swagger/) érhető el). Az API lehetővé teszi a felhasználók számára, hogy saját alkalmazáson vagy webszolgáltatáson keresztül kezelhetik a képzések létrehozását vagy frissítését.

A tartalmak fordítására szolgáló egyéni modell használatának díja a felhasználó Translator Text API díjszabási szintjétől függ. A díjszabási csomag részleteiért tekintse meg a Cognitive Services [Translator Text API díjszabási lapját](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) .

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Biztonságosan lefordítható bármikor, bárhol az alkalmazásokon és szolgáltatásokon

Az egyéni rendszerek zökkenőmentesen érhetők el és integrálhatók bármilyen termék-vagy üzleti munkafolyamatba, illetve bármely eszközön a Microsoft Translator Text API a szabványos REST-technológián keresztül.

## <a name="next-steps"></a>Következő lépések

- Olvassa át a [díjszabási részleteket](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- A [rövid útmutatóból](quickstart-build-deploy-custom-model.md) megtudhatja, hogyan hozhat létre fordítási modellt egyéni fordítóban.
