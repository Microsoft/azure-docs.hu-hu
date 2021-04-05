---
title: Felügyelt identitások használata az Event hub Azure Stream Analytics feladatokból való eléréséhez (előzetes verzió)
description: Ez a cikk azt ismerteti, hogyan használhatók a felügyelt identitások a Azure Stream Analytics-feladatok Azure Event Hubs bemenetre és kimenetre való hitelesítéséhez.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 4dc718f21f2ef3beabc31821bd60b571ac07be05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018751"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>Felügyelt identitások használata az Event hub Azure Stream Analytics feladatokból való eléréséhez (előzetes verzió)

Azure Stream Analytics támogatja a felügyelt identitások hitelesítését az Azure Event Hubs-bemenethez és-kimenethez is. A felügyelt identitások megszüntetik a felhasználó-alapú hitelesítési módszerek korlátozásait, például a jelszó módosítása vagy a felhasználói jogkivonat lejárata miatti újrahitelesítés szükségességét a 90 naponta. Ha eltávolítja a manuális hitelesítés szükségességét, a Stream Analytics üzemelő példányok teljes mértékben automatizálva lehetnek.  

A felügyelt identitás egy Azure Active Directoryban regisztrált felügyelt alkalmazás, amely egy adott Stream Analytics feladatnak felel meg. A felügyelt alkalmazás használatával hitelesíthető egy célként megadott erőforrás, beleértve a tűzfal vagy a virtuális hálózat (VNet) mögött lévő Event Hubsokat is. A tűzfalak megkerülésével kapcsolatos további információkért lásd: [hozzáférés engedélyezése az Azure Event Hubs névterekhez privát végpontokon keresztül](../event-hubs/private-link-service.md#trusted-microsoft-services).

Ebből a cikkből megtudhatja, hogyan engedélyezheti a felügyelt identitást egy Stream Analytics-feladathoz tartozó Event Hubs beviteléhez vagy kimenetéhez a Azure Portalon keresztül.A felügyelt identitás engedélyezése előtt rendelkeznie kell egy Stream Analytics feladatokkal és az Event hub-erőforrással.

### <a name="limitation"></a>Korlátozás
Az előzetes verzióban a Event Hubs on Azure Portal mintavételi bemenete nem fog működni a felügyelt Identitásos hitelesítési mód használata esetén.

## <a name="create-a-managedidentity"></a>Felügyelt identitás létrehozása  

Először létre kell hoznia egy felügyelt identitást a Azure Stream Analytics feladatokhoz.  

1. A Azure Portal nyissa meg a Azure Stream Analytics feladatot.  

1. A bal oldali navigációs menüben válassza a konfigurálás területen található **felügyelt identitás** elemet    **. Ezután jelölje be a **rendszerhez rendelt felügyelt identitás használata** melletti jelölőnégyzetet,   majd válassza a **Mentés** lehetőséget.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="Rendszerhez rendelt felügyelt identitás":::  

1. A Stream Analytics-feladatok identitásához tartozó egyszerű szolgáltatás a Azure Active Directoryban jön létre. Az újonnan létrehozott identitás életciklusát az Azure felügyeli. Ha a Stream Analytics feladatot törli, a társított identitást (azaz az egyszerű szolgáltatásnevet) az Azure automatikusan törli.  

   A konfiguráció mentésekor a szolgáltatásnév objektumazonosító (OID) szerepel a résztvevő AZONOSÍTÓJAként az alábbi ábrán látható módon:  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="Résztvevő azonosítója":::

   Az egyszerű szolgáltatásnév neve megegyezik a Stream Analytics feladatokkal. Ha például a feladattípus neve  `MyASAJob` , az egyszerű szolgáltatásnév neve is  `MyASAJob` .  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>A Stream Analytics-feladathoz tartozó engedélyek megadása az Event hub eléréséhez

Ahhoz, hogy a Stream Analytics-feladatok felügyelt identitás használatával férhessenek hozzá az Event hub-hoz, az Ön által létrehozott egyszerű szolgáltatásnak különleges engedélyekkel kell rendelkeznie az Event hub-hoz.

1. Nyissa meg az **Access Control (iam)** az Event hub-ban.

1. Válassza a + **szerepkör-hozzárendelés** **hozzáadása** és hozzáadása lehetőséget.

1. A **szerepkör-hozzárendelés hozzáadása** lapon adja meg a következő beállításokat:

   |Paraméter|Érték|
   |---------|-----|
   |Szerepkör|Az Azure Event Hubs adattulajdonos|
   |Hozzáférés hozzárendelése ehhez|Felhasználó, csoport vagy egyszerű szolgáltatásnév|
   |Válassza ezt:|Adja meg a Stream Analytics-feladatoknak a nevét|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="Szerepkör-hozzárendelés hozzáadása":::

1. Kattintson a **Save (Mentés** ) gombra, és várjon egy percet, amíg a módosítások propagálása megtörtént.

Ezt a szerepkört az Event hub-névtér szintjén is megadhatja, amely természetes módon propagálja az engedélyeket az abban létrehozott összes Event Hubs számára. Ez azt is megteheti, hogy a névtérben lévő összes Event Hubs felügyelt identitás-hitelesítő erőforrásként használható a Stream Analytics feladatban.

## <a name="create-anevent-hub-input-or-output"></a>Event hub-bemenet vagy-kimenet létrehozása  

Most, hogy beállította a felügyelt identitását, készen áll arra, hogy hozzáadja az Event hub-erőforrást bemenetként vagy kimenetként a Stream Analytics feladathoz.  

### <a name="add-the-event-hub-as-an-input"></a>Az Event hub hozzáadása bemenetként 

1. Lépjen a Stream Analytics feladathoz, és navigáljon a **bemenetek** lapra a **feladatok topológiája** alatt.

1. Válassza a **stream-bemenet hozzáadása > Event hub** elemet. A bemeneti tulajdonságok ablakban keresse meg és válassza ki az Event hub elemet, majd válassza a **felügyelt identitás** lehetőséget a *hitelesítési mód* legördülő menüből.

1. Töltse ki a többi tulajdonságot, és válassza a **Mentés** lehetőséget.

### <a name="add-the-event-hub-as-an-output"></a>Az Event hub hozzáadása kimenetként

1. Lépjen a Stream Analytics feladathoz, és navigáljon a **kimenetek** lapra a **feladatok topológiája** alatt.

1. Válassza a **hozzáadás > Event hub** elemet. A kimeneti tulajdonságok ablakban keresse meg és válassza ki az Event hub-t, majd válassza a **felügyelt identitás** lehetőséget a *hitelesítési mód* legördülő menüből.

1. Töltse ki a többi tulajdonságot, és válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics Event Hubs kimenete](event-hubs-output.md)
* [Adatok streamelése az Event Hubsból](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
