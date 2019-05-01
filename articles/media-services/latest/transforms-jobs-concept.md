---
title: Átalakítások és feladatok az Azure Media Services |} A Microsoft Docs
description: Media Services használata közben szeretne létrehozni egy-egy átalakítási szabályok vagy specifikációk a videók feldolgozásához leírása. Ez a cikk áttekintést nyújt az átalakítás van, és hogyan használható a.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 4a4f8478eef96ebe10d729e0ff380faf6f4b6779
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937370"
---
# <a name="transforms-and-jobs"></a>Átalakítások és feladatok

Ez a témakör kapcsolatos részleteket nyújt [alakítja át az](https://docs.microsoft.com/rest/api/media/transforms) és [feladatok](https://docs.microsoft.com/rest/api/media/jobs) , és elmagyarázza, ezek az entitások közötti kapcsolat. 

## <a name="overview"></a>Áttekintés 

### <a name="transformsjobs-workflow"></a>Átalakítások és feladatok munkafolyamat

Az alábbi ábrán látható átalakítások és feladatok munkafolyamat.

![Átalakítások](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Jellemző munkafolyamat

1. Hozzon létre egy-egy átalakítási 
2. Adott átalakítási alatt-feladatok elküldése 
3. Lista átalakítások 
4. Egy-egy átalakítási törlése, ha nem tervezi a jövőben használni. 

#### <a name="example"></a>Példa

Tegyük fel, hogy szeretne kinyerni a miniatűr képként – az összes videó első keret a lépéseket, akkor a következők: 

1. Adja meg a recept, vagy a szabály a videók feldolgozásához – "a videó első keret adatokként a miniatűr". 
2. Minden egyes, a szolgáltatás vázolnak fel: 
    1. Hol található a videót  
    2. Hol lehet írni a kimeneti asztalnak a miniatűrjére. 

A **átalakítása** hozhat létre a recept egyszer (1. lépés), és küldje el a feladatokat az adott recept (2. lépés).

> [!NOTE]
> Tulajdonságainak **átalakítása** és **feladat** a DateTime típusú állandóan UTC formátumban vannak.

## <a name="transforms"></a>Átalakítások

A következő ábra azt mutatja a **átalakítása** objektum és az általa hivatkozott, beleértve a Származtatás kapcsolatokat objektumokat. A szürke nyilak a típusát, hogy a feladat-referenciák és a zöld nyíl osztály származtatását kapcsolatok megjelenítéséhez.<br/>Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

Használat **alakítja át** és videók elemzése gyakori feladatok konfigurálása. Minden egyes **átalakítása** egy módszereivel, vagy egy munkafolyamatot a video- és audiotartalmak fájlok feldolgozása kapcsolatos feladatokat ismerteti. Egyetlen átalakító egynél több szabály is alkalmazhat. Egy-egy átalakítási megadhatja például, hogy minden videó kódolhatók-e a megadott sávszélességű, MP4-fájlokat, és, hogy egy miniatűr képére jöjjön létre a videó első keretből. Az átalakítási szerepeltetni kívánt minden egyes szabály egy TransformOutput bejegyzést jelentene. Átalakítások hozhat létre Media Services-fiók a Media Services v3 API-val, vagy valamelyik közzétett SDK-k használatával. A Media Services v3, így használhatja a Resource Manager-sablonok létrehozása és üzembe helyezése az API által az Azure Resource Manager alkalmazások alakítja át a Media Services-fiók. Szerepköralapú hozzáférés-vezérlés segítségével átalakítások való hozzáférés zárolása.

A frissítési műveletet a [átalakítása](https://docs.microsoft.com/rest/api/media/transforms) entitás szánnak módosítása a leírást, illetve az alapul szolgáló TransformOutputs prioritásait. Javasoljuk, hogy az ilyen frissítések hajtható végre az összes folyamatban lévő feladatok befejezését. Ha azt tervezi, újraírási a recept, hozzon létre egy új átalakítás szeretne.

## <a name="jobs"></a>Feladatok

A következő ábra azt mutatja a **feladat** objektum és az általa hivatkozott, beleértve a Származtatás kapcsolatokat objektumokat.<br/>Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

A **feladat** van az Azure Media Services tényleges kérelem a alkalmazni a **átalakítása** egy adott a bemeneti videó vagy hang tartalomhoz. Az átalakítás létrehozása után küldhet feladatokat a Media Services API-k és az egyéb közzétett SDK-k használatával. A **feladat** információkat, például a bemeneti videó helyét, és a kimeneti helyét adja meg. Megadhatja a helyét, a bemeneti videó használatával: HTTPS URL-címek, SAS URL-címeit, vagy [eszközök](https://docs.microsoft.com/rest/api/media/assets).  

Használat [HTTPS bemeneti feladat](job-input-from-http-how-to.md) Ha már egy URL-CÍMEN keresztül érhető el a tartalmat, és nincs szükség a forrás tárolásához az Azure-ban (például S3 szintű importálhat). Ez a módszer akkor is alkalmas, ha rendelkezik a tartalom az Azure Blob storage-ban, de nem szükséges lehet egy eszköz a fájlhoz. Jelenleg ez a módszer csak támogatja egyetlen fájl a bemeneti.
 
Használat [feladat bemeneteként eszköz](job-input-from-local-file-how-to.md) Ha egy eszköz már szerepel a bemeneti tartalom vagy a tartalom helyi fájl tárolja. Emellett akkor is jó választás, ha azt tervezi, hogy a bemeneti objektum közzététele streamelési vagy letöltése (például: szeretne közzétenni a mp4 letölthető, de is szeretné, ha szöveget vagy face észlelési beszédfelismerés). Ez a módszer támogatja a több fájlból eszközök (például adatfolyam-csoportok, amelyek helyileg is kódolású MBR).
 
A folyamat és a feladatok állapotának figyelésével az Event Grid-események szerezhető meg. További információkért lásd: [EventGrid események figyelése](job-state-events-cli-how-to.md).

A frissítési műveletet a [feladat](https://docs.microsoft.com/rest/api/media/jobs) entitás módosításához is használható a *leírás*, és a *prioritású* tulajdonságok a feladat elküldése után. Módosítás a *prioritású* a tulajdonság csak akkor, ha a feladat még mindig aszinkron állapotban van. Ha a feladat már megkezdődött a feldolgozás, vagy véget ért, prioritás módosítása nem befolyásolja.

## <a name="configure-media-reserved-units"></a>Konfigurálja a Media szolgáltatás számára fenntartott egységek

A hangelemzés és videó elemzési feladatokat a Media Services v3 által aktivált vagy a Video Indexer azt javasoljuk 10 S3 szintű Media szolgáltatás számára fenntartott egységek (helyet) fiókját kiépítéséhez. Ha több mint 10 S3-helyet, nyisson meg egy támogatási jegyet a a [az Azure portal](https://portal.azure.com/).

További információkért lásd: [méretezhető médiafeldolgozás a parancssori felület segítségével](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Tegyen fel kérdéseket, küldje el visszajelzését, frissítések beszerzése

Tekintse meg a [Azure Media Services-Közösség](media-services-community.md) kérdések, küldje el visszajelzését, és tudnivalók a Media Services-frissítések különböző módon olvashatja.

## <a name="see-also"></a>Lásd még

* [Hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [A Media Services entitások szűrési, rendezési, stránkování](entities-overview.md)

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Videók feltöltése, kódolása és streamelése a .NET használatával](stream-files-tutorial-with-api.md)
- [Oktatóanyag: Elemezhet videókat a Media Services v3 .NET használatával](analyze-videos-tutorial-with-api.md)
