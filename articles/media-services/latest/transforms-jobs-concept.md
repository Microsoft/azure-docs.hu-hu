---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Kötelező mezők. További információ: aka.ms/skyeye/meta.
title: átalakítások és feladatok Media Servicesban: Azure Media Services Leírás: megtudhatja, hogyan hozhat létre átalakítókat a videók feldolgozásához szükséges szabályok leírásához Azure Media Servicesban.
szolgáltatások: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. munkaterhelés: MS. topic: konceptuális MS. Date: 08/19/2019 MS. Author: inhenkel
---

# <a name="transforms-and-jobs-in-media-services"></a>Átalakítások és feladatok Media Services

Ez a témakör az [átalakításokkal](/rest/api/media/transforms) és a [feladatokkal](/rest/api/media/jobs) kapcsolatos adatokat ismerteti, és bemutatja az entitások közötti kapcsolatot.

## <a name="overview"></a>Áttekintés

### <a name="transformsjobs-workflow"></a>Átalakítások/feladatok munkafolyamat

A következő ábra a transzformációk/feladatok munkafolyamatot mutatja be:

![Átalakítások és feladatok munkafolyamata Azure Media Services](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Jellemző munkafolyamat

1. Hozzon létre egy átalakítót.
2. Feladatok küldése a transzformáció alatt.
3. Lista átalakítások.
4. Ha nem tervezi, hogy a későbbiekben nem kívánja használni a transzformációt, törölje az átalakítást.

#### <a name="example"></a>Példa

Tegyük fel, hogy az összes videó miniatűr képként való kinyerését kívánja kinyerni – a lépéseket a következőképpen teheti meg:

1. Adja meg a receptet vagy a videók feldolgozására vonatkozó szabályt: "a videó első képkockáját használja miniatűrként".
2. Minden videó esetében el kell utasítania a szolgáltatást:
    1. Hol található a videó.
    2. Hová kell írni a kimenet miniatűr képét.

Az **átalakítással** egyszerre hozhatja létre a receptet (1. lépés), és elküldheti az adott receptet használó feladatokat (2. lépés).

> [!NOTE]
> **A DateTime** típus **átalakításának** és feladatainak tulajdonságai mindig UTC formátumban jelennek meg.

## <a name="transforms"></a>Átalakítások

Az **átalakítások** használatával konfigurálhatja a videók kódolására vagy elemzésére szolgáló gyakori feladatokat. Mindegyik **transzformáció** a videó-vagy hangfájlok feldolgozására szolgáló feladatok egy receptjét vagy munkafolyamatát ismerteti. Egyetlen átalakító több szabályt is alkalmazhat. Egy átalakító például megadhatja, hogy minden videó egy adott bitrátán lévő MP4-fájlba legyen kódolva, és hogy a videó első képkockából jöjjön létre egy miniatűr kép. Minden olyan szabályhoz hozzá kell adnia egy TransformOutput-bejegyzést, amelyet fel szeretne venni az átalakításba. Az előállítók használatával adhatja meg, hogyan történjen a bemeneti médiafájlok feldolgozásának átalakítása.

### <a name="viewing-schema"></a>Séma megtekintése

A Media Services v3-as verzióban az előzetes beállítások az API-ban erősen beírt entitások. Ezekhez az objektumokhoz a "séma" definíciója [nyílt API-specifikációban (vagy hencegés)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)található. A [REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset), a [.net SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset)vagy más Media Services v3 SDK dokumentációjában megtekintheti az előre definiált definíciókat (például a **StandardEncoderPreset**-t) is.

### <a name="creating-transforms"></a>Átalakítások létrehozása

Az átalakításokat a REST, a CLI vagy a közzétett SDK-k használatával is létrehozhatja. Az Media Services V3 API-t a Azure Resource Manager vezérli, így a Resource Manager-sablonokkal is létrehozhatja és telepítheti az átalakításokat a Media Services-fiókban. Az Azure szerepköralapú hozzáférés-vezérlés használatával zárolhatja az átalakításokhoz való hozzáférést.

### <a name="updating-transforms"></a>Átalakítások frissítése

Ha frissítenie kell az [átalakítót](/rest/api/media/transforms), használja a **frissítési** műveletet. Ennek célja a Leírás módosítása vagy a mögöttes TransformOutputs prioritása. Javasoljuk, hogy ezeket a frissítéseket az összes folyamatban lévő feladat befejezése után végezze el. Ha újra szeretné írni a receptet, létre kell hoznia egy új átalakítót.

### <a name="transform-object-diagram"></a>Objektum diagramjának átalakítása

A következő ábrán az **átalakító** objektum és az általa hivatkozott objektumok láthatók, beleértve a származtató kapcsolatokat is. A szürke nyíl egy olyan típust mutat be, amelyet a feladatok és a zöld nyilak mutatnak az osztály származtató kapcsolatainak megjelenítéséhez.

Válassza ki a képet a teljes méret megtekintéséhez.  

[![Az átalakító objektumot és az általa hivatkozott objektumokat ábrázoló diagram, beleértve az objektumok közötti osztály-származtató kapcsolatokat.](./media/api-diagrams/transform-small.png)](./media/api-diagrams/transform-large.png#lightbox)

## <a name="jobs"></a>Feladatok

A **feladatnak** az a tényleges kérése, hogy Media Services az **átalakítást** egy adott bemeneti videóra vagy hangtartalomra alkalmazza. Az átalakítás létrehozása után Media Services API-kkal vagy a közzétett SDK-kkal is elküldheti a feladatokat. A **feladatok** olyan információkat határoznak meg, mint a bemeneti videó helye és a kimenet helye. Megadhatja a bemeneti videó helyét a: HTTPS URL-címek, SAS URL-címek vagy [eszközök](/rest/api/media/assets)használatával.  

### <a name="job-input-from-https"></a>Feladathoz tartozó bemenet HTTPS-ről

Ha a tartalom már elérhető egy URL-címen keresztül, és nem szükséges a forrásfájl tárolása az Azure-ban (például az S3-ból való importálás), használja a HTTPS-alapú [feladatokat](job-input-from-http-how-to.md) . Ez a módszer akkor is megfelelő, ha rendelkezik a tartalommal az Azure Blob Storage-ban, de nem szükséges, hogy a fájl egy eszközön legyen. Ez a módszer jelenleg csak egyetlen fájlt támogat a bevitelhez.

### <a name="asset-as-job-input"></a>Az eszköz feladatként való bevitele

Ha a bemeneti tartalom már egy eszközön van, vagy a tartalom helyi fájlban van tárolva, használja az [eszköz feladatként való bevitelét](job-input-from-local-file-how-to.md) . Ez akkor is jó megoldás, ha a bemeneti adategységet szeretné közzétenni a streaminghez vagy a letöltéshez (tegyük fel, hogy közzé kívánja tenni az MP4 letöltését, de a szöveg-vagy Arcfelismerés-felismerést is el szeretné végezni). Ez a módszer támogatja a többfájlos eszközöket (például a helyileg kódolt MBR-adatfolyam-készleteket).

### <a name="checking-job-progress"></a>A feladatok állapotának ellenőrzése

A feladatok előrehaladását és állapotát a Event Gridával folytatott figyelési események is beszerezhetők. További információ: [események figyelése a EventGrid használatával](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Feladatok frissítése

A [feladat](/rest/api/media/jobs) entitásban található frissítési művelettel a feladat elküldése után módosíthatja a *leírást* és a *prioritási* tulajdonságokat. A *priority* tulajdonság módosítása csak akkor lép érvénybe, ha a feladat továbbra is várólistán lévő állapotban van. Ha a feladat megkezdte a feldolgozást, vagy befejeződött, a módosítás prioritása nincs hatással.

### <a name="job-object-diagram"></a>Feladatütemezés diagramja

A következő ábrán a **feladatütemezés** és az általa hivatkozott objektumok láthatók, beleértve a származtató kapcsolatokat is.

Kattintson a képre a teljes méretű megjelenítéshez.  

[![A feladattípust és az általa hivatkozott objektumokat bemutató diagram, beleértve az objektumok közötti osztály-származtató kapcsolatokat.](./media/api-diagrams/job-small.png)](./media/api-diagrams/job-large.png#lightbox)

## <a name="configure-media-reserved-units"></a>Media szolgáltatás számára fenntartott egységek konfigurálása

A Media Services v3 vagy Video Indexer által aktivált hangelemzési és videó-elemzési feladatokhoz kifejezetten ajánlott 10 S3-as Media szolgáltatás számára fenntartott egységekkel (MRUs) ellátni a fiókját. Ha több mint 10 S3 MRUs van szüksége, nyisson meg egy támogatási jegyet a [Azure Portal](https://portal.azure.com/)használatával.

Részletekért lásd: [a médiafájlok feldolgozásának skálázása a CLI-vel](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="see-also"></a>Lásd még

* [Hibakódok](/rest/api/media/jobs/get#joberrorcode)
* [Media Services entitások szűrése, rendezése és lapozása](entities-overview.md)

## <a name="next-steps"></a>Következő lépések

- A fejlesztés megkezdése előtt tekintse át [az Media Services V3 API](media-services-apis-overview.md) -kkal való fejlesztést (az API-k elérésére vonatkozó információkat, elnevezési konvenciókat stb.).
- Tekintse meg a következő oktatóanyagokat:

    - [Oktatóanyag: távoli fájl kódolása URL-cím alapján és a videó továbbítása](stream-files-tutorial-with-rest.md)
    - [Oktatóanyag: videók feltöltése, kódolása és továbbítása](stream-files-tutorial-with-api.md)
    - [Oktatóanyag: videók elemzése Media Services v3-val](analyze-videos-tutorial-with-api.md)
