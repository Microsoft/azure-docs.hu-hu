---
title: Többlépéses webes tesztek figyelése – Azure Application Insights
description: Többlépéses webes tesztek beállítása a webalkalmazások Azure-beli figyeléséhez Application Insights
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 1d3597eaf54c40fb1f986d822af0dd6b8c8a7b2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719848"
---
# <a name="multi-step-web-tests"></a>Többlépéses webes tesztek

A webhelyeken a többlépéses webes tesztek segítségével figyelheti az URL-címek és interakciók rögzített sorozatát. Ebből a cikkből megtudhatja, hogyan hozhat létre többlépéses webes tesztet a Visual Studio Enterprise használatával.

> [!NOTE]
> A többlépéses webes tesztek a Visual Studio webtest-fájloktól függenek. A rendszer [bejelentette](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) , hogy a Visual Studio 2019 a webteszt funkciójának utolsó verziója. Fontos tisztában lenni azzal, hogy a Visual Studio 2019-es webteszt funkciója továbbra is támogatott, és a termék támogatási életciklusa során továbbra is támogatott lesz. A Azure Monitor termék csapata a több lépésből álló rendelkezésre állási tesztek jövőjével kapcsolatos [](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)kérdéseket tárgyal.  
> </br>
> A többlépéses webes tesztek **nem támogatottak** a [Azure Government](../../azure-government/index.yml) felhőben.


## <a name="pre-requisites"></a>Előfeltételek

* Visual Studio 2017 Enterprise vagy újabb.
* A Visual Studio webes teljesítmény-és terhelés-tesztelési eszközei.

Az előfeltételként szolgáló tesztelési eszközök megkeresése. Indítsa el a **Visual Studio telepítő**  >  **egyes összetevőit**  >  **, és tesztelje** a  >  **webes teljesítményt és a terheléses tesztelési eszközöket**.

![Képernyőfelvétel a Visual Studio telepítő felhasználói felületéről a webes teljesítmény és a terhelés-tesztelési eszközök elem melletti jelölőnégyzettel jelölt egyedi összetevőkkel](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> A több lépésből álló webes tesztek további költségekkel is rendelkeznek. További információkért tekintse meg a [hivatalos díjszabási útmutatót](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Többlépéses webes teszt rögzítése 

> [!WARNING]
> A több lépésből álló rögzítő használata már nem ajánlott. A rögzítő a statikus HTML-lapokra, alapszintű interakciókkal lett kifejlesztve, és nem biztosít funkcionális élményt a modern weblapokhoz.

A Visual Studio webes tesztek létrehozásával kapcsolatos útmutatásért tekintse meg a [hivatalos Visual studio 2019 dokumentációját](/visualstudio/test/how-to-create-a-web-service-test).

## <a name="upload-the-web-test"></a>A webes teszt feltöltése

1. A rendelkezésre állás ablaktáblán a Application Insights portálon válassza a **tesztelési**  >  **teszt típusa**  >  **többlépéses webes teszt** lehetőséget.

2. A tesztelési helyszínek, a gyakoriság és a riasztás paramétereinek megadása.

### <a name="frequency--location"></a>Gyakoriság & helye

|Beállítás| Magyarázat
|----|----|----|
|**Tesztelési gyakoriság**| Beállítja, hogy a teszt milyen gyakran fusson az egyes tesztelési helyekről. Öt perces alapértelmezett gyakorisággal és öt teszthellyel a helyén átlagosan percenként egy teszt történik.|
|**Tesztelési helyszínek**| Azok a helyek, ahonnan a kiszolgálók webes kéréseket küldenek az URL-címre. Az **ajánlott tesztelési helyszínek minimális száma öt** annak biztosítására, hogy a hálózati problémák alapján megkülönböztetni tudja a webhelyén felmerülő problémákat. Legfeljebb 16 hely választható ki.

### <a name="success-criteria"></a>Sikerességi feltételek

|Beállítás| Magyarázat
|----|----|----|
| **Teszt időtúllépése** |Csökkentse ezt az értéket, hogy figyelmeztessen a lassú válaszokra. A teszt akkor sikertelen, ha nem érkeznek meg a webhely válaszai ezen az időtartamon belül. Ha bejelölte a **Függő kérelmek elemzése** lehetőséget, akkor az összes képnek, stílusfájlnak, szkriptnek és más függő erőforrásnak meg kell érkeznie ezen az időn belül.|
| **HTTP-válasz** | A visszaadott állapotkód, amely sikeresnek számít. A 200-as kód jelzi, hogy normál weblap lett visszaküldve.|
| **Tartalom egyezése** | Egy karakterlánc, például "Welcome!" Teszteljük, hogy minden válaszban előfordul-e a kis- és nagybetűket figyelembe véve is pontos egyezés. Egyszerű sztringnek kell lennie helyettesítő karakterek nélkül. Ne feledje, hogy ha a laptartalom megváltozik, lehet, hogy ezt is frissíteni kell. **A tartalmi egyezés csak az angol karaktereket támogatja** |

### <a name="alerts"></a>Riasztások

|Beállítás| Magyarázat
|----|----|----|
|**Közel valós idejű (előzetes verzió)** | Javasoljuk, hogy a közel valós idejű riasztásokat használja. Az ilyen típusú riasztások konfigurálása a rendelkezésre állási teszt létrehozása után történik.  |
|**Riasztási hely küszöbértéke**|Legalább 3/5 helyet ajánlunk. A riasztási hely küszöbértéke és a tesztelési helyek száma közötti optimális kapcsolat a **riasztási hely küszöbértéke**  =  **– 2, legalább öt tesztelési hely.**|

## <a name="configuration"></a>Konfiguráció

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Idő és véletlenszerű számok csatlakoztatása a teszthez

Tegyük fel, hogy egy olyan eszközt tesztel, amely időfüggő adatokat fogad, például részvényeket egy külső adatcsatornától. A webes teszt rögzítésekor meghatározott időpontokat kell használni, de ezeket a teszt StartTime és EndTime paramétereiként kell beállítani.

![A félelmetes Stock alkalmazás képernyőképe](./media/availability-multistep/app-insights-72webtest-parameters.png)

A teszt futtatásakor az EndTime paraméternek mindig az aktuális időnek kell lennie, a StartTime paraméternek pedig a 15 perccel korábbi időnek.

A webes tesztelési idő beépülő modulja lehetővé teszi az parametrizálja-időpontok kezelését.

1. Adjon hozzá egy webes teszt beépülő modult minden kívánt változóparaméter-értékhez. A webes teszt eszköztárában válassza ki a **Webes teszt beépülő modul hozzáadása** elemet.
    
    ![Webes teszt beépülő modul hozzáadása](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    Ebben a példában a Dátum és idő beépülő modul két példányát használjuk. Az egyik példány a „15 perccel ezelőtt” a másik pedig a „most” paraméterértékhez tartozik.

2. Nyissa meg a beépülő modulok tulajdonságait. Adjon meg egy nevet, és állítsa be úgy, hogy az aktuális időt használja. Az egyiknél állítsa be a következőt: Add Minutes = -15.

    ![Környezeti paraméterek](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. A webes teszt paramétereiben a beépülő modulok nevére a {{plug-in name}} segítségével hivatkozzon.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Ezután töltse fel a tesztet a portálra. Ez a teszt minden futtatásánál a dinamikus értékeket fogja használni.

### <a name="dealing-with-sign-in"></a>A bejelentkezés kezelése

Ha a felhasználók bejelentkeznek az alkalmazásába, számos lehetősége van a bejelentkezés szimulálására a bejelentkezés után megjelenő lapok teszteléséhez. A használt megközelítés az alkalmazás által kínált biztonság típusától függ.

Minden esetben ajánlott létrehozni egy fiókot az alkalmazásában tesztelési célra. Ha lehetséges, korlátozza az engedélyt ezen a tesztfiókon, így elkerülheti, hogy a webes tesztek hatással legyenek a tényleges felhasználókra.

**Egyszerű felhasználónév és jelszó** A szokásos módon rögzíthet egy webes tesztet. Először törölje a cookie-kat.

**SAML-hitelesítés**

|Tulajdonság neve| Description|
|----|-----|
| Célközönség URI-ja | Az SAML-jogkivonat célközönségének URI-ja.  Ez a Access Control Service (ACS) URI-ja – beleértve az ACS-névteret és a gazdagép nevét. |
| Tanúsítvány jelszava | Az ügyféltanúsítvány jelszava, amely hozzáférést biztosít a beágyazott titkos kulcshoz. |
| Ügyféltanúsítvány  | Az ügyféltanúsítvány értéke Base64 kódolású formátumú titkos kulccsal. |
| Névazonosító | A jogkivonat nevének azonosítója |
| Lejárati idő | Az a TimeSpan, amelyhez a jogkivonat érvényes lesz.  Az alapértelmezett érték 5 perc. |
| Hatálybalépési idő | Az a TimeSpan, amelyhez a múltban létrehozott jogkivonat érvényes lesz (az időeltérések megcímzéséhez).  Az alapértelmezett érték (negatív) 5 perc. |
| Cél környezeti paraméter neve | A generált állítást fogadó környezeti paraméter. |


**Ügyfél titka** Ha az alkalmazás olyan bejelentkezési útvonallal rendelkezik, amely egy ügyfél titkos kulcsát is magában foglalja, használja ezt az útvonalat. Az Azure Active Directory (AAD) mint egy példa olyan szolgáltatásra, amely titkos ügyfélkulccsal történő bejelentkezést biztosít. Az AAD-ben a titkos ügyfélkulcs az alkalmazáskulcs.

Itt egy alkalmazáskulcsot használó Azure-webalkalmazás webes tesztelésre találhat példát:

![Minta képernyőkép](./media/availability-multistep/client-secret.png)

Szerezze be a tokent az AAD-ből a titkos ügyfélkulcs használatával (AppKey).
Nyerje ki a tulajdonosi jogkivonatot a válaszból.
Az engedélyezési fejléc tulajdonosi jogkivonatával hívja meg az API-t.
Győződjön meg arról, hogy a webes teszt tényleges ügyfél, azaz saját alkalmazással rendelkezik a HRE-ben, és használja a clientId + alkalmazás kulcsát. A tesztelés alatt álló szolgáltatás saját alkalmazással is rendelkezik a HRE-ben: az alkalmazás appID URI-ja a webes tesztben jelenik meg az erőforrás mezőben.

### <a name="open-authentication"></a>Nyílt hitelesítés
Nyílt hitelesítés például a Microsoft- vagy Google-fiókkal történő bejelentkezés. Számos OAuth protokollt használó alkalmazás biztosítja a titkos ügyfélkódos alternatívát, így az első feladat ennek a lehetőségnek a megvizsgálása.

Ha a tesztnek az OAuth protokollal kell bejelentkeznie, az általános megközelítés a következő:

Használjon egy eszközt, például a Fiddlert a webböngésző, a hitelesítési hely és az alkalmazás közötti forgalom vizsgálatához.
Jelentkezzen be legalább két alkalommal különböző gépek vagy böngészők használatával, illetve hosszú időközönként (hogy a tokennek legyen ideje lejárni).
A különböző munkamenetek összehasonlításával azonosítsa a hitelesítési helyről visszaadott tokent, amelyet a rendszer a bejelentkezést követően továbbad az alkalmazáskiszolgálónak.
Rögzítsen egy webes tesztet a Visual Studióval.
Paraméterezze a tokeneket. Ehhez állítsa be a paramétereket, amikor a hitelesítő visszaküldi a tokent, és használja a webhely felé indított lekérdezésben. (A Visual Studio megpróbálja paraméterezni a tesztet, de a tokeneket nem paraméterezi megfelelően.)

## <a name="troubleshooting"></a>Hibaelhárítás

Dedikált [hibaelhárítási cikk](troubleshoot-availability.md).

## <a name="next-steps"></a>Következő lépések

* [Rendelkezésre állási riasztások](availability-alerts.md)
* [URL pingelése webes tesztek](monitor-web-app-availability.md)
