---
title: Adatbányászati jelentések létrehozása
description: átfogó és részletes információk készítése a hálózati eszközökről különböző rétegeken, például protokollokon, belső vezérlőprogram-verziókban vagy programozási parancsokon.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/20/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0738dc7f2b7012002fc6ddf5cc68dd0c8e42b04d
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522479"
---
# <a name="sensor-data-mining-queries"></a>Érzékelő adatbányászati lekérdezések

## <a name="about-sensor-data-mining-queries"></a>Tudnivalók az érzékelő adatbányászati lekérdezésekről

Az adatbányászati eszközök lehetővé teszik a különböző rétegekben található hálózati eszközök átfogó és részletes információinak létrehozását. Létrehozhat például egy lekérdezést a következő alapján:

- Időszakok

- Kapcsolódás az internethez

- Portok

- Protokollok

- Belső vezérlőprogram verziója

- Programozási parancsok

- Az eszköz tétlensége

A jelentés finomhangolása szűrők alapján végezhető el. Lekérdezheti például egy adott alhálózatot, amelyben a belső vezérlőprogram frissült.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="Aktív eszközök listája.":::

A lekérdezések kezelésére számos eszköz áll rendelkezésre. Exportálhatja és mentheti például a következőt:.

> [!NOTE]
> A rendszergazdák és a biztonsági elemzők hozzáférhetnek az adatbányászati lehetőségekhez.

### <a name="dynamic-updates"></a>Dinamikus frissítések

A létrehozott adatbányászati lekérdezéseket a rendszer minden alkalommal dinamikusan frissíti, amikor megnyitja őket. Például:

- Ha június 1-jén hozza létre a belső vezérlőprogram-verziókról szóló jelentést, és június 10-én újra megnyitja a jelentést, akkor ez a jelentés a június 10-én pontos információkkal frissül.

- Ha létrehoz egy jelentést, amely észleli az elmúlt 30 napban felderített új eszközöket június 1-jén, és június 30-ig nyitja meg a jelentést, akkor az elmúlt 30 napban megjelennek az eredmények.

### <a name="data-mining-use-cases"></a>Adatbányászati használati esetek

A különböző biztonsági csapatokra vonatkozó biztonsági igények széles körének kezeléséhez a lekérdezéseket használhatja:

- **SoC-incidens válasza**: valós időben készít jelentést, amely segít az incidensek azonnali megválaszolásában. Az adatbányászat például létrehozhat egy jelentést azon eszközök listájához, amelyekhez javításra lehet szükség.

- **Kriminalisztika**: jelentés létrehozása a vizsgálati jelentések előzményei alapján.

- **Informatikai hálózat integritása**: olyan jelentés létrehozása, amely segít a teljes hálózati biztonság javításában. Létrehozható például egy jelentés generálása, amely gyenge hitelesítési hitelesítő adatokat tartalmazó eszközöket listáz.

- **Láthatóság**: olyan jelentés létrehozása, amely az összes lekérdezési elemre kiterjed, és megtekinti a hálózat összes alapparaméterét.

## <a name="data-mining-storage"></a>Adatbányászati tár

Az adatbányászati adatokat a rendszer folyamatosan menti és tárolja, kivéve, ha egy eszközt törölnek. Az adatbányászati eredmények exportálható, és külsőleg is menthetők egy biztonságos kiszolgálóra. Emellett az érzékelő automatikus napi biztonsági mentéseket hajt végre, amelyek biztosítják a rendszerek folytonosságát és az adatok megőrzését.


## <a name="predefined-data-mining-queries"></a>Előre meghatározott adatbányászati lekérdezések

A következő előre meghatározott lekérdezések érhetők el. Ezek a lekérdezések valós időben jönnek létre.

- **CVEs**: az elmúlt 24 órában az ismert sebezhetőségekkel észlelt eszközök listája.

