---
title: Vezető felügyelet a Microsoft kereskedelmi piactérről
description: Ismerje meg, hogyan hozhat létre és fogadhat ügyfeleinek vezetőit a Microsoft AppSource és az Azure Marketplace-ajánlatokból
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 1d5f2248d94796d5e3ee76301642a95abddebfe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94489335"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Érdeklődők a kereskedelmi piactéri ajánlattól

Az érdeklődők az ajánlatok [Microsoft AppSource](https://appsource.microsoft.com) és az [Azure Marketplace](https://azuremarketplace.microsoft.com)-en való üzembe helyezésére és telepítésére is kíváncsiak. A vásárlói érdeklődőket az ajánlat közzétételét követően a kereskedelmi piactéren kaphatja meg. Ez a cikk a következő vezető felügyeleti fogalmakat ismerteti:

* Hogyan állítja elő a kereskedelmi Marketplace-ajánlat az ügyfeleket, hogy ne maradjon le üzleti lehetőségekről. 
* Az Ügyfélkapcsolat-kezelési (CRM) rendszernek az ajánlathoz való kapcsolódása, hogy az érdeklődők egyetlen központi helyen kezelhetők legyenek.
* Az Ön által elküldött érdeklődői adatküldés után nyomon követheti az Ön számára elérhető ügyfeleket.

## <a name="generate-customer-leads"></a>Ügyfél-érdeklődők előállítása

Az alábbi helyeken jönnek létre az érdeklődők:

- Az ügyfél beleegyezett, hogy megosztja az adatait, miután kiválasztotta a **kapcsolatot** a kereskedelmi piactéren. Ez az érdeklődő *kezdeti érdeklődést* eredményez. Az Önnel kapcsolatos információkat a termék beszerzésére vonatkozó érdeklődéssel rendelkező ügyféllel osztjuk meg. Az érdeklődő a beszerzési tölcsér felső része.

    ![Dynamics 365 Kapcsolatfelvétel](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Az ügyfél most kiválasztja a **Letöltés** lehetőséget (vagy kiválasztja a **létrehozás** elemet a [Azure Portal](https://portal.azure.com/)) az ajánlat beszerzéséhez. Ez az érdeklődő egy *aktív* érdeklődő. Az Önnel kapcsolatos információkat megosztjuk a termék üzembe helyezését megkezdő ügyféllel.

    ![SQL-Letöltés most gomb](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server létrehozás gomb](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Egy ügyfél kiválasztja a **tesztelési meghajtót** vagy az **ingyenes próbaverziót** az ajánlat kipróbálásához. A tesztelési és az ingyenes próbaverziók gyorsított lehetőséget biztosítanak az üzleti tevékenység azonnali megosztására a potenciális ügyfelekkel a belépési korlátok nélkül.

    ![Dynamics 365 Test Drive gomb](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 ingyenes próbaverzió gomb](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Kapcsolódás a CRM rendszerhez

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Az érdeklődők megismerése

Az ügyfél beszerzési folyamata során kapott összes érdeklődő meghatározott mezőkben lévő adattal rendelkezik. Az első kikeresni kívánt mező az a `LeadSource` mező, amely a következő formátumot követi: **forrás-művelet**  |  **ajánlat**.

**Források**: a mező értékét a rendszer az érdeklődőt létrehozó piactér alapján tölti fel. A lehetséges értékek:, `"AzureMarketplace"` `"AzurePortal"` és `"AppSource (SPZA)"` .

**Műveletek**: a mező értéke az ügyfél által az érdeklődőt létrehozó piactéren végrehajtott művelet alapján van feltöltve.

Lehetséges értékek:

- **"Ins"**: a *telepítésre* áll. Ez a művelet az Azure Marketplace-en vagy a AppSource van, amikor egy ügyfél beszerzi a terméket.
- **"PLT"**: a *partner által vezetett próbaverzióra* áll. Ez a művelet a AppSource, ha az ügyfél kiválasztja a **Kapcsolatfelvétel** lehetőséget.
- **"DNC –"**: a (z) nem *kapcsolatra* vonatkozó. Ez a művelet abban az esetben AppSource, ha az alkalmazás oldalán felsorolt partnereket kérik a kapcsolatfelvételre. Megosztunk egy értesítést arról, hogy az ügyfél szerepel az alkalmazáson, de nem kell kapcsolatba lépnie.
- **"Létrehozás"**: Ez a művelet csak a Azure Portal belül történik, és akkor jön létre, amikor egy ügyfél megvásárolja az ajánlatot a fiókjába.
- **"StartTestDrive"**: Ez a művelet csak a **tesztvezetés** beállítás esetében jön létre, amikor egy ügyfél elindítja a tesztelési meghajtót.

**Ajánlatok**: több ajánlat is lehet a kereskedelmi piactéren. A mező értékét a rendszer az érdeklődőt létrehozó ajánlat alapján tölti fel. A közzétevő AZONOSÍTÓját és az ajánlat AZONOSÍTÓját is elküldjük ebben a mezőben, és azokat az értékeket, amelyeket az ajánlatnak a piactéren való közzétételekor adott meg.

Az alábbi példák a várt formátumú értékeket mutatják `publisherid.offerid` : 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Vásárlói adatok

Az ügyfél adatait több mezőn keresztül küldik el. Az alábbi példa egy érdeklődő ügyfél-információit mutatja be:

- FirstName: John
- LastName: Smith
- E-mail: jsmith \@ Microsoft.com
- Telefon: 1234567890
- Ország: Egyesült Államok
- Vállalat: Microsoft
- Cím: műszaki igazgató

>[!NOTE]
>Az előző példában szereplő összes érték mindig elérhető az egyes érdeklődők számára. Mivel az "ügyfél-érdeklődők létrehozása" című szakaszban említett lépések több lépésből állnak, az érdeklődők kezelésének legjobb módja a rekordok visszavonása és a követő ablakok személyre szabása. Így minden ügyfél megfelelő üzenetet kap, és létrehoz egy egyedi kapcsolatot.

## <a name="best-practices-for-lead-management"></a>Ajánlott eljárások az érdeklődők felügyeletéhez

Íme néhány javaslat az értékesítési cikluson keresztüli vezetéshez:

- **Folyamat**: adjon meg egy egyértelmű értékesítési folyamatot, a mérföldköveket, az elemzést és a csoport törlését.
- **Minősítés**: az előfeltételek meghatározása, amely azt jelzi, hogy egy érdeklődő teljesen minősített-e. Győződjön meg arról, hogy az értékesítési vagy a marketing képviselői körültekintően vezetnek be, mielőtt a teljes értékesítési folyamaton átveszik őket.
- **Utólagos követés**: ne feledkezzen meg 24 órán belül. Közvetlenül az ügyfél üzembe helyezése után azonnal elérhetővé válik a CRM-ben. e-mailben elküldheti őket, miközben még mindig melegek. Kérje a telefonhívás ütemezését, hogy jobban megértse, ha a termék jó megoldás a problémára. A tipikus tranzakció várhatóan számos követő hívást igényel.
- **Ápolás**: táplálja az érdeklődőket, hogy minél magasabb haszonkulcsot kapjon. Jelentkezzen be, de ne bombázza őket. Javasoljuk, hogy legalább néhány alkalommal küldje el az e-maileket, mielőtt bezárná őket; Ne adja meg az első kísérlet után. Ne feledje, hogy ezek az ügyfelek közvetlenül a termékkel foglalkoznak, és időt vesznek igénybe az ingyenes próbaverzióban; nagyszerű kilátások.

A technikai beállítás bevezetését követően az aktuális értékesítési és marketing stratégia és működési folyamatok részeként építse be ezeket az érdeklődőket. Fontos, hogy jobban megértse a teljes értékesítési folyamatot, és szeretne szorosan együttműködni Önnel, hogy kiváló minőségű érdeklődőket és elegendő adatmennyiséget biztosítson a siker érdekében. Örömmel vesszük visszajelzését arról, hogy miként optimalizálható és növelhető az érdeklődők számára elérhető további információk, amelyek segítenek az ügyfelek sikerességében. Tudassa velünk, ha szeretne [visszajelzést](mailto:AzureMarketOnboard@microsoft.com) küldeni, és javaslatokat tesz arra, hogy az értékesítési csapat sikeresebb legyen a kereskedelmi piactéren.

## <a name="next-steps"></a>Következő lépések

- [Az érdeklődők felügyeletével kapcsolatos gyakori kérdések és hibaelhárítás](../lead-management-faq.md)