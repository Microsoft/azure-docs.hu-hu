---
title: Azure Stack Edge Pro R Security | Microsoft Docs
description: Ismerteti azokat a biztonsági és adatvédelmi funkciókat, amelyek védik Azure Stack Edge Pro R-és Azure Stack Edge mini R-eszközeit, szolgáltatásait és adatait a helyszínen és a felhőben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 922ef24bff7d6e60a3ba3b91113003f8206b8177
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548641"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Biztonság és adatvédelem Azure Stack Edge Pro R és Azure Stack Edge mini R rendszerhez

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

A biztonság jelentős jelentőséggel bír az új technológiák bevezetésekor, különösen akkor, ha a technológiát bizalmas vagy tulajdonosi adatokkal használják. Azure Stack Edge Pro R és Azure Stack Edge mini R segítségével biztosíthatja, hogy csak a felhatalmazott entitások tekinthetik meg, módosíthatják vagy törölhetik az adataikat.

Ez a cikk a Azure Stack Edge Pro R és a Azure Stack Edge mini R biztonsági funkcióit ismerteti, amelyek segítenek a megoldás összes összetevőjének és a bennük tárolt adatainak a védelmében.

A megoldás négy fő összetevőből áll, amelyek egymással együttműködnek:

- **Azure stack Edge szolgáltatás, amely az Azure nyilvános vagy Azure Government felhőben üzemel**. Az eszköz megrendelésének létrehozásához használt felügyeleti erőforrás, konfigurálja az eszközt, majd kövesse nyomon a befejezési sorrendet.
- **Azure stack Edge robusztus eszköz**. Az Ön által szállított robusztus, fizikai eszköz, amellyel a helyszíni adatait az Azure nyilvános vagy Azure Government felhőbe importálhatja. Az eszköz lehet Azure Stack Edge Pro R vagy Azure Stack Edge mini R.
- **Az eszközhöz csatlakoztatott ügyfelek/gazdagépek**. Az infrastruktúrában lévő azon ügyfelek, amelyek az eszközhöz csatlakoznak, és a védeni kívánt adatait tartalmazzák.
- **Felhőbeli tárolás**. A hely az Azure Cloud platformon, ahol az adatgyűjtés történik. Ez a hely általában a létrehozott Azure Stack peremhálózati erőforráshoz csatolt Storage-fiók.

## <a name="service-protection"></a>Szolgáltatás védelme

Az Azure Stack Edge szolgáltatás az Azure-ban üzemeltetett felügyeleti szolgáltatás. A szolgáltatás az eszköz konfigurálására és kezelésére szolgál.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>Eszközvédelem

A robusztus eszköz egy helyszíni eszköz, amely segít az adatok helyi feldolgozásában, majd az Azure-ban való elküldésében. Az eszköz:

- Aktiválási kulcsra van szüksége az Azure Stack Edge szolgáltatás eléréséhez.
- Az eszköz jelszavai mindig védelmet biztosítanak.
- Egy zárolt eszköz. Az eszköz alaplapi-felügyeleti vezérlője (BMC) és a BIOS jelszóval védett. A BMC-t korlátozott felhasználói hozzáférés védi.
- A biztonságos rendszerindítás engedélyezve van, amely biztosítja, hogy az eszköz csak a Microsoft által biztosított megbízható szoftver használatával induljon el.
- Futtatja a Windows Defender Application Controlt (WDAC). A WDAC lehetővé teszi, hogy csak a kód-integritási házirendekben meghatározott megbízható alkalmazásokat futtasson.
- A tartalmaz egy platformmegbízhatósági modul (TPM), amely hardveres, biztonsággal kapcsolatos funkciókat hajt végre. A TPM pontosan kezeli és védi az eszközön megőrizni kívánt titkokat és adatokat.
- Csak a szükséges portok vannak megnyitva az eszközön, és az összes többi port le van tiltva. További információkért tekintse [meg az eszközre vonatkozó portokra vonatkozó követelmények](azure-stack-edge-pro-r-system-requirements.md) listáját.
- A rendszer naplózza az eszköz hardverének és szoftverének összes hozzáférését. 
    - Az eszköz szoftvere esetében az alapértelmezett tűzfal-naplók gyűjtése történik az eszközről érkező bejövő és kimenő forgalomhoz. Ezek a naplók a támogatási csomagban vannak csomagolva.
    - Az eszköz hardvere esetében a rendszer az eszközön lévő összes olyan eseményt naplózza az eszközön, mint az eszköz vázának megnyitása és bezárása.

    A hardveres és szoftveres behatolási eseményeket tartalmazó naplókról és a naplók beszerzéséről a [speciális biztonsági naplók összegyűjtése](azure-stack-edge-gpu-troubleshoot.md)című témakörben olvashat bővebben.


