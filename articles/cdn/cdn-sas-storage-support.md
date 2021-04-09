---
title: A Azure CDN használata SAS-vel | Microsoft Docs
description: Azure CDN támogatja a közös hozzáférésű aláírás (SAS) használatát, hogy korlátozott hozzáférést biztosítson a privát tárolók tárolójához.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: ccf55e0e3986de8afe23cb646d4df743b576900c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725322"
---
# <a name="using-azure-cdn-with-sas"></a>A Azure CDN használata SAS használatával

Amikor beállítja a Storage-fiókot az Azure Content Delivery Network (CDN) számára a tartalom gyorsítótárazásához, alapértelmezés szerint bárki, aki ismeri a Storage-tárolók URL-címét, hozzáférhet a feltöltött fájlokhoz. A Storage-fiókban lévő fájlok védetté tételéhez a Storage-tárolók nyilvánosról magánjellegűre való hozzáférését állíthatja be. Ha azonban így tesz, a fájlok eléréséhez senki sem fog tudni hozzáférni. 

Ha korlátozott hozzáférést szeretne adni privát tárolókhoz, használhatja Azure-tárfiókjának közös hozzáférésű jogosultságkód (SAS) funkcióját. A SAS olyan URI, amely az Azure Storage erőforrásainak korlátozott hozzáférési jogosultságát biztosítja anélkül, hogy közzétenné a fiók kulcsát. A biztonsági társítást olyan ügyfelek számára is megadhatja, akik nem bíznak a Storage-fiók kulcsával, hanem arra, akihez a hozzáférést delegálni szeretné bizonyos Storage-fiók erőforrásaihoz. Ha megosztott hozzáférési aláírási URI-t oszt ki ezekre az ügyfeleknek, egy adott időszakra vonatkozóan hozzáférést biztosít számukra egy erőforráshoz.
 
A SAS használatával különböző paramétereket adhat meg egy blobhoz, például az indítási és lejárati időt, az engedélyeket (olvasási/írási) és az IP-tartományokat. Ez a cikk azt ismerteti, hogyan használható az SAS a Azure CDNsal együtt. Az SAS-vel kapcsolatos további információkért, beleértve a létrehozási módját és paramétereit, lásd: [közös hozzáférésű aláírások (SAS) használata](../storage/common/storage-sas-overview.md).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>A Azure CDN beállítása a Storage SAS-vel való működéshez
A következő három lehetőség ajánlott az SAS Azure CDN használatával történő használatához. Az összes beállítás azt feltételezi, hogy már létrehozott egy működő SAS-t (lásd az előfeltételek című részt). 
 
