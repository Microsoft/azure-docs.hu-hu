---
title: Támadási vektoros jelentések létrehozása
description: A támadási vektor jelentései a kihasználható eszközök sebezhetőségi láncának grafikus megjelenítését teszik lehetővé.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 6c178f379561ca4a572a7ae8f556ae6753d6e2be
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784373"
---
# <a name="attack-vector-reporting"></a>Támadási vektorok jelentése

## <a name="about-attack-vector-reports"></a>A támadási vektor jelentései

A támadási vektor jelentései a kihasználható eszközök sebezhetőségi láncának grafikus megjelenítését teszik lehetővé. Ezek a biztonsági rések a támadók számára hozzáférést biztosíthatnak a legfontosabb hálózati eszközökhöz. A támadási vektor szimulátor valós időben kiszámítja a támadási vektorokat, és egy adott cél esetében elemzi az összes támadási vektort.

A támadási vektor használata lehetővé teszi a kockázatcsökkentő tevékenységek hatásának kiértékelését a támadási sorozatban. Ezután meghatározhatja például, hogy egy rendszerfrissítés megszakítja-e a támadó elérési útját a támadási lánc megszakadásával, vagy ha a másik támadási útvonal továbbra is fennáll. Ez az információ segít rangsorolni a Szervizelési és a kockázatcsökkentő tevékenységeket.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Megtekintheti a riasztásokat a vezérlési központban.":::

> [!NOTE]
> A rendszergazdák és a biztonsági elemzők elvégezhetik az ebben a részben ismertetett eljárásokat.

## <a name="create-an-attack-vector-report"></a>Támadási vektoros jelentés létrehozása

Támadási vektor szimulációjának létrehozása:

1. Szimuláció hozzáadásához válassza az oldalsó menü :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="plusz bejelentkezés":::elemét.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="A támadási vektor szimulálása.":::

2. Adja meg a szimulációs tulajdonságokat:

   - **Name**: szimulációs név.

   - **Maximális vektorok**: egyetlen szimulált vektorok maximális száma.

   - **Megjelenítés az eszköz** térképén: a támadási vektort szűrőként jeleníti meg az eszköz térképén.

   - **Minden forrásoldali eszköz**: a támadási vektor az összes eszközt támadási forrásként fogja figyelembe venni.

   - **Támadás forrása**: a támadási vektor csak a megadott eszközöket fogja figyelembe venni támadási forrásként.

   - **Minden céleszköz**: a támadási vektor az összes eszközt támadási célként fogja figyelembe venni.

   - **Támadási cél**: a támadási vektor csak a megadott eszközöket fogja figyelembe venni támadási célként.

   - **Eszközök kizárása**: a megadott eszközök ki lesznek zárva a támadási vektor szimulációból.

   - **Alhálózatok kizárása**: a megadott alhálózatok ki lesznek zárva a támadási vektor szimulációból.

3. Válassza a **szimulációs Hozzáadás** lehetőséget. A szimuláció hozzá lesz adva a szimulációk listájához.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Adjon hozzá egy új szimulációt.":::

4. Válassza ki, :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: hogy szeretné-e szerkeszteni a szimulációt.

   Válassza ki, :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: hogy törölni kívánja-e a szimulációt.

   Válassza ki, :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: hogy kedvencként szeretné-e megjelölni a szimulációt.

5. Megjelenik a támadási vektorok listája, amely magában foglalja a vektoros pontszámot (100), a támadási forrás eszközét és a támadási cél eszközét. Válasszon ki egy adott támadást a támadási vektorok grafikus ábrázolásához.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Támadási vektorok.":::

## <a name="see-also"></a>Lásd még

[Támadási vektorok jelentése](how-to-create-attack-vector-reports.md)


