---
title: Üzemeltetett felhasználói felület konfigurálása Bing Custom Searchhoz | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Bing Custom Search üzemeltetett felhasználói felületének konfigurálására és integrálására használható.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: ca3f9da681c60608b5b196b17191f0b4d549f305
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96338367"
---
# <a name="configure-your-hosted-ui-experience"></a>Üzemeltetett felhasználói felület konfigurálása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Custom Search egy üzemeltetett felhasználói felületet biztosít, amellyel JavaScript-kódrészletként könnyedén integrálhatja weblapjait és webalkalmazásait. A Bing Custom Search portál használatával konfigurálhatja a felhasználói felület elrendezését, színét és keresési beállításait.



## <a name="configure-the-custom-hosted-ui"></a>Az egyéni tárolt felhasználói felület konfigurálása

A webalkalmazásokhoz tartozó üzemeltetett felhasználói felület konfigurálásához kövesse az alábbi lépéseket. A módosítások elvégzése után a jobb oldali ablaktábla a felhasználói felület előnézetét jeleníti meg. A megjelenített keresési eredmények nem tényleges eredmények a példányhoz.

1. Jelentkezzen be Bing Custom Search [portálra](https://customsearch.ai).  
  
2. Válassza ki Bing Custom Search példányát.

3. Kattintson a **Hosted UI** (Üzemeltetett felhasználói felület) lapra.  
  
4. Válasszon ki egy elrendezést.

    - Keresősáv és eredmények (alapértelmezett): megjelenít egy keresőmezőt az alatta lévő keresési eredményekkel.
    - Csak eredmények: csak a keresési eredményeket jeleníti meg, keresési mező nélkül. Az elrendezés használatakor meg kell adnia a keresési lekérdezést ( `&q=<query string>` ). Adja hozzá a lekérdezési paramétert a kérelem URL-címéhez a JavaScript-kódrészletben, vagy a HTML-végpont hivatkozását.
    - Felugró ablak: egy keresőmezőt tartalmaz, és megjeleníti a keresési eredményeket egy csúszó átfedésben.

5. Válasszon ki egy színtémát. Testreszabhatja a színeket úgy, hogy illeszkedjen az alkalmazáshoz a **téma testreszabása** lehetőségre kattintva. A szín módosításához adja meg a szín RGB HEXADECIMÁLIS értékét (például `#366eb8` ), vagy kattintson a szín előnézetére.

   A módosításokat a portál jobb oldalán tekintheti meg. Ha az **Alaphelyzetbe állítás** gombra kattint, a rendszer visszaállítja a módosításokat a kiválasztott téma alapértelmezett színeire.

   > [!NOTE]
   > A színek kiválasztásánál érdemes megfontolni a kisegítő lehetőségeket.

6. A **további konfigurációk** területen adja meg a megfelelő értékeket az alkalmazáshoz. Ezek a beállítások nem kötelezőek. Ha szeretné megtekinteni, hogy milyen hatással van a alkalmazásra vagy eltávolításra, tekintse meg a jobb oldali előnézet ablaktáblát. Az elérhető konfigurációs lehetőségek a következők:  

7. Adja meg a keresési előfizetés kulcsát, vagy válasszon egyet a legördülő listából. A legördülő lista az Azure-fiók előfizetéséhez tartozó kulcsokkal van feltöltve. Lásd: [Cognitive Services API-fiók](../cognitive-services-apis-create-account.md).  

8. Ha engedélyezte az automatikus kiegészítést, adja meg az automatikus javaslat előfizetési kulcsát, vagy válasszon egyet a legördülő listából. A legördülő lista az Azure-fiók előfizetéséhez tartozó kulcsokkal van feltöltve. Az egyéni automatikus kiegészítéshez egy adott előfizetési szintet kell megadni, lásd a [díjszabást](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Egyéni felhasználói felület használata

Az üzemeltetett felhasználói felületet a következő módokon használhatja: 

- A szkript belefoglalása a weblapra  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Vagy használja a következő URL-címet egy böngészőben.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Szükség szerint adja hozzá a következő lekérdezési paramétereket az URL-címhez. További információ ezekről a paraméterekről: [Custom Search API](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) -hivatkozás.
  >
  > - q
  > - mkt
  > - biztonságos keresési
  > - setlang

  > [!IMPORTANT]
  > A lap nem jelenítheti meg az adatvédelmi nyilatkozatát vagy más megjegyzéseit és feltételeit. A használatra való alkalmassága eltérő lehet.  

Ha további információkat szeretne, például az egyéni konfiguráció AZONOSÍTÓját, ugorjon a **végpontok** elemre az **éles** lapon.

## <a name="configuration-options"></a>Beállítási lehetőségek

Az üzemeltetett felhasználói felület működésének konfigurálásához kattintson a **további konfigurációk** lehetőségre, és adjon meg értékeket. Ezek a beállítások nem kötelezőek. Ha szeretné megtekinteni, hogy milyen hatással van a alkalmazásra vagy eltávolításra, tekintse meg a jobb oldali előnézet ablaktáblát. 

### <a name="web-search-configurations"></a>Webes keresési konfigurációk

- Webes eredmények engedélyezve: meghatározza, hogy engedélyezve van-e a webes keresés (a lap tetején megjelenik egy weblap)
- Automatikus kiegészítés engedélyezése: meghatározza, hogy engedélyezve van-e az egyéni automatikus kiegészítés (a további díjak [díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) ).
- Webes eredmények/oldal: a webes keresési eredmények száma, amely egyszerre jeleníthető meg (a maximális érték oldalanként 50).
- Kép felirata: meghatározza, hogy a képek megjelenjenek-e a keresési eredmények között.

A következő konfigurációk láthatók, ha a **speciális konfigurációk megjelenítése** lehetőségre kattint:

- Kiemelés szavai: meghatározza, hogy az eredmények félkövérrel jelennek-e meg keresési kifejezésekkel.
- Hivatkozás célja: meghatározza, hogy a weblap megjelenik-e egy új böngészőablakban (üres) vagy ugyanazon böngésző lapon (saját), amikor a felhasználó egy keresési eredményre kattint.

### <a name="image-search-configurations"></a>Képkeresési konfigurációk

- Képtalálatok engedélyezve: meghatározza, hogy engedélyezve van-e a képkeresés (a lap tetején megjelenik egy images lap).
- Képeredmények oldalanként: a képkeresés eredményeinek száma, amely egyszerre jeleníthető meg (a maximális érték oldalanként 150).

A következő konfiguráció jelenik meg, ha a **speciális konfigurációk megjelenítése** lehetőségre kattint.  
  
- Szűrők engedélyezése: hozzáadja azokat a szűrőket, amelyeket a felhasználó a Bing által visszaadott rendszerképek szűrésére használhat. A felhasználó például csak animált GIF-fájlok esetében szűrheti az eredményeket.

### <a name="video-search-configurations"></a>Videós keresési konfigurációk

- Videós eredmények engedélyezve: meghatározza, hogy engedélyezve van-e a videó keresése (a lap tetején megjelenik egy videó lap).
- Videós eredmények oldalanként: a videó keresési eredményeinek száma, amely egyszerre jeleníthető meg (a maximum 150 találat/oldal).

A következő konfiguráció jelenik meg, ha a **speciális konfigurációk megjelenítése** lehetőségre kattint.  
  
- Szűrők engedélyezése: hozzáadja azokat a szűrőket, amelyeket a felhasználó a Bing által visszaadott videók szűrésére használhat. A felhasználó például szűrheti a videók eredményeit az elmúlt 24 órában felderített adott felbontással vagy videókkal.

### <a name="miscellaneous-configurations"></a>Egyéb konfigurációk

- Oldal címe: a keresési eredmények lap cím területén megjelenő szöveg (nem az előugró elrendezéshez).
- Eszköztár témája: meghatározza a keresési eredmények oldal cím területének háttérszínét.

A következő konfigurációk láthatók, ha a **speciális konfigurációk megjelenítése** lehetőségre kattint.  

|1\. oszlop  |2\. oszlop  |
|---------|---------|
|Keresőmező szövegének helyőrzője   | A beviteli mezőben a bevitel előtt megjelenő szöveg.        |
|Cím hivatkozásának URL-címe    |A cím hivatkozásának célja         |
|Embléma URL-címe     | A cím mellett megjelenő rendszerkép         |
|Favicon    | Ikon jelenik meg a böngésző címsorában.          |

A következő konfigurációk csak akkor érvényesek, ha a futtatott felhasználói felületet a HTML-végponton keresztül használja (nem érvényesek a JavaScript-kódrészlet használatakor).

- Oldalcím
- Eszköztár témája
- Cím hivatkozásának URL-címe
- Embléma URL-címe
- Faviicon URL-címe  

## <a name="next-steps"></a>Következő lépések

- [Díszítő jelölők használata szövegkiemeléshez](../bing-web-search/hit-highlighting.md)
- [Weboldalak lapozása](../bing-web-search/paging-search-results.md)