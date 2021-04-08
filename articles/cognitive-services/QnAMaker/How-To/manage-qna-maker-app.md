---
title: QnA Maker alkalmazás kezelése – QnA Maker
description: QnA Maker lehetővé teszi, hogy több személy is működjenek együtt a Tudásbázisban. A QnA Maker lehetővé teheti a Tudásbázis aktív tanulással való minőségének javítását. Az egyik áttekintheti, elfogadhatja vagy elutasíthatja a meglévő kérdések eltávolítása vagy módosítása nélkül.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 49bfca118e53bbe3e4287b2ce25e5baffa717175
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102217329"
---
# <a name="manage-qna-maker-app"></a>QnA Maker alkalmazás kezelése

A QnA Maker lehetővé teszi a különböző szerzők és a tartalomkezelési szerkesztők együttműködését azáltal, hogy a közreműködő szerepköre alapján korlátozza a közreműködők hozzáférését.
További információ a [QnA Maker közreműködő hitelesítési fogalmakról](../Concepts/role-based-access-control.md).

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) hozzáadása

QnA Maker lehetővé teszi, hogy több személy is működjenek együtt az összes Tudásbázisban ugyanabban a QnA Maker erőforrásban. Ez a szolgáltatás [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md)érhető el.

## <a name="access-at-the-qna-maker-resource-level"></a>Hozzáférés a QnA Maker erőforrás szintjén

QnA Maker szolgáltatásban nem oszthat meg egy adott tudásbázist. Ha részletesebb hozzáférés-vezérlést szeretne, érdemes lehet a tudásbázist különböző QnA Maker erőforrásokon keresztül kiosztania, majd szerepköröket hozzáadnia az egyes erőforrásokhoz.

## <a name="add-a-role-to-a-resource"></a>Szerepkör hozzáadása egy erőforráshoz

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Felhasználói fiók hozzáadása a QnA Maker erőforráshoz

A következő lépések a közreműködő szerepkört használják, de a szerepkörök bármelyike felvehető a következő lépések használatával

1. Jelentkezzen be az [Azure](https://portal.azure.com/) Portalra, és lépjen a QnA Maker-erőforráshoz.

    ![QnA Maker erőforráslista](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Lépjen a **Access Control (iam)** lapra.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Válassza a **Hozzáadás** lehetőséget.

    ![QnA Maker IAM Hozzáadás](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Válasszon ki egy szerepkört a következő listából:

    |Szerepkör|
    |--|
    |Tulajdonos|
    |Közreműködő|
    |Cognitive Services QnA Maker olvasó|
    |Cognitive Services QnA Maker szerkesztő|
    |Cognitive Services felhasználó|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker a szerepkör hozzáadása.":::

1. Adja meg a felhasználó e-mail-címét, és kattintson a **Mentés** gombra.

    ![QnA Maker e-mail-cím hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>QnA Maker tudásbázisok megtekintése

Ha az a személy, akivel a QnA Maker szolgáltatását a [QnA Maker portálra](https://qnamaker.ai)osztotta, az adott szolgáltatás összes tudásbázisát láthatja a szerepkörük alapján.

Ha kijelölnek egy tudásbázist, az adott QnA Maker erőforráson aktuális szerepkörük látható a Tudásbázis neve mellett.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Képernyőkép a Tudásbázisban a weblap bal felső sarkában, a Tudásbázis neve mellett zárójelben lévő, szerkesztési módban található Tudásbázisban.":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](./manage-knowledge-bases.md)
