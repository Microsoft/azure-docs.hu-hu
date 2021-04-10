---
title: Hangtartalom létrehozása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A hangtartalom létrehozása egy online eszköz, amely lehetővé teszi a Microsoft szöveg-beszéd kimenetének testreszabását és finomhangolását az alkalmazásokhoz és termékekhez.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: eb8d2d956eac23c02201f7fd855dcae71e960e8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100388583"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>A hanganyag-létrehozási eszközzel fejlesztheti a szintézist

A [hangtartalom létrehozása](https://aka.ms/audiocontentcreation) egy könnyen használható és hatékony eszköz, amely lehetővé teszi, hogy rendkívül természetes hanganyagokat hozzon létre különböző forgatókönyvek, például Hangoskönyvek, Hírek közvetítése, videók elbeszélése és csevegési robotok számára. A hangtartalom létrehozása révén hatékonyan és költséghatékonyan alakíthatja ki a szöveg-beszéd hangokat, és megtervezheti a testreszabott hangélményeket.

Az eszköz a [Speech szintézis Markup Language (SSML) nyelvén](speech-synthesis-markup.md)alapul. Lehetővé teszi, hogy valós időben vagy kötegelt szintézisben módosítsa a szöveg-beszéd kimeneti attribútumokat, például a hangkaraktereket, a hangstílusokat, a beszéd sebességét, a kiejtést és a prosody.

Könnyedén hozzáférhet több mint 150 előre összeállított hanghoz, amely több, mint 50 különböző nyelven érhető el, beleértve a legkorszerűbb neurális TTS-hangokat és az egyéni hangvételt, ha létrehozta az egyiket. 

Tekintse meg a hangtartalom létrehozásának [videós oktatóanyagát](https://www.youtube.com/watch?v=O1wIJ7mts_w) .

## <a name="how-to-get-started"></a>Útmutató az első lépésekhez

A hangtartalom létrehozása ingyenes eszköz, azonban a felhasznált Azure Speech szolgáltatásért kell fizetnie. Az eszközzel való együttműködéshez Azure-fiókkal kell bejelentkeznie, és létre kell hoznia egy beszédfelismerési erőforrást. Minden egyes Azure-fiókhoz havi ingyenes beszédfelismerési kvóták tartoznak, amelyek többek között 500 000 karaktert tartalmaznak a neurális TTS-hangok (havonta) 5 000 000, a standard és az egyéni hangok (havonta), valamint az 1 egyéni hang-végpont üzemeltetési szolgáltatás (havonta) esetében. A havi kiosztott összeg általában elég ahhoz, hogy egy kisméretű Tartalomért felelős csapat körülbelül 3-5 személyt keressen. Íme egy Azure-fiók létrehozásának és a beszédfelismerési erőforrás beszerzésének lépései. 

### <a name="step-1---create-an-azure-account"></a>1. lépés – Azure-fiók létrehozása

A hangtartalom létrehozásához rendelkeznie kell egy [Microsoft-fiók](https://account.microsoft.com/account) és egy [Azure-fiókkal](https://azure.microsoft.com/free/ai/). [A fiók beállításához](./overview.md#try-the-speech-service-for-free)kövesse az alábbi utasításokat. 

[Azure Portal](https://portal.azure.com/) a központi hely az Azure-fiók kezeléséhez. Létrehozhatja a beszédfelismerési erőforrást, kezelheti a termék hozzáférését, és megfigyelheti az egyszerű webalkalmazások és az összetett Felhőbeli üzembe helyezések összes elemét. 

### <a name="step-2---create-a-speech-resource"></a>2. lépés – beszédfelismerési erőforrás létrehozása

Miután regisztrált az Azure-fiókra, létre kell hoznia egy beszédfelismerési erőforrást az Azure-fiókjában a Speech Services eléréséhez. Megtekintheti a [beszédfelismerési erőforrások létrehozásának](./overview.md#create-the-azure-resource)utasításait. 

Az új beszédfelismerési erőforrás üzembe helyezése néhány percet vesz igénybe. Az üzembe helyezés befejezését követően elkezdheti a hangtartalom létrehozási menetét. 

 >[!NOTE]
   > Ha azt tervezi, hogy neurális hangokat használ, győződjön meg arról, hogy az erőforrást [egy olyan régióban hozza létre, amely támogatja a neurális hangokat](regions.md#standard-and-neural-voices).
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>3. lépés – jelentkezzen be a hangtartalom létrehozására az Azure-fiókkal és a beszédfelismerési erőforrással

1. Az Azure-fiók és a beszédfelismerési erőforrás beszerzése után az első **lépések** lehetőségre kattintva bejelentkezhet a [hangtartalom-létrehozásba](https://aka.ms/audiocontentcreation) .
2. Ekkor megjelenik a **beszédfelismerési erőforrás** oldala. Válassza ki a használni kívánt beszédfelismerési erőforrást. A hang létrehozásához kattintson **az Ugrás a studióra** elemre. Új beszédfelismerési erőforrás létrehozásához kattintson az **új létrehozása** lehetőségre. Amikor legközelebb bejelentkezik a hangtartalom-létrehozási eszközbe, közvetlenül az aktuális beszédfelismerési erőforrásban található hang-és munkahelyi fájlokra fogunk hivatkozni. 
3. A beszédfelismerési erőforrást bármikor módosíthatja a **Beállítások** lehetőséggel, amely a felső NAV-ban található.

## <a name="how-to-use-the-tool"></a>Az eszköz használata

Ez az ábra azokat a lépéseket mutatja be, amelyek a szöveg-beszéd kimenetek finomhangolásához szükségesek. Az alábbi hivatkozásokat követve további információkat tudhat meg az egyes lépésekről.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="A szöveg és a beszéd közötti kimenet finomhangolásához szükséges lépések diagramja":::


1. Válassza ki a használni kívánt beszédfelismerési erőforrást.
2. [Hozzon létre egy hang-hangolási fájlt](#create-an-audio-tuning-file) egyszerű SZÖVEGES vagy SSML szkriptek használatával. Írja be vagy töltse fel a tartalmat a hangtartalom létrehozásához.
3. Válassza ki a parancsfájl tartalmának hangját és nyelvét. A hangtartalom létrehozása magában foglalja az összes [Microsoft szöveg-beszéd hangokat](language-support.md#text-to-speech). Használhatja a standard, a neurális vagy a saját egyéni hangját is.
   >[!NOTE]
   > Az egyéni neurális hangok számára elérhető a vezérelt hozzáférés, amely lehetővé teszi, hogy a természetes hangú beszédhez hasonló, nagy felbontású hangokat hozzon létre. További részletekért lásd: [kapuzás folyamat](./text-to-speech.md).

4. Kattintson a **Lejátszás** ikonra (egy háromszögre) az alapértelmezett szintézis kimenetének megtekintéséhez. Ezután javítsa a kimenetet a kiejtés, a törés, a szurok, a sebesség, a hanglejtés, a hangstílus és más beállítások módosításával. A beállítások teljes listájáért lásd: [Speech szintézis Markup Language](speech-synthesis-markup.md). Itt látható egy [videó](https://www.youtube.com/watch?v=O1wIJ7mts_w) , amely bemutatja, hogyan finomíthatja a beszédfelismerési kimenetet hanggal a tartalom létrehozásával. 
5. [A beállított hang](#export-tuned-audio)mentése és exportálása. Ha menti a hangolási sávot a rendszeren, folytathatja a munkát, és megismételheti a kimenetet. Ha elégedett a kimenettel, létrehozhat egy hang-létrehozási feladatot az exportálási funkcióval. Megfigyelheti az exportálási feladat állapotát, és letöltheti a kimenetet az alkalmazásaival és termékeivel való használatra.

## <a name="create-an-audio-tuning-file"></a>Hang-hangolási fájl létrehozása

A tartalmat kétféleképpen lehet beolvasni a hangtartalom-létrehozási eszközbe.

**1. lehetőség:**

1. Kattintson a jobb felső sarokban található **új fájl** ikonra egy új hang-hangolási fájl létrehozásához.
2. Írja be vagy illessze be a tartalmat a szerkesztési ablakba. Az egyes fájlok karaktereinek száma 20 000. Ha a parancsfájl 20 000 karakternél hosszabb, a 2. lehetőség használatával a tartalmat automatikusan több fájlba is kioszthatja. 
3. Ne felejtse el menteni.

**2. lehetőség:**

1. Egy vagy több szövegfájl importálásához kattintson a **feltöltés** gombra. Az egyszerű szöveg-és SSML egyaránt támogatottak. Ha a parancsfájl több mint 20 000 karakterből áll, Ossza szét a fájlt bekezdések, karakter vagy reguláris kifejezések alapján. 
3. A szövegfájlok feltöltésekor ellenőrizze, hogy a fájl megfelel-e a követelményeknek.

   | Tulajdonság | Érték/megjegyzések |
   |----------|---------------|
   | Fájlformátum | Egyszerű szöveg (. txt)<br/> SSML szövege (. txt)<br/> A zip-fájlok nem támogatottak |
   | Kódolási formátum | UTF-8 |
   | Fájlnév | Minden fájlnak egyedi névvel kell rendelkeznie. Az ismétlődések nem támogatottak. |
   | Szöveg hossza | A szövegfájl karakteres korlátozása 20 000. Ha a fájlok túllépik a korlátozást, ossza meg a fájlokat az eszköz utasításait követve. |
   | SSML-korlátozások | Minden SSML-fájl csak egyetlen SSML tartalmazhat. |

**Egyszerű szöveges példa**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**SSML – példa**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Hangolt hang exportálása

Miután áttekintette a hangkimenetet, és elégedett a hangolással és a beállítással, exportálhatja a hangot.

1. Az **Exportálás** gombra kattintva hozzon létre egy hang-létrehozási feladatot. A **Hangkönyvtárba való exportálás** ajánlott, mivel a hosszú hangkimenetet és a teljes hangkimeneti élményt támogatja. Közvetlenül is letöltheti a hangot a helyi lemezre, de csak az első 10 perc érhető el.
2. Válassza ki a beállított hang kimeneti formátumát. A támogatott formátumok és mintavételi díjak listája alább található.
3. A feladat állapotát az **Exportálás feladat** lapon tekintheti meg. Ha a feladat meghiúsul, tekintse meg a teljes jelentés részletes információit tartalmazó oldalt.
4. Ha a feladat befejeződött, a hang letölthető a **hangkönyvtár** lapon.
5. Kattintson a **Letöltés** gombra. Most már készen áll az egyéni hangolt hang használatára alkalmazásaiban vagy termékeiben.

**Támogatott hangformátumok**

| Formátum | 16 kHz-es mintavételi arány | 24 kHz-es mintavételi arány |
|--------|--------------------|--------------------|
| WAV | riff-16khz-16bit-mono-PCM | riff-24khz-16bit-mono-PCM |
| MP3 | hang-16khz-128kbitrate-mono-MP3 | hang-24khz-160kbitrate-mono-MP3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Hangtartalom-létrehozási felhasználók hozzáadása/eltávolítása

Ha egynél több felhasználó szeretné használni a hangtartalom létrehozását, felhasználói hozzáférést biztosíthat az Azure-előfizetéshez és a beszédfelismerési erőforráshoz. Ha hozzáad egy felhasználót egy Azure-előfizetéshez, a felhasználó elérheti az Azure-előfizetéshez tartozó összes erőforrást. Ha azonban csak egy felhasználót vesz fel egy beszédfelismerési erőforráshoz, a felhasználó csak a beszédfelismerési erőforráshoz férhet hozzá, és nem fér hozzá az Azure-előfizetéshez tartozó egyéb erőforrásokhoz. A beszédfelismerési erőforráshoz hozzáféréssel rendelkező felhasználók hangtartalmat hozhatnak létre.

### <a name="add-users-to-a-speech-resource"></a>Felhasználók hozzáadása egy beszédfelismerési erőforráshoz

Az alábbi lépéseket követve hozzáadhat egy felhasználót egy beszédfelismerési erőforráshoz, hogy a hangtartalom létrehozását is használhassa.

1. Keresse meg a **kognitív szolgáltatásokat** a [Azure Portalban](https://portal.azure.com/), majd válassza ki azt a beszédfelismerési erőforrást, amelyhez felhasználókat szeretne hozzáadni.
2. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="Szerepkör-hozzárendelés lap":::
1. Kattintson a **Hozzáadás** > **Szerepkör-hozzárendelés hozzáadása** lehetőségre a Szerepkör-hozzárendelés hozzáadása szakasz megnyitásához. A szerepkör legördülő listában válassza ki a **kognitív szolgáltatás felhasználói** szerepkörét. Ha meg szeretné adni a beszédfelismerési erőforrás felhasználói tulajdonosát, kiválaszthatja a **tulajdonosi** szerepkört.
1. A listából válasszon ki egy felhasználót. Ha nem látja a felhasználót a listában, beírhatja a kijelölés szövegmezőbe a megjelenítendő nevek és e-mail-címek kereséséhez. Ha a felhasználó nem ebben a címtárban van, megadhatja a felhasználó [Microsoft-fiókját](https://account.microsoft.com/account) (amely az Azure Active Directory által megbízhatónak számít).
1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez. Néhány pillanat elteltével a felhasználónak a kognitív szolgáltatás felhasználói szerepköréhez kell rendelnie a beszédfelismerési erőforrás hatókörében.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="Szerepkör hozzáadása párbeszédpanel":::

1. A hozzáadott felhasználók egy meghívót tartalmazó e-mailt fognak kapni. Miután   >  **Az Azure-hoz való csatlakozásra** kattintott a meghívó elfogadása elemre, használhatja a [hangtartalom létrehozását](https://aka.ms/audiocontentcreation).

Azok a felhasználók, akik ugyanabban a beszédfelismerési erőforrásban találhatók, láthatják egymás munkáját a hangtartalom-létrehozási Studióban. Ha azt szeretné, hogy az egyes felhasználók egyedi és privát munkaterülettel rendelkezzenek a hangtartalom létrehozásakor, [hozzon létre egy új beszédfelismerési erőforrást](#step-2---create-a-speech-resource) minden felhasználó számára, és adjon meg minden felhasználónak egyedi hozzáférést a beszédfelismerési erőforráshoz. 

### <a name="remove-users-from-a-speech-resource"></a>Felhasználók eltávolítása egy beszédfelismerési erőforrásból
1. Keresse meg a **kognitív szolgáltatásokat** a Azure Portalban, válassza ki azt a beszédfelismerési erőforrást, amelyből el szeretné távolítani a felhasználókat.
2. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre. Kattintson a **szerepkör-hozzárendelések** lapra a beszédfelismerési erőforrás összes szerepkör-hozzárendelésének megtekintéséhez.
3. Válassza ki az eltávolítani kívánt felhasználókat, majd kattintson   >  **az OK eltávolítása gombra**.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="Eltávolítás gomb":::

### <a name="enable-users-to-grant-access"></a>Hozzáférés engedélyezésének engedélyezése a felhasználók számára
Ha azt szeretné, hogy az egyik felhasználó hozzáférjen más felhasználókhoz, a felhasználónak a beszédfelismerési erőforrás tulajdonosi szerepkörét kell megadnia, és a felhasználót az Azure címtár-olvasóként kell beállítania. 
1. Adja hozzá a felhasználót a beszédfelismerési erőforrás tulajdonosához. Lásd: [felhasználók hozzáadása egy beszédfelismerési erőforráshoz](#add-users-to-a-speech-resource).
    :::image source="media/audio-content-creation/add-role.png" alt-text="Szerepkör-tulajdonos mező":::
1. Válassza ki a bal felső sarokban található összecsukott menüt. Kattintson a **Azure Active Directory** elemre, majd kattintson a **felhasználók** elemre.
1. Keresse meg a felhasználó Microsoft-fiók, és lépjen a felhasználó részletes oldalára. Kattintson a **hozzárendelt szerepkörök** elemre.
1. Kattintson a **hozzárendelések hozzáadása**  ->  **Directory-olvasók** elemre.

## <a name="see-also"></a>Lásd még

* [Hosszú hang API](./long-audio-api.md)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
