---
title: Adatmezők leképezése az Azure Sentinel-entitásokra | Microsoft Docs
description: Táblázatok adatmezőinek leképezése az Azure Sentinel-entitásokra az elemzési szabályokban, a jobb incidensi információk érdekében
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: cb91d269f6b166510db54637d17d776e71137408
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456241"
---
# <a name="map-data-fields-to-entities-in-azure-sentinel"></a>Adatmezők leképezése az Azure Sentinelben lévő entitásokra 

> [!IMPORTANT]
>
> - Az entitás-hozzárendelési szolgáltatás új verziója **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

> [!IMPORTANT]
>
> - A jelen dokumentum végén található [Megjegyzések](#notes-on-the-new-version) a visszamenőleges kompatibilitással kapcsolatos fontos információkat és az entitás-hozzárendelés új és régi verziói közötti különbségeket ismertetik.

## <a name="introduction"></a>Bevezetés

Az entitások leképezése az [ütemezett lekérdezés-elemzési szabályok](tutorial-detect-threats-custom.md)konfigurációjának szerves részét képezi. Ez a szabály a "kimenet (riasztások és incidensek") szabályokkal gazdagítja azokat az alapvető információkat, amelyek az alábbi vizsgálati folyamatok és javító műveletek építőelemeként szolgálnak.

Az alább részletezett eljárás az elemzési szabály létrehozása varázsló részét képezi. Itt egymástól függetlenül kezeljük az entitás-hozzárendelések meglévő elemzési szabályban való hozzáadásának vagy módosításának forgatókönyvét.

## <a name="how-to-map-entities"></a>Entitások leképezése

1. Az Azure Sentinel navigációs menüjében válassza az **elemzés** lehetőséget.

1. Válasszon ki egy ütemezett lekérdezési szabályt, és kattintson a **Szerkesztés** gombra. Vagy hozzon létre egy új szabályt a képernyő felső részén található **&#10132; ütemezett lekérdezési szabály létrehozása** elemre kattintva.

1. Kattintson a **szabály logikájának beállítása** fülre.

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="Mezők leképezése entitásokra":::

1. A **riasztás javítása** szakasz **entitás-hozzárendelés** területén válassza ki az entitás típusát az entitás **típusa** legördülő listából.

1. Válassza ki az entitás **azonosítóját** . Az azonosítók olyan entitások attribútumai, amelyek megfelelő módon azonosíthatók. Válasszon egyet az **azonosító** legördülő listából, majd válasszon egy olyan adatmezőt az **érték** legördülő listából, amely megfelel az azonosítónak. Bizonyos kivételek esetén az **értékek** listáját a szabály lekérdezésének tárgyát képező tábla adatmezői töltik fel.

    Egy adott entitáshoz **legfeljebb három azonosítót** adhat meg. Néhány azonosító megadása kötelező, mások opcionálisak. Legalább egy szükséges azonosítót ki kell választania. Ha nem, a figyelmeztető üzenet arra utasítja, hogy mely azonosítók szükségesek. A legjobb eredmények érdekében – a maximális egyedi azonosításhoz – ha lehetséges, **erős** azonosítókat kell használnia, és több erős azonosító használata is lehetővé teszi az adatforrások közötti nagyobb korrelációt. Tekintse meg az elérhető [entitások és azonosítók](entities-reference.md)teljes listáját.

1. További entitások leképezéséhez kattintson az **új entitás hozzáadása** lehetőségre. Egyetlen elemzési szabályban **legfeljebb öt entitást** lehet leképezni. Egynél több típust is leképezheti. Leképezheti például a két **IP-** entitást, egyet a *forrás IP-cím* mezőjéből, egyet pedig egy *cél IP-cím* mezőből. Így mindkettő nyomon követhető.

    Ha meggondolja magát, vagy ha hibát vétett, eltávolíthatja az entitások leképezését az entitás legördülő lista melletti Kuka ikonra kattintva.

1. Ha befejezte az entitások leképezését, kattintson a **felülvizsgálat és létrehozás** fülre. Ha a szabály ellenőrzése sikeres, kattintson a **Mentés** gombra.

## <a name="notes-on-the-new-version"></a>Megjegyzések az új verzióhoz

- Ha korábban már definiált entitás-hozzárendeléseket ehhez az elemzési szabályhoz a régi verzió használatával, ezek a leképezések megjelennek a lekérdezési kódban. Az új verzióban definiált entitás-hozzárendelések **nem jelennek meg a lekérdezési kódban**. Az elemzési szabályok egyszerre csak az entitás-hozzárendelések egy verzióját támogatják, az új verzió pedig elsőbbséget élvez. Ezért az itt definiált egyetlen hozzárendelés a lekérdezés kódjában definiált **bármely és minden** leképezést a lekérdezés futtatásakor **figyelmen kívül** hagy. 

- Ha továbbra is az entitás-hozzárendelés **régi verzióját** kell használnia (feltéve, hogy az új verzió még előzetes verzióban érhető el), akkor továbbra is hozzáférhet az URL-cím egyik funkciójának használatával. Vigye a kurzort a `https://portal.azure.com/` és `#blade` a közé, majd szúrja be a szöveget `?feature.EntityMapping=false` .

  - A régi verzió korlátai továbbra is érvényben maradnak. Csak a felhasználó, a gazdagép, az IP-cím, az URL-cím és a fájlkivonat-entitások képezhetők le, és csak az egyikük.

  - A régi verzióra való visszatérés **előtt** **el kell távolítania** az új verzió használatával létrehozott entitás-hozzárendeléseket, ellenkező esetben a régi verziót használó entitás-hozzárendelések **nem fognak működni**.

- Ha az entitás-hozzárendelés új verziója általánosan elérhető, a továbbiakban nem fogja tudni használni a régi verziót. Erősen ajánlott áttelepíteni a régi entitások hozzárendeléseit az új verzióra.


## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan képezheti le az adatmezőket entitásokra az Azure Sentinel Analytics-szabályokban. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Az [ütemezett lekérdezési elemzési szabályok](tutorial-detect-threats-custom.md)teljes képének beolvasása.
- További információ az [Azure Sentinel-beli entitásokról](entities-in-azure-sentinel.md).
