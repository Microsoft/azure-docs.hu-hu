---
title: Ajánlott eljárások
description: Ismerkedjen meg az ajánlott eljárásokkal és a gyakori hibaelhárítási forgatókönyvekkel a Azure App Service-ben futó alkalmazáshoz.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: 91fd974c730037907258cb4a670f6fa836bfda6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144881"
---
# <a name="best-practices-for-azure-app-service"></a>Gyakorlati tanácsok az Azure App Service-hez
Ez a cikk a [Azure app Service](./overview.md)használatának ajánlott eljárásait foglalja össze. 

## <a name="colocation"></a><a name="colocation"></a>Bérelt kiszolgálói
Ha olyan Azure-erőforrások, mint például egy webalkalmazás és egy adatbázis különböző régiókban találhatók, a következő hatásokkal járhat:

* Megnövekedett késés az erőforrások közötti kommunikációban
* Az [Azure díjszabási oldalán](https://azure.microsoft.com/pricing/details/data-transfers)feljegyzett, a kimenő adatforgalomra vonatkozó pénzügyi költségek.

Az azonos régióban található közös elhelyezés olyan Azure-erőforrások esetében ajánlott, mint például a webalkalmazások és a tartalom vagy az adatokat tároló adatbázis-vagy Storage-fiók. Erőforrások létrehozásakor ügyeljen arra, hogy ugyanabban az Azure-régióban legyenek, hacsak nincs konkrét üzleti vagy tervezési oka, hogy azok ne legyenek. App Service alkalmazást az adatbázissal megegyező régióba helyezheti át, ha a [app Service klónozási funkció](app-service-web-app-cloning.md) jelenleg elérhető a prémium szintű app Service csomag alkalmazásaiban.   

## <a name="when-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>Ha az alkalmazások a vártnál több memóriát használnak
Ha észreveszi, hogy egy alkalmazás a vártnál több memóriát használ fel, ahogy azt a figyelési vagy szolgáltatási javaslatok alapján jelezte, vegye figyelembe a [app Service automatikus gyógyulási funkciót](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Az automatikus javító funkció egyik beállítása a memória küszöbértékén alapuló egyéni műveleteket veszi igénybe. A műveletek az e-mail-értesítésekben a spektrumra terjednek ki, hogy a feldolgozói folyamat újrahasznosítása révén a rendszer a memórián keresztül, a helyszínen történő csökkentéssel Az automatikus javítás a web.configon keresztül és egy felhasználóbarát felhasználói felületen keresztül konfigurálható a [app Service támogatási hely bővítményének](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)jelen blogbejegyzésében leírtak szerint.   

## <a name="when-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>Ha az alkalmazások a vártnál több PROCESSZORt használnak
Ha észreveszi, hogy egy alkalmazás a vártnál több CPU-t használ, vagy a figyelési vagy szolgáltatási javaslatok alapján megismétli a CPU-tüskéket, vegye fontolóra a App Service terv vertikális felskálázását vagy horizontális felskálázását. Ha az alkalmazás állapot-nyilvántartó, a vertikális felskálázás az egyetlen lehetőség, míg ha az alkalmazás állapota állapot nélküli, a horizontális felskálázás nagyobb rugalmasságot és nagyobb méretezhetőséget biztosít. 

A "állapot nélküli" és "állapot nélküli" alkalmazásokkal kapcsolatos további információkért tekintse meg ezt a videót: [skálázható, végpontok közötti többrétegű alkalmazás tervezése Azure app Serviceon](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). A App Service skálázással és az automatikus skálázási lehetőségekkel kapcsolatos további információkért lásd: [webalkalmazás skálázása Azure app Serviceban](manage-scale-up.md).  

## <a name="when-socket-resources-are-exhausted"></a><a name="socketresources"></a>A szoftvercsatorna erőforrásainak kimerülése esetén
A kimenő TCP-kapcsolatok kimerítésének gyakori oka az ügyféloldali kódtárak használata, amelyek nem használhatók a TCP-kapcsolatok újrafelhasználására, vagy ha egy magasabb szintű protokoll, például a HTTP-Keep-Alive nincs használatban. Tekintse át a App Service-csomag alkalmazásai által hivatkozott könyvtárak dokumentációját, hogy a rendszer konfigurálja vagy hozzáférjen a kódban a kimenő kapcsolatok hatékony újrafelhasználásához. A kapcsolatok kiszivárgásának elkerülése érdekében kövesse a könyvtár dokumentációját is a megfelelő létrehozáshoz és felszabadításhoz, illetve a karbantartáshoz. Amíg az ügyfél-kódtárak vizsgálata folyamatban van, a hatás a több példányra történő horizontális felskálázással enyhíthető lehet.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js és kimenő HTTP-kérelmek
Node.js és számos kimenő HTTP-kérelem használata esetén fontos a HTTP-Keep-Alive kezelése. A [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` csomag használatával egyszerűbbé teheti a kódot.

Mindig kezelje a `http` választ, még akkor is, ha nem tesz semmit a kezelőben. Ha nem megfelelően kezeli a választ, az alkalmazás elakad, mert nem érhető el több szoftvercsatorna.

Például a vagy a csomag használata esetén `http` `https` :

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Ha App Service Linuxon fut egy több magot tartalmazó gépen, akkor egy másik ajánlott eljárás a PM2 használata több Node.js folyamat elindításához az alkalmazás végrehajtásához. Ezt megteheti egy indítási parancs megadásával a tárolóban.

Például négy példány elindításához:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="when-your-app-backup-starts-failing"></a><a name="appbackup"></a>Ha az alkalmazás biztonsági mentése meghiúsul
A két leggyakoribb ok, amiért az alkalmazás biztonsági mentése meghiúsul: érvénytelen tárolási beállítások és érvénytelen adatbázis-konfiguráció. Ezek a hibák általában akkor fordulnak elő, ha módosulnak a tárolási vagy adatbázis-erőforrások, illetve az ilyen erőforrások elérésének módosításai (például a biztonsági mentési beállításokban kiválasztott adatbázishoz tartozó hitelesítő adatok). A biztonsági mentések jellemzően ütemezés szerint futnak, és hozzáférést igényelnek a tárolóhoz (a mentett fájlok kivonásához) és az adatbázisokhoz (a biztonsági mentésbe foglalandó tartalmak másolásához és olvasásához). Az ilyen erőforrások elérésének meghiúsulása konzisztens biztonsági mentési hibát eredményezhet. 

Ha a biztonsági mentési hibák történnek, tekintse át a legutóbbi eredmények listáját, és Ismerje meg, hogy milyen típusú hiba történik. A tárolási hozzáférési hibák esetében tekintse át és frissítse a biztonsági mentési konfigurációban használt tárolási beállításokat. Adatbázis-hozzáférési hibák esetén tekintse át és frissítse a kapcsolatok karakterláncait az Alkalmazásbeállítások részeként; Ezután folytassa a biztonsági mentési konfiguráció frissítésével, hogy megfelelően tartalmazza a szükséges adatbázisokat. Az alkalmazások biztonsági mentésével kapcsolatos további információkért lásd: [webalkalmazás biztonsági mentése Azure app Serviceban](manage-backup.md).

## <a name="when-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>Új Node.js alkalmazások telepítésekor Azure App Service
Azure App Service Node.js alkalmazások alapértelmezett konfigurációja a leggyakoribb alkalmazások igényeinek legmegfelelőbb. Ha a Node.js-alkalmazás konfigurációja kihasználhatja a személyre szabott finomhangolást, hogy javítsa a teljesítményt vagy optimalizálja a CPU/memória/hálózati erőforrások erőforrás-felhasználását, tekintse [meg az ajánlott eljárásokat és a hibaelhárítási útmutatót a Node-alkalmazásokhoz a Azure app Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md) Ez a cikk ismerteti azokat a iisnode-beállításokat, amelyeket a Node.js alkalmazásához konfigurálnia kell, ismerteti az alkalmazás által megtekinthető különböző forgatókönyveket vagy problémákat, és bemutatja, hogyan kezelheti ezeket a problémákat.


## <a name="next-steps"></a>Következő lépések
Az ajánlott eljárásokkal kapcsolatos további információkért látogasson el a [app Service Diagnostics](./overview-diagnostics.md) webhelyre, ahol az erőforrásra vonatkozó, gyakorlatban alkalmazható ajánlott eljárásokat talál.

- Navigáljon a webalkalmazáshoz a [Azure Portal](https://portal.azure.com).
- Kattintson a bal oldali navigációs sávon található **problémák diagnosztizálásához és megoldásához** , amely megnyitja app Service diagnosztikát.
- Válassza az **ajánlott eljárások** Kezdőlap csempét.
- Kattintson az **ajánlott eljárások a rendelkezésre állás & teljesítmény** vagy **ajánlott eljárások az optimális konfigurációhoz** lehetőségre az alkalmazás aktuális állapotának megtekintéséhez az ajánlott eljárásokkal kapcsolatban.

Ezzel a hivatkozással közvetlenül is megnyithatja App Service diagnosztikát az erőforráshoz: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .