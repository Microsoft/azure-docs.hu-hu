---
title: A Azure CDN számlázás ismertetése | Microsoft Docs
description: Ismerje meg az Azure Content Delivery Network által üzemeltetett tartalmak számlázási struktúráját, beleértve a számlázási régiókat, a kézbesítési díjakat és a költségek kezelését.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: allensu
ms.openlocfilehash: aa2f00a732a3978524fc017481285859c9535387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018681"
---
# <a name="understanding-azure-cdn-billing"></a>Az Azure CDN számlázásának ismertetése

Ez a gyakori kérdések az Azure Content Delivery Network (CDN) által üzemeltetett tartalmak számlázási szerkezetét ismertetik.

## <a name="what-is-a-billing-region"></a>Mi az a számlázási régió?
A számlázási régió egy földrajzi terület, amelynek alapján megállapítható, hogy milyen arányban kell fizetni az objektumok Azure CDNból való kézbesítésére. Az aktuális számlázási zónák és azok régiói a következők:

- 1. zóna: Észak-Amerika, Európa, Közel-Kelet és Afrika

- 2. zóna: Ázsia és a Csendes-óceáni térség (beleértve a Japánt)

- 3. zóna: Dél-Amerika

- 4. zóna: Ausztrália és Új-Zéland

- 5. zóna: India

További információ a pontok közötti (POP) régiókkal kapcsolatban: [Azure CDN pop-helyszínek régiónként](./cdn-pop-locations.md). A Mexikóban lévő POP például a Észak-Amerika régióban található, ezért az 1. zónában szerepel. 

További információ a Azure CDN díjszabásáról: [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Hogyan számítják ki a kézbesítési díjat régiónként?
A Azure CDN számlázási régió a forrás-kiszolgáló azon helyén alapul, amely a tartalmat a végfelhasználó számára kézbesíti. Az ügyfél célja (fizikai helye) nem tekintendő a számlázási régiónak.

Ha például egy Mexikóban található felhasználó kiadja a kérést, és ezt a kérést egy, az Egyesült Államok-előugróban található kiszolgáló szervizeli vagy forgalmi feltételek miatt, a számlázási régió lesz a Egyesült Államok.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Mi az a számlázható Azure CDN tranzakció?
Minden olyan HTTP-kérelem, amely a CDN-nél leáll, számlázható esemény, amely minden választ tartalmaz: sikeres, sikertelen vagy egyéb. Azonban a különböző válaszok különböző forgalmi mennyiségeket hozhatnak. Például a *304 nem módosult* , és a csak más fejlécekre adott válaszok kis forgalmat eredményeznek, mert kis fejlécre választanak. Hasonlóképpen, a hibaüzenetek (például a *404 nem található*) számlázva vannak, de a kis-és nagyszámú válasz miatt kisebb költségekkel járnak.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Milyen egyéb Azure-költségek vannak társítva Azure CDN használatra?
A Azure CDN használata az objektumok forrásaként használt szolgáltatások használati díját is megterheli. Ezek a költségek jellemzően a CDN teljes használati költségének kis hányadát jelentik.

Ha az Azure Blob Storage-t használja a tartalom forrásaként, a gyorsítótár kitöltéséhez a következő tárolási díjakat is fel kell vennie:

- Tényleges GB használatban: a forrásoldali objektumok tényleges tárterülete.

- Tranzakciók: a gyorsítótár kitöltéséhez szükséges módon.

- Adatátvitel GB-ban: a CDN-gyorsítótárak kitöltéséhez továbbított adatmennyiség.

> [!NOTE]
> Október 2019-től kezdődően, ha Azure CDNt használ a Microsofttól, az Azure-ból a CDN-re üzemeltetett adatátviteli költségek díjmentesek. A Verizon és a Akamai Azure CDN Azure CDN a lent ismertetett díjszabás hatálya alá tartozik.

Az Azure Storage számlázási szolgáltatásával kapcsolatos további információkért lásd: az [Azure Storage számlázásának ismertetése – sávszélesség, tranzakciók és kapacitás](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Ha *üzemeltetett szolgáltatás-kézbesítést* használ, a következő díjakat kell fizetnie:

- Azure számítási idő: a forrásként szolgáló számítási példányok.

- Az Azure számítási átadása: a számítási példányokból érkező adatforgalom a Azure CDN gyorsítótárak kitöltéséhez.

Ha az ügyfél bájt-tartományra vonatkozó kérelmeket használ (a forrástól függetlenül), a következő szempontokat kell figyelembe venni:

- A *byte-Range kérelem* egy számlázható tranzakció a CDN-ben. Ha egy ügyfél egy bájtos tartományra vonatkozó kérelmet ad ki, akkor ez a kérelem az objektum egy részhalmazára (tartományára) érvényes. A CDN csak a kért tartalom részleges részét válaszolja meg. Ez a részleges válasz egy számlázható tranzakció, és az átvitel összege a tartomány válaszának (plusz fejlécek) méretére korlátozódik.

- Ha egy kérelem egy objektum csak egy részére érkezik (egy byte-Range fejléc megadásával), akkor a CDN a teljes objektumot beolvashatja a gyorsítótárba. Ennek eredményeképpen annak ellenére, hogy a CDN-ből származó számlázható tranzakció részleges válasz, a forrástól származó számlázható tranzakció az objektum teljes méretét is magában foglalja.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Mennyi adatátviteli tevékenység történik a gyorsítótár támogatásához?
Minden alkalommal, amikor egy CDN-POP-nak ki kell töltenie a gyorsítótárát, kérést küld a gyorsítótárban lévő objektum eredetére. Ennek eredményeképpen a forrás számlázandó tranzakciót eredményezett minden egyes gyorsítótár-Kihagyás esetén. A gyorsítótár-kihagyók száma számos tényezőtől függ:

- A tartalom gyorsítótárazásának módja: Ha a tartalom magas ÉLETTARTAMmal (élettartammal)/Expiration, és gyakran van elérhető, így a gyorsítótárban népszerű marad, akkor a terhelés túlnyomó többségét a CDN kezeli. Egy tipikus jó gyorsítótár-találati arány 90%-os, ami azt jelenti, hogy az ügyfelek kéréseinek kevesebb mint 10%-át vissza kell térni a forráshoz, vagy a gyorsítótár-kihagyás vagy az objektum frissítése érdekében.

- Hány csomópontra van szükség az objektum betöltéséhez: minden alkalommal, amikor egy csomópont betölt egy objektumot a forrásból, egy számlázható tranzakciót használ. Ennek eredményeképpen a több csomópontból elérhető globális tartalom több számlázható tranzakciót eredményez.

- TTL-befolyás: egy objektum magasabb ÉLETTARTAMa azt jelenti, hogy a forrást ritkábban kell beolvasni. Ez azt is jelenti, hogy az ügyfelek (például a böngészők) továbbra is gyorsítótárazzák az objektumot, ami csökkentheti a tranzakciókat a CDN-nek.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Mely származási szolgáltatások jogosultak a Microsoft Azure CDN való ingyenes adatátvitelre? 
Ha a következő Azure-szolgáltatások egyikét használja CDN-forrásként, nem számítunk fel díjat a forrásról a CDN-durranás felé irányuló adatátvitel alól. 

- Azure Storage
- Azure Media Services
- Azure Virtual Machines
- Virtual Network
- Load Balancer
- Application Gateway
- Azure DNS
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Azure app Service
- Azure Functions
- Azure Data Factory
- Azure API Management
- Azure Batch 
- Azure Adatkezelő
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Azure SQL Database
- Felügyelt Azure SQL-példány
- Azure Cache for Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Hogyan a költségek leghatékonyabban kezelhetők?
Állítsa be a leghosszabb ÉLETTARTAMot a tartalomban.