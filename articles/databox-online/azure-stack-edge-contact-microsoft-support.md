---
title: A Azure Stack Edge Pro, Azure Data Box Gateway támogatási jegyének naplózása | Microsoft Docs
description: Megtudhatja, hogyan naplózhatja a támogatási kérelmet az Azure Stack Edge Pro vagy Data Box Gateway Orders szolgáltatással kapcsolatos problémákhoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: f76652600f42d7e82914836537935ac9a74decb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102436794"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-pro-and-azure-data-box-gateway"></a>Támogatási jegy megnyitása Azure Stack Edge Pro-hoz és Azure Data Box Gateway

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-databox-gateway-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-databox-gateway-sku.md)]

Ez a cikk Azure Stack Edge Pro-re vonatkozik, és Azure Data Box Gateway mindkettőt az Azure Stack Edge Pro/Azure Data Box Gateway szolgáltatás kezeli. Ha problémák merülnek fel a szolgáltatással kapcsolatban, létrehozhat egy szolgáltatási kérelmet a technikai támogatáshoz. Ez a cikk végigvezeti a következő lépéseken:

* Támogatási kérelem létrehozása.
* Támogatási kérelmek életciklusának kezelése a portálon belülről.

## <a name="create-a-support-request"></a>Támogatási kérelem létrehozása

Hozzon létre egy támogatási kérést a következő lépésekkel:

1. Lépjen a Azure Stack Edge Pro-vagy Data Box Gateway-sorrendbe. Navigáljon a **támogatás + hibaelhárítás** szakaszhoz, majd válassza az **új támogatási kérelem** lehetőséget.

2. Az **új támogatási kérelemben** az **alapok** lapon végezze el a következő lépéseket:

    1. A **probléma típusa** legördülő listából válassza a **technikai** lehetőséget.
    2. Válassza ki az **Előfizetést**.
    3. A **szolgáltatás** alatt keresse meg **a saját szolgáltatások** szolgáltatást. A legördülő listából válassza a **Azure stack Edge Pro és a Data Box Gateway** lehetőséget.
    4. Válassza ki az **erőforrást**. Ez megfelel a megrendelés nevének.
    5. Adja meg a tapasztalt probléma rövid **összefoglalását** . 
    6. Válassza ki a **probléma típusát**.
    7. A kiválasztott probléma típusa alapján válasszon ki egy megfelelő **problémát altípust**.
    8. Válassza a **tovább lehetőséget: megoldások >>**.

        ![Alapvető beállítások](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. A **részletek** lapon hajtsa végre a következő lépéseket:

    1. Adja meg a probléma kezdő dátumát és időpontját.
    2. Adja meg a probléma **leírását** .
    3. A **fájl feltöltése** lapon válassza a mappa ikont a feltölteni kívánt fájlok tallózásához.
    4. A **diagnosztikai adatok megosztásának** megadásához.
    5. Az előfizetés alapján a rendszer automatikusan kitölti a **támogatási csomagot** .
    6. A legördülő listából válassza ki a **súlyosságot**.
    7. Válasszon ki egy **előnyben részesített kapcsolattartási módszert**.
    8. Az előfizetési terv alapján a rendszer automatikusan kiválasztja a **válaszidőt** .
    9. Adja meg a támogatni kívánt nyelvet.
    10. A **kapcsolattartási adatok** területen adja meg a nevét, e-mail-címét, telefonszámát, nem kötelező elérhetőségét, országát/régióját. Microsoft ügyfélszolgálata ezeket az információkat a további információk, a diagnosztika és a megoldás eléréséhez használja fel. 
    11. Válassza a **Tovább: Áttekintés + >>létrehozása** elemet.

        ![Probléma](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. A **felülvizsgálat + létrehozás** lapon tekintse át a támogatási jegyhez kapcsolódó információkat. Válassza a **Létrehozás** lehetőséget. 

    ![2. probléma](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    A támogatási jegy létrehozása után a támogatási szakember a lehető leghamarabb kapcsolatba lép Önnel, hogy folytassa a kérést.

## <a name="get-hardware-support"></a>Hardveres támogatás beszerzése

Ez az információ csak Azure Stack eszközre vonatkozik. A hardveres problémák jelentésének folyamata a következő:

1. Hardveres probléma esetén nyisson meg egy támogatási jegyet a Azure Portal. A **probléma típusa** területen válassza a **Azure stack hardver** lehetőséget. Válassza ki a **probléma altípusát** **hardverhibaként**.

    ![Hardveres probléma](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    Miután létrehozta a támogatási jegyet, a támogatási szakember a lehető leghamarabb kapcsolatba lép Önnel, hogy folytassa a kérést.

2. Ha Microsoft ügyfélszolgálata megállapítja, hogy ez egy hardveres probléma, akkor a következő műveletek egyike történik:

    * A rendszer elküldje egy mező típusú helyettesítő egységet a meghibásodott hardver részhez. Jelenleg a tápegységek és a szilárdtest-meghajtók az egyetlen támogatott FRUs.
    * A következő munkanapon belül csak a FRUs lesznek lecserélve, minden más esetben teljes rendszer-cserét (FSR) kell leszállítani.

3. Ha a rendszer azt állapítja meg, hogy a cserélhető adatcsere a helyi idő szerint (hétfőtől péntekig) van-e szükség, a helyszíni technikust a következő munkanapon kell eljuttatni az adott helyre a cserélhető adatcsere végrehajtásához. A teljes rendszer cseréje általában sokkal hosszabb időt vesz igénybe, mivel a részek szállítása a gyárból történik, és a szállítás és a késedelmes késések is vonatkozhatnak rájuk.

## <a name="manage-a-support-request"></a>Támogatási kérelem kezelése

A támogatási jegy létrehozása után a jegyet a teljes életciklusán keresztül kezelheti a portálon.

### <a name="to-manage-your-support-requests"></a>A támogatási kérések kezelése

1. A Súgó és támogatás lapon keresse meg a **tallózás > Súgó + támogatás** menüpontot.

    ![Támogatási kérelmek kezelése](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. A **Súgó és támogatás** szolgáltatásban megjelenik a **legújabb támogatási kérelmek** táblázatos listája.

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Válasszon ki egy támogatási kérést, és kattintson rá. Megtekintheti a kérelem állapotát és részleteit. Ha ezt a kérést szeretné követni, kattintson az **+ új üzenet** elemre.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan lehet [elhárítani a Azure stack Edge Pro-val kapcsolatos problémákat](azure-stack-edge-troubleshoot.md).
Ismerje meg, hogyan lehet [elhárítani a Data Box Gateway kapcsolatos problémákat](../databox-gateway/data-box-gateway-troubleshoot.md).