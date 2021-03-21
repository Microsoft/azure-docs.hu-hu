---
title: Azure Data Box Gateway biztonság | Microsoft Docs
description: Ismerteti azokat a biztonsági és adatvédelmi funkciókat, amelyek védik a Azure Data Box Gateway virtuális eszközét, szolgáltatását és adatait a helyszínen és a felhőben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 13d3809611714992f24a66a96c22074e69fba9bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786656"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Biztonság és adatvédelem Azure Data Box Gateway

A biztonság jelentős jelentőséggel bír az új technológiák bevezetésekor, különösen akkor, ha a technológiát bizalmas vagy tulajdonosi adatokkal használják. Azure Data Box Gateway segítségével biztosíthatja, hogy csak a felhatalmazott entitások tudják megtekinteni, módosítani vagy törölni az adatait.

Ez a cikk azokat a Azure Data Box Gateway biztonsági funkciókat ismerteti, amelyek segítenek a megoldás egyes összetevőinek és a bennük tárolt adatainak a védelmében.

A Data Box Gateway megoldás négy fő összetevőből áll, amelyek egymással együttműködnek:

- **Az Azure-ban üzemeltetett Data Box Gateway szolgáltatás**. Az eszköz megrendelésének létrehozásához használt felügyeleti erőforrás, konfigurálja az eszközt, majd kövesse nyomon a befejezési sorrendet.
- **Data Box Gateway eszköz**. Az Ön által megadott rendszer hypervisorában kiépített virtuális eszköz. A virtuális eszköz használatával importálhatja a helyszíni adatait az Azure-ba.
- **Az eszközhöz csatlakoztatott ügyfelek/gazdagépek**. Az infrastruktúra azon ügyfelei, amelyek a Data Box Gateway eszközhöz csatlakoznak, és a védeni kívánt adatait tartalmazzák.
- **Felhőbeli tárolás**. A hely az Azure Cloud platformon, ahol az adatgyűjtés történik. Ez a hely általában a létrehozott Data Box Gateway erőforráshoz csatolt Storage-fiók.

## <a name="data-box-gateway-service-protection"></a>Data Box Gateway szolgáltatás védelme

A Data Box Gateway szolgáltatás az Azure-ban üzemeltetett felügyeleti szolgáltatás. A szolgáltatás az eszköz konfigurálására és kezelésére szolgál.

[!INCLUDE [data-box-gateway-service-protection](../../includes/data-box-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box Gateway eszköz védelme

A Data Box Gateway eszköz egy olyan virtuális eszköz, amely egy Ön által megadott helyszíni rendszer hypervisorában van kiépítve. Az eszköz segít az Azure-ba való adatküldésben. Az eszköz:

- Aktiválási kulcsra van szüksége az Azure Stack Edge Pro/Data Box Gateway szolgáltatás eléréséhez.
- Az eszköz jelszavai mindig védelmet biztosítanak.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Az eszköz védetté aktiválása az aktiválási kulcs használatával

Csak egy engedélyezett Data Box Gateway eszköz csatlakozhat az Azure-előfizetésében létrehozott Data Box Gateway szolgáltatáshoz. Az eszköz engedélyezéséhez aktiválnia kell egy aktiválási kulcsot az eszköz Data Box Gateway szolgáltatással való aktiválásához.

[!INCLUDE [data-box-gateway-activation-key](../../includes/data-box-gateway-activation-key.md)]

További információ: [aktiválási kulcs beszerzése](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Az eszköz jelszavas védelemmel való ellátása

A jelszavak biztosítják, hogy csak a jogosult felhasználók férjenek hozzá az adataihoz. Data Box Gateway az eszközök zárolt állapotban vannak.

A következőket teheti:

- Kapcsolódjon az eszköz helyi webes FELÜLETéhez egy böngészőben, majd adjon meg egy jelszót az eszközre való bejelentkezéshez.
- Távolról kapcsolódhat az eszköz PowerShell-felületéhez HTTP-n keresztül. A távfelügyelet alapértelmezés szerint be van kapcsolva. Ezután megadhatja az eszköz jelszavát az eszközre való bejelentkezéshez. További információ: [távoli kapcsolódás a Data Box Gateway eszközhöz](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-gateway-password-best-practices](../../includes/data-box-gateway-password-best-practices.md)]
- A [jelszó megváltoztatásához](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)használja a helyi webes felhasználói felületet. Ha megváltoztatja a jelszót, ügyeljen arra, hogy tájékoztassa az összes távelérési felhasználót, hogy ne legyen probléma a bejelentkezéskor.

## <a name="protect-your-data"></a>Az adatok védelme

Ez a szakasz azokat a Data Box Gateway biztonsági szolgáltatásokat ismerteti, amelyek a továbbítást és a tárolt információkat védik.

### <a name="protect-data-at-rest"></a>Az inaktív adatok védelme

[!INCLUDE [data-box-gateway-data-rest](../../includes/data-box-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>A repülésben tárolt adatvédelem

[!INCLUDE [data-box-gateway-data-flight](../../includes/data-box-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>Adattárolás a Storage-fiókok használatával

[!INCLUDE [data-box-gateway-data-storage-accounts](../../includes/data-box-gateway-protect-data-storage-accounts.md)]

- Forgassa el és [szinkronizálja rendszeresen a Storage-fiók kulcsait](data-box-gateway-manage-shares.md#sync-storage-keys) , hogy megvédje a Storage-fiókját a jogosulatlan felhasználóktól.

### <a name="protect-the-device-data-using-bitlocker"></a>Az eszköz adatainak védelme a BitLocker használatával

A Data Box Gateway virtuális gépen lévő virtuális lemezek biztonságossá tételéhez ajánlott engedélyezni a BitLockert. Alapértelmezés szerint a BitLocker nincs engedélyezve. További információkért lásd:

- [Titkosítási támogatás beállításai a Hyper-V kezelőjében](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [BitLocker-támogatás egy virtuális gépen](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>Személyes adatok kezelése

A Data Box Gateway szolgáltatás a következő helyzetekben gyűjt személyes adatokat:

[!INCLUDE [data-box-gateway-manage-personal-data](../../includes/data-box-gateway-manage-personal-data.md)]

A megosztásokat elérő vagy törölhető felhasználók listájának megtekintéséhez kövesse a [megosztások kezelése a Data Box Gatewayban](data-box-gateway-manage-shares.md)című témakör lépéseit.

További információkért tekintse át a Microsoft adatvédelmi szabályzatát a [megbízhatósági központban](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Következő lépések

[A Data Box Gateway eszköz üzembe helyezése](data-box-gateway-deploy-prep.md)
