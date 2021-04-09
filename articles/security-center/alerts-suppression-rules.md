---
title: A riasztások mellőzése szabályok használatával elkerülheti a hamis pozitív vagy más nemkívánatos biztonsági riasztásokat a Azure Security Centerban.
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure Security Center letiltási szabályai a nemkívánatos biztonsági riasztások elrejtéséhez
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 02/17/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 646495597565bbb033ac3adaa15f3754f33e8fd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634165"
---
# <a name="suppress-alerts-from-azure-defender"></a>Riasztások letiltása az Azure Defenderből

Ez az oldal azt ismerteti, hogyan használhatja a riasztások letiltási szabályait a hamis pozitív vagy más nemkívánatos biztonsági riasztások mellőzéséhez az Azure Defendertől.

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|Ingyenes<br>(A legtöbb biztonsági riasztás csak az Azure Defenderrel érhető el)|
|Szükséges szerepkörök és engedélyek:|A **biztonsági rendszergazda** és a **tulajdonos** létrehozhat/törölhet szabályokat.<br>A **biztonsági olvasó** és az **olvasó** megtekintheti a szabályokat.|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||


## <a name="what-are-suppression-rules"></a>Mik azok a letiltási szabályok?

A különböző Azure Defender-csomagok a környezet bármely területén észlelnek fenyegetéseket, és biztonsági riasztásokat hoznak.

Ha egyetlen riasztás nem érdekes vagy releváns, akkor manuálisan is elvégezheti. Azt is megteheti, hogy a letiltási szabályok funkció használatával automatikusan elveti a hasonló riasztásokat a jövőben. Általában letiltási szabályt kell használnia a következőkhöz:

- Hamis pozitívként azonosított riasztások letiltása

- A túl gyakran kiváltott riasztások mellőzése

A letiltási szabályok határozzák meg azokat a feltételeket, amelyek esetében a riasztásokat automatikusan el kell utasítani.

> [!CAUTION]
> A biztonsági riasztások letiltása csökkenti az Azure Defender veszélyforrások elleni védelmi hatékonyságát. Körültekintően ellenőrizze az esetleges elnyomási szabályok lehetséges hatását, és figyelje az idő múlásával.

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="Riasztási elnyomási szabály létrehozása":::

## <a name="create-a-suppression-rule"></a>Mellőzési szabály létrehozása

A nemkívánatos biztonsági riasztások mellőzésére szolgáló szabályok létrehozásának lehetséges megoldásai:

- A riasztások felügyeleticsoport-szinten való mellőzéséhez használja az Azure Policyt
- A riasztások előfizetésszinten való mellőzéséhez használhatja az Azure Portalt vagy a REST API-t, az alább ismertetett módon

A tiltási szabályok csak azokat a riasztásokat hagyhatják el, amelyek már aktiválva lettek a kijelölt előfizetéseken.

Szabály létrehozása közvetlenül a Azure Portalban:

1. A Security Center biztonsági riasztásokat tartalmazó oldalán:

    - Válassza ki azt a riasztást, amelyet többé nem szeretne látni, majd a részletek ablaktáblán válassza a **művelet végrehajtása** lehetőséget.

    - Vagy válassza a **mellőzési szabályok** hivatkozást az oldal tetején, és a letiltási szabályok lapon válassza az **új tiltási szabály létrehozása** elemet:

        ![Új letiltási szabály létrehozása * * gomb](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. Az új letiltási szabály panelen adja meg az új szabály részleteit.
    - A szabály **minden erőforráson** elvetheti a riasztást, így a későbbiekben nem kap ilyen riasztást.     
    - A szabály lemondhatja a riasztást **bizonyos feltételek** esetén – ha egy adott IP-címhez, folyamat nevéhez, felhasználói fiókhoz, Azure-erőforráshoz vagy helyhez kapcsolódik.

    > [!TIP]
    > Ha egy adott riasztásban megnyitotta az új szabály lapot, a rendszer automatikusan konfigurálja a riasztást és az előfizetést az új szabályban. Ha az **új letiltási szabály létrehozása** hivatkozást használta, a kiválasztott előfizetések megfelelnek a portál aktuális szűrőjének.

    [![Mellőzési szabály létrehozási panelje](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. Adja meg a szabály részleteit:
    - **Név** – a szabály neve. A szabály nevének betűvel vagy számmal kell kezdődnie, hossza 2 és 50 karakter között lehet, és kötőjeleken (-) vagy aláhúzásjeleken (_) kívül nem tartalmazhat más szimbólumot. 
    - **Állapot** -engedélyezve vagy letiltva.
    - **OK** – válasszon egyet a beépített okok közül, vagy a "más" lehetőséget, ha azok nem felelnek meg az igényeinek.
    - **Lejárati dátum** – a szabály befejezési dátuma és időpontja. A szabályok legfeljebb hat hónapig futhatnak.
1. Szükség esetén a **szimulálás** gombbal tesztelheti a szabályt, hogy megtekintse, hány riasztást kellett volna elutasítani, ha ez a szabály aktív volt.
1. Mentse a szabályt. 


## <a name="edit-a-suppression-rule"></a>Letiltási szabály szerkesztése

A létrehozott szabály szerkesztéséhez használja a letiltási szabályok lapot.

1. A Security Center biztonsági riasztásai lapon válassza a **letiltási szabályok** hivatkozást az oldal tetején.
1. Megnyílik a letiltási szabályok lap, amely a kijelölt előfizetésekre vonatkozó összes szabályt megjeleníti.

    [![Mellőzési szabályok listája](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Egyetlen szabály szerkesztéséhez nyissa meg a szabályhoz tartozó három pontot (...), és válassza a **Szerkesztés** lehetőséget.
1. Végezze el a szükséges módosításokat, majd válassza az **alkalmaz** lehetőséget. 

## <a name="delete-a-suppression-rule"></a>Mellőzési szabály törlése

Egy vagy több létrehozott szabály törléséhez használja a letiltási szabályok lapot.

1. A Security Center biztonsági riasztásai lapon válassza a **letiltási szabályok** hivatkozást az oldal tetején.
1. Megnyílik a letiltási szabályok lap, amely a kijelölt előfizetésekre vonatkozó összes szabályt megjeleníti.
1. Egyetlen szabály törléséhez nyissa meg a szabály három pontot (...), majd válassza a **Törlés** lehetőséget.
1. Több szabály törléséhez jelölje be a törölni kívánt szabályok jelölőnégyzetét, majd válassza a **Törlés** lehetőséget.
    ![Egy vagy több elnyomási szabály törlése](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="create-and-manage-suppression-rules-with-the-api"></a>Letiltási szabályok létrehozása és kezelése az API-val

A riasztások letiltási szabályait a Security Center REST API segítségével hozhatja létre, tekintheti meg vagy törölheti. 

A REST API a következő letiltási szabályokra vonatkozó HTTP-metódusok:

- **Put**: letiltási szabály létrehozása vagy frissítése egy adott előfizetésben.

- **Beolvasás**:

    - Egy adott előfizetéshez konfigurált összes szabály listázása. Ez a metódus a vonatkozó szabályok tömbjét adja vissza.

    - Egy adott előfizetéshez tartozó adott szabály részleteinek beolvasása. Ez a metódus egy letiltási szabályt ad vissza.

    - Egy letiltási szabály hatásának szimulálása még a tervezési fázisban. Ez a hívás azonosítja, hogy a meglévő riasztások közül melyik lett elvetve, ha a szabály aktív volt.

- **Törlés**: töröl egy meglévő szabályt (de nem módosítja az általa már elvetett riasztások állapotát).

A részletekért és a használati példákért tekintse meg az [API dokumentációját](/rest/api/securitycenter/). 


## <a name="next-steps"></a>Következő lépések

Ez a cikk az Azure Security Center letiltási szabályait ismerteti, amelyek automatikusan elmulasztják a nemkívánatos riasztásokat.

Az Azure Defender biztonsági riasztásokkal kapcsolatos további információkért tekintse meg a következő lapokat:

- [Biztonsági riasztások és a szándék-megölési lánc](alerts-reference.md) – útmutató az Azure Defender által esetlegesen kapott biztonsági riasztásokhoz.