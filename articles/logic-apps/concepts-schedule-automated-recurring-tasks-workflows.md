---
title: Ismétlődő feladatok és munkafolyamatok ütemezése a Azure Logic Appsban
description: Áttekintés az ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezéséről Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: e9fbafa9f3c33d10496e84f61e1f2b97f6328d3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "100581810"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezése és futtatása az Azure Logic Apps használatával

A Logic Apps használatával automatizált ismétlődő feladatokat és folyamatokat hozhat létre és futtathat ütemezett időközönként. Ha olyan logikai alkalmazás-munkafolyamatot hoz létre, amely egy beépített ismétlődési eseményindítóval vagy egy csúszó ablak eseményindítóval kezdődik, amely az ütemezések típusú eseményindítók, a feladatokat azonnal, később vagy ismétlődő időközönként is futtathatja. Az Azure-on belüli és kívüli szolgáltatásokat, például HTTP-vagy HTTPS-végpontokat hívhat meg, üzeneteket küldhet az Azure-szolgáltatásba, például az Azure Storage-ba és a Azure Service Busba, vagy beolvashatja a fájlmegosztást feltöltött fájlokat. Az ismétlődési eseményindítóval összetett ütemezéseket és speciális ismétlődéseket is beállíthat a futó feladatokhoz. Ha többet szeretne megtudni a beépített ütemezett eseményindítókkal és műveletekkel kapcsolatban, tekintse meg az eseményindítók és az [ütemezett műveletek](#schedule-actions) [időzítését](#schedule-triggers) ismertető témakört. 

> [!TIP]
> Ütemezheti és futtathatja az ismétlődő munkaterheléseket anélkül, hogy külön logikai alkalmazást hozna létre minden ütemezett feladathoz, és a [munkafolyamatok régiónként és előfizetésben](../logic-apps/logic-apps-limits-and-config.md#definition-limits)való futtatásával korlátozva van. Ehelyett használhatja az Azure rövid útmutató sablonja által létrehozott Logic app-mintát [: Logic apps Feladatütemező](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> A Logic Apps Feladatütemező sablon létrehoz egy CreateTimerJob logikai alkalmazást, amely egy TimerJob logikai alkalmazást hív meg. Ezt követően a CreateTimerJob logikai alkalmazást API-ként hívhatja meg egy HTTP-kérelem elküldésével, és a kérelem bemenetként való átadásával. A CreateTimerJob logikai alkalmazás minden egyes hívása meghívja a TimerJob Logic alkalmazást, amely egy új TimerJob-példányt hoz létre, amely folyamatosan fut a megadott ütemezés alapján, vagy amíg a megadott korlátot nem teljesíti. Így tetszőleges számú TimerJob-példányt futtathat úgy, hogy a munkafolyamatok korlátai miatt nem kell aggódnia, mert a példányok nem egyéni logikai alkalmazások munkafolyamat-definíciói vagy erőforrásai.

Ez a lista néhány példát mutat be, amelyeket a beépített eseményindítók használatával futtathat:

* Belső adat beolvasása, például minden nap SQL tárolt eljárás futtatása.

* Külső adatok beolvasása, például a NOAA szolgáltatás 15 percenkénti lekéréses időjárási jelentései.

* Jelentésadatok küldése, például e-mailben az összes megrendelés összegzése az elmúlt héten megadott összegnél nagyobb mértékben.

* Adatok feldolgozása, például a ma feltöltött képek tömörítése hétköznapokon a munkaidőn kívül.

* Az adattisztítást, például a három hónapnál régebbi összes Tweet törlését.

* Az archivált adatok, például a leküldéses számlák egy backup szolgáltatásba, minden nap 1:00-kor, a következő kilenc hónapban.

A következő művelet futtatása előtt a beépített műveleteket is használhatja a munkafolyamat szüneteltetéséhez, például:

* Várjon, amíg egy hétköznapon el nem küldi az állapot-frissítést e-mailben.

* Késleltesse a munkafolyamatot addig, amíg egy HTTP-hívásnak nincs ideje befejezni az eredmény folytatása és beolvasása előtt.

Ez a cikk a beépített eseményindítók és műveletek ütemezett funkcióit ismerteti.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Eseményindítók ütemezett indítása

A logikai alkalmazás munkafolyamatát az ismétlődési eseményindító vagy a csúszó ablak eseményindító használatával indíthatja el, amely nincs egyetlen adott szolgáltatáshoz vagy rendszerhez társítva. Ezek az eseményindítók elindítják és futtatják a munkafolyamatot a megadott ismétlődés alapján, ahol kiválaszthatja az intervallumot és a gyakoriságot, például a másodpercek, a percek, az órák, a napok, a hetek és a hónapok számát A kezdési dátumot és időpontot is beállíthatja az időzónával együtt. Minden alkalommal, amikor egy eseményindító tüzek, Logic Apps létrehoz és futtat egy új munkafolyamat-példányt a logikai alkalmazáshoz.

Ezek az eseményindítók közötti különbségek:

* **Ismétlődés**: a munkafolyamatot a megadott ütemterv alapján rendszeres időközönként futtatja. Ha az eseményindító elmulasztja az ismétlődéseket, például a fennakadások vagy a letiltott munkafolyamatok miatt, az ismétlődési eseményindító nem dolgozza fel a kihagyott ismétlődéseket, de a következő ütemezett intervallummal újraindítja az ismétlődéseket.

  Ha a **nap** lehetőséget választja a gyakoriság beállításnál, megadhatja a nap óráját, valamint az óra percét, például minden nap 2:30-kor. Ha a **hét** lehetőséget választja a gyakoriság beállításnál, akkor a hét napjait is kiválaszthatja, például a szerda és a szombat lehetőséget. Megadhat egy kezdési dátumot és időpontot is az ismétlődési ütemtervhez tartozó időzónával együtt.

  > [!TIP]
  > Ha az ismétlődés nem ad meg konkrét [kezdési dátumot és időpontot](#start-time), az első ismétlődés azonnal fut a logikai alkalmazás mentésekor vagy telepítésekor, az eseményindító ismétlődésének beállítása ellenére. A viselkedés elkerülése érdekében adjon meg egy kezdési dátumot és időpontot, amikor az első ismétlődést futtatni szeretné.
  >
  > Ha az ismétlődés nem ad meg olyan speciális ütemezési beállításokat, mint például a jövőbeli ismétlődések futtatásához megadott időpontok, ezek az ismétlődések az utolsó futási időn alapulnak. Ennek eredményeképpen az ismétlődések indítási időpontja a tárolási hívások során felmerülő tényezők, például a késések miatt eltérhet. Ha meg szeretné győződni arról, hogy a logikai alkalmazás nem hagyja ki az ismétlődést, különösen akkor, ha a gyakoriság napokban vagy már nem érhető el, próbálkozzon a következő beállításokkal:
  >
  > * Adja meg az ismétlődés kezdő dátumát és időpontját, valamint azokat a megadott időpontokat, amikor a későbbi ismétlődéseket az **ezen órákban** elnevezett tulajdonságok használatával és **ezen a percekben** szeretné futtatni, amelyek csak a **napi** és **heti** gyakorisággal érhetők el.
  >
  > * Az ismétlődési eseményindító helyett használja a [csúszó ablak eseményindítóját](../connectors/connectors-native-sliding-window.md).

  További információ: [ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása az ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md).

* **Csúszó ablak**: rendszeres időközönként futtatja a munkafolyamatot, amely folytonos adattömbökben kezeli az adategységeket. Ha az eseményindító elmulasztja az ismétlődéseket, például fennakadások vagy letiltott munkafolyamatok miatt, a csúszó ablak eseményindítója visszatér, és feldolgozza a kihagyott ismétlődéseket.

  Megadhatja a kezdési dátumot és időt, az időzónát és egy időtartamot, amellyel késleltetheti az egyes ismétlődéseket a munkafolyamatban. Ez az trigger nem támogatja a speciális ütemterveket, például a nap adott óráját, az óra percét és a hét napjait. További információ: [ismétlődő feladatok és munkafolyamatok létrehozása, beosztása és futtatása a csúszó ablakos triggerrel](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Ütemezett műveletek

A Logic app-munkafolyamatban bármilyen művelet után a késleltetés és a késleltetés, amíg a műveletek a következő művelet futtatása előtt nem tudják a munkafolyamatot várni.

* **Késleltetés**: várjon a következő művelet futtatására a megadott számú időegységre (például másodperc, perc, óra, nap, hét vagy hónap). További információ: [a következő művelet késleltetése a munkafolyamatokban](../connectors/connectors-native-delay.md).

* **Késleltetés eddig**: várja meg a következő művelet futtatását a megadott dátumig és időpontig. További információ: [a következő művelet késleltetése a munkafolyamatokban](../connectors/connectors-native-delay.md).

<a name="start-time"></a>

## <a name="patterns-for-start-date-and-time"></a>A kezdő dátum és idő mintái

Íme néhány példa, amely bemutatja, hogyan szabályozhatja az ismétlődést a kezdő dátummal és időponttal, valamint azt, hogy az Logic Apps szolgáltatás hogyan futtatja ezeket az ismétlődéseket:

| Kezdési idő | Ismétlődés ütemezés nélkül | Ismétlődés az ütemtervtel (csak ismétlődési eseményindító) |
|------------|-----------------------------|----------------------------------------------------|
| nEz egy | Azonnal futtatja az első munkafolyamatot. <p>A jövőbeli munkaterheléseket az utolsó futtatási idő alapján futtatja. | Azonnal futtatja az első munkafolyamatot. <p>A jövőbeli munkaterheléseket a megadott ütemterv alapján futtatja. |
| Kezdési idő a múltban | **Ismétlődési** eseményindító: a megadott kezdési idő alapján kiszámítja a futtatási időpontokat, és elveti a korábbi futtatási időpontokat. <p><p>Futtatja az első számítási feladatot a következő későbbi futtatási időpontban. <p><p>A jövőbeli munkaterheléseket az utolsó futtatási idő alapján futtatja. <p><p>**Csúszó ablak** trigger: a megadott kezdési időpont alapján kiszámítja a futtatási időpontokat, és kiértékeli a korábbi futtatási időpontokat. <p><p>A jövőbeli munkaterheléseket a megadott kezdési időpont alapján futtatja. <p><p>További magyarázatért tekintse meg a táblázat következő példáját. | A kezdési időpontból kiszámított ütemterv alapján az első számítási feladatot a kezdési időpontnál *hamarabb* futtatja. <p><p>A jövőbeli munkaterheléseket a megadott ütemterv alapján futtatja. <p><p>**Megjegyzés:** Ha ütemezéssel adja meg az ismétlődést, de nem ad meg órákat vagy percet az ütemezéshez, Logic Apps a későbbi futási időket az első futási idő órában vagy percben használva számítja ki. |
| Kezdési idő most vagy a jövőben | Futtatja az első számítási feladatot a megadott kezdési időpontban. <p><p>**Ismétlődési** eseményindító: a jövőbeli munkaterheléseket az utolsó futtatási idő alapján futtatja. <p><p>**Csúszó ablakos** trigger: a jövőbeli munkaterheléseket a megadott kezdési időpont alapján futtatja. | A kezdési időpontból kiszámított ütemterv alapján az első számítási feladatot a kezdési időpontnál *hamarabb* futtatja. <p><p>A jövőbeli munkaterheléseket a megadott ütemterv alapján futtatja. <p>**Megjegyzés:** Ha ütemezéssel adja meg az ismétlődést, de nem ad meg órákat vagy percet az ütemezéshez, Logic Apps a későbbi futási időket az első futási idő órában vagy percben használva számítja ki. |
||||

*Példa korábbi kezdési időre és ismétlődésre, de nincs ütemezése*

Tegyük fel, hogy az aktuális dátum és idő szeptember 8., 2017, 1:00 PM. Megadhatja a kezdési dátumot és az időpontot szeptember 7., 2017., 2:00 PM, amely a múltban, valamint egy ismétlődés, amely két naponként fut.

| Kezdési idő | Aktuális idő | Ismétlődés | Ütemezés |
|------------|--------------|------------|----------|
| 2017-09-**07** T14:00:00Z <br>(2017-09 –**07** , 2:00 PM) | 2017-09 –**08** T13:00:00Z <br>(2017-09 –**08** , 1:00 PM) | Két naponta | nEz egy |
|||||

Az ismétlődési eseményindító esetében a Logic Apps motor a kezdési idő alapján kiszámítja a futási időt, elveti a múltbeli futtatási időpontokat, a következő jövőbeli kezdési időt használja az első futtatáshoz, és kiszámítja a jövőbeli futtatásokat az utolsó futtatási idő alapján.

Így néz ki az ismétlődés:

| Kezdési idő | Első futtatás időpontja | Jövőbeli futtatási idők |
|------------|----------------|------------------|
| 2017-09 –**07** , 2:00 PM | 2017-09-**09** , 2:00 PM | 2017-09 –**11** , 2:00 PM </br>2017-09 –**13** , 2:00 PM </br>2017-09 –**15** , 2:00 PM </br>és így tovább... |
||||

Tehát attól függetlenül, hogy a múltban milyen messzire van szükség a kezdési időponthoz, például: 2017-09-**05** , 2:00 pm vagy 2017-09-2:00 **01** , az első futtatás mindig a következő jövőbeli kezdési időpontot használja.

A csúszó ablakos trigger esetén a Logic Apps motor a kezdési idő alapján kiszámítja a futási időt, a korábbi futtatási időpontokat, az első futtatás kezdő időpontját, és a jövőbeli futtatásokat a kezdési időpont alapján számítja ki.

Így néz ki az ismétlődés:

| Kezdési idő | Első futtatás időpontja | Jövőbeli futtatási idők |
|------------|----------------|------------------|
| 2017-09 –**07** , 2:00 PM | 2017-09 –**08** , 1:00 PM (aktuális idő) | 2017-09-**09** , 2:00 PM </br>2017-09 –**11** , 2:00 PM </br>2017-09 –**13** , 2:00 PM </br>2017-09 –**15** , 2:00 PM </br>és így tovább... |
||||

Tehát attól függetlenül, hogy a múltban milyen messzire van szükség a kezdési időponthoz, például: 2017-09-**05** , 2:00 pm vagy 2017-09-2:00 **01** , az első futtatáskor mindig a megadott kezdési időpontot használja.

<a name="daylight-saving-standard-time"></a>

## <a name="recurrence-for-daylight-saving-time-and-standard-time"></a>Ismétlődés a nyári időszámításhoz és a téli időponthoz

Az ismétlődő beépített eseményindítók tiszteletben tartják a beállított ütemtervet, beleértve a megadott időzónát is. Ha nem választja ki az időzónát, a nyári időszámításra (DST) hatással lehet az eseményindítók futtatásakor, például ha a kezdési időt egy órára továbbítja a DST indításakor és egy órával a DST-időszak végéig. A feladatok ütemezésekor Logic Apps a várólistára helyezi az üzenetet, és megadja, hogy az üzenet mikor válik elérhetővé az utolsó feladat futtatásakor és a következő feladat futásának UTC-ideje szerint.

Ha el szeretné kerülni ezt a váltást, hogy a logikai alkalmazás a megadott kezdési időpontban fusson, győződjön meg róla, hogy kijelöl egy időzónát. Így a logikai alkalmazás UTC-ideje a szezonális időváltozások ellen is tolódik.

<a name="dst-window"></a>

> [!NOTE]
> Előfordulhat, hogy a 2:00 – 3:00 közötti kezdő eseményindítók problémákba ütközhet, mert a DST-változások a 2:00 ÓRAKOR történnek, ami azt okozhatja, hogy a kezdési idő érvénytelenné vagy kétértelművé válik. Ha több logikai alkalmazás is van ugyanazon a kétértelmű intervallumon belül, átfedésben lehetnek. Emiatt előfordulhat, hogy el szeretné kerülni a 2:00 és a 3:00 közötti kezdési időpontokat.

Tegyük fel például, hogy van két logikai alkalmazás, amely naponta fut. Az egyik logikai alkalmazás a 1:30-as helyi idő szerint fut, míg a másik óra múlva a következő időpontban fut: 2:30 AM helyi idő. Mi történik az alkalmazások kezdő időpontjaival, amikor a DST elindul és végződik?

* Az eseményindítók csak akkor futnak, ha az idő egy órával tovább fut?

* Az eseményindítók kétszer futnak, amikor az idő egy órával visszafelé halad?

Ha ezek a logikai alkalmazások az UTC-6:00 Central Time (USA & Canada) zónát használják, akkor ez a szimuláció azt mutatja, hogy az UTC-idő a 2019-ben a DST-változások ellensúlyozására szolgál, és szükség szerint egy óra hátra vagy továbbítása szükséges, hogy az alkalmazások a várt helyi időpontokban továbbra is a kihagyott vagy duplikált Futtatás nélkül fussanak

* **03/10/2019: a DST kezdete: 2:00 ÓRAKOR, eltolási idő egy órával előre**

  A DST-kezdést követően az UTC-idő egy órával visszafelé halad, hogy a logikai alkalmazás továbbra is ugyanazon a helyi időpontban fusson:

  * Logikai alkalmazás #1

    | Date | Idő (helyi) | Idő (UTC) | Jegyzetek |
    |------|--------------|------------|-------|
    | 03/09/2019 | 1:30:00 | 7:30:00 | A DST érvénybe léptetésének napját megelőző UTC. |
    | 03/10/2019 | 1:30:00 | 7:30:00 | Az UTC ugyanaz, mert a DST nem lépett érvénybe. |
    | 03/11/2019 | 1:30:00 | 6:30:00 | Az UTC a DST életbe léptetése után egy órával korábbira váltott. |
    |||||

  * Logikai alkalmazás #2

    | Date | Idő (helyi) | Idő (UTC) | Jegyzetek |
    |------|--------------|------------|-------|
    | 03/09/2019 | 2:30:00 | 8:30:00 | A DST érvénybe léptetésének napját megelőző UTC. |
    | 03/10/2019 | 3:30:00 * | 8:30:00 | A DST már érvényben van, ezért a helyi idő egy órával tovább mozgott, mert az UTC-6:00 időzóna az UTC-5:00 értékre módosult. További információkért lásd: a [2:00-3:00 am közötti kezdő eseményindítók](#dst-window). |
    | 03/11/2019 | 2:30:00 | 7:30:00 | Az UTC a DST életbe léptetése után egy órával korábbira váltott. |
    |||||

* **11/03/2019: a DST a 2:00 ÓRAKOR ér véget, és a váltás ideje egy óra hátra**

  A kompenzációhoz az UTC-idő egy órára továbbítható, így a logikai alkalmazás továbbra is ugyanazon a helyi időpontban fut:

  * Logikai alkalmazás #1

    | Date | Idő (helyi) | Idő (UTC) | Jegyzetek |
    |------|--------------|------------|-------|
    | 11/02/2019 | 1:30:00 | 6:30:00 ||
    | 11/03/2019 | 1:30:00 | 6:30:00 ||
    | 11/04/2019 | 1:30:00 | 7:30:00 ||
    |||||

  * Logikai alkalmazás #2

    | Date | Idő (helyi) | Idő (UTC) | Jegyzetek |
    |------|--------------|------------|-------|
    | 11/02/2019 | 2:30:00 | 7:30:00 ||
    | 11/03/2019 | 2:30:00 | 8:30:00 ||
    | 11/04/2019 | 2:30:00 | 8:30:00 ||
    |||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Futtatás csak egyszer

Ha a későbbiekben csak egyszer szeretné futtatni a logikai alkalmazást, használhatja a **Scheduler: Run Once Jobs** sablont. Miután létrehozta az új logikai alkalmazást, de a Logic Apps Designer megnyitása előtt, a **sablonok** szakaszban, a **Kategória** listáról válassza az **ütemterv** lehetőséget, majd válassza ki ezt a sablont:

![Válassza a "Scheduler: Futtatás a feladatok után" sablont](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Vagy ha a logikai alkalmazást a **http-kérelem fogadása** után is elindíthatja, és a kezdési időpontot az trigger paraméterének adja át. Az első művelethez használja a **késleltetés az ütemezésig** műveletet, és adja meg a következő művelet futásának időpontját.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Ismétlődések – példa

Az alábbi példa a beállításokat támogató eseményindítók különböző ismétlődéseit jeleníti meg:

| Eseményindító | Ismétlődés | Időköz | Gyakoriság | Kezdési idő | Ezeken a napokon | Ezekben az órákban | Ezekben a percekben | Megjegyzés |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Megismétlődésének <br>Csúszóablak | Futtatás 15 percenként (nincs kezdő dátum és idő) | 15 | Minute | nEz egy | érhető | nEz egy | nEz egy | Ez az ütemezés azonnal elindul, majd az utolsó futási idő alapján kiszámítja a jövőbeli ismétlődéseket. |
| Megismétlődésének <br>Csúszóablak | Futtatás 15 percenként (kezdő dátummal és idővel) | 15 | Minute | *StartDate* T *kezdő időpont*– Z | érhető | nEz egy | nEz egy | Ez az ütemezés nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál, majd az utolsó futási idő alapján kiszámítja a jövőbeli ismétlődéseket. |
| Megismétlődésének <br>Csúszóablak | Minden órában fut, az óra (kezdő dátummal és idővel) | 1 | Óra | *StartDate* THH: 00:00Z | érhető | nEz egy | nEz egy | Ez az ütemterv nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál. A jövőbeli ismétlődések óránként futnak a "00" percben, amely Logic Apps a kezdési időpontból számítja ki a számítást. <p>Ha a gyakoriság értéke "Week" vagy "Month", akkor ez az ütemterv hetente vagy havonta egy nappal fut le. |
| Megismétlődésének <br>Csúszóablak | Futtatás óránként, naponta (nincs kezdő dátum és idő) | 1 | Óra | nEz egy | érhető | nEz egy | nEz egy | Ez az ütemezés azonnal elindul, és az utolsó futási idő alapján kiszámítja a jövőbeli ismétlődéseket. <p>Ha a gyakoriság értéke "Week" vagy "Month", akkor ez az ütemterv hetente vagy havonta egy nappal fut le. |
| Megismétlődésének <br>Csúszóablak | Futtatás óránként, minden nap (kezdő dátummal és idővel) | 1 | Óra | *StartDate* T *kezdő időpont*– Z | érhető | nEz egy | nEz egy | Ez az ütemezés nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál, majd az utolsó futási idő alapján kiszámítja a jövőbeli ismétlődéseket. <p>Ha a gyakoriság értéke "Week" vagy "Month", akkor ez az ütemterv hetente vagy havonta egy nappal fut le. |
| Megismétlődésének <br>Csúszóablak | Minden órában 15 percenként fut, óránként (kezdő dátummal és idővel) | 1 | Óra | *StartDate* T00:15:00Z | érhető | nEz egy | nEz egy | Ez az ütemterv nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál. A jövőbeli ismétlődések a "15" perces jellel futnak, amely Logic Apps a kezdési időpontból számítja ki, így: 00:15, 1:15, 2:15 AM és így tovább. |
| Ismétlődés | Minden órában 15 percenként fut, óránként (nincs kezdő dátum és idő) | 1 | Nap | nEz egy | érhető | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Ez az ütemterv a következő időpontban fut: 00:15, 1:15, 2:15, és így tovább. Emellett ez az ütemterv az "Hour" gyakoriságának és a "15" perces kezdési időpontnak felel meg. |
| Ismétlődés | Futtassa 15 percenként a megadott percenkénti jelzéseket (nincs kezdő dátum és idő). | 1 | Nap | nEz egy | érhető | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ez az ütemezés addig nem indul el, amíg a következő megadott 15 perces megjelölés be nem fejeződik. |
| Ismétlődés | A logikai alkalmazás mentésekor naponta, a percek alatt, *illetve* a percben is futtatható. | 1 | Nap | nEz egy | érhető | 8 | nEz egy | Kezdési dátum és idő nélkül ez az ütemterv a logikai alkalmazás mentésekor (PUT művelet) történő mentés időpontja alapján fut le. |
| Ismétlődés | Napi Futtatás 8:00 ÓRAKOR (kezdő dátummal és idővel) | 1 | Nap | *StartDate* T08:00:00Z | érhető | nEz egy | nEz egy | Ez az ütemterv nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál. A jövőbeli előfordulások naponta, 8:00 ÓRAKOR futnak. | 
| Ismétlődés | Napi Futtatás 8:00 ÓRAKOR (nincs kezdő dátum és idő) | 1 | Nap | nEz egy | érhető | 8 | 00 | Ez az ütemterv minden nap 8:00 ÓRAKOR fut. |
| Ismétlődés | Napi Futtatás 8:00 órakor és 4:00 PM | 1 | Nap | nEz egy | érhető | 8, 16 | 0 | |
| Ismétlődés | Napi Futtatás 8:30 ÓRAKOR, 8:45, 4:30 PM és 4:45 PM | 1 | Nap | nEz egy | érhető | 8, 16 | 30, 45 | |
| Ismétlődés | Minden szombaton 5:00 ÓRAKOR fut (nincs kezdő dátum és idő) | 1 | Hét | nEz egy | Szombat | 17 | 0 | Ez az ütemterv minden szombaton 5:00 ÓRAKOR fut. |
| Ismétlődés | Minden szombaton 5:00 ÓRAKOR fut (kezdő dátummal és idővel) | 1 | Hét | *StartDate* KÖZLEMÉNNYEL17:00:00Z | Szombat | nEz egy | nEz egy | Ez az ütemterv nem *indul el a megadott* kezdési dátumnál és időpontnál (ebben az esetben a 2017. szeptember 9-én, 5:00 órakor). A jövőbeli ismétlődések minden szombaton 5:00 ÓRAKOR futnak. |
| Ismétlődés | Minden kedden, csütörtökön és a logikai alkalmazás *mentésekor a* percben is futtatható.| 1 | Hét | nEz egy | "Kedd", "csütörtök" | 17 | nEz egy | |
| Ismétlődés | A munkaidőn belül minden órában futtatható. | 1 | Hét | nEz egy | Válassza a szombat és a vasárnap kivételével az összes napot. | Válassza ki a nap azon óráját, amelyet szeretne. | Válassza ki a kívánt órányi percet. | Ha például a munkaidő 8:00 – 5:00 PM, válassza a "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" lehetőséget a nap órájában *plusz* "0", az óra percében. |
| Ismétlődés | Hetente egyszer fut a hétvégén | 1 | Hét | nEz egy | "Szombat", "vasárnap" | Válassza ki a nap azon óráját, amelyet szeretne. | Szükség szerint válassza ki az óra bármelyik percét. | Ez az ütemterv minden szombaton és vasárnap a megadott ütemterv szerint fut. |
| Ismétlődés | Futás 15 percenként, csak hétfőn | 2 | Hét | nEz egy | Hétfőtől | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ez az ütemterv 15 percenként minden más hétfőn fut. |
| Ismétlődés | Minden hónap futtatása | 1 | Month (hónap) | *StartDate* T *kezdő időpont*– Z | érhető | érhető | érhető | Ez az ütemezés nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál, és kiszámítja a jövőbeli ismétlődéseket a kezdő dátumon és időpontban. Ha nem ad meg kezdési dátumot és időpontot, az ütemterv a létrehozás dátumát és időpontját használja. |
| Ismétlődés | Minden órában futtatható havonta egy napra | 1 | Month (hónap) | {Lásd: Megjegyzés} | érhető | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Lásd: Megjegyzés} | Ha nem ad meg kezdési dátumot és időpontot, az ütemterv a létrehozás dátumát és időpontját használja. Az ismétlődési ütemterv percének vezérléséhez határozza meg az óra percét, a kezdési időpontot, vagy használja a létrehozási időt. Ha például a kezdési vagy a létrehozási idő 8:25, akkor ez az ütemterv 8:25 ÓRAKOR, 9:25 AM, 10:25 és hasonló módon fut. |
|||||||||

## <a name="next-steps"></a>Következő lépések

* [Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása az ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md)
* [Ismétlődő feladatok és munkafolyamatok létrehozása, beosztása és futtatása a csúszó ablakos triggerrel](../connectors/connectors-native-sliding-window.md)
* [Munkafolyamatok szüneteltetése késleltetési műveletekkel](../connectors/connectors-native-delay.md)