### <a name="prerequisites"></a>Előfeltételek
A kezdéshez hozzon létre egy Storage-fiókot, majd hozzon létre egy SAS-t az eszközhöz. Kétféle tárolt hozzáférési aláírás hozható elő: egy szolgáltatás SAS vagy egy fiók SAS. További információ: [a közös hozzáférésű aláírások típusai](../storage/common/storage-sas-overview.md#types-of-shared-access-signatures).

Miután létrehozott egy SAS-jogkivonatot, a blob Storage-fájlhoz az URL-cím hozzáfűzésével férhet hozzá `?sv=<SAS token>` . Az URL-cím formátuma a következő: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Például:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

A paraméterek beállításával kapcsolatos további információkért lásd a [sas-paraméterekkel kapcsolatos szempontokat](#sas-parameter-considerations) és a [közös hozzáférésű aláírás paramétereit](../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works).

![CDN SAS-beállítások](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>1. lehetőség: a Azure CDN a blob Storage-ba való továbbítása az SAS használatával

Ez a legegyszerűbb beállítás, és egyetlen SAS-tokent használ, amelyet a rendszer a Azure CDNtól a forráskiszolgáló felé továbbít.
 
1. Válasszon ki egy végpontot, válassza a **gyorsítótárazási szabályok** lehetőséget, majd válassza az **összes egyedi URL-cím** gyorsítótárazása lehetőséget a **lekérdezési karakterlánc gyorsítótárazási** listájában.

    ![CDN-gyorsítótárazási szabályok](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Miután beállította az SAS-t a Storage-fiókjához, a fájl eléréséhez az SAS-tokent kell használnia a CDN-végpont és a forráskiszolgáló URL-címei alapján. 
   
   Az eredményül kapott CDN-végpont URL-címének formátuma a következő: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Például:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. A gyorsítótár időtartamának finomhangolásához használja a gyorsítótárazási szabályokat, vagy adja hozzá `Cache-Control` a fejléceket a forráskiszolgálón. Mivel a Azure CDN egyszerű lekérdezési karakterláncként kezeli az SAS-tokent, ajánlott eljárásként olyan gyorsítótárazási időtartamot kell beállítania, amely a SAS lejárati idején vagy azt megelőzően lejár. Ellenkező esetben, ha egy fájl gyorsítótárazása hosszabb ideig tart, mint az SAS aktív, előfordulhat, hogy a fájl elérhető lesz a Azure CDN a forráskiszolgáló lejárati idejének lejárta után. Ha ez a helyzet bekövetkezik, és a gyorsítótárazott fájl nem érhető el, el kell végeznie egy törlési műveletet a fájlon, hogy törölje azt a gyorsítótárból. A gyorsítótár időtartamának Azure CDNon való beállításával kapcsolatos további információkért lásd: [Azure CDN gyorsítótárazási viselkedés szabályozása gyorsítótárazási szabályokkal](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>2. lehetőség: rejtett CDN SAS-jogkivonat Újraírási szabály használatával
 
Ez a beállítás csak a **Verizon-profilokból származó Azure CDN Premium** esetén érhető el. Ezzel a beállítással biztonságossá teheti a blob Storage-t a forráskiszolgálón. Akkor érdemes ezt a beállítást használni, ha nincs szüksége konkrét hozzáférési korlátozásokra a fájlhoz, de szeretné megakadályozni, hogy a felhasználók közvetlenül hozzáférjenek a tárolási forráshoz a Azure CDN kiszervezési idejének javítása érdekében. A felhasználónak ismeretlen SAS-tokenre van szükség ahhoz, hogy bárki hozzáférjen a forrás-kiszolgáló megadott tárolójában található fájlokhoz. Az URL-re vonatkozó Újraírási szabály miatt azonban az SAS-token nem szükséges a CDN-végponton.
 
1. A [Rules Engine](./cdn-verizon-premium-rules-engine.md) használatával hozzon létre egy URL-átírási szabályt. Az új szabályok a propagálásig akár 4 órát is igénybe vehetik.

   ![CDN kezelése gomb](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN-szabályok motorja gomb](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   A következő minta URL-írási szabály egy reguláris kifejezési mintát használ egy rögzítési csoporttal és egy *sasstoragedemo* nevű végponttal:
   
   Forrás:   
   `(container1/.*)`


   Cél:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL-cím újraírása szabály – bal oldali ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ CDN URL-cím újraírása szabály – jobb](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Az új szabály aktívvá válása után bárki hozzáférhet a CDN-végpont megadott tárolójában lévő fájlokhoz, függetlenül attól, hogy SAS-jogkivonatot használnak-e az URL-címben. A formátum a következő: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Például:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. A gyorsítótár időtartamának finomhangolásához használja a gyorsítótárazási szabályokat, vagy adja hozzá `Cache-Control` a fejléceket a forráskiszolgálón. Mivel a Azure CDN egyszerű lekérdezési karakterláncként kezeli az SAS-tokent, ajánlott eljárásként olyan gyorsítótárazási időtartamot kell beállítania, amely a SAS lejárati idején vagy azt megelőzően lejár. Ellenkező esetben, ha egy fájl gyorsítótárazása hosszabb ideig tart, mint az SAS aktív, előfordulhat, hogy a fájl elérhető lesz a Azure CDN végpontból, miután a SAS lejárati ideje eltelt. Ha ez a helyzet bekövetkezik, és a gyorsítótárazott fájl nem érhető el, el kell végeznie egy törlési műveletet a fájlon, hogy törölje azt a gyorsítótárból. A gyorsítótár időtartamának Azure CDNon való beállításával kapcsolatos további információkért lásd: [Azure CDN gyorsítótárazási viselkedés szabályozása gyorsítótárazási szabályokkal](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>3. lehetőség: a CDN biztonsági jogkivonat hitelesítésének használata Újraírási szabállyal

Azure CDN biztonsági jogkivonat-hitelesítés használatához a Verizon-profilnak rendelkeznie kell egy **Azure CDN Premium** csomaggal. Ez a legbiztonságosabb és testreszabható megoldás. Az ügyfél-hozzáférés a biztonsági jogkivonaton beállított biztonsági paramétereken alapul. Miután létrehozta és beállította a biztonsági jogkivonatot, az összes CDN-végpont URL-címére szükség lesz. Az URL-re vonatkozó Újraírási szabály miatt azonban az SAS-token nem szükséges a CDN-végponton. Ha az SAS-token később érvénytelenné válik, Azure CDN többé nem fogja tudni újraérvényesíteni a tartalmat a forráskiszolgálón.

1. [Hozzon létre egy Azure CDN biztonsági jogkivonatot](./cdn-token-auth.md#setting-up-token-authentication) , és aktiválja azt a CDN-végponthoz tartozó szabályok motorja és az elérési út alapján, ahol a felhasználók hozzáférhetnek a fájlhoz.

   A biztonsági jogkivonat-végpont URL-címének formátuma a következő:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Például:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   A biztonsági jogkivonat hitelesítésének paraméterei eltérnek az SAS-token paramétereinek beállításaitól. Ha úgy dönt, hogy lejárati időt használ a biztonsági jogkivonat létrehozásakor, akkor azt ugyanarra az értékre kell állítania, mint az SAS-jogkivonat lejárati ideje. Így biztosítható, hogy a lejárati idő kiszámítható legyen. 
 
2. A [Rules Engine](./cdn-verizon-premium-rules-engine.md) használatával hozzon létre egy URL-átírási szabályt, amely lehetővé teszi, hogy az SAS-jogkivonat hozzáférhessen a tárolóban lévő összes blobhoz. Az új szabályok a propagálásig akár 4 órát is igénybe vehetik.

   A következő minta URL-írási szabály egy reguláris kifejezési mintát használ egy rögzítési csoporttal és egy *sasstoragedemo* nevű végponttal:
   
   Forrás:   
   `(container1/.*)`
   
   Cél:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL-cím újraírása szabály – bal oldali ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ CDN URL-cím újraírása szabály – jobb](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Ha megújítja az SAS-t, győződjön meg arról, hogy az URL-cím újraírása szabályt az új SAS-jogkivonattal frissíti. 

## <a name="sas-parameter-considerations"></a>SAS-paraméterek szempontjai

Mivel a SAS-paraméterek nem láthatók a Azure CDN számára, Azure CDN a rájuk vonatkozó kézbesítési viselkedését nem tudja módosítani. A definiált paraméterek korlátozásai csak azokra a kérelmekre érvényesek, amelyeket a Azure CDN a forráskiszolgálón, nem az ügyféltől érkező kérések Azure CDN. Ezt a különbséget fontos figyelembe venni az SAS-paraméterek beállításakor. Ha ezek a speciális funkciók szükségesek, és a [3. lehetőséget](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)használja, állítsa be a megfelelő korlátozásokat a Azure CDN biztonsági jogkivonatra.

| SAS-paraméter neve | Leírás |
| --- | --- |
| Indítás | Az az idő, ameddig Azure CDN megkezdődhet a blob-fájl elérésének ideje. Az óra torzítása miatt (ha egy órajel-jel különböző időpontokban érkezik a különböző összetevőknél), akkor a 15 perccel korábbi időpontot válassza, ha azt szeretné, hogy az eszköz azonnal elérhető legyen. |
| Befejezés | Az az idő, amely után a Azure CDN már nem fér hozzá a blob-fájlhoz. A korábban gyorsítótárazott fájlok Azure CDN továbbra is elérhetők. A fájl lejárati idejének szabályozásához állítsa be a megfelelő lejárati időt a Azure CDN biztonsági jogkivonatban, vagy Ürítse ki az eszközt. |
| Engedélyezett IP-címek | Választható. Ha a **verizon Azure CDNt** használja, ezt a paramétert beállíthatja a [Verizon Edge Server IP-címtartományok Azure CDN](./cdn-pop-list-api.md)által meghatározott tartományokra. Ha **Azure CDNt** használ a Akamai-ból, az IP-címtartományok paraméter nem állítható be, mert az IP-címek nem statikusak.|
| Engedélyezett protokollok | A fiók SAS-fiókjával végzett kérelem számára engedélyezett protokoll (ok). A HTTPS-beállítás ajánlott.|

## <a name="next-steps"></a>Következő lépések

Az SAS-vel kapcsolatos további információkért tekintse meg a következő cikkeket:
- [Közös hozzáférésű jogosultságkódok (SAS) használata](../storage/common/storage-sas-overview.md)
- [Közös hozzáférésű aláírások, 2. rész: SAS létrehozása és használata blob Storage-tárolóval](../storage/common/storage-sas-overview.md)

A jogkivonat-hitelesítés beállításával kapcsolatos további információkért lásd: [Azure Content Delivery Network-eszközök biztonságossá tétele jogkivonat-hitelesítéssel](./cdn-token-auth.md).