- **Kizárt CVEs**: a manuálisan kizárt CVEs listája. Ha pontosabb eredményeket szeretne elérni a VA-jelentésekben és a támadási vektorokban, akkor manuálisan is testreszabhatja a CVE-listát a CVEs hozzáadásával és kizárásával.

- **Internetes tevékenység**: az internethez csatlakozó eszközök.

- Nem **aktív eszközök**: az elmúlt hét napban nem közölt eszközök.

- **Aktív eszközök**: aktív hálózati eszközök az elmúlt 24 órában.

- **Távelérés**: távoli munkamenet-protokollokon keresztül kommunikáló eszközök.

- **Programozási parancsok**: ipari programozást küldő eszközök.

Ezek a jelentések automatikusan elérhetők a **jelentések** képernyőn, ahol a ro-felhasználók és más felhasználók megtekinthetik őket. A RO-felhasználók nem férhetnek hozzá az adatbányászati jelentésekhez.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Az adatbányászati képernyő.":::

## <a name="create-a-data-mining-query"></a>Adatbányászati lekérdezés létrehozása

Adatbányászati jelentés létrehozása:

1. Válassza az **adatbányászat** lehetőséget az oldal menüjében. Az előre meghatározott javasolt jelentések automatikusan megjelennek.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Válassza az adatbányászat lehetőséget az oldal ablaktáblán.":::

2. Válassza a(z) :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: lehetőséget.

3. Válassza az **új jelentés** lehetőséget, és adja meg a jelentést.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="Hozzon létre egy új jelentést a képernyő kitöltésével.":::

   A következő paraméterek érhetők el:

   - Adja meg a jelentés nevét és leírását.

   - A kategóriák esetében válassza a következők egyikét:

      - **Kategóriák (mind)** az összes jelentés eredményének megtekintéséhez a hálózatban lévő összes eszközről.

      - **Általános** a standard kategóriák kiválasztásához.

   - Válassza ki az Önt érdeklő jelentési paramétereket.

   - Válasszon rendezési sorrendet (**Order by**). Eredmények rendezése tevékenység vagy kategória alapján.

   - Kattintson a Mentés elemre a jelentés **oldalain** a jelentés eredményének **a jelentés oldaláról** elérhető jelentésként való mentéséhez. Ez lehetővé teszi a RO-felhasználók számára a létrehozott jelentés futtatását.

   - További szűrők hozzáadásához válassza a **szűrők (Hozzáadás)** lehetőséget. A helyettesítő karakteres kérések támogatottak.

   - Válasszon ki egy erőforráscsoportot (az eszköz-hozzárendelésben definiálva).

   - IP-címet kell megadni.

   - Válasszon portot.

   - Itt adhatja meg a MAC-címeket.

4. Kattintson a **Mentés** gombra. A jelentés eredményei megnyitva az **adatbányászat** lapon.

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="Jelentések eredményei az adatbányászati oldalon látható módon.":::

### <a name="manage-data-mining-reports"></a>Adatbányászati jelentések kezelése

Az alábbi táblázat az adatbányászat felügyeleti lehetőségeit ismerteti:

| Ikon képe | Description |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | Módosítsa a jelentés paramétereit. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | Exportálás PDF-ként. |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |Exportálás CSV-fájlként. |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | További információk megjelenítése, például a legutóbbi módosítás dátuma. Ezzel a funkcióval létrehozhat egy lekérdezési eredmény pillanatképét. Ehhez például a csapat vezetőinek vagy a SOC-elemzőknek való további vizsgálatra lehet szükség. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | Megjelenítés **a jelentések lapon** vagy elrejtés a **jelentések** oldalon. :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="A jelentések elrejtése vagy felfedése."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | Törölje a jelentést. |

#### <a name="create-customized-directories"></a>Testreszabott könyvtárak létrehozása 

A kategóriákhoz tartozó címtárak létrehozásával rendezheti az adatbányászati lekérdezések részletes információit. Létrehozhat például címtárakat a protokollok vagy a helyszínek számára.

Új könyvtár létrehozása:

1. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::Új könyvtár hozzáadásához válassza a lehetőséget.

2. Válassza az **új könyvtár** lehetőséget az új könyvtár űrlap megjelenítéséhez.

3. Nevezze el az új könyvtárat.

4. Húzza a szükséges jelentéseket az új könyvtárba. Bármikor áthúzhatja a jelentést a fő nézetbe.

5. Kattintson a jobb gombbal az új könyvtárra a megnyitásához, szerkesztéséhez vagy törléséhez.

#### <a name="create-snapshots-of-report-results"></a>Jelentések eredményeiről készült pillanatképek létrehozása

Előfordulhat, hogy a további vizsgálathoz bizonyos lekérdezési eredményeket kell mentenie. Előfordulhat például, hogy az eredményeket különböző biztonsági csapatokkal kell megosztania.

A pillanatképek mentése a jelentés eredményei között történik, és nem hozhatók dinamikus lekérdezések.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="Pillanatképek.":::

Pillanatkép létrehozása:

1. Nyissa meg a szükséges jelentést.

2. Válassza ki az információs ikont :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: .

3. Válassza az **új elkészítése** lehetőséget.

4. Adja meg a pillanatkép nevét, majd válassza a **Mentés** lehetőséget.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="Készítsen pillanatképet.":::

#### <a name="customize-the-cve-list"></a>A CVE-lista testreszabása

A CVE-listát manuálisan is testreszabhatja a következőképpen:

  - CVEs belefoglalása/kizárása

  - A CVE-pontszám módosítása

Manuális módosítások végrehajtása a CVE-jelentésben:

1.  Az oldalsó menüben válassza az **adatbányászat** lehetőséget.

2. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::Az **adatbányászati** ablak bal felső sarkában válassza a lehetőséget. Ezután válassza az **új jelentés** lehetőséget.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="Hozzon létre egy új jelentést.":::

3. A bal oldali panelen válasszon a következő lehetőségek közül:

   - **Ismert sebezhetőségek**: mindkét lehetőséget kiválasztja, és a jelentés két táblájában jeleníti meg az eredményeket, egyet a CVEs és a másikat a kizárt CVEs.

   - **CVEs**: válassza ezt a lehetőséget, ha be szeretné mutatni az összes CVEs listáját.

   - **Kizárt CVEs**: ezzel a beállítással megjelenítheti az összes kizárt CVEs listáját.

4. Adja meg a **név** és a **Leírás** adatait, majd válassza a **Mentés** lehetőséget. Az új jelentés megjelenik az **adatbányászati** ablakban.

5. A CVEs kizárásához nyissa meg a CVEs adatbányászati jelentését. Megjelenik az összes CVEs listája.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="C V E jelentés.":::

6. A listában szereplő elemek kiválasztásának engedélyezéséhez válassza ki :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: és válassza ki a testreszabni kívánt CVEs. Az **operatív** sáv alul látható.

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="Képernyőkép az adatbányászati műveletek sávjáról.":::

7. Válassza ki a kizárni kívánt CVEs, majd válassza a **rekordok törlése** lehetőséget. A kiválasztott CVEs nem jelennek meg a CVEs listájában, és megjelenik a kizárt CVEs listájában, amikor létrehoz egyet.

8. A kizárt CVEs hozzáadásához a CVEs listájában adja meg a kizárt CVEs vonatkozó jelentést, és törölje a listából azokat az elemeket, amelyeket vissza szeretne vonni a CVEs listájára.

9. Válassza ki azt a CVEs, amelynek a pontszámát módosítani szeretné, majd válassza a **CVE-pontszám frissítése** lehetőséget.

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="A CVE-pontszám frissítése.":::

10. Adja meg az új pontszámot, és kattintson **az OK gombra**. A frissített pontszám a kiválasztott CVEs jelenik meg.



## <a name="sensor-reports-based-on-data-mining"></a>Adatbányászaton alapuló érzékelők jelentései

A **jelentések** lehetőségből elért rendszeres jelentések előre meghatározott adatbányászati jelentések. Nem az adatbányászatban elérhető dinamikus lekérdezések, hanem az adatbányászati lekérdezés eredményeinek statikus ábrázolása.

Az adatbányászati lekérdezés eredményei csak olvasási jogosultsággal rendelkező felhasználók számára érhetők el. A rendszergazdák és a biztonsági elemzők, akik csak olvasási jogosultsággal rendelkező felhasználókat szeretnének hozzáférni az adatbányászati lekérdezések által generált adatokhoz, az adatokat jelentésként kell menteni.

A jelentések az adatbányászati lekérdezés eredményei által generált információkat tükrözik. Ide tartoznak a jelentések nézetben elérhető alapértelmezett adatbányászati jelentések is. A rendszergazda és a biztonsági elemzők egyéni adatbányászati lekérdezéseket is létrehozhatnak, és jelentésként menthetik őket. Ezek a jelentések a RO-felhasználók számára is elérhetők.

Jelentés létrehozása:

1. Kattintson a **jelentések** elemre az oldalsó menüben.

2. Válassza ki a megjeleníteni kívánt jelentést. A választás lehet **Egyéni** vagy **automatikusan generált** jelentés, például **programozási parancsok** és **távelérés**.

3. A jelentés exportálásához válassza ki a képernyő jobb felső sarkában található egyik ikont:

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: Exportálás PDF-fájlba.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: Exportálás CSV-fájlba.

> [!NOTE] 
> A RO felhasználó csak a számukra létrehozott jelentéseket láthatja.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="Válassza ki a generált jelentést.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="A távelérési jelentés létrehozva.":::

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>A helyszíni felügyeleti konzol jelentései adatbányászati jelentések alapján

A helyszíni felügyeleti konzol lehetővé teszi, hogy jelentéseket készítsen a hozzá csatlakoztatott összes érzékelőhöz. A jelentések az érzékelő adatbányászati lekérdezések alapján történnek.

A következő jelentések készíthetők:

- **Aktív eszközök (az elmúlt 24 órában)**: Megjeleníti azon eszközök listáját, amelyek 24 órán belül megjelenítik a hálózati tevékenységet.

- **Nem aktív eszközök (az elmúlt 7 napban)**: Megjeleníti azon eszközök listáját, amelyek az elmúlt hét napban nem mutatnak hálózati tevékenységet.

- **Programozási parancsok**: Megjeleníti azon eszközök listáját, amelyek az elmúlt 24 órában elküldhették a programozási parancsokat.

- **Távelérés**: Megjeleníti azon eszközök listáját, amelyek az elmúlt 24 órában hozzáfértek a távoli forrásokhoz.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="Képernyőkép a jelentések nézetről.":::

Amikor kiválasztja az érzékelőt a helyszíni felügyeleti konzolról, az adott érzékelőn konfigurált összes egyéni jelentés megjelenik a jelentések listájában. Minden érzékelőhöz létrehozhat egy alapértelmezett jelentést vagy egy egyéni jelentést, amely az adott érzékelőn konfigurálva van.

Jelentés létrehozása:

1. A bal oldali ablaktáblán válassza a **jelentések** elemet. Megjelenik a **jelentések** ablak.

2. Az **érzékelők** legördülő listából válassza ki azt az érzékelőt, amelyhez jelentést szeretne készíteni.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="Képernyőkép az érzékelők nézetről.":::

3. A jobb oldali legördülő listából válassza ki a létrehozni kívánt jelentést.

4. A jelentés eredményeinek PDF-fájl létrehozásához válassza a következőt: :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: .

## <a name="next-steps"></a>Következő lépések

[Érzékelő trendek és statisztikai jelentések](how-to-create-trends-and-statistics-reports.md).