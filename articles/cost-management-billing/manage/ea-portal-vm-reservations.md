---
title: Nagyvállalati szerződéses Azure-virtuálisgépek fenntartott példányai
description: Ez a cikk azt foglalja össze, hogyan takaríthat meg pénzt a vállalati regisztrációjában az Azure Reserved VM Instances szolgáltatás segítségével.
author: bandersmsft
ms.author: banders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 6303a94cec9efc01815b6dc6c697abdfe0f84227
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220900"
---
# <a name="azure-ea-vm-reserved-instances"></a>Nagyvállalati szerződéses Azure-virtuálisgépek fenntartott példányai

Ez a cikk azt foglalja össze, hogyan takaríthat meg pénzt a vállalati regisztrációjában az Azure Reserved VM Instances szolgáltatás segítségével. További információ a fenntartott példányokról: [Mi az az Azure Reservations?](../reservations/save-compute-costs-reservations.md).

## <a name="reservation-exchanges-and-refunds"></a>Foglalások cseréje és visszatérítése

A foglalásokat le tudja cserélni azonos típusú foglalásokra. Kérheti egy foglalás díjának visszatérítését is 50 000 USD/év összegig, ha már nincs rá szüksége. Az Azure Portalon cserélheti le a foglalásokat vagy kérheti a díjaik visszatérítését. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../reservations/exchange-and-refund-azure-reservations.md).

### <a name="partial-refunds"></a>Részleges visszatérítések

Részleges visszatérítést akkor biztosítunk, amikor egy EA-ügyfél kerettúllépéssel, nem Azure-előrefizetéssel (korábbi nevén pénzügyi keret) vásárolt foglalást ad vissza.

Az EA Portalon a visszatérítés az előző hónapban negatív, az aktuális hónapban pedig pozitív helyesbítésként látható. A foglalások cseréjéhez hasonlóan jelenik meg. A jóváírási emlékeztető az eredeti számlaszámra hivatkozik, ezért az eredeti vásárlás és a jóváírási emlékeztető egyeztetéséhez az eredeti számlaszámot használja.

## <a name="reservation-costs-and-usage"></a>Foglalási költségek és használati adatok

A Nagyvállalati Szerződéssel rendelkező ügyfelek megtekinthetik a költség- és használati adatokat az Azure Portalon és a REST API-kban. A foglalási költségekre és használati adatokra vonatkozóan az alábbi lehetőségei vannak:

- Lekérheti a foglalás megvásárlására vonatkozó adatokat.
- Megtudhatja, melyik előfizetés, erőforráscsoport vagy erőforrás használt foglalást.
- Jóváírást kérhet a foglalás használata alapján.
- Kiszámíthatja a foglalási megtakarításokat.
- Lekérheti a foglalás kihasználatlansági adatait.
- Amortizálhatja a foglalási költségeket.

A foglalási költségekkel és használati adatokkal kapcsolatos további információkért tekintse meg a [Nagyvállalati szerződés foglalási költségeinek és kihasználtságának lekérése](../reservations/understand-reserved-instance-usage-ea.md) szakaszt.

A díjszabással kapcsolatos további információkért tekintse meg a [Linux rendszerű virtuális gépek díjszabási](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) oldalát vagy a [Windows rendszerű virtuális gépek díjszabási](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) oldalát.

## <a name="reserved-instances-api-support"></a>Fenntartott példányok API támogatása

Az Azure API-k használatával programozott módon kérhet le információkat a szervezet számára az Azure-szolgáltatások vagy -szoftverek foglalásaival kapcsolatban. Az API-k például a következőkre használhatók:

- Megvásárolható foglalások keresése
- Foglalás vásárlása
- Vásárolt foglalások megtekintése
- Foglalások hozzáférésének megtekintése és kezelése
- Foglalások felosztása vagy egyesítése
- Foglalások hatókörének módosítása

További információkért lásd [az Azure-foglalás automatizálásához elérhető API-kat](../reservations/reservation-apis.md).

## <a name="azure-reserved-virtual-machine-instances"></a>Azure-beli fenntartott virtuálisgép-példányok

A fenntartott példányok a használatalapú fizetéshez képest akár 72 százalékkal csökkenthetik a virtuális gép költségeit az összes virtuális gépen. Az Azure Hybrid Benefittel együtt használva akár 82 százalékos megtakarítást jelenthetnek. Az egy vagy három éves időszakokra vonatkozó előzetes fizetéssel a fenntartott példányok nagyobb segítséget nyújtanak a számítási feladatok, a költségvetés és az előrejelzések kezelésében. Az üzleti igények változásakor le is cserélheti vagy vissza is vonhatja a foglalásokat.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Fenntartott virtuálisgép-példányok megvásárlása

Egy Azure-beli fenntartott virtuálisgép-példány megvásárlásához a nagyvállalati Azure regisztrációs rendszergazdájának engedélyeznie kell a _Fenntartott példány_ vásárlási lehetőséget. Ez a lehetőség az [Azure EA Portal](https://ea.azure.com/)_Regisztráció_ lapjának _Regisztráció részletei_ szakaszában található.

Ha az EA-regisztrációhoz engedélyezett a fenntartott példányok hozzáadása, akkor az EA-regisztrációval társított aktív előfizetéssel rendelkező bármely fióktulajdonos vásárolhat fenntartott virtuálisgép-példányt az [Azure Portalon](https://aka.ms/reservations). További információkért lásd: [Előre fizetés virtuális gépekért és költségmegtakarítás fenntartott virtuálisgép-példányokkal](../../virtual-machines/prepay-reserved-vm-instances.md).

### <a name="how-to-view-reserved-instance-purchase-details"></a>A fenntartott példányok vásárlásával kapcsolatos részletek megtekintése

A fenntartott példányok vásárlásának részleteit az [Azure Portal](https://aka.ms/reservations) vagy az [Azure EA Portal](https://ea.azure.com/) bal oldalán található _Foglalások_ menüben tekintheti meg. Válassza a bal oldali menüben a **Jelentések** lehetőséget, majd görgessen le a _Használati adatok összegzése_ lapon a _Díjak szolgáltatások szerint_ szakaszhoz. A szakasz aljára görgetve láthatja a fenntartott példányok vásárlásának és használati adatainak listáját a szolgáltatás neve melletti `1 year` vagy `3 years` jelöléssel, például: `Standard_DS1_v2 eastus 1 year` vagy `Standard_D2s_v3 eastus2 3 years`.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Hogyan változtathatom meg a fenntartott példányhoz társított előfizetést, vagy hogyan vihetem át a fenntartott példány előnyeit egy ugyanazon fiókhoz tartozó előfizetésre?

A fenntartott példányok előnyeit kapó előfizetést az alábbiak szerint módosíthatja:

- Jelentkezzen be az [Azure Portalra](https://aka.ms/reservations).
- Frissítse az alkalmazott előfizetés hatókörét egy másik, ugyanabban a fiókban lévő előfizetés társításával.

A foglalás hatókörének módosításáról szóló további információkért tekintse meg a [foglalás hatókörének módosítását](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope) ismertető részt.

### <a name="how-to-view-reserved-instance-usage-details"></a>A fenntartott példányok használatával kapcsolatos részletek megtekintése

A fenntartott példányok használatával kapcsolatos részleteket az [Azure Portalon](https://aka.ms/reservations) vagy az [Azure EA Portalon](https://ea.azure.com/) tekintheti meg (olyan nagyvállalati szerződéssel rendelkező ügyfelek esetén, akik hozzáférnek a számlázási adatok megtekintéséhez) a _Jelentések_ > _Használati adatok összegzése_ > _Díjak szolgáltatások szerint_ területen. A fenntartott példányok a „Reservation” szót tartalmazó szolgáltatásnevekként azonosíthatók, például: `Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)`.

A használati adatok és a speciális jelentés letölthető CSV-fájlja további használati információt tartalmaz a fenntartott példánnyal kapcsolatban. A _További információ_ mező segít meghatározni a fenntartott példány használatát.

Ha nem az Azure Hybrid Benefittel vásárolt Azure-beli fenntartott virtuálisgép-példányokat, a fenntartott példányok két mérőszámot (hardverhasználati és szoftverhasználati) bocsátanak ki. Ha az Azure Hybrid Benefitet használja a fenntartott példány megvásárlására, a szoftverhasználati mérőszám nem látható a fenntartott példány használatának részleteiben.

### <a name="reserved-instance-billing"></a>Fenntartott példány számlázása

Nagyvállalati ügyfelek esetén Azure-előrefizetés használatával vásárolhatók Azure-beli fenntartott virtuálisgép-példányok. Ha a regisztrációban elegendő Azure-előrefizetési egyenleg áll rendelkezésre a fenntartott példány megvásárlásához, akkor a rendszer levonja az összeget az Azure-előrefizetési egyenlegéből, és nem kap számlát a vásárlásról.

Abban az esetben, ha az Azure Nagyvállalati Szerződéssel rendelkező ügyfelek felhasználták a teljes Azure-előrefizetésüket, továbbra is vásárolhatnak fenntartott példányokat, de ezek a vásárlások fel lesznek számolva a következő túlhasználati számlán. Ha vannak a fenntartott példányokkal járó túlhasználati díjak, azok a normál túlhasználati számla részét képezik.

### <a name="reserved-instance-expiration"></a>Fenntartott példány lejárata

E-mail-értesítéseket fog kapni, az első 1 30 nappal a foglalás lejárta előtt, a másikat pedig lejáratkor. Ha a fenntartás lejár, az üzemelő virtuális gépek továbbra is futnak majd, és a későbbiekben használatalapú fizetési díjszabás vonatkozik rájuk. További információ: [Fenntartott virtuálisgép-példányok ajánlata](https://azure.microsoft.com/pricing/reserved-vm-instances/).

## <a name="next-steps"></a>További lépések

- További információ az Azure fenntartott példányokról: [Mi az az Azure Reservations?](../reservations/save-compute-costs-reservations.md).
- A vállalati foglalási költségekkel és használati adatokkal kapcsolatos további információkért tekintse meg a [Nagyvállalati szerződés foglalási költségeinek és kihasználtságának lekérése](../reservations/understand-reserved-instance-usage-ea.md) szakaszt.
- A díjszabással kapcsolatos további információkért tekintse meg a [Linux rendszerű virtuális gépek díjszabási](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) oldalát vagy a [Windows rendszerű virtuális gépek díjszabási](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) oldalát.
