---
title: Az Azure Functions méretezése és üzemeltetése
description: Megtudhatja, hogyan választhat Azure Functions fogyasztási terv és a Prémium csomag között.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 490fa46deabc822e416705fe9bf9c5cdb58f8cd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936761"
---
# <a name="azure-functions-hosting-options"></a>Üzemeltetési lehetőségek Azure Functions

Ha az Azure-ban hoz létre egy Function alkalmazást, ki kell választania egy üzemeltetési csomagot az alkalmazáshoz. A Azure Functionshoz három alapszintű üzemeltetési csomag érhető el: a [fogyasztási csomag](consumption-plan.md), a [Prémium csomag](functions-premium-plan.md)és a [dedikált (App Service) csomag](dedicated-plan.md). Minden üzemeltetési csomag általánosan elérhető a Linux és a Windows rendszerű virtuális gépeken egyaránt.

A kiválasztott üzemeltetési csomag a következő viselkedéseket diktálja:

* A Function alkalmazás skálázása.
* Az egyes functions app-példányok számára elérhető erőforrások.
* Speciális funkciók támogatása, például Azure Virtual Network-kapcsolat.

Ez a cikk a különböző üzemeltetési csomagok, valamint a Kubernetes-alapú üzemeltetés részletes összehasonlítását ismerteti.

## <a name="overview-of-plans"></a>A csomagok áttekintése

A következő összefoglalás a függvények három fő üzemeltetési csomagjának előnyeit foglalja le:

