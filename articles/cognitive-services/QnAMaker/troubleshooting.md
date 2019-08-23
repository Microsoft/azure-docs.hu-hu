---
title: Hibaelhárítás – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker szolgáltatással kapcsolatos leggyakrabban felmerülő kérdések válogatott listája segít a szolgáltatás gyorsabb és jobb eredményekkel való elfogadásában.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b91adc9dd5f221a6d354a98bdc0f4a7fd4483ecb
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982351"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA Maker hibaelhárítása

A QnA Maker szolgáltatással kapcsolatos leggyakrabban felmerülő kérdések válogatott listája segít a szolgáltatás gyorsabb és jobb eredményekkel való elfogadásában.

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>A QnA Maker portál Súgó robotjának használata

A QnA Maker segítséget nyújt a QnA Maker portálon a segítségére. A Súgó robot minden weblapon elérhető. A robot a QnA Maker segítségével ad választ, és biztosítja a [ C# robot-keretrendszer kódjának projektjét](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) a robot számára, így gyorsan megkezdheti a saját válaszának használatát. 

![! [QnA Maker egy * * Súgó * * robotot biztosít a QnA Maker portálon, hogy segítsen.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>A Tudásbázis kezelése

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>A QnA Maker egy részét véletlenül törölt, Mit tegyek? 

Összes törlése végleges, beleértve a kérdés és válasz párt, fájlok, URL-címek, egyéni kérdéseket és válaszokat, tudásbázisok vagy Azure-erőforrások. Ellenőrizze, hogy a Tudásbázis a exportálja a **beállítások** oldalhoz, mielőtt bármelyik részét a Tudásbázis törlése. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Miért van saját URL-címe / fájl (ok) nem kinyerésekor kérdés-válasz párt?

Akkor lehet, hogy a QnA Maker nem válaszokat automatikusan kinyerni néhány kérdést és választ (kérdés-válasz) tartalom érvényes GYIK URL-címekről. Ezekben az esetekben egy .txt fájlban illessze be a szövegét, és tekintse meg, ha az eszköz betöltheti azt. Azt is megteheti, besorolást hozzáadhat tartalmat a Tudásbázis keretében a [QnA Maker portal](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Milyen méretű lehet a létrehozott tudásbázis?

A Tudásbázis mérete attól függ, hogy a Termékváltozat az Azure search úgy dönt, hogy a QnA Maker szolgáltatás létrehozásakor. Olvasási [Itt](./Tutorials/choosing-capacity-qnamaker-deployment.md) további részletekért.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Miért nem látom semmit a legördülő menü jelenik meg, hozzon létre egy új Tudásbázis?

Még nem hozott létre egyetlen QnA Maker szolgáltatást az Azure-ban még. Olvasási [Itt](./How-To/set-up-qnamaker-service-azure.md) megtudhatja, hogyan valósítható meg.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Hogyan oszthatom meg a Tudásbázis másokkal?

Megosztása a QnA Maker szolgáltatás szintjén működik, vagyis a szolgáltatásban az összes tudásbázisok meg lesz osztva. Olvasási [Itt](./How-To/collaborate-knowledge-base.md) hogyan működhet a Tudásbázis.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Megoszthat-e, amely nem szerepel az azonos AAD-bérlő módosítása Tudásbázis közreműködő Tudásbázis? 

Megosztás Azure szerepköralapú hozzáférés-vezérlés (RBAC) alapul. Ha megoszthatja _bármely_ erőforrás egy másik felhasználó az Azure-ban is megoszthatja a QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Ha egy App Service-csomag az 5 QnAMaker tudásbázisok. Is jogosultságokat rendelhet hozzá olvasási/írási 5 különböző felhasználók így azok férhetnek csak 1 QnAMaker Tudásbázis?

Egy teljes QnAMaker szolgáltatással, nem az egyéni tudásbázisok megoszthatja.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hogyan válthatok az alapértelmezett üzenetet, ha nem jó talál egyezést?

Az alapértelmezett üzenetet a beállításokat az App service része.
- Nyissa meg az Azure Portalon, az App service erőforrás

![az App Service qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kattintson a **beállítások** lehetőség

![qnamaker az App Service-beállítások](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Módosítsa a **DefaultAnswer** beállítás
- Indítsa újra az App service

![qnamaker az App Service-újraindítás](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Miért érdemes a SharePoint-hivatkozás nem első ki kell olvasni?

Lásd: [adatforrás-helyek](./Concepts/data-sources-supported.md#data-source-locations) további információt.

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>A Tudásbázisban végzett frissítések nem fognak megjelenni közzététele. Mi ennek az oka?

Minden szerkesztési művelet az egy tábla frissítése, tesztelési vagy beállítás kell megnyitása előtt közzé lehet tenni. Kattintson a **mentéséhez és a vonat** gomb minden szerkesztési művelet után.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>A Tudásbázis támogatja a részletes adattartalmat vagy multimédiás nem?

A tudásbázis támogatja a Markdown-formátumot. Azonban az URL-címek automatikus – kibontási korlátozott HTML-Markdown átalakítási képességek. Ha azt szeretné, teljes értékű Markdown használata, a tartalmat közvetlenül a tábla módosítása, vagy töltse fel a Tudásbázis a részletes tartalommal.

Multimédia, például a képek és videók, jelenleg nem támogatott.

### <a name="does-qna-maker-support-non-english-languages"></a>A QnA Maker támogatja a nem angol nyelvű?

További részletekre kíváncsi [támogatott nyelvek](./Overview/languages-supported.md).

Ha több nyelven elérhető tartalmait, mindenképpen hozzon létre egy külön szolgáltatás, az egyes nyelvekhez.

## <a name="manage-service"></a>Szolgáltatás kezelése

### <a name="when-should-i-restart-my-app-service"></a>Mikor kell újraindítani az app Service-ben? 

Az app service frissítése, ha a figyelmeztetés ikon mellett a Tudásbázis következő, a verzió értékét a **végpont kulcsok** tábla a **felhasználói beállítások** [oldal](https://www.qnamaker.ai/UserSettings).

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Mikor kell frissíteni a végpont kulcsaim?

Frissítse a végpont kulcsokat, ha azt gyanítja, hogy azok biztonsága sérült.

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>Használhatom ugyanazt az Azure Search erőforrást tudásbázisok több nyelv használatával?

Több nyelv és több tudásbázisok szeretné használni, a felhasználók számára, az egyes nyelvekhez tartozó QnA Maker erőforrás létrehozásához. Ez egy külön Azure Search-szolgáltatást hoz létre a nyelv alapján. Ettől eltérő nyelv tudásbázisok egy egyetlen az Azure search szolgáltatásban keverése csökkentett teljesítményű, az eredmények relevancia eredményez.

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>Hogyan módosíthatom a QnA Maker által használt Azure Search-erőforrás neve?

Az Azure Search-erőforrás neve a QnA Maker erőforrásnév bizonyos betűk hozzáfűzi a végén a rendszer. Így nehéz különbséget tenni a QnA Maker több keresési erőforrások között. Hozzon létre egy külön Azure Search-szolgáltatás (elnevezési azt a kívánt módon), és csatlakoztassa a kérdések és válaszok szolgáltatást. A lépések hasonlóak a lépéseket kell tennie hogy [frissítése az Azure Search](How-To/upgrade-qnamaker-service.md#upgrade-azure-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Ha QnA Maker visszaadja `Runtime core is not initialized,` , Hogyan javíthatom?

Lehet, hogy az App Service lemezterülete megtelt. A lemezterület kijavításának lépései:

1. A [Azure Portal](https://portal.azure.com)válassza ki a QnA Maker app Service-t, majd állítsa le a szolgáltatást.
1. Noha még mindig az App Service-ben van, válassza a **fejlesztői eszközök**, majd a **speciális eszközök**, majd a **tovább**lehetőséget. Ekkor megnyílik egy új böngészőablak.
1. Válassza a **hibakeresési konzol**, majd a **cmd** parancsot egy parancssori eszköz megnyitásához. 
1. Navigáljon a _site/wwwroot/az adat/QnAMaker/_ könyvtárhoz.
1. Távolítsa el az összes olyan mappát, `rd`amelynek a neve megkezdődik. 

    Ne **törölje** a következőket:

    * KbIdToRankerMappings. txt fájl
    * EndpointSettings. JSON fájl
    * EndpointKeys mappa 

1. Indítsa el az App Service-t.
1. A tudásbázishoz férhet hozzá, hogy ellenőrizze, működik-e most. 


## <a name="integrate-with-other-services-including-bots"></a>Integráció más szolgáltatásokkal, beleértve a robotok

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Szükséges a Bot Framework használata a QnA Maker használatához?

Nem, a [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) -t nem kell használnia a QnA Maker. QnA Maker azonban a [Azure bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)számos sablonjának egyikét kínálja. Bot Service lehetővé teszi, hogy a gyors intelligens robotfejlesztést keresztül a Microsoft Bot Framework, és hogy fut egy kiszolgáló nélküli környezetben.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Hogyan hozhatok létre egy új robotot a QnA Maker használatával?

Kövesse a [ez](./Tutorials/create-qna-bot.md) Robotjait létrehozása az Azure Bot Service dokumentációjában.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Hogyan használhat egy másik tudásbázist egy meglévő Azure bot Service-szel?

Az alábbi információkra van szüksége a Tudásbázisról:

* Tudásbázis-azonosító.
* Tudásbázis közzétett végpontjának állomásneve – a közzététel után a **Beállítások** lapon található.
* A Tudásbázis közzétett végponti kulcsa – a **Beállítások** lapon, a közzététel után. 

Ezekkel az információkkal a Azure Portal a robot app Service-t. A **Beállítások – > konfiguráció – > Alkalmazásbeállítások**területen módosítsa ezeket az értékeket.  

A Tudásbázis Endpoint Key címkéje `QnAAuthkey` az ABS szolgáltatásban található. 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Két vagy több ügyfélalkalmazás is megoszthat egy tudásbázist? 

Igen, a Tudásbázis tetszőleges számú ügyféltől kérdezhető le. Ha úgy tűnik, hogy a Tudásbázis válasza lassú vagy időtúllépést okoz, érdemes lehet frissíteni a tudásbázishoz társított app Service-szolgáltatási szintet.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Hogyan ágyazhatom be a QnA Maker szolgáltatást a honlapomba?

Kövesse az alábbi lépéseket a QnA Maker szolgáltatást webes csevegési vezérlőként beágyazása a webhely:

1. Hozzon létre a gyakori kérdések robot utasításait követve [Itt](./Tutorials/create-qna-bot.md).
2. A webes csevegési engedélyezése a lépéseket követve [Itt](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

## <a name="data-storage"></a>Adattárolás

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Milyen adatokat tárolja, és azt tárolására? 

A QnA Maker szolgáltatás létrehozásakor kiválasztott egy Azure-régióban. A tudásbázisok és a naplófájlok ebben a régióban vannak tárolva. 
