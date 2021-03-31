---
title: Egyéni hang-beszéd szolgáltatás létrehozása
titleSuffix: Azure Cognitive Services
description: Ha készen áll az adatok feltöltésére, nyissa meg az egyéni hangportált. Hozzon létre vagy válasszon ki egy egyéni hangprojektet. A projektnek meg kell osztania a megfelelő nyelvi/területi beállításokat és a nemek tulajdonságait a hangképzéshez használni kívánt adatként.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 541448f08e4ce9961d34063dcc225bf89d969a73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "101703371"
---
# <a name="create-a-custom-voice"></a>Egyéni hang létrehozása

Az [Egyéni hangra való felkészülés](how-to-custom-voice-prepare-data.md)során a különböző adattípusokat ismertetjük, amelyek segítségével betaníthatja az egyéni hangokat és a különböző formátumokra vonatkozó követelményeket. Az adatok előkészítése után megkezdheti a feltöltést az [Egyéni hangportálra](https://aka.ms/custom-voice-portal), vagy az egyéni hangképzési API-n keresztül. Itt ismertetjük az egyéni hangoknak a portálon keresztül történő betanításának lépéseit.

> [!NOTE]
> Ez az oldal azt feltételezi, hogy elolvasta az első [lépéseket az egyéni](how-to-custom-voice.md) hangon, és [előkészíti az egyéni](how-to-custom-voice-prepare-data.md)hangfelvételt, és létrehozott egy egyéni hangprojektet.

Győződjön meg arról, hogy milyen nyelveken támogatott az egyéni hang: a [Testreszabás nyelve](language-support.md#customization).

## <a name="upload-your-datasets"></a>Az adatkészletek feltöltése

Ha készen áll az adatok feltöltésére, nyissa meg az [Egyéni hangportált](https://aka.ms/custom-voice-portal). Hozzon létre vagy válasszon ki egy egyéni hangprojektet. A projektnek meg kell osztania a megfelelő nyelvi/területi beállításokat és a nemek tulajdonságait a hangképzéshez használni kívánt adatként. Válassza ki például, hogy `en-GB` az angol nyelvű hangfelvételek angol nyelvűek-e, és az Egyesült Királyság akcentusa.

Lépjen az **adatok** lapra, és kattintson az **adatok feltöltése** elemre. A varázslóban válassza ki a megfelelő adattípust, amely megfelel az előkészített előírásoknak.

A feltöltött adatkészleteknek meg kell felelniük a választott adattípusra vonatkozó követelményeknek. Fontos, hogy a feltöltés előtt megfelelően formázza az adatait. Ez biztosítja, hogy az egyéni hangszolgáltatás pontosan dolgozza fel az adatfeldolgozást. Nyissa meg az [Adatelőkészítés egyéni hangra](how-to-custom-voice-prepare-data.md) lehetőséget, és győződjön meg róla, hogy az adatai helyesen vannak formázva.

> [!NOTE]
> Az ingyenes előfizetés (F0) felhasználói egyszerre két adatkészletet tölthetnek fel. A standard előfizetés (S0) felhasználók öt adatkészletet tölthetnek fel egyszerre. Ha eléri a korlátot, várjon, amíg az adatkészletek közül legalább az egyik be nem fejeződik az importálás. Ezután próbálkozzon újra.

> [!NOTE]
> Az előfizetések által importálandó adatkészletek maximális száma 10. zip-fájl az ingyenes előfizetés (F0) felhasználóinak és a 500 a standard előfizetés (S0) felhasználói számára.

Az adatkészletek automatikusan érvényesítve lesznek a feltöltés gomb megnyomásakor. Az adatellenőrzés a hangfájlok ellenőrzési sorozatát tartalmazza, hogy ellenőrizze a fájlformátumot, a méretet és a mintavételezési sebességet. Javítsa ki a hibákat, és küldje el újra. Az Adatimportálási kérelem sikeres kezdeményezése után egy olyan bejegyzést kell látnia az adattáblában, amely megfelel az imént feltöltött adatkészletnek.

Az alábbi táblázat az importált adatkészletek feldolgozási állapotait mutatja be:

| Állapot | Értelmezés |
| ----- | ------- |
| Feldolgozás | Az adatkészlet beérkezett, és feldolgozás alatt áll. |
| Sikeres | Az adatkészlet érvényesítve lett, és most már használható hangmodell létrehozásához. |
| Sikertelen | Az adatkészletet számos ok miatt meghiúsult a feldolgozás során, például a hibák, az adatproblémák vagy a hálózati problémák miatt. |

Az ellenőrzés befejezését követően a **hosszúságú kimondott szöveg** oszlopban megtekintheti az egyes adatkészletek egyező hosszúságú kimondott szöveg teljes számát. Ha a kiválasztott adattípus hosszú hangú szegmentálást igényel, ez az oszlop csak az Ön által az átiratok alapján vagy a Speech transzkripció Service-ben szegmentált hosszúságú kimondott szöveg tükrözi. A sikeresen importált hosszúságú kimondott szöveg és a hozzájuk tartozó leképezési átiratok részletes eredményeinek megtekintéséhez továbbra is letöltheti az érvényesített adatkészletet. Tipp: a hosszú hangú szegmentálás több mint egy órát is igénybe vehet az adatok feldolgozásának befejezéséhez.

Az adatrészletek nézetben további információkat talál a kiejtési pontszámokról és az egyes adatkészletek zajszintről. A kiejtés pontszáma 0 és 100 között lehet. A 70-es pontszám általában egy beszédfelismerési hibát vagy parancsfájl-eltérést jelez. A nagy hangsúlysal csökkentheti a kiejtési pontszámot, és hatással lehet a generált digitális hangra.

Egy magasabb, a hang-zaj arány (SNR) azt jelzi, hogy a hang alacsonyabb zajt mutat. A Professional Studióban való felvételsel általában 50 + SNR is elérhet. A 20 alatti SNR rendelkező hang nyilvánvaló zajt eredményezhet a generált hangon.

Gondolja át újra a hosszúságú kimondott szöveg az alacsony kiejtési pontszámokkal vagy a gyenge jelek és zaj arányával. Ha nem tudja újra felvenni a rekordot, kizárhatja ezeket a hosszúságú kimondott szöveg az adatkészletből.

> [!NOTE]
> Szükség van arra, hogy ha egyéni neurális hangot használ, regisztrálnia kell a hangfelvételt a **hangtehetség** lapon. A rögzítési parancsfájl előkészítésekor ügyeljen arra, hogy az alábbi mondattal megszerezze a hangfelvételi visszajelzéseket a hanganyagok használatával a TTS hangmodell létrehozásához és a szintetikus beszéd létrehozásához. "I [állapot az első és az utolsó név] tisztában vagyok azzal, hogy a hangfelvételeket [a vállalat neve] fogja használni a hang szintetikus verziójának létrehozásához és használatához."
Ezt a mondatot fogjuk használni annak ellenőrzéséhez, hogy a betanítási adatkészletekben lévő felvételeket ugyanaz a személy végzi-e, mint a beleegyezik. [További információ az adatai feldolgozásáról, valamint a hangalapú tehetségek ellenőrzésének módjáról](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

## <a name="build-your-custom-voice-model"></a>Egyéni hangmodell létrehozása

Az adatkészlet ellenőrzése után felhasználhatja az egyéni hangmodell összeállításához.

1.  Navigáljon a **szöveg-beszéd > egyéni hang > [a projekt neve] > modellre**.

2.  Kattintson a **tanítási modell** elemre.

3.  Ezután adjon meg egy **nevet** és egy **leírást** , amely segítséget nyújt a modell azonosításához.

    Gondosan válassza ki a nevet. Az itt megadott név lesz az a név, amelyet a SSML-bevitel részeként a beszédfelismerési kérelemben szereplő hang megadására fog használni. Csak betűket, számokat és néhány írásjelet (például:-, \_ , és (",") tartalmazhat. Használjon különböző neveket a különböző hangmodellekhez.

    A **Leírás** mező gyakori használata a modell létrehozásához használt adatkészletek nevének rögzítése.

4.  A **betanítási adatok kiválasztása** lapon válasszon ki egy vagy több olyan adatkészletet, amelyet a képzéshez használni szeretne. Mielőtt elküldi a hosszúságú kimondott szöveg számát, tekintse át a következőt:. Az "adaptív" betanítási módszer használatával tetszőleges számú hosszúságú kimondott szöveg indíthat az en-US és a zh-CN hangmodellekhez. Más területi beállítások esetén több mint 2 000 hosszúságú kimondott szöveg kell kijelölnie, hogy egy standard szinttel lehessen betanítani egy hangot, beleértve a "statisztikai parametrikus" és az "összefűzött" betanítási módszereket, valamint több mint 300 hosszúságú kimondott szöveg az egyéni neurális hang betanításához. 

    > [!NOTE]
    > Az ismétlődő hangnevek el lesznek távolítva a betanításból. Győződjön meg arról, hogy a kiválasztott adatkészletek nem tartalmazzák ugyanazokat a hangneveket több. zip-fájlban.

    > [!TIP]
    > Az ugyanahhoz a beszélőhöz tartozó adatkészletek használata szükséges a minőségi eredményekhez. A különböző képzési módszerek különböző betanítási adatmennyiséget igényelnek. A "statisztikai parametrikus" metódussal rendelkező modellek betanításához legalább 2 000 különböző hosszúságú kimondott szöveg szükséges. Az "Összefűzés" módszer esetében ez 6 000 hosszúságú kimondott szöveg, míg a "neurális" esetében a minimális adatméretre vonatkozó követelmény 300 hosszúságú kimondott szöveg.

5. Válassza ki a **betanítási módszert** a következő lépésben. 

    > [!NOTE]
    > Ha szeretné betanítani a neurális hangvételt, meg kell adnia egy hangtehetségi profilt a hangfelvételt tartalmazó hangfelvételi fájllal, amely a beszédfelismerési adatok használatával betanítja az egyéni hangmodellt. Az egyéni neurális hang korlátozott hozzáféréssel érhető el. Győződjön meg róla, hogy tisztában van a [felelős AI-követelményekkel](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) , és [alkalmazza a hozzáférést itt](https://aka.ms/customneural). 
    
    Ezen az oldalon azt is megadhatja, hogy a parancsfájlt a teszteléshez töltse fel. A tesztelési parancsfájlnak 1Mb-nál kisebb txt-fájlnak kell lennie. A támogatott kódolási formátum a következőkből áll: ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE vagy UTF-16-BE. A Kimondás minden bekezdése külön hangot fog eredményezni. Ha az összes mondatot egyetlen hanggal szeretné egyesíteni, egy bekezdésben végezze el azokat. 

6. A hangmodell létrehozásának megkezdéséhez kattintson a **tanítás** elemre.

A betanítási táblázat egy új bejegyzést jelenít meg, amely megfelel az újonnan létrehozott modellnek. A tábla a következő állapotot is megjeleníti: feldolgozás, sikeres, sikertelen.

A megjelenített állapot az adatkészlet hangmodellre alakításának folyamatát tükrözi, ahogy az itt látható.

| Állapot | Értelmezés |
| ----- | ------- |
| Feldolgozás | A hangmodell létrehozása folyamatban van. |
| Sikeres | A hangmodell létrehozása megtörtént, és üzembe helyezhető. |
| Sikertelen | A hangmodell sok ok miatt sikertelen volt a képzésben, például a láthatatlan adatproblémákkal vagy a hálózati problémákkal kapcsolatban. |

A betanítási idő a feldolgozott hangadatok mennyiségétől és a kiválasztott betanítási módszertől függően változhat. 30 perctől 40 óráig terjedhet fel. Ha a modell betanítása sikeres volt, elkezdheti a tesztelést. 

> [!NOTE]
> Az ingyenes előfizetés (F0) felhasználói egyszerre egy hangbetűtípust tudnak betanítani. A standard előfizetés (S0) felhasználói egyszerre három hangokat is betanítanak. Ha eléri a korlátot, várjon, amíg a hangbetűkészletek közül legalább az egyik befejeződik, majd próbálkozzon újra.

> [!NOTE]
> Az egyéni neurális hangok betanítása nem ingyenes. Tekintse meg a [díjszabást](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) . 

> [!NOTE]
> Az előfizetéshez engedélyezett hangmodellek maximális száma 10 modell ingyenes előfizetés (F0) felhasználóhoz és 100 a standard előfizetés (S0) felhasználói számára.

Ha a neurális hangképzési funkciót használja, kiválaszthatja a valós idejű adatfolyam-forgatókönyvekre optimalizált modellek betanítását, vagy egy olyan HD neurális modellt, amely aszinkron, [hosszú hangszintézisre](long-audio-api.md)van optimalizálva.  

## <a name="test-your-voice-model"></a>A hangmodell tesztelése

Az egyes képzések automatikusan 100 minta hangfájlt hoznak majd a modell teszteléséhez. A hangmodell sikeres kiépítését követően tesztelheti azt a használathoz való üzembe helyezése előtt.

1.  Navigáljon a **szöveg-beszéd > egyéni hang > [a projekt neve] > modellre**.

2.  Kattintson a tesztelni kívánt modell nevére.

3.  A modell részletei lapon a **tesztelés** lapon találja a minta hangfájlokat. 

A hang minősége számos tényezőtől függ, többek között a betanítási adatok méretétől, a rögzítés minőségétől, a átiratok pontosságával, valamint a betanítási adatok rögzített hangjának a kívánt használati esethez tartozó személyiségével, és így tovább. Ha többet szeretne megtudni [a technológiával kapcsolatos képességekről és korlátozásokról, valamint a modell minőségének javításához ajánlott eljárásokról](/legal/cognitive-services/speech-service/custom-neural-voice/characteristics-and-limitations-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

## <a name="create-and-use-a-custom-voice-endpoint"></a>Egyéni hang-végpont létrehozása és használata

Miután sikeresen létrehozta és tesztelte a hangmodellt, egy egyéni szöveg-beszéd végponton helyezheti üzembe. Ezután ezt a végpontot használja a szokásos végpont helyett, amikor szöveg-beszéd kéréseket végez a REST APIon keresztül. Az egyéni végpontot csak a betűkészlet üzembe helyezéséhez használt előfizetés hívhatja meg.

Új egyéni hangvégpont létrehozásához lépjen a **szöveg-beszéd > egyéni hang > végpont** elemre. Válassza a **végpont hozzáadása** lehetőséget, és adja meg az egyéni végpont **nevét** és **leírását** . Ezután válassza ki azt az egyéni hangmodellt, amelyet hozzá szeretne rendelni ehhez a végponthoz.

Miután rákattintott a **Hozzáadás** gombra, a végpont táblában megjelenik egy bejegyzés az új végponthoz. Egy új végpont létrehozását eltarthat néhány percig. Ha a központi telepítés állapota **sikeres**, a végpont készen áll a használatra.

Ha nem használja az egész időt, **felfüggesztheti** és **folytathatja** a végpontot. Ha egy végpontot újraaktiválnak a felfüggesztés után, a végpont URL-címe változatlan marad, így nem kell módosítania a kódot az alkalmazásokban. 

A végpontot új modellre is frissítheti. A modell módosításához győződjön meg arról, hogy az új modell neve megegyezik azzal, amelyet frissíteni szeretne. 

> [!NOTE]
> Az ingyenes előfizetés (F0) felhasználóinak csak egy modelljét lehet központilag telepíteni. A standard előfizetés (S0) felhasználók legfeljebb 50 végpontot hozhatnak létre, amelyek mindegyike saját egyéni hanggal rendelkezik.

> [!NOTE]
> Az egyéni hang használatához meg kell adnia a hangmodell nevét, az egyéni URI-t közvetlenül egy HTTP-kérelemben kell használnia, és ugyanazt az előfizetést kell használnia a TTS szolgáltatás hitelesítésének továbbítására.

A végpont üzembe helyezése után a végpont neve hivatkozásként jelenik meg. Kattintson a hivatkozásra a végpontra jellemző információk megjelenítéséhez, például a végponti kulcs, a végpont URL-címe és a mintakód számára.

A végpont online tesztelése az egyéni hangportálon keresztül is elérhető. A végpont teszteléséhez válassza a végpont **ellenőrzése** lehetőséget a **végpont részletei** lapon. Megjelenik az Endpoint Testing (végpont tesztelése) oldal. Adja meg a megjelenítendő szöveget (egyszerű szöveges vagy [SSML formátumban](speech-synthesis-markup.md) a szövegmezőben. Ha szeretné meghallgatni az egyéni hangbetűtípusban elhangzó szöveget, válassza a **Lejátszás** lehetőséget. Ez a tesztelési funkció az egyéni beszédfelismerési használatért lesz felszámítva.

Az egyéni végpont funkcionálisan megegyezik a szöveg-beszéd kérelmekhez használt standard végponttal. További információ: [REST API](rest-text-to-speech.md) .

## <a name="next-steps"></a>Következő lépések

* [Útmutató: hangminták rögzítése](record-custom-voice-samples.md)
* [Text-to-Speech API-referencia](rest-text-to-speech.md)
* [Hosszú hang API](long-audio-api.md)