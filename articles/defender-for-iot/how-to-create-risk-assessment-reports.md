---
title: Kockázatértékelési jelentések létrehozása
description: Betekintést nyerhet az egyes érzékelők által észlelt hálózati kockázatokkal, illetve az összes érzékelő által észlelt kockázatok összesített nézetével.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d13520a6b2f5e473942d7d0221a4e6f0708b6411
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522257"
---
# <a name="risk-assessment-reporting"></a>Kockázatértékelési jelentéskészítés

## <a name="about-risk-assessment-reports"></a>A kockázatértékelési jelentések

A kockázatértékelési jelentések a következőket biztosítják:

- A szervezeti érzékelők által észlelt eszközök összesített biztonsági pontszáma.

- Az egyes érzékelők által észlelt összes hálózati eszköz biztonsági pontszáma.

- A sebezhető eszközök számának részletezése, valamint a fejlesztést és a biztonságos eszközöket igénylő eszközök.

-  Betekintés a biztonsági és működési problémákba:

    - Konfigurációs problémák

    - Biztonsági szint alapján rangsorolt eszközök biztonsági rése

    - Hálózati biztonsági problémák

    - Hálózati működési problémák

    - Kapcsolatok az ICS-hálózatokkal

    - Internetes kapcsolatok

    - Ipari kártevő-mutatók

    - Protokollokkal kapcsolatos problémák

    - Támadási vektorok

### <a name="risk-mitigation"></a>Kockázatcsökkentő

A jelentések ajánlásokat nyújtanak a biztonsági pontszám javításához. Telepítse például a legújabb biztonsági frissítéseket, frissítse a firmware-t a legújabb verzióra, vagy kövesse a riasztásokat.

## <a name="about-security-scores"></a>A biztonsági pontszámok

Minden jelentésben létrejön a hálózati biztonsági pontszám teljes értéke. A pontszám a 100 százalékos biztonság százalékos arányát jelöli. A 30%-os pontszám például azt jelzi, hogy a hálózat 30%-os biztonságban van.

A kockázatértékelési pontszámok a csomagok ellenőrzése, a viselkedési modellezési motorok és a SCADA-specifikus állapotú gépek kialakításán alapulnak.

A **biztonságos eszközök** olyan eszközök, amelyek biztonsági pontszáma 90% fölött van.

**Fejlesztést igénylő eszközök**: 70%-os és 89%-os biztonsági pontszámmal rendelkező eszközök.

A **sebezhető eszközök** olyan eszközök, amelyek biztonsági pontszáma 70% alá esik.

## <a name="create-risk-assessment-reports"></a>Kockázatértékelési jelentések létrehozása

Hozzon létre egy PDF-kockázatfelmérési jelentést. A jelentés neve automatikusan risk-assessment-report-1.pdfként jön létre. A rendszer minden létrehozott új jelentés esetében frissíti a számot.  Megjelenik a létrehozás időpontja és napja.

### <a name="create-a-sensor-risk-assessment-report"></a>Érzékelő kockázatbecslési jelentés létrehozása

Hozzon létre egy kockázatértékelési jelentést az érzékelő által bejelentkezett észlelések alapján.

Jelentés létrehozása:

1. Jelentkezzen be az érzékelő konzolra.
1. Válassza a **kockázatértékelés** lehetőséget az oldalsó menüben.
1. Válassza a **jelentés előállítása** lehetőséget. A jelentés az archivált jelentések szakaszban jelenik meg.
1. A letöltéshez válassza ki a jelentést az archivált jelentések szakaszban.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="A kockázatértékelés nézete.":::

Vállalati embléma importálása:

- Válassza az **importálási embléma** lehetőséget.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>Helyszíni felügyeleti konzol kockázatbecslési jelentésének létrehozása

Kockázatértékelési jelentés létrehozása a helyszíni felügyeleti konzol által kezelt érzékelők által végrehajtott észlelések alapján. 

Jelentés létrehozása:

1. Válassza a **kockázatértékelés** lehetőséget az oldalsó menüben.

2. Válasszon ki egy érzékelőt az **érzékelő kiválasztása** legördülő listából.

3. Válassza a **jelentés előállítása** lehetőséget.

4. Válassza a **Letöltés** lehetőséget az **archivált jelentések** szakaszban.

Vállalati embléma importálása:

- Válassza az **importálási embléma** lehetőséget.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Importálja az emblémát a kockázatfelmérési nézetben.":::

## <a name="next-steps"></a>Következő lépések

[Támadási vektorok jelentése](how-to-create-attack-vector-reports.md)

