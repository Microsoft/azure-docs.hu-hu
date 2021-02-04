---
title: Tudásbázis szerkesztése – QnA Maker
description: A QnA Maker lehetővé teszi, hogy egy könnyen használható szerkesztési élményt biztosítson a Tudásbázis tartalmának kezeléséhez.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 8d9f54c1fcd142c77fb73c294b81bf0f11ee083f
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99557695"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>QnA-párok szerkesztése a Tudásbázisban

A QnA Maker lehetővé teszi, hogy egy könnyen használható szerkesztési élményt biztosítson a Tudásbázis tartalmának kezeléséhez.

A QnA párokat egy adatforrásból, például egy fájlból vagy URL-címről, vagy szerkesztői forrásként kell hozzáadni. A szerkesztői forrás azt jelzi, hogy a QnA pár kézzel lett hozzáadva a QnA-portálhoz. Az összes QnA-pár szerkesztésre elérhető.

<a name="add-an-editorial-qna-set"></a>

## <a name="question-and-answer-pairs"></a>Kérdés-válasz párok

A Tudásbázis kérdések és válaszok (QnA) párokból áll.  Minden pár egyetlen választ tartalmaz, és egy pár tartalmazza az adott _válaszhoz_ tartozó összes információt. A válasz egy adatbázis-sorra vagy egy adatstruktúra-példányra is lazán hasonlíthat. A kérdés-válasz (QnA) pár **kötelező** beállításai a következők:

* a **felhasználói lekérdezésnek a QnA Maker** gépi tanuláshoz használt szövege, amely a felhasználó kérdéséhez igazodik a különböző szóhasználattal, de ugyanaz a válasz
* a **Válasz** – a pár válasza az a válasz, amelyet a rendszer akkor ad vissza, amikor egy felhasználói lekérdezés megfelel a kapcsolódó kérdésnek

Az egyes párokat **azonosítók** jelölik.

A párok **opcionális** beállításai a következők:

* **A kérdés alternatív formái** – ez segít QnA Maker a megfelelő választ visszaadni a kérdéses szövegezések széles körére
* **Metaadatok**: a metaadatok egy QnA-párral vannak társítva, és kulcs-érték párokként jelennek meg. A metaadatok címkéi a QnA párok szűrésére és a lekérdezési egyeztetést végző készlet korlátozására használhatók.
* Többfordulatú **kérések**, amelyek a több fordulatos beszélgetés folytatására szolgálnak

