---
title: Azure Active Directory ellenőrizhető hitelesítő adatok (előzetes verzió)
description: Megtudhatja, hogyan engedélyezheti a ellenőrizhető hitelesítő adatok előzetes verzióját
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: d6e72b6d6f566fcf3f52e1c48ab6824c0e9a968e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222792"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>A ellenőrizhető hitelesítő adatok kihagyása (előzetes verzió)

A cikk tartalma:

- Az OK, amiért lehetséges, hogy ki kell jelentkeznie.
- A szükséges lépések.
- Mi történik az adataival?
- A meglévő ellenőrizhető hitelesítő adatokra gyakorolt hatás.

> [!IMPORTANT]
> Azure Active Directory ellenőrizhető hitelesítő adatok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

- Ellenőrizhető hitelesítő adatok bevezetésének befejezése.

## <a name="potential-reasons-for-opting-out"></a>A leiratkozás lehetséges okai

Jelenleg nincs lehetőség a tartományi információk módosítására. Ennek eredményeképpen, ha hibát vétett, vagy úgy dönt, hogy változást kíván végrehajtani, nem érhető el más lehetőség a kilépések és az újraindítások mellett.

## <a name="the-steps-required"></a>A szükséges lépések

1. A Azure Portal keressen ellenőrizhető hitelesítő adatokat.
2. A bal oldali menüben válassza a **Beállítások** lehetőséget.
3. A szakasz alatt **állítsa alaphelyzetbe a szervezetét**, válassza a **minden hitelesítő adat törlése lehetőséget, és az előzetes verzió letiltását**.

   ![beállítások alaphelyzetbe állítása](media/how-to-opt-out/settings-reset.png)

4. Olvassa el a figyelmeztető üzenetet, és folytassa a **Törlés és a Letiltás** lehetőséget.

   ![beállítások törlése és letiltás](media/how-to-opt-out/delete-and-opt-out.png)

Mostantól kikapcsolta a ellenőrizhető hitelesítő adatok előzetesét. Olvasson tovább, hogy megértse, mi történik a motorháztető alatt.

## <a name="what-happens-to-your-data"></a>Mi történik az adataival?

Ha elvégezte a Azure Active Directory ellenőrizhető hitelesítő adatok szolgáltatásból való kilépést, a következő műveletek végrehajtására kerül sor:

- A Key Vaultban található kulcsok nem lesznek [törölve](../../key-vault/general/soft-delete-overview.md).
- A kiállító objektumot a rendszer törli az adatbázisból.
- A bérlői termékazonosító törölve lesz az adatbázisból. 
- Az összes szerződés objektum törölve lesz az adatbázisból.

A letiltást követően nem fogja tudni helyreállítani a műveletét, és nem végezheti el a műveletet. Ez a lépés egy egyirányú művelet, és újra kell választania, ami új létrehozást eredményezett.  

## <a name="effect-on-existing-verifiable-credentials"></a>A meglévő ellenőrizhető hitelesítő adatokra gyakorolt hatás

Az összes már kiadott ellenőrizhető hitelesítő adat továbbra is fennáll. A rendszer nem titkosítja a titkosítást, mivel a művelet az ION használatával megoldható marad.
Ha azonban a függő entitások meghívja a status API-t, a rendszer mindig hibaüzenetet küld.

## <a name="next-steps"></a>Következő lépések

- Ellenőrizhető hitelesítő adatok beállítása az Azure- [bérlőn](get-started-verifiable-credentials.md)