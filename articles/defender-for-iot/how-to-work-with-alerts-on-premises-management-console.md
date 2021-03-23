---
title: Riasztások kezelése a helyszíni felügyeleti konzolon
description: A helyszíni felügyeleti konzol segítségével vállalati nézetet kaphat a hálózat legutóbbi fenyegetésekről, és jobban megismerheti, hogyan kezelik az érzékelőket a felhasználók.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: a99c489c54b2671a463c0c3dad1909c74b4fa203
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781466"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Riasztások kezelése a helyszíni felügyeleti konzolon 

A felügyeleti konzol **riasztások** ablakában a következőket teheti:

- Riasztási szűrők használata

- Riasztási számlálók használata

- Riasztási adatok megtekintése

- Riasztási események kezelése

- Riasztás kizárási szabályainak létrehozása

- Riasztási kizárási szabályok kiváltása külső rendszerekből

- Incidens-munkafolyamatok felgyorsítása riasztási csoportokkal

## <a name="view-alerts-in-the-on-premises-management-console"></a>Riasztások megtekintése a helyszíni felügyeleti konzolon

A helyszíni felügyeleti konzol összesíti az összes csatlakoztatott érzékelő riasztásait. Ez vállalati áttekintést nyújt a hálózat legutóbbi fenyegetésekről, és segít jobban megérteni, hogyan kezelik az érzékelőket a felhasználók.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Képernyőkép a riasztások ablakról.":::

### <a name="work-with-alert-filters"></a>Riasztási szűrők használata

A **riasztások** ablakban a helyszíni felügyeleti konzolhoz csatlakoztatott érzékelők által generált riasztások jelennek meg. Megtekintheti a csatlakoztatott érzékelőkkel kapcsolatos összes riasztást, vagy megjelenítheti a megadott riasztásokból érkező riasztásokat:

- Hely

- Zóna

- Eszköz

- Érzékelő

Válassza a **Szűrők törlése** lehetőséget az összes riasztás megtekintéséhez.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Törölje a szűrőket a szűrők törlése gomb kiválasztásával.":::

### <a name="work-with-alert-counters"></a>Riasztási számlálók használata

A riasztási számlálók a riasztások súlyosság és a nyugtázott állapot szerinti részletezését biztosítják.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="A riasztási számláló azt mutatja, hogy hány riasztás van.":::

A riasztási számlálóban a következő súlyossági szintek jelennek meg:

- **Kritikus**: olyan kártékony támadást jelez, amelyet azonnal kell kezelni.

- **Főverzió**: olyan biztonsági fenyegetést jelez, amely fontos a megoldáshoz.

- **Minor**: az alapkonfiguráció viselkedését jelző, biztonsági fenyegetést okozó eltérést jelez.

- **Figyelmeztetés**: bizonyos eltéréseket jelez az alapkonfigurációtól a biztonsági fenyegetések nélkül.

A súlyossági szintek előre definiálva vannak.

A számlálót beállíthatja úgy, hogy számokat biztosítson a nyugtázott és a nem nyugtázott riasztások alapján. A nem visszaigazolt riasztások aktiválva lettek a Defender for IoT érzékelők esetében, de az érzékelőn belüli operátorok még nem vizsgálták át őket.

Ha a **visszaigazolt riasztások megjelenítése** lehetőség be van jelölve, a **riasztások** ablakban megjelenik az összes visszaigazolt riasztás.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Megtekintheti a visszaigazolt riasztásokat.":::

### <a name="view-alert-information"></a>Riasztási adatok megtekintése

A riasztás a következő információkat jeleníti meg:

- A riasztási esemény összegzése.

- Riasztás súlyossága

- Az észlelt érzékelőn lévő riasztásra mutató hivatkozás.

- Riasztási UUID. Az UUID az érzékelőn észlelt riasztási eseményhez társított riasztási AZONOSÍTÓból áll, és egy kötőjeltel elválasztva, majd egy egyedi rendszerazonosító számmal.

**Helyszíni felügyeleti konzol riasztási UUID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="Az eszköz csatlakoztatva van, de nincs engedélyezve.":::

**Érzékelő riasztásának azonosítója**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="Érzékelő riasztásának azonosítója.":::

Az UUID-eszközök használata biztosítja, hogy a helyszíni felügyeleti konzolon megjelenített minden riasztás egyedi számmal kereshető és azonosítható legyen. Erre azért van szükség, mert több érzékelőből generált riasztások ugyanazt a riasztási azonosítót is létrehozzák.

> [!NOTE]
> Alapértelmezés szerint az UUID-azonosítók a következő partneri rendszerekben jelennek meg a továbbítási szabályok meghatározásakor: ArcSight, syslog Servers, QRadar, Sentinel és NetWitness. Nincs szükség beállításra.

A riasztási adatok megtekintése:

- A riasztás listából válassza ki a riasztást.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="A riasztási információk képernyőképe.":::

A riasztás megtekintése az érzékelőben:

- Válassza az **érzékelő megnyitása** a riasztásból lehetőséget.

Az eszközök megtekintése a zóna térképén:

- Ha szeretné megtekinteni az eszközt a riasztással ellátott eszközre és az ahhoz csatlakozó összes eszközre összpontosítva, válassza az **eszközök megjelenítése** lehetőséget.

## <a name="manage-alert-events"></a>Riasztási események kezelése

Több lehetőség is rendelkezésre áll a riasztási események kezeléséhez a helyszíni felügyeleti konzolról.

- Riasztási események megismerése vagy nyugtázása. Válassza a **tanulás & tudomásul** vétele lehetőséget az összes engedélyezett riasztási esemény megismeréséhez, valamint az összes olyan riasztási esemény elfogadásához, amelyik jelenleg nem ismerhető fel.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Az összes megismeréséhez válassza a & tudomásul vétele lehetőséget.":::

- Riasztási események elnémítása és feloldása.

Ha többet szeretne megtudni a riasztási események megismeréséről, visszaigazolásáról és elnémításáról, tekintse meg az érzékelő [riasztási események kezelése](how-to-manage-the-alert-event.md) című cikket.

## <a name="export-alert-information"></a>Riasztási adatok exportálása

Riasztási adatok exportálása CSV-fájlba. Exportálhatja az összes észlelt riasztás adatait, vagy exportálhatja az adatokat a szűrt nézet alapján. A rendszer a következő adatokat exportálja:

- Forrás címe
- Cél címe
- Riasztás címe
- A riasztás súlyossága
- Riasztási üzenet
- További információ
- Elismert állapot
- PCAP rendelkezésre állása

Exportálás:

1. Válassza a riasztások lehetőséget az oldal menüjében.
1. Válassza az Export (Exportálás) lehetőséget.
1. Válassza a kiterjesztett riasztások exportálása lehetőséget, hogy a riasztási információkat külön sorokban exportálja a több eszközt magában foglaló riasztások esetén. Ha a kiterjesztett riasztások exportálása lehetőséget választja, a. csv-fájl a riasztás duplikált sorát fogja létrehozni az egyes sorok egyedi elemeivel. Ezzel a beállítással egyszerűbbé válik az exportált riasztási események vizsgálata.  

## <a name="create-alert-exclusion-rules"></a>Riasztás kizárási szabályainak létrehozása

Utasítsa a Defendert a IoT, hogy figyelmen kívül hagyja a riasztási eseményindítókat a következő alapján:

- Időzónák és időszakok

- Eszköz címe (IP, MAC, alhálózat)

- Riasztások nevei

- Egy adott érzékelő

Riasztás kizárási szabályainak létrehozása, ha azt szeretné, hogy a Defender a IoT figyelmen kívül hagyja a riasztást kiváltó tevékenységeket.

Ha például biztos abban, hogy egy adott érzékelő által figyelt összes OT-eszköz két napig tart a karbantartási eljárások között, megadhat egy kizárási szabályt, amely arra utasítja a Defendert, hogy IoT az érzékelő által az előre meghatározott időszakban észlelt riasztásokat.

### <a name="alert-exclusion-logic"></a>Riasztás kizárási logikája

A riasztási szabály logikája `AND` alapul. Ez azt jelenti, hogy a rendszer csak akkor aktiválja a riasztást, ha az összes szabály feltétele teljesül.

Ha nincs megadva a szabály feltétele, akkor a feltétel minden beállítást tartalmaz. Ha például nem tartalmazza az érzékelő nevét a szabályban, a rendszer az összes érzékelőre alkalmazza.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Képernyőkép a kizárási szabály létrehozása nézetről.":::

A szabály-összefoglalók a **kizárási szabály** ablakban jelennek meg.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Képernyőfelvétel a kizárási szabály összefoglaló nézetéről.":::

A kizárási szabályok használata mellett a riasztások elnémításával is letilthatja a riasztásokat.

### <a name="create-exclusion-rules"></a>Kizárási szabályok létrehozása

Kizárási szabályok létrehozása:

1. A helyszíni felügyeleti konzol bal oldali paneljén válassza a **riasztás kizárása** lehetőséget. Adjon meg egy új kizárási szabályt a  :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: megnyíló ablak jobb felső sarkában található Hozzáadás ikonra kattintva. Megnyílik a **kizárási szabály létrehozása** párbeszédpanel.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Hozzon létre egy riasztás kizárását az itt található információk kitöltésével.":::

2. Adja meg a szabály nevét a **név** mezőben. A név nem tartalmazhat idézőjeleket ( `"` ).

3. Az **időzóna/** idő szakaszban adja meg az adott időzónán belüli időtartamot. Akkor használja ezt a funkciót, ha egy kizárási szabályt hoz létre egy adott időszakra vonatkozóan egy adott időzónában, de a többi időzónában egyidejűleg kell megvalósítani. Előfordulhat például, hogy egy kizárási szabályt kell alkalmaznia a 8:00 és a 10:00 között három különböző időzónában. Ebben az esetben hozzon létre három különálló kizárási szabályt, amely ugyanazt az időtartamot és a vonatkozó időzónát használja.

4. Válassza a **Hozzáadás** lehetőséget. A kizárási időszakban nem jön létre riasztás a csatlakoztatott érzékelőkön.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Képernyőkép az időszakos nézetről.":::

5. A **by Device címe** szakaszban adja meg a következőket:

  - A kizárni kívánt eszköz IP-címe, MAC-címe vagy alhálózati címe.

  - A kizárt eszközök, a forrás és a célhely forgalmi iránya.

6. Válassza a **Hozzáadás** lehetőséget.

7. A riasztás **címének** megadása szakaszban kezdje el beírni a riasztás címét. A legördülő listában válassza ki a kizárni kívánt riasztási címet vagy címeket.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Képernyőkép: a riasztás címének nézete.":::

8. Válassza a **Hozzáadás** lehetőséget.

9. Az **érzékelő neve nevű** szakaszban kezdje el beírni az érzékelő nevét. A legördülő listából válassza ki a kizárni kívánt érzékelőt vagy érzékelőket.

10. Válassza a **Hozzáadás** lehetőséget.

11. Válassza a **Mentés** lehetőséget. Az új szabály megjelenik a szabályok listájában.

A riasztásokat elnémításával vagy a riasztások kizárási szabályainak létrehozásával is elvégezheti. Ez a szakasz mindkét szolgáltatás lehetséges használati eseteit ismerteti.

- **Kizárási szabály**. Kizárási szabály írása:

  - Tudd, hogy az eseményt ki szeretné zárni az adatbázisból. Tegyük fel például, hogy egy adott érzékelő által észlelt forgatókönyv nem releváns riasztásokat indít el. Előfordulhat például, hogy karbantartási munkát végez egy adott hely szervezeti PLC, és szeretné letiltani a hely PLC kapcsolatos riasztásokat.

  - Azt szeretné, hogy a Defender IoT egy adott időtartományhoz tartozó eseményeket (a rendszer-karbantartási feladatok esetében).

  - Egy adott alhálózaton lévő eseményeket szeretné figyelmen kívül hagyni.

  - Több érzékelőből származó riasztási eseményt szeretne vezérelni egyetlen szabállyal.

  - Nem kívánja nyomon követni a riasztás kizárását eseményként az eseménynaplóban.

- **Némítás**. Riasztás elnémítása, ha:

  - Az elnémítva szükséges elemek nem tervezettek. Nem tudhatja meg, hogy milyen események lesznek lényegtelenek.

  - Szeretné letiltani a riasztást a **riasztások** ablakból, de továbbra is nyomon szeretné követni az eseménynaplóban.

  - Egy adott csatornán lévő eseményeket szeretné figyelmen kívül hagyni.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Riasztási kizárási szabályok kiváltása külső rendszerekből

Riasztási kizárási szabályok kiváltása külső rendszerekből. Például kezelheti a kizárási szabályokat a nagyvállalati jegyű rendszerekről vagy a hálózati karbantartási folyamatokat kezelő rendszerekről.

Az érzékelők, a motorok, a kezdési idő és a befejezési idő megadása a szabály alkalmazásához. További információ: [Defender for IOT API-érzékelő és felügyeleti konzol API](references-work-with-defender-for-iot-apis.md)-k.

Az API használatával létrehozott szabályok a **kizárási szabály** ablakban a ro néven jelennek meg.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Képernyőkép a kizárási szabály szerkesztése nézetről.":::

## <a name="see-also"></a>Lásd még

[Riasztások használata az érzékelőn](how-to-work-with-alerts-on-your-sensor.md)