![QnA Maker tudásbázisok](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="add-an-editorial-qna-pair"></a>Szerkesztői QnA pár hozzáadása

Ha nem rendelkezik már meglévő tartalommal a Tudásbázis feltöltéséhez, QnA-párokat adhat hozzá a QnA Maker portálon.

1. Jelentkezzen be a [QnA-portálra](https://www.qnamaker.ai/), majd válassza ki azt a tudásbázist, amelyhez hozzá szeretné adni a QnA-párosítást.
1. A Tudásbázis **Szerkesztés** lapján válassza a **QnA-pár hozzáadása** lehetőséget egy új QnA-pár hozzáadásához.

    > [!div class="mx-imgBorder"]
    > ![QnA pár hozzáadása](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. Az új QnA-pár sorban adja hozzá a szükséges kérdések és válaszok mezőket. A többi mező megadása nem kötelező. Az összes mező bármikor módosítható.

1. Szükség esetén **[alternatív megfogalmazást](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)** is hozzáadhat. Az alternatív megfogalmazás a kérdés bármilyen formája, amely jelentősen eltér az eredeti kérdéstől, de ugyanazt a választ kell biztosítania.

    Ha a Tudásbázis közzé van téve, és az [aktív tanulás](use-active-learning.md) be van kapcsolva, a QnA Maker összegyűjti az Ön által elfogadott alternatív megfogalmazás-beállításokat. Ezek a beállítások az előrejelzés pontosságának növeléséhez vannak kiválasztva.

1. Szükség esetén **[metaadatokat](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)** is hozzáadhat. A metaadatok megtekintéséhez válassza a helyi menü **beállítások megtekintése** elemét. A metaadatok olyan szűrőket biztosítanak a válaszokhoz, amelyeket az ügyfélalkalmazás, például egy csevegési robot biztosít.

1. Igény szerint hozzáadhat **[követő utasításokat](multiturn-conversation.md)** is. A követő kérések további beszélgetési útvonalakat biztosítanak az ügyfélalkalmazás számára a felhasználónak való megjelenítéshez.

1. Válassza a **Mentés és a betanítás** lehetőséget az új QnA-párokat tartalmazó előrejelzések megjelenítéséhez.

## <a name="rich-text-editing-for-answer"></a>Rich Text formátumú szerkesztés a válaszhoz

A válasz szövegének gazdag szöveges szerkesztése lehetővé teszi, hogy egy egyszerű eszköztáron Markdown a stílusát.

1. Válassza ki a válasz szövegmezőjét, a Rich Text szerkesztő eszköztár a QnA pár sorában jelenik meg.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Rich Text Szerkesztőről, amely egy QnA-sor kérdését és válaszát jelöli.](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    A válaszban már megjelenő szöveg helyesen jelenik meg, amikor a felhasználó egy robottól fogja látni.

1. Szerkessze a szöveget. A Rich Text szerkesztési eszköztáron válassza a tulajdonságok formázása lehetőséget, vagy a váltás funkció használatával váltson a Markdown szintaxisra.

    > [!div class="mx-imgBorder"]
    > ![A Rich Text szerkesztővel szövegeket írhat és formázhat, és a Mentés másként Markdown is elvégezheti.](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |Rich Text Editor-funkciók|Billentyűparancsok|
    |--|--|
    |Váltás a Rich Text szerkesztő és a Markdown között. `</>`|CTRL+M|
    |Félkövér. **B**|CTR és LB|
    |Dőlt, dőlt betűvel jelezve ****|CTRL+I|
    |Rendezetlen listák||
    |Rendezett lista||
    |Bekezdés stílusa||
    |Rendszerkép – nyilvános URL-címről elérhető rendszerkép hozzáadása.|CTRL + G|
    |Hivatkozás hozzáadása nyilvánosan elérhető URL-címhez.|CTRL + K|
    |Hangulatjel – Hozzáadás a hangulatjelek közül kiválasztva.|CTRL + E|
    |Speciális menü – Visszavonás|CTRL+Z|
    |Speciális menü – újra|CTRL+Y|

1. Adjon hozzá egy képet a válaszhoz a Rich Text eszköztár rendszerkép ikonjának használatával. A helyszíni szerkesztőnek szüksége van a nyilvánosan elérhető rendszerkép URL-címére és a képhez tartozó helyettesítő szövegre.


    > [!div class="mx-imgBorder"]
    > ![A képernyőképen a nyilvánosan elérhető rendszerkép URL-címével és a beírt képhez tartozó helyettesítő szöveggel jelenik meg a helyi szerkesztő.](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. Adja meg az URL-címre mutató hivatkozást a válaszban szereplő szöveg kiválasztásával, majd válassza a hivatkozás ikont az eszköztáron, vagy válassza a hivatkozás ikont az eszköztáron, majd írja be az új szöveget és az URL-címet.

    > [!div class="mx-imgBorder"]
    > ![A Rich Text Editor használatával adjon hozzá egy nyilvánosan elérhető rendszerképet és annak helyettesítő szövegét.](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>QnA pár szerkesztése

Bármely QnA pár mező szerkeszthető, az eredeti adatforrástól függetlenül. Előfordulhat, hogy egyes mezők nem láthatók a környezeti eszköztáron található aktuális **nézet** beállításai miatt.

## <a name="delete-a-qna-pair"></a>QnA pár törlése

A QnA törléséhez kattintson a QnA sor jobb szélén található **Törlés** ikonra. Ez egy állandó művelet. Nem vonható vissza. A párok törlése előtt érdemes lehet a TUDÁSBÁZISt a **közzétételi** lapról exportálni.

![QnA pár törlése](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>A QnA pár AZONOSÍTÓjának megkeresése

Ha meg kell találnia a QnA pár AZONOSÍTÓját, két helyen is megtalálhatja:

* Vigye a kurzort a Delete (Törlés) ikonra a QnA pár sorában, amelyre kíváncsi. A hover szövege magában foglalja a QnA párosítási AZONOSÍTÓját.
* Exportálja a tudásbázist. A Tudásbázisban minden QnA pár tartalmazza a QnA-párt AZONOSÍTÓját.

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Alternatív kérdéseket adhat hozzá egy meglévő QnA-párosításhoz, így növelheti annak valószínűségét, hogy a felhasználó lekérdezése megegyezzen.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>QnA-párok összekapcsolása

A QnA párok összekapcsolása a [követő kérésekkel](multiturn-conversation.md)történik. Ez egy logikai kapcsolat a QnA-párok között, a Tudásbázis szintjén felügyelve. A következő utasításokat szerkesztheti a QnA Maker-portálon.

A válasz metaadataiban nem lehet összekapcsolni a QnA párokat.

## <a name="add-metadata"></a>Metaadatok hozzáadása

Adja hozzá a metaadatokat úgy, hogy először kiválasztja a **megtekintési beállításokat**, majd kiválasztja a **metaadatok megjelenítése** lehetőséget. Ekkor megjelenik a metaadatok oszlop. Ezután válassza ki a **+** jelet egy metaadat-pár hozzáadásához. Ez a pár egy kulcsból és egy értékből áll.

További információ a metaadatokat a QnA Maker-portálon a metaadatokkal kapcsolatban:
* [Szerzői metaadatok hozzáadása a QnA-párokhoz](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Lekérdezések előrejelzése – válaszok szűrése metaadatok alapján](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>QnA-párok módosításainak mentése

A módosítások elvesztésének elkerülése érdekében időnként válassza a **Mentés és a betanítás** lehetőséget.

![Metaadatok hozzáadása](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Mikor kell használni a Rich Text szerkesztési és a Markdown

A válaszok [gazdag szövegű szerkesztése](#add-an-editorial-qna-set) lehetővé teszi, hogy a szerzőként egy formázási eszköztár használatával gyorsan kiválassza és formázza a szöveget.

A [Markdown](../reference-markdown-format.md) jobb eszköz, ha automatikusan létre kell hoznia egy tartalmat a CI/CD-folyamat vagy a [Batch-tesztelés](../index.yml)részeként importálandó tudásbázisok létrehozásához.

## <a name="editing-your-knowledge-base-locally"></a>A Tudásbázis helyi szerkesztése

A Tudásbázis létrehozása után javasolt a [QnA Maker-portálon](https://qnamaker.ai)lévő Tudásbázis szövegének szerkesztése a helyi fájlok exportálása és újraimportálása helyett. Előfordulhat azonban, hogy a tudásbázist helyileg kell szerkesztenie.

Exportálja a tudásbázist a **Beállítások** lapról, majd szerkessze a tudásbázist a Microsoft Excelben. Ha egy másik alkalmazás használatával szerkeszti az exportált fájlt, előfordulhat, hogy az alkalmazás szintaktikai hibákat tartalmaz, mert az nem teljes TSV-kompatibilis. A Microsoft Excel TSV-fájljai általában nem vezetnek be formázási hibákat.

Miután végzett a szerkesztéssel, importálja újra a TSV-fájlt a **Beállítások** lapról. Ez teljes mértékben lecseréli az aktuális tudásbázist az importált tudásbázisba.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Együttműködés a tudásbázison](../index.yml)

* [Az QnA Maker által használt Azure-erőforrások kezelése](set-up-qnamaker-service-azure.md)