| | |
| --- | --- |  
|**[Használatalapú csomag](consumption-plan.md)**| Automatikusan méretezhető, és csak a számítási erőforrásokért kell fizetnie, ha a függvények futnak.<br/><br/>A használati terv a függvények gazdagépének példányait dinamikusan hozzáadja és eltávolítja a bejövő események száma alapján.<br/><br/> ✔ Alapértelmezett üzemeltetési csomag.<br/>A ✔ csak akkor kell fizetnie, ha a függvények futnak.<br/>✔ A skálázást automatikusan, akár nagy terhelésű időszakok esetén is.|  
|**[Prémium szintű csomag](functions-premium-plan.md)**|Az előre betöltött feldolgozók által igénybe vehető, az üresjárat utáni késleltetést nem igénylő alkalmazásokat használó, automatikusan méretezhető, a virtuális hálózatokhoz való csatlakozást követően a rendszer az igények alapján automatikusan méretezi a méretezést. <br/><br/>Vegye figyelembe a Azure Functions prémium csomagot a következő helyzetekben: <br/><br/>✔ A Function apps folyamatosan, vagy majdnem folyamatosan fut.<br/>✔ Nagy mennyiségű kisméretű végrehajtás és egy magas végrehajtási számla van, de a használati terv alacsony GB-nál is kevesebb időt vesz igénybe.<br/>✔ Több CPU-vagy memória-beállításra van szüksége, mint amit a használati terv biztosít.<br/>✔ A kódnak hosszabb ideig kell futnia, mint a felhasználási tervben engedélyezett maximális végrehajtási idő.<br/>✔ Olyan funkciókat kell megkövetelni, amelyek nem érhetők el a használati tervben, például virtuális hálózati kapcsolaton keresztül.|  
|**[Dedikált terv](dedicated-plan.md)** |A függvényeket egy App Service csomagon belül futtathatja normál [app Service csomag díjszabásával](https://azure.microsoft.com/pricing/details/app-service/windows/).<br/><br/>A legjobb olyan hosszan futó helyzetekben, ahol a [Durable functions](durable/durable-functions-overview.md) nem használható. A következő helyzetekben vegye fontolóra App Service tervet:<br/><br/>✔ Rendelkezik olyan meglévő, nem használt virtuális gépekkel, amelyek már futtatnak más App Service példányokat.<br/>✔ Szeretné megadni a függvények futtatására szolgáló egyéni rendszerképet. <br/>✔ Prediktív skálázás és költségek szükségesek.|  

A cikkben szereplő összehasonlító táblázatok a következő üzemeltetési lehetőségeket is tartalmazzák, amelyek a legnagyobb mennyiségű vezérlést és elkülönítést biztosítják a Function Apps-alkalmazások futtatásához.  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | A App Service Environment (benyújtó) egy App Service funkció, amely teljesen elkülönített és dedikált környezetet biztosít a App Service alkalmazások biztonságos, nagy léptékű futtatásához.<br/><br/>A ASE megfelelőek a következőket igénylő alkalmazás-munkaterhelésekhez: <br/><br/>✔ Nagyon nagy léptékű.<br/>✔ A teljes számítási elkülönítést és a hálózati hozzáférés biztonságos elérését.<br/>✔ Nagy memóriahasználat.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | A Kubernetes egy teljesen elkülönített és dedikált környezetet biztosít, amely a Kubernetes platformon fut.<br/><br/> A Kubernetes a következőket igénylő alkalmazás-munkaterhelések esetén megfelelő: <br/>✔ Egyéni hardverkövetelmények.<br/>✔ Elkülönítés és biztonságos hálózati hozzáférés.<br/>✔ Lehetőség hibrid vagy többfelhős környezetben való futtatásra.<br/>✔ Fut a meglévő Kubernetes-alkalmazások és-szolgáltatások mellett.|  

A cikk többi táblázata összehasonlítja a különböző funkciókkal és viselkedésekkel kapcsolatos terveket. A dinamikus üzemeltetési csomagok (fogyasztás és prémium) közötti költség-összehasonlításért tekintse meg a [Azure functions díjszabási oldalát](https://azure.microsoft.com/pricing/details/functions/). A dedikált csomag különböző lehetőségeinek díjszabását a [app Service díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/app-service/windows/)tekintheti meg. 

## <a name="operating-systemruntime"></a>Operációs rendszer/futtatókörnyezet

Az alábbi táblázat a üzemeltetési csomagok támogatott operációs rendszer-és nyelvi futtatókörnyezet-támogatását mutatja be.

| | <sup>1</sup> . Linux<br/>Csak kód | Windows<sup>2</sup><br/>Csak kód | Linux<sup>1, 3</sup><br/>Docker-tároló |
| --- | --- | --- | --- |
| **[Használatalapú csomag](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Nincs támogatás  |
| **[Prémium szintű csomag](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Dedikált terv](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | n.a. | n.a. |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

a Python Runtime stack egyetlen támogatott operációs rendszer <sup>a Linux rendszerben</sup> . <br/>
<sup>2</sup> a Windows az egyetlen támogatott operációs rendszer a PowerShell futásidejű verem számára.<br/>
a <sup>3</sup> Linux az egyetlen támogatott operációs rendszer a Docker-tárolók számára.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>Méretezés

A következő táblázat összehasonlítja a különböző üzemeltetési csomagok skálázási viselkedését.

| | Horizontális felskálázás | Példányok maximális száma |
| --- | --- | --- |
| **[Használatalapú csomag](consumption-plan.md)** | [Esemény vezérelt](event-driven-scaling.md). Automatikus méretezés automatikusan, akár nagy terhelésű időszakok esetén is. Azure Functions az infrastruktúra a processzor-és memória-erőforrásokat úgy méretezi, hogy a funkciók gazdagépének további példányait adja hozzá a bejövő trigger eseményeinek száma alapján. | 200 |
| **[Prémium szintű csomag](functions-premium-plan.md)** | [Esemény vezérelt](event-driven-scaling.md). Automatikus méretezés automatikusan, akár nagy terhelésű időszakok esetén is. A Azure Functions infrastruktúra a függvények által aktivált események száma alapján méretezi a processzor-és memória-erőforrásokat a functions gazdagép további példányainak hozzáadásával. |100|
| **[Dedikált](dedicated-plan.md)**<sup>1</sup> . csomag | Manuális/automatikus méretezés |10-20|
| **[](dedicated-plan.md)**<sup>1</sup> . kiegészítő | Manuális/automatikus méretezés |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Eseményvezérelt, az [KEDA](https://keda.sh)-t használó Kubernetes-fürtök esetén a-alapú autoskálázás. | &nbsp; &nbsp; Fürttől függően változik&nbsp;&nbsp;|

<sup>1</sup> a különböző app Service-csomagokra vonatkozó beállításokra vonatkozó meghatározott korlátokat a [app Service csomag korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)című részben tekintheti meg.

## <a name="cold-start-behavior"></a>A hidegindító viselkedése

|    |    | 
| -- | -- |
| **[Felhasználási &nbsp; terv](consumption-plan.md)** | Az alkalmazások üresjárat esetén nullára méretezhetők, ami azt jelenti, hogy egyes kérések további késéssel is rendelkezhetnek indításkor.  A használati tervnek van néhány optimalizációja, amelyekkel csökkenthető a hideg kezdési idő, beleértve az olyan előre bemelegítő függvényekből való húzást is, amelyek már rendelkeznek a Function Host és a nyelvi folyamatok futtatásával. |
| **[Prémium szintű csomag](functions-premium-plan.md)** | Állandóan meleg példányok, hogy elkerülje a hideg indítást. |
| **[Dedikált terv](dedicated-plan.md)** | Ha dedikált csomagban fut, a functions-gazdagép folyamatosan futhat, ami azt jelenti, hogy a hideg indítás nem igazán jelent problémát. |
| **[ASE](dedicated-plan.md)** | Ha dedikált csomagban fut, a functions-gazdagép folyamatosan futhat, ami azt jelenti, hogy a hideg indítás nem igazán jelent problémát. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | A KEDA konfigurációjától függően az alkalmazások konfigurálható a hideg indítás elkerüléséhez. Ha úgy van konfigurálva, hogy nulla értékűre legyen állítva, akkor a rendszer az új események esetében egy hideg kezdést tapasztal. 

## <a name="service-limits"></a>Szolgáltatási korlátozások

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>Hálózatkezelési funkciók

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>Számlázás

| | | 
| --- | --- |
| **[Használatalapú csomag](consumption-plan.md)** | Csak a függvények futtatásának idejére kell fizetnie. A számlázás a végrehajtások száma, a végrehajtási idő és a felhasznált memória alapján történik. |
| **[Prémium szintű csomag](functions-premium-plan.md)** | A Prémium csomag a szükséges és az előre bemelegített példányok által használt fő másodpercek és memória számán alapul. A csomagoknak legalább egy példányát mindig melegen kell tartani. Ez a csomag a legelőre kiszámítható díjszabást biztosítja. |
| **[Dedikált csomag](dedicated-plan.md)* | Ugyanezt a funkciót a App Service csomagban lévő Function apps esetében is megfizeti, mint más App Service-erőforrásokhoz, például a webalkalmazásokhoz.|
| **[App Service Environment (bemutató)](dedicated-plan.md)** | Az infrastruktúráért fizetett és a közszolgáltatási infrastruktúra méretével nem módosítható a havi díj. App Service díjcsomag vCPU is. Egy ASE környezeten belül az összes üzemeltetett alkalmazás az elkülönített díjszabású termékváltozatba tartozik. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Csak a Kubernetes-fürt költségeit kell fizetnie; a függvények további számlázása nem történik meg. A Function alkalmazás alkalmazás-számítási feladatként fut a fürt tetején, ugyanúgy, mint egy normál alkalmazás. |

## <a name="next-steps"></a>Következő lépések

+ [Üzembe helyezési technológiák Azure Functions](functions-deployment-technologies.md) 
+ [Azure Functions – fejlesztői útmutató](functions-reference.md)