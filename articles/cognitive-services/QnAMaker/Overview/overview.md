---
title: Mi a QnA Maker?
titleSuffix: Azure Cognitive Services
description: A QnA Maker egy olyan felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát alkalmaz a felhasználó természetes nyelvi kérdéseinek lehető legjobb megválaszolására.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 04/05/2019
ms.author: tulasim
ms.openlocfilehash: 963769315302ba4e7d1600253b617c7cb0f02bc5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794258"
---
# <a name="what-is-qna-maker"></a>Mi a QnA Maker?

A QnA Maker egy olyan felhőalapú API-szolgáltatás, amely egy beszélgetésre szánt, kérdés és válasz típusú réteget hoz létre az adatok alapján. 

A QnA Maker lehetővé teszi egy tudásbázis létrehozását az olyan, félig strukturált tartalmakból, mint a Gyakori kérdések (GYIK) URL-címei, a termékútmutatók, a támogatási dokumentumok, illetve az egyéni kérdések és válaszok. A QnA Maker szolgáltatás egyezteti a felhasználók természetes nyelvi kérdéseit a Tudásbázisban szereplő kérdésekkel és válaszokkal, majd a lehető legjobban megfelelő választ adja vissza.

A könnyen kezelhető [webes portálon](https://qnamaker.ai) létrehozhat, felügyelhet, betaníthat és közzétehet szolgáltatásokat fejlesztői tapasztalat nélkül is. Miután közzétett egy szolgáltatást egy végponton, ügyfélalkalmazások, például csevegőrobotok beszélgethetnek a felhasználókkal, hogy megválaszolják a kérdéseiket. 

![Áttekintés](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>Fő QnA Maker-folyamatok

A QnA Maker két fő szolgáltatást biztosít az adatokhoz:

* **Kibontási**: Strukturált kérdés-válasz adatok ki kell olvasni a strukturált és részben strukturált [adatforrások](../Concepts/data-sources-supported.md) , például – gyakori kérdések és kézikönyvek. Ez a kinyerés végrehajtható a tudásbázis [létrehozásának](https://aka.ms/qnamaker-docs-createkb) részeként vagy azt követően, a szerkesztési folyamat részeként.

* **Egyező**: Miután a Tudásbázis lett [betanított és tesztelt](https://aka.ms/qnamaker-docs-trainkb), [közzététele](https://aka.ms/qnamaker-docs-publishkb) azt. Ez engedélyez egy végpontot a QnA Maker-tudásbázishoz, amely azután használható lesz a robotban vagy az ügyfélalkalmazásban. Ez a végpont fogadja a felhasználói kérdéseket, és válaszol a tudásbázisban található legjobb válasszal, mellékelve a találat megbízhatósági pontszámát is.

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>QnA Maker-architektúra

A QnA Maker-architektúra a következő két összetevőből áll:

1. **A QnA Maker szolgáltatások**: A felügyeleti élmény a QnA Maker Tudásbázis, amely tartalmazza a kezdeti létrehozása, frissítése, képzés és közzétételi. Ezek a tevékenységek a [portálon](https://qnamaker.ai) vagy a [kezelési API-kon](https://go.microsoft.com/fwlink/?linkid=2092179) keresztül végezhetők el. 

2. **A QnA Maker adatok és a futtatókörnyezet**: Ez a megadott régióban található Azure-előfizetésében üzembe helyezve. Tudásbázisainak tartalmát a rendszer az [Azure Search](https://azure.microsoft.com/services/search/) szolgáltatásban tárolja, és a végpontot [App Services-szolgáltatásként](https://azure.microsoft.com/services/app-service/) helyezi üzembe. Választhatja azt a lehetőséget is, hogy üzembe helyez egy [Application Insights](https://azure.microsoft.com/services/application-insights/)-erőforrást elemzési célból.

![Architektúra](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>A szolgáltatásokkal kapcsolatos legfontosabb tudnivalók

- Teljes **kód nélküli** felhasználói élményt [hozzon létre egy robot](../Quickstarts/create-publish-knowledge-base.md#create-a-bot) a Tudásbázis.
- **Nincs hálózati szabályozás az előrejelzések esetében**. A tranzakciók száma helyett a szolgáltatás üzemeltetéséért fizethet. További részleteket a [díjszabási oldalon](https://aka.ms/qnamaker-docs-pricing) talál.
- **Igény szerinti skálázás**. Válassza ki a forgatókönyvhöz illő egyéni összetevők megfelelő SKU-it. Lásd QnA Maker szolgáltatáshoz tartozó [kapacitás kiválasztásának módját](https://aka.ms/qnamaker-docs-capacity) ismertető részt.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker-szolgáltatás létrehozása](../how-to/set-up-qnamaker-service-azure.md)
