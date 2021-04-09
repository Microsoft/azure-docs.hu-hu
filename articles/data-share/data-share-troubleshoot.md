---
title: Az Azure Data Share hibaelhárítása
description: Megtudhatja, hogyan oldhatja meg a meghívásokkal és hibákkal kapcsolatos problémákat, amikor adatmegosztásokat hoz létre vagy fogad az Azure-adatmegosztásban.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: 3aa1c0b8579bd37d2bb51cbde70997131c696813
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97964507"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Az Azure-adatmegosztás gyakori problémáinak elhárítása 

Ez a cikk az Azure-adatmegosztással kapcsolatos gyakori problémák elhárítását ismerteti. 

## <a name="azure-data-share-invitations"></a>Azure-adatmegosztási meghívások 

Bizonyos esetekben, amikor az új felhasználók kiválasztja a **meghívás fogadása** e-mailben lehetőséget, a meghívások üres listáját láthatják. 

:::image type="content" source="media/no-invites.png" alt-text="A meghívások üres listáját bemutató képernyőkép.":::

A probléma a következő okok egyike lehet:

* **Az Azure-beli adatmegosztási szolgáltatás nincs regisztrálva az Azure-bérlő bármely Azure-előfizetésének erőforrás-szolgáltatója.** Ez a probléma akkor fordul elő, ha az Azure-bérlő nem rendelkezik adatmegosztási erőforrással. 

    Az Azure Data Share-erőforrást a létrehozásakor a rendszer automatikusan erőforrás-szolgáltatóként regisztrálja az Azure-előfizetésben. Az adatmegosztási szolgáltatást manuálisan is regisztrálhatja a következő lépések segítségével. A lépések elvégzéséhez szüksége lesz a [közreműködő szerepkörre](../role-based-access-control/built-in-roles.md#contributor) az Azure-előfizetéshez. 

    1. Az Azure Portalon lépjen az **Előfizetések** oldalra.
    1. Válassza ki azt az előfizetést, amelyet az Azure-beli adatmegosztási erőforrás létrehozásához használni kíván.
    1. Válassza az **erőforrás-szolgáltatók** lehetőséget.
    1. Keressen rá a **Microsoft. DataShare** kifejezésre.
    1. Válassza a **Regisztráció** lehetőséget.

* **A meghívót az Azure bejelentkezési e-mail-címe helyett az e-mail aliasára küldi a rendszer.** Ha már regisztrálta az Azure-beli adatmegosztási szolgáltatást, vagy létrehozott egy adatmegosztási erőforrást az Azure-bérlőben, de még mindig nem látja a meghívót, előfordulhat, hogy az e-mail-aliasa címzettként szerepel. Forduljon az adatszolgáltatóhoz, és győződjön meg arról, hogy a meghívót elküldi az Azure bejelentkezési e-mail-címére, és nem az e-mail-aliasát.

* **A meghívót már elfogadták.** Az e-mailben található hivatkozás a Azure Portal **adatmegosztási meghívások** lapjára lép. Ezen a lapon csak a függőben lévő meghívókat listázza. Az elfogadott meghívások nem jelennek meg az oldalon. A fogadott megosztások megtekintéséhez és a cél Azure Adatkezelő-fürt beállításainak konfigurálásához lépjen a meghívó elfogadásához használt adatmegosztási erőforráshoz.

## <a name="creating-and-receiving-shares"></a>Megosztások létrehozása és fogadása

Új megosztás létrehozásakor, adatkészletek hozzáadásakor vagy leképezési adatkészletekben a következő hibák jelenhetnek meg:

* Nem sikerült hozzáadni az adatkészleteket.
* Az adatkészletek leképezése nem sikerült.
* Nem adható meg az adatmegosztási erőforrás x-hozzáférése y-hez.
* Nem rendelkezik megfelelő engedélyekkel x-hez.
* Nem lehet írási engedélyeket adni az Azure-beli adatmegosztási fiókhoz egy vagy több kiválasztott erőforráshoz.

Ha nem rendelkezik megfelelő engedélyekkel az Azure-adattárhoz, előfordulhat, hogy az egyik hiba jelenik meg. További információkért lásd: [Szerepkörök és követelmények](concepts-roles-permissions.md). 

Az Azure-adattárakból származó adatok megosztásához vagy fogadásához írási engedéllyel kell rendelkeznie. Ez az engedély jellemzően a közreműködő szerepkör részét képezi. 

Ha első alkalommal oszt meg adatmegosztást vagy adatok fogadását az Azure-adattárból, szüksége lesz a *Microsoft. Authorization/szerepkör-hozzárendelésekre/írási* engedélyre is. Ez az engedély jellemzően a tulajdonosi szerepkör részét képezi. Még akkor is, ha létrehozta az Azure adattár-erőforrást, nem feltétlenül az erőforrás tulajdonosa. 

Ha rendelkezik a megfelelő engedélyekkel, az Azure adatmegosztási szolgáltatás automatikusan lehetővé teszi az adatmegosztási erőforrás felügyelt identitásának elérését az adattárhoz. Ez a folyamat eltarthat néhány percig. Ha ez a késés miatt sikertelen, próbálkozzon újra néhány perc múlva.

Az SQL-alapú megosztáshoz további engedélyek szükségesek. További információ az előfeltételekről: [SQL-források megosztása](how-to-share-from-sql.md).

## <a name="snapshots"></a>Pillanatképek
A pillanatképek számos okból sikertelenek lehetnek. Nyisson meg egy részletes hibaüzenetet a pillanatkép kezdési idejének és az egyes adatkészletek állapotának kiválasztásával. 

A pillanatképek általában sikertelenek a következő okok miatt:

* Az adatmegosztásnak nincs engedélye a forrás-adattárból való olvasásra vagy a cél adattárba való írásra. További információkért lásd: [Szerepkörök és követelmények](concepts-roles-permissions.md). Ha első alkalommal készít pillanatképet, előfordulhat, hogy az adatmegosztási erőforráshoz néhány percre van szükség, hogy hozzáférjen az Azure-adattárhoz. Néhány perc elteltével próbálkozzon újra.
* A tűzfal blokkolja az adatmegosztási kapcsolatokat a forrás adattárhoz vagy a célként megadott adattárhoz.
* A rendszer törölte A megosztott adatkészletet, a forrás adattárat vagy a tároló adattárát.

A Storage-fiókok esetében a pillanatkép meghiúsulhat, mert a rendszer frissíti a fájlt a forrásban, miközben a pillanatkép zajlik. Ennek eredményeképpen előfordulhat, hogy egy 0 bájtos fájl jelenik meg a célhelyen. A forrás frissítése után a pillanatképek sikeresek lesznek.

SQL-források esetén a pillanatképek sikertelenek lehetnek a következő okok miatt:

* Az adatmegosztási engedélyt biztosító forrás SQL-parancsfájl vagy cél SQL-parancsfájl nem fut. Vagy Azure SQL Database vagy Azure szinapszis Analytics (korábban Azure SQL Data Warehouse) esetében a parancsfájl SQL-hitelesítéssel fut Azure Active Directory hitelesítés helyett.  
* A forrás adattár vagy a cél SQL-adattár szüneteltetve van.
* A pillanatkép-készítési folyamat vagy a célként megadott adattár nem támogatja az SQL-adattípusokat. További információ: [megosztás SQL-forrásokból](how-to-share-from-sql.md#supported-data-types).
* A forrás adattárat vagy a cél SQL-adattárat más folyamatok zárolják. Az Azure-beli adatmegosztások nem zárolják ezeket az adattárakat. Az adattárakon meglévő zárolások azonban sikertelenek lehetnek.
* A célként megadott SQL-táblázatot egy Foreign Key korlátozás hivatkozik. Egy pillanatkép során, ha a céltábla neve megegyezik a forrásadatok táblájával, az Azure-adatmegosztás elveszíti a táblát, és új táblát hoz létre. Ha a cél SQL-táblát egy idegenkulcs-megkötés hivatkozik, a tábla nem helyezhető el.
* A rendszer létrehoz egy cél CSV-fájlt, de az Excelben nem olvashatók be az adatforrások. Ez a probléma akkor fordulhat elő, ha a forrás SQL-tábla nem angol karaktereket tartalmazó olyan információt tartalmaz. Az Excelben válassza az **adatlekérdezés** lapot, és válassza ki a CSV-fájlt. Válassza ki a fájl forrás **65001: Unicode (UTF-8)** elemet, majd töltse be az adatforrást.

## <a name="updated-snapshot-schedules"></a>Frissített pillanatkép-ütemtervek
Miután az adatszolgáltató frissíti az eljuttatott megosztáshoz tartozó pillanatkép-ütemtervet, az adatfogyasztónak le kell tiltania az előző pillanatkép-ütemtervet. Ezután engedélyezze a frissített pillanatkép-ütemtervet a fogadott megosztáshoz. 

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa a [megosztási adatelemzéssel](share-your-data.md) . 

Ha szeretné megtudni, hogyan fogadhat adatfogadást, folytassa az [elfogadás és fogadás adatgyűjtéssel](subscribe-to-data-share.md) foglalkozó oktatóanyagot.