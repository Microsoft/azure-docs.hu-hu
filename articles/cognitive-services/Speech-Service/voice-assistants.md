---
title: Hangsegédek – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A hangsegédek funkcióinak, képességeinek és korlátozásainak áttekintése a Speech szoftverfejlesztői készlet (SDK) használatával.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: fc85eec008ef099d63d538e4871a1a84573f5a18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790445"
---
# <a name="what-is-a-voice-assistant"></a>Mi az a hangvezérelt asszisztens?

A beszédfelismerési szolgáltatást használó hangsegédek lehetővé teszik a fejlesztők számára, hogy természetes, emberi jellegű társalgási felületet hozzanak létre alkalmazásaikban és tapasztalataikban.

A hangsegéd szolgáltatás gyors, megbízható interakciót biztosít egy eszköz és egy asszisztens implementáció között, amely az (1) [közvetlen vonalas beszédet](direct-line-speech.md) (Azure bot Service) használja a hangképességek a botokhoz való hozzáadásához, vagy (2) a hangvezérlési forgatókönyvek egyéni parancsait.

## <a name="choosing-an-assistant-solution"></a>Asszisztensi megoldás kiválasztása

A hangsegéd létrehozásának első lépése, hogy eldöntse, mit kell tennie. A beszédfelismerési szolgáltatás több, egymást kiegészítő megoldást biztosít a Segéd interakcióinak kialakításához. Felvehet hang-és hangvételi képességeket a rugalmas és sokoldalú robotra, amely a [közvetlen vonalas beszélgetési](direct-line-speech.md) csatornán keresztül Azure bot Service használatával készült, vagy az egyszerű hangvezérlési forgatókönyvek esetében egyszerűvé teszi az [egyéni parancsok](custom-commands.md) alkalmazását.

| ha akarod... | Ezt követően tekintse meg... | Példa: |
|-------------------|------------------|----------------|
|Nyílt végű beszélgetés robusztus szakértelem-integrációval és teljes körű üzembe helyezési ellenőrzéssel | Azure Bot Service robot [közvetlen vonalas beszédfelismerési](direct-line-speech.md) csatornával | <ul><li>"Be kell menni a Seattle-be"</li><li>"Milyen típusú pizzát rendelhetek?"</li></ul>
|Hangvezérelt vagy egyszerű, feladathoz igazodó beszélgetések egyszerűsített szerzői és üzemeltetési szolgáltatásokkal | [Custom Commands](custom-commands.md) | <ul><li>"Az általános megvilágítás bekapcsolása"</li><li>"5 fokos meleg"</li><li>Az [itt elérhető](https://speech.microsoft.com/customcommands) egyéb minták</li></ul>

Javasoljuk, hogy a [közvetlen vonalas beszédfelismerést](direct-line-speech.md) a legjobb alapértelmezett beállításként válassza, ha még nem biztos benne, hogy mit szeretne kezelni. Integrálja az eszközöket és a szerzői támogatást, például a [virtuális asszisztensi megoldást és a vállalati sablont](/azure/bot-service/bot-builder-enterprise-template-overview) , valamint a [QnA Maker szolgáltatást](../qnamaker/overview/overview.md) , hogy közös mintákon építsen, és a meglévő tudásbázisokat használja.

Az [egyéni parancsok](custom-commands.md) megkönnyítik a hangvezérelt hangvezérelt alkalmazások készítését a hang-első interakciós élményhez. Egységes szerzői élményt, automatikus üzemeltetési modellt és viszonylag alacsony bonyolultságot biztosít, amely segít a legjobb megoldás kialakításában a hangvezérelt forgatókönyvekhez.

   ![Asszisztensi megoldások összehasonlítása](media/voice-assistants/assistant-solution-comparison.png "Asszisztensi megoldások összehasonlítása")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>Hivatkozási architektúra hangsegéd létrehozásához a Speech SDK használatával

   ![A hangsegéd-előkészítési szolgáltatás folyamatának fogalmi diagramja](media/voice-assistants/overview.png "A hangsegéd folyamata")

## <a name="core-features"></a>Alapvető funkciók

Akár [közvetlen vonalas beszédet](direct-line-speech.md) , akár [egyéni parancsokat](custom-commands.md) választ a Segéd-interakciók létrehozásához, a testreszabási funkciók széles választékát használhatja a Segéd testreszabásához a márka, a termék és a személyiség számára.

| Kategória | Funkciók |
|----------|----------|
|[Egyéni kulcsszó](./custom-keyword-basics.md) | A felhasználók olyan egyéni kulcsszóval indíthatnak el beszélgetéseket, mint például a "Hey contoso". Az alkalmazás a Speech SDK-ban egy egyéni kulcsszavas motorral rendelkezik, amely konfigurálható egy egyéni kulcsszóval, [amelyet itt is](./custom-keyword-basics.md)létrehozhat. A hangsegédek kiszolgálóoldali ellenőrzés használatával növelhetik a kulcsszó aktiválásának pontosságát (az eszköz helyett).
|[Beszéd szövege](speech-to-text.md) | A hangsegédek valós idejű hanganyagot alakítanak át felismert szöveggé a beszédfelismerési szolgáltatásból származó [beszéd – szöveg](speech-to-text.md) használatával. Ez a szöveg a Segéd implementációjában és az ügyfélalkalmazásban is elérhető.
|[Szöveg – beszéd](text-to-speech.md) | A Segédtől érkező szöveges válaszokat a beszédfelismerési szolgáltatás [szövegről beszédre](text-to-speech.md) történő felhasználásával szintetizálja. Ezt a szintézist ezután az ügyfélalkalmazás elérhetővé válik egy hangadatfolyamként. A Microsoft lehetőséget nyújt saját, kiváló minőségű neurális TTS-hang létrehozására, amely hangvételt biztosít a márka számára. További információért [vegye fel velünk a kapcsolatot](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Első lépések a hangsegédek használatába

Olyan gyors útmutatókat is kínálunk, amelyek kevesebb, mint 10 perc alatt futtatják a kódot. Ez a táblázat a hangsegéd-gyors üzembe helyezések listáját tartalmazza, nyelv szerint rendezve.

* [Gyors útmutató: egyéni hangsegéd létrehozása közvetlen vonalas beszédfelismerés használatával](quickstarts/voice-assistants.md)
* [Rövid útmutató: hangvezérlési alkalmazás létrehozása egyéni parancsokkal](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>Mintakód és oktatóanyagok

Hangsegéd létrehozásához használható mintakód a GitHubon érhető el. Ezek a minták lefedik az ügyfélalkalmazás számára a Segédhez való csatlakozást számos népszerű programozási nyelven.

* [Hangsegéd-minták a GitHubon](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [Oktatóanyag: a Azure Bot Service és a C# Speech SDK használatával létrehozott asszisztens hangfelismerése](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Oktatóanyag: egyéni parancsokat tartalmazó alkalmazás létrehozása egyszerű hangutasításokkal](./how-to-develop-custom-commands-application.md)

## <a name="customization"></a>Testreszabás

Az Azure Speech Services használatával létrehozott hangsegédek a testreszabási lehetőségek teljes skáláját használhatják.

* [Custom Speech](./custom-speech-overview.md)
* [Egyéni hang](how-to-custom-voice.md)
* [Egyéni kulcsszó](custom-keyword-overview.md)

> [!NOTE]
> A testreszabási lehetőségek nyelv/területi beállítás szerint változnak (lásd a [támogatott nyelveket](language-support.md)).

## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](overview.md#try-the-speech-service-for-free)
* [További információ az egyéni parancsokról](custom-commands.md)
* [További információ a közvetlen vonalas beszédről](direct-line-speech.md)
* [A Speech SDK beszerzése](speech-sdk.md)