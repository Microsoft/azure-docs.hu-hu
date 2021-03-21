---
title: Útmutató a hatékony horgonyok élményéhez
description: Útmutatás és szempontok az Azure térbeli horgonyok használatával történő hatékony létrehozásához és megkereséséhez.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: a6bc500516de7e554c38a335ea57519a39c8f602
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95487503"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Hatékony Anchor-élmény létrehozása Azure térbeli horgonyok használatával

Ez a cikk útmutatást és szempontokat tartalmaz, amelyek segítségével hatékonyan hozhat létre és kereshet horgonyokat az Azure térbeli horgonyok használatával.

## <a name="anchor-improvement-over-time"></a>Javítás az idő múlásával

Az Azure térbeli horgonyok minden alkalommal, amikor megtalálják a horgonyokat, megpróbáljuk javítani a jövőbeli megkeresési műveletek minőségét. Ezt úgy tesszük, hogy a gyűjtött környezeti adatokat felhasználva kibővítjük a keresett horgonyokra vonatkozó vizuális információkat. Ez a folyamat a motorháztető alatt fut, és az Azure térbeli horgonyok szolgáltatás által futtatott offline optimalizálás a környezet optimalizálása érdekében. Az egyes műveletek során összegyűjtött további adatok erőteljesebben értik a környezetet. Ez javítja a minőséget, és lehetővé teszi, hogy jobban megkeresse a horgonyokat a környezeti változások, az idő múlásával, valamint a különböző nézőpontokból és perspektíváokból származó horgonyokat kereső felhasználók számára.

## <a name="good-anchors"></a>Jó horgonyok

Habár az Azure térbeli horgonyok az idő múlásával javítják a horgonyok minőségét, fontos, hogy a jó horgonyok létrehozásához a felhasználói élményben (UX) betanítsa vagy vezérelje a felhasználókat. A jó alapok létrehozásához való befektetéssel a végfelhasználók megbízhatóan megtalálják a horgonyokat:

- Különböző eszközökön.
- Különböző időpontokban.
- Különböző megvilágítási körülmények között.
- A kívánt perspektívából a tárhelyen belül.

## <a name="static-and-dynamic-locations"></a>Statikus és dinamikus helyszínek

A rögzítési élmény megtervezésének része a helyszínek kiválasztása. A helyek statikusak lesznek, és a hely rendszergazdája határozza meg? Vagy dinamikusak lesznek, és a felhasználó határozza meg?

Előfordulhat, hogy a kiskereskedelmi tárolók kezelője egy statikus, áruházbeli élményt szeretne használni a felhasználók megkereséséhez. A vegyes valóságshow-játékok fejlesztői azonban valószínűleg azt szeretnék, hogy a felhasználók hol játszanak.

Statikus helyek esetén a rendszergazdák megtanítják, hogy a tárhelyet a jó horgonyokkal töltött idő alapján kell kidolgozni.

A dinamikus helyszínek esetében érdemes meggondolni, hogyan taníthatja meg vagy irányíthatja a felhasználókat az UX-ben, hogy jó horgonyokat hozzon létre.

## <a name="stable-visual-features"></a>Stabil vizualizációs funkciók

A vegyes valóságot és a kibővített valóságot használó eszközökön használt vizuális nyomkövető rendszerek a környezet vizuális funkcióit használják. A legmegbízhatóbb felhasználói élmény eléréséhez:

- Olyan *helyen hozza létre* a horgonyokat, amelyek stabil vizualizációs funkciókkal rendelkeznek (azaz olyan funkciókkal, amelyek nem változnak gyakran).

- *Ne* hozzon létre olyan nagyméretű üres felületen lévő horgonyokat, amelyek nem rendelkeznek megkülönböztető tulajdonságokkal.

- *Ne* hozzon létre horgonyokat a kifejezetten tükröző anyagokon.

- *Ne* hozzon létre horgonyokat olyan felületeken, ahol a minta ismétlődik, például szőnyeg vagy háttérkép.

![Példák jó környezetre a horgonyokhoz és a hibás környezetekhez a horgonyokhoz](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Különböző megtekintési perspektívák

Horgony létrehozásakor gondolja át azokat a személyeket, akik később megpróbálják megtalálni a horgonyt.

Vegyünk például egy horgonyt egy olyan helyiség közepén, amely két ajtóval rendelkezik. Valószínű, hogy lehetővé szeretné tenni a felhasználók számára, hogy bármelyik ajtóról belépjenek a helyiségbe. A horgony létrehozásakor mindkét ajtóból be kell olvasnia a pozícióját. A perspektívákat úgy módosíthatja, hogy a horgony körül rögzítse a környezet adatait, így a felhasználók bármelyik ajtóról megkereshetik a horgonyt.

Általánosságban elmondható, hogy a horgony létrehozásakor a rendszer megvizsgálja azon személyek perspektíváit, akik megpróbálják megkeresni. Tehát ha a virtuális tartalmat egy kültéri szobrászatba helyezi, érdemes megmutatni a szobrot a vizsgálat során, miközben a horgonyt hozza létre. Ha a horgony egy helyiség sarkában van, csak egy irányt kell megközelítenie. A horgony létrehozásakor csak ebből a nézőpontból lehet beolvasni.

## <a name="multiple-anchors"></a>Több horgony

A megvilágítás különbséget tehet az alkalmazás által észlelt vizuális funkciókban. Az erős természetes fényben létrehozott horgonyok nehéznek bizonyulnak mesterséges fényben, és fordítva.

Ha ezt a problémát tapasztalja, segít két horgony létrehozásában. Ugyanazon a helyen hozzon létre egy horgonyt a napfényben, egy másikat pedig mesterséges fényben. Az alkalmazás ezután lekérdezheti mindkét horgonyt. Ha a horgony található, az alkalmazás a horgonyt fogja tartalmazni.

Hasonlóképpen, azokban a környezetekben, ahol a vizualizációs funkciók módosulnak, mivel a legtöbb objektum mozog, több horgony segíthet. Ha egy horgony túlságosan nehéznek bizonyul a környezet jelentős változásai miatt, a horgonyt egy újat is lecserélheti. Ezt megteheti például egy olyan kiskereskedelmi tárolóban, ahol az elrendezés néhány havonta frissül.

## <a name="targets-and-rooms"></a>Célok és szobák

Sok esetben a horgony az alkalmazás felhasználói felületének belépési pontja. Gyorsan és megbízhatóan szeretné átvenni ezt a lépést, így a felhasználók megadhatják a felhasználói élményt. Az idő, amikor a felhasználók megtalálják a horgonyokat, fontos tervezési lépés. Érdemes meggondolni, hogyan találhatja meg a horgonyokat két széles forgatókönyv esetén: *célok* és *szobák*.

### <a name="targets"></a>Targets

A cél forgatókönyvben a horgony helye jól ismert. Például egy kitalált Mixed-Reality festészeti alkalmazásban az egyik felhasználó egy virtuális vászonra helyezi a falra. Arra utasítja a helyiség többi felhasználóját, hogy az eszközeit a falon ugyanazon a helyen mutasson, hogy megkeresse a horgonyt, és megkezdje a felhasználói élményt.

Egy másik példa a cél forgatókönyvre egy olyan kávézóban, amely a következőt olvassa: "keresés a Deals". A Coffee Shop itt elhelyezett horgonyt tartalmaz. Ahogy a felhasználók beolvasják a jelt, megkeresik a horgonyt, és megadják a kibővített valóságot, amely a kávén található ajánlatokat keresi meg.

A cél forgatókönyvben a fotók segíthetnek. Ha az eszközön a felhasználók számára egy fényképet jelenít meg, akkor gyorsan azonosíthatja, hogy mi a valós világban való vizsgálat. Előfordulhat például, hogy a felhasználók a GPS használatával megérkeznek a kívánt cél általános területére. A felhasználó megérkezése után az alkalmazás a cél fényképét jeleníti meg. A felhasználó megtekinti a területet, megkeresi a célt, és megkeresi a horgonyt.

![Egy horgony illusztrációja, amely a cél fényképét jeleníti meg a felhasználó mobileszközön](./media/start-here-edit.png)

### <a name="rooms"></a>Szobák

A Room-forgatókönyvben a felhasználók csak azt tudják, hogy van egy horgony itt valahol. A felhasználók beszkennelik a helyet az eszközzel, és gyorsan megkeresik a horgonyt.

Ez a megoldás általában jól válogatott horgonyok létrehozását igényli, ahogy azt a különböző megtekintési perspektívák tárgyalják. Ha a horgony létrehozásakor számos perspektívából beolvasta a termet, a felhasználók szinte bárhol ellenőrizhetik, amikor megpróbálják megkeresni.

![Ábra arról, hogy a felhasználó hogyan tud beolvasni egy helyet a horgony megtalálásához](./media/scan-room.png)

Lényegében több időt is igénybe vehet, amikor létrehozza a horgonyt, így később a felhasználók bekereshetik és gyorsan megkereshetik a horgonyt. A tapasztalatok létrehozásakor figyelembe kell vennie ezt a fontos kompromisszumot.

A korábban megjelenő kevert-Reality festészeti alkalmazás példája nem működik megfelelően a szobákban. Itt az a felhasználó, aki a horgonyt helyezi, másokat is szeretne csatlakoztatni a gyors élményhez. A felhasználók nem szeretnének várni a élmény elindítására, amíg a szobát alaposan be nem vizsgálja. Mivel az összes felhasználó pontosan tudja, hol szeretné megkeresni az eszközét a horgonyok megtalálásához, ez a példa jobban működik célként.

## <a name="anchor-location"></a>Horgony helye

A vizuális nyomkövető rendszerek a környezet vizuális szolgáltatásaira támaszkodnak. Minél több vizuális funkciót tartalmaz a vizsgálat, annál nagyobb a valószínűsége annak, hogy megtaláljon egy horgonyt.

Az ebben a szakaszban található általános útmutatást követve hozzon létre egy olyan UX-t, amely a környezet hasznos vizsgálatára ösztönöz.

Először is, ha a felhasználó néhány másodpercen belül nem talál horgonyt, az alkalmazásnak ösztönöznie kell a felhasználókat az eszköz áthelyezésére, hogy több perspektívát rögzítsen. Az alkalmazás arra is ösztönözheti a felhasználókat, hogy a környezetbe lépjenek, hogy több perspektívából is megkeressék a horgonyt. Minél több funkciói perspektívát lát az eszköz, annál jobbat, mivel növeli a horgony elérésének valószínűségét, és további környezeti adatokat is gyűjt, amelyeket a rendszer a horgony minőségének javítására fog használni.

A cél forgatókönyvek esetében kérje meg a felhasználót, hogy a cél körül mozogjon, hogy megtekinthesse a különböző perspektívákat. Más szóval, kérje meg a felhasználót, hogy rögzítse a célt az új perspektívából, amíg a horgony nem található.

A Room-forgatókönyvek esetében kérje meg a felhasználót, hogy lassan vizsgálja meg a helyet. Tegyük fel például, hogy a felhasználó bekapcsolja a 180 fokos, vagy akár 360 fokos mértékben a szobát. Vagy kérje meg a felhasználót, hogy tekintse meg a szobát egy új perspektívából.

A legértelmesebb módszer a teljes helyiség vizsgálata. A helyiség vizsgálata a környezet több vizualizációs funkcióját rögzíti, mint például a közeli fal vizsgálata. A közeli fal vizsgálata nem rögzíti a környezet számos hasznos vizuális funkcióját.

Az eszközt nem lehet ismételten az oldalról a oldalról áthelyezni a horgony keresésekor. Ez egyszerűen az azonos perspektívából származó pontokat rögzíti.

## <a name="experience-tests"></a>Tapasztalati tesztek

Ebben a cikkben az általános irányelveket ismertetjük. A térbeli Horgonyokkal olyan alkalmazásokat írunk, amelyek a valós világban működnek. Emiatt érdemes időt fordítani az alkalmazás rögzített forgatókönyvek valós környezetekben történő tesztelésére. Ez különösen igaz olyan környezetekben, amelyek a felhasználók számára az alkalmazás használatának várható helyét jelölik.
