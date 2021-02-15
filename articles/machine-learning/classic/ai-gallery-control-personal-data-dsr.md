---
title: 'ML Studio (klasszikus): Azure AI Gallery-alapú adatkezelés – Azure'
description: A Azure AI Gallery termékben lévő felhasználói adatait exportálhatja és törölheti az interface vagy a AI Gallery Catalog API használatával. Ez a cikk bemutatja, hogyan.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: mldocs
ms.openlocfilehash: 3461b8026079d089bacca9f12aa94e13f5cb2874
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519235"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Terméken belüli felhasználói adatok megtekintése és törlése Azure AI Gallery



A Azure AI Gallery a termék felhasználói adatait az interface vagy a AI Gallery Catalog API használatával tekintheti meg és törölheti. Ebből a cikkből megtudhatja, hogyan.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Az AI-galériában lévő adatai megtekintése a felhasználói felületen

A Azure AI Gallery webhely felhasználói felületén közzétett elemeket megtekintheti. A felhasználók a nyilvános és a nem listázott megoldásokat, projekteket, kísérleteket és egyéb közzétett elemeket is megtekinthetik:

1.    Jelentkezzen be a [Azure AI Galleryba](https://gallery.azure.ai/).
2.    Kattintson a profil képre a jobb felső sarokban, majd a fiók nevét a profil oldal betöltéséhez.
3.    A profil lap megjeleníti a katalógusban közzétett összes elemet, beleértve a nem listázott bejegyzéseket is.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Az adatai megtekintéséhez használja a AI Gallery katalógus API-ját

Programozott módon megtekintheti a AI Gallery Catalog API-n keresztül gyűjtött adatokat, amely a következő címen érhető el: https://catalog.cortanaanalytics.com/entities . Az adatmegjelenítéshez a szerző AZONOSÍTÓját kell megadnia. A lista nélküli entitások katalógus API-n keresztüli megtekintéséhez hozzáférési jogkivonat szükséges.

A katalógusra adott válaszokat JSON formátumban adja vissza a rendszer.

### <a name="get-an-author-id"></a>Szerző AZONOSÍTÓjának beolvasása
A Szerző azonosítója a Azure AI Gallery való közzétételkor használt e-mail-cím alapján történik. Nem változik:

1. Jelentkezzen be [Azure AI Galleryba](https://gallery.azure.ai/).
2. Kattintson a profil képre a jobb felső sarokban, majd a fiók nevét a profil oldal betöltéséhez.
3. A címsorban található URL-cím a következő alfanumerikus azonosítót jeleníti meg `authorId=` . Például a következő URL-címhez: \
    `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

    Szerző azonosítója: \
    `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Hozzáférési jogkivonat beszerzése

A lista nélküli entitások katalógus API-n keresztül történő megtekintéséhez hozzáférési token szükséges. Hozzáférési jogkivonat nélkül a felhasználók továbbra is megtekinthetik a nyilvános entitásokat és más felhasználói adatokat.

Hozzáférési jogkivonat beszerzéséhez meg kell vizsgálnia `DataLabAccessToken` egy HTTP-kérelem fejlécét, amelyet a böngésző a katalógus API-ba való bejelentkezéskor tesz elérhetővé:

1.    Jelentkezzen be a [Azure AI Galleryba](https://gallery.azure.ai/).
2.    Kattintson a profil képre a jobb felső sarokban, majd a fiók nevét a profil oldal betöltéséhez.
3.    Nyissa meg a böngésző Fejlesztői eszközök ablaktábláját az F12 billentyű lenyomásával, válassza a hálózat fület, és frissítse az oldalt. 
4. A karakterlánc- *katalógusban* lévő kérelmek szűréséhez írja be a szűrő szövegmezőbe.
5.    Az URL-címre irányuló kérelmekben `https://catalog.cortanaanalytics.com/entities` Keresse meg a Get kérelmet, és válassza a *fejlécek* fület. görgessen le a *kérések fejlécek* szakaszhoz.
6.    A fejléc alatt az `DataLabAccessToken` alfanumerikus token szerepel. Az adatbiztonság megőrzése érdekében ne ossza meg ezt a jogkivonatot.

### <a name="view-user-information"></a>Felhasználói adatok megtekintése
Az előző lépésekben kapott szerzői azonosító használatával tekintse meg a felhasználói profil információit a `[AuthorId]` következő URL-cím lecserélése után:

`https://catalog.cortanaanalytics.com/users/[AuthorID]`

Például ez az URL-kérelem:

`https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

Egy választ ad vissza, például:

```json
{"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}
```

### <a name="view-public-entities"></a>Nyilvános entitások megtekintése

A katalógus API a közzétett entitásokkal kapcsolatos adatokat tárolja a Azure AI Gallery, amelyeket közvetlenül a [mesterséges intelligencia](https://gallery.azure.ai/)-katalógus webhelyén is megtekintheti. 

A közzétett entitások megtekintéséhez látogasson el a következő URL-címre, és cserélje le `[AuthorId]` a kifejezést a [Szerző azonosítójának beolvasása](#get-an-author-id) elemre.

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'`

Például:

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'`

### <a name="view-unlisted-and-public-entities"></a>Nem listázott és nyilvános entitások megtekintése

Ez a lekérdezés csak nyilvános entitásokat jelenít meg. Ha meg szeretné tekinteni az összes entitást, beleértve a listán nem szereplőket is, adja meg az előző szakaszban beszerzett hozzáférési jogkivonatot.

1.    Hozzon létre egy HTTP GET-kérést a katalógus [URL-](https://www.getpostman.com)címére a következő témakörben leírtak szerint: [hozzáférési token beszerzése](#get-your-access-token).
2.    Hozzon létre egy nevű HTTP-kérési fejlécet a `DataLabAccessToken` hozzáférési jogkivonathoz beállított értékkel.
3.    Küldje el a HTTP-kérelmet.

> [!TIP]
> Ha a listán nem szereplő entitások nem jelennek meg a katalógus API válaszai között, akkor előfordulhat, hogy a felhasználó érvénytelen vagy lejárt hozzáférési jogkivonattal rendelkezik. Jelentkezzen ki a Azure AI Galleryból, majd ismételje meg a [hozzáférési token beszerzése](#get-your-access-token) című témakör lépéseit a jogkivonat megújításához. 
