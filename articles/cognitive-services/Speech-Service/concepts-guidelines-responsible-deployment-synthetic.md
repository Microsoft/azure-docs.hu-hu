---
title: Útmutató a szintetikus hangtechnológia felelős üzembe helyezéséhez
titleSuffix: Azure Cognitive Services
description: A Microsoft általános tervezési irányelvei a szintetikus hangtechnológia használatához. Ezek olyan tanulmányokban lettek kifejlesztve, amelyeket a Microsoft a hangtehetségekkel, a fogyasztókkal, valamint a beszédfelismerési rendellenességekkel foglalkozó személyekkel folytatott a szintetikus hang felelősségteljes fejlesztése érdekében.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 3a0b645acd7c21ff0416c748cdd2caf7041be508
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730813"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Útmutató a szintetikus hangtechnológia felelős üzembe helyezéséhez

Ebben a cikkben megismerheti a Microsoft általános tervezési irányelveit a szintetikus hangtechnológia használatához. Ezek az irányelvek olyan tanulmányokban lettek kifejlesztve, amelyeket a Microsoft hangtehetségekkel, fogyasztókkal és a beszédfelismerési rendellenességekkel foglalkozó személyekkel folytatott a szintetikus hangok felelősségteljes fejlesztése érdekében.

A szintetikus beszédfelismerési technológiák üzembe helyezéséhez a következő irányelvek vonatkoznak a legtöbb forgatókönyvre.

### <a name="disclose-when-the-voice-is-synthetic"></a>Kitakarás, ha a hang szintetikus
Ha azt kívánja, hogy egy hang ne legyen a számítógép által létrehozva, akkor az nem csupán a félrevezető következményekkel jár, hanem növeli a bizalmat a szervezeten belül is. További információ a [közzétételről](concepts-disclosure-guidelines.md).

A Microsoft megköveteli ügyfelei számára, hogy felfedik az egyéni neurális hangfelismerések szintetikus természetét a felhasználók számára. 
* Győződjön meg arról, hogy megfelelő nyilvánosságra hozatalt biztosít a célközönségeknek, különösen akkor, ha jól ismert személyek hangját használják, és az azt kézbesítő személytől származó információkra vonatkozó ítéletet tesznek közzé, függetlenül attól, hogy azok tudatosan vagy tudattalan vannak.  Például a közzététel szóbeli megosztása lehet a szórás elején. További információért látogasson el a [közzétételi mintákra](concepts-disclosure-patterns.md).   
* Érdemes lehet a szülők vagy más feleknek megfelelő nyilvánosságra hozni a kiskorúak és gyermekek számára tervezett használati eseteket – ha a használati eset a kiskorúak vagy gyermekek számára készült, meg kell győződnie arról, hogy a szülők vagy a jogi őrök képesek megérteni a szintetikus média használatával kapcsolatos információkat, és a megfelelő döntést a kiskorúak vagy gyermekek számára a felhasználói élmény igénybe vehetik. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Válassza ki a megfelelő hangtípusokat a forgatókönyvhöz
Körültekintően vegye figyelembe a használat kontextusát és a szintetikus hang használatával járó lehetséges károkat. Előfordulhat például, hogy a nagy megbízhatóságú szintetikus hangok nem megfelelőek a magas kockázatú helyzetekben, például a személyes üzenetkezelés, a pénzügyi tranzakciók vagy az emberi alkalmazkodást vagy az empátiát igénylő összetett helyzetekben. 

Előfordulhat, hogy a felhasználók a hangtípusokra vonatkozóan eltérő elvárásokat is tartalmazhatnak. Ha például egy szintetikus hang alapján olvas bizalmas híreket, néhány felhasználó inkább empatikus és emberi hasonló tónust szeretne, míg mások inkább elfogulatlan hangvételt igényelnek. Vegye fontolóra az alkalmazás tesztelését a felhasználói beállítások jobb megismerése érdekében.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Átlátható legyen a képességek és korlátozások
A felhasználók nagyobb valószínűséggel nagyobb elvárásokkal járnak, amikor a nagy megbízhatóságú szintetikus hanganyagokkal kommunikálnak. Ha a rendszerképességek nem felelnek meg ezeknek az elvárásoknak, a megbízhatóság romolhat, és kellemetlen, vagy akár ártalmas élményt is eredményezhet.

### <a name="provide-optional-human-support"></a>Opcionális emberi támogatás biztosítása
A nem egyértelmű, tranzakciós forgatókönyvek (például a Call Support Center) esetében a felhasználók nem mindig bíznak a számítógép-ügynökben, hogy megfelelően válaszoljanak a kéréseiket. Ezekben az esetekben szükség lehet az emberi támogatásra, függetlenül a rendszer hangjának vagy funkciójának reális minőségétől.

## <a name="considerations-for-voice-talent"></a>A hangalapú tehetségek szempontjai
A hangtehetségek, például a hangszínészek, a szintetikus hangok létrehozásához a következő iránymutatás vonatkozik.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Értelmes beleegyezett a hangtehetségből
A hangtehetségeknek szabályoznia kell a hangmodelljét (hogyan és hol lesznek használatban), és kompenzálni kell a használatukat. A Microsoft az egyéni hangvételt igénylő ügyfeleknek kifejezetten írásos engedélyt kell kapniuk a hangtehetségtől a szintetikus hang létrehozásához  Ha egy jól ismert személy szintetikus hangját hozza létre, meg kell adnia egy módszert a hang mögötti személynek a tartalom szerkesztéséhez vagy jóváhagyásához.

Bizonyos hangtehetségek nem ismerik a technológia lehetséges kártékony felhasználását, és a rendszertulajdonosok által a technológia képességeivel kapcsolatos ismeretekkel kell rendelkezniük. A Microsoft megköveteli, hogy az ügyfelek közvetlenül a hangtehetséggel vagy a hangvételi tehetséggel megosszák a Microsoft által a hangvételi [feladatokkal kapcsolatos információkat](/legal/cognitive-services/speech-service/disclosure-voice-talent) , amelyek a szintetikus hangok fejlesztését és a szöveg és a beszédfelismerési szolgáltatások együttes használatát ismertetik.

## <a name="considerations-for-those-with-speech-disorders"></a>A beszédfelismerési rendellenességekkel kapcsolatos megfontolások
Ha beszédfelismerési rendellenességgel rendelkező személyekkel dolgozik, a szintetikus hangtechnológia létrehozásához vagy üzembe helyezéséhez a következő irányelvek érvényesek.

### <a name="provide-guidelines-to-establish-contracts"></a>A szerződések létrehozásához szükséges irányelvek megadása
Útmutatást nyújt a szerződések létrehozásához a szintetikus hangvételt használó személyekkel való kommunikációhoz. A szerződésnek meg kell fontolnia azon felek megadását, akik a hangvételt, a használat időtartamát, a tulajdonjog-átadási feltételeket, a hangbetűkészlet törlésének eljárásait, valamint a jogosulatlan hozzáférés megelőzésének módját. Emellett engedélyezheti a hangbetűkészletek tulajdonjogának átadását a családtagok halála után, ha az engedélyt megadták.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>A beszéd mintázatok következetlenségének fiókja
A saját hangbetűkészleteit rögzítő beszédes problémákkal rendelkező személyek esetében a beszédfelismerési minta (az adathígtrágya vagy bizonyos szavak kimondása) inkonzisztenciaa megnehezítheti a rögzítési folyamatot. Ezekben az esetekben a szintetikus hangtechnika és a rögzítési munkamenetek (azaz a szüneteket és a rögzítési munkamenetek további számát biztosítják).

### <a name="allow-modification-over-time"></a>Módosítás időbeli változásának engedélyezése
Azok a személyek, akik beszédi rendellenességeket kívánnak készíteni a szintetikus hangon, hogy tükrözzék az öregedést (például egy gyermek a pubertást érik el). A magánszemélyek is rendelkezhetnek olyan stilisztikai beállításokkal, amelyek idővel változnak, és előfordulhat, hogy módosítani szeretnék a szurok, a hangsúly vagy más hangjellemzőket.


## <a name="see-also"></a>Lásd még

* [A hangalapú tehetségek közzététele](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [A közzététel módja](concepts-disclosure-guidelines.md)
* [Közzétételi tervezési minták](concepts-disclosure-patterns.md)