### <a name="protect-the-device-via-activation-key"></a>Az eszköz védetté aktiválása az aktiválási kulcs használatával

Csak a jogosult Azure Stack Edge Pro R vagy Azure Stack Edge mini R-eszköz csatlakozhat az Azure-előfizetésében létrehozott Azure Stack Edge szolgáltatáshoz. Az eszköz engedélyezéséhez aktiválnia kell az eszközt az Azure Stack Edge szolgáltatással.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

További információ: [aktiválási kulcs beszerzése](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Az eszköz jelszavas védelemmel való ellátása

A jelszavak biztosítják, hogy csak a jogosult felhasználók férjenek hozzá az adataihoz. Azure Stack Edge Pro R-eszközök zárolt állapotban indulnak.

A következőket teheti:

- Kapcsolódjon az eszköz helyi webes FELÜLETéhez egy böngészőben, majd adjon meg egy jelszót az eszközre való bejelentkezéshez.
- Távolról kapcsolódhat az eszköz PowerShell-felületéhez HTTP-n keresztül. A távfelügyelet alapértelmezés szerint be van kapcsolva. A távfelügyelet úgy is konfigurálva van, hogy csak annyi felügyeletet (JEA) használjon, amellyel korlátozhatja a felhasználók által elvégezhető műveleteket. Ezután megadhatja az eszköz jelszavát az eszközre való bejelentkezéshez. További információ: [távoli kapcsolódás az eszközhöz](azure-stack-edge-gpu-connect-powershell-interface.md).
- Az eszköz helyi peremhálózati felhasználója korlátozott hozzáféréssel rendelkezik az eszközhöz a kezdeti konfiguráláshoz és a hibaelhárításhoz. Az eszközön, az adatátvitelen és a tárolón futó számítási munkaterhelések mind elérhetők az Azure nyilvános vagy kormányzati portálján a felhőben található erőforráshoz.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- A [jelszó megváltoztatásához](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password)használja a helyi webes felhasználói felületet. Ha megváltoztatja a jelszót, ügyeljen arra, hogy értesítse az összes távelérési felhasználót, hogy ne legyenek a bejelentkezéssel kapcsolatos problémák.

### <a name="establish-trust-with-the-device-via-certificates"></a>Megbízhatósági kapcsolat létrehozása az eszközzel tanúsítványok használatával

Azure Stack Edge robusztus eszköz lehetővé teszi saját tanúsítványok használatát, és telepíti azokat, amelyeket az összes nyilvános végponthoz használni fog. További információért lépjen [a tanúsítvány feltöltése](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)elemre. Az eszközön telepíthető összes tanúsítvány listáját a [tanúsítványok kezelése az eszközön című oldalon](azure-stack-edge-gpu-manage-certificates.md)tekintheti meg.

- Amikor számítási lehetőséget konfigurál az eszközön, létrejön egy IoT-eszköz és egy IoT Edge eszköz. Ezeket az eszközöket a rendszer automatikusan hozzárendeli a szimmetrikus hozzáférési kulcsokhoz. Ajánlott biztonsági eljárásként ezeket a kulcsokat rendszeresen elforgatják a IoT Hub szolgáltatáson keresztül.

## <a name="protect-your-data"></a>Az adatok védelme

Ez a szakasz azokat a biztonsági szolgáltatásokat ismerteti, amelyek a továbbítást és a tárolt információkat védik.

### <a name="protect-data-at-rest"></a>Az inaktív adatok védelme

Az eszközön tárolt összes adat duplán titkosított, az adatokhoz való hozzáférés ellenőrzése és az eszköz inaktiválása után az adatok biztonságosan törlődnek az adatlemezekről.

#### <a name="double-encryption-of-data"></a>Az adatgyűjtés kettős titkosítása

A lemezek adatait két titkosítási réteg védi:

- Az első titkosítási réteg a BitLocker XTS-AES 256 bites titkosítás az adatköteteken.
- A második réteg a beépített titkosítással rendelkező merevlemezek.
- Az operációs rendszer kötetének egyetlen titkosítási rétege a BitLocker.

> [!NOTE]
> Az operációsrendszer-lemezen egy rétegbeli BitLocker XTS-AES-256 szoftveres titkosítás található.

Ha az eszköz aktiválva van, a rendszer arra kéri, hogy mentsen egy olyan kulcsot tartalmazó fájlt, amely olyan helyreállítási kulcsokat tartalmaz, amelyek segítenek helyreállítani az eszközön lévő adatokat, ha az eszköz nem indul el. A fájlban két kulcs található:

- Az egyik kulcs helyreállítja az eszköz konfigurációját az operációsrendszer-köteteken.
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- A második kulcs feloldja a hardveres titkosítást az adatlemezeken.

> [!IMPORTANT]
> Mentse a kulcsfájl biztonságos helyen kívül az eszközön. Ha az eszköz nem indul el, és nem rendelkezik a kulccsal, az adatvesztést eredményezhet.

- Bizonyos helyreállítási forgatókönyvek megkérik a mentett kulcsfájl megadására. 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>Korlátozott hozzáférés az adatkezeléshez

A megosztásokban és a Storage-fiókokban tárolt információhoz való hozzáférés korlátozott.

- A megosztási adatokhoz hozzáférő SMB-ügyfeleknek a megosztáshoz társított felhasználói hitelesítő adatokra van szükségük. Ezek a hitelesítő adatok a megosztás létrehozásakor vannak meghatározva.
- A megosztáshoz hozzáférő NFS-ügyfeleknek explicit módon kell megadniuk az IP-címüket a megosztás létrehozásakor.
- Az eszközön létrehozott Edge Storage-fiókok helyiek, és az adatlemezek titkosítása védi őket. Azokat az Azure Storage-fiókokat, amelyeket ezek a peremhálózat-tárolási fiókok leképeznek, az előfizetések és az Edge Storage-fiókhoz társított 2 512 bites Storage-hozzáférési kulcsok védik (ezek a kulcsok különböznek az Azure Storage-fiókokhoz társított kulcstól). További információ: [az adatok védelme a Storage-fiókokban](#protect-data-in-storage-accounts).
- A BitLocker XTS-AES 256 bites titkosítás a helyi adatvédelemre szolgál.

#### <a name="secure-data-erasure"></a>Biztonságos adattörlés

Ha az eszközön a merevlemez alaphelyzetbe áll, biztonságos törlés történik az eszközön. A biztonságos törlés a NIST SP 800-88r1 kiürítésével végzi el az adatok törlését a lemezeken.

### <a name="protect-data-in-flight"></a>A repülésben tárolt adatvédelem

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>A Storage-fiókok adatainak védelme

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- Forgassa el és [szinkronizálja rendszeresen a Storage-fiók kulcsait](azure-stack-edge-j-series-manage-storage-accounts.md) , hogy megvédje a Storage-fiókját a jogosulatlan felhasználóktól.

## <a name="manage-personal-information"></a>Személyes adatok kezelése

Az Azure Stack Edge szolgáltatás a következő helyzetekben gyűjt személyes adatokat:

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

A megosztásokat elérő vagy törlő felhasználók listájának megtekintéséhez kövesse a [megosztások kezelése az Azure stack Edge](azure-stack-edge-j-series-manage-shares.md)-ben című témakör lépéseit.

További információkért tekintse át a Microsoft adatvédelmi szabályzatát a [megbízhatósági központban](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Következő lépések

[Az Azure Stack Edge Pro R-eszköz üzembe helyezése](azure-stack-edge-gpu-deploy-prep.md)
