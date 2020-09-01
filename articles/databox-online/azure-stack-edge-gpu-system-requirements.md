---
title: Microsoft Azure Stack Edge rendszerre vonatkozó követelmények | Microsoft Docs
description: Az Azure Stack Edge szoftver-és hálózatkezelési követelményeinek megismerése
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 7754c57563ec5acb9028c2ace217f318fea5e959
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256273"
---
# <a name="system-requirements-for-azure-stack-edge-with-gpu"></a>A GPU-val Azure Stack Edge rendszerkövetelményei 

Ez a cikk ismerteti az Microsoft Azure Stack Edge-megoldás és az Azure Stack Edge-hez csatlakozó ügyfelek fontos rendszerkövetelményeit. Javasoljuk, hogy figyelmesen tekintse át az információkat az Azure Stack Edge üzembe helyezése előtt. Az üzembe helyezés és az azt követő művelet során szükség szerint visszatérhet ezekre az adatokra.

Az Azure Stack Edge rendszerkövetelményei a következők:

- A **gazdagépek szoftveres követelményei** – a támogatott platformokat, a helyi konfiguráció felhasználói felületének böngészőit, az SMB-ügyfeleket, valamint az eszközt elérő ügyfelekre vonatkozó további követelményeket ismerteti.
- **Az eszköz hálózati követelményei** – információt nyújt a fizikai eszköz működésével kapcsolatos hálózati követelményekről.

## <a name="supported-os-for-clients-connected-to-device"></a>Az eszközhöz csatlakoztatott ügyfelek támogatott operációs rendszere

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Az eszközt elérő ügyfelek által támogatott protokollok

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-tiered-storage-accounts"></a>Támogatott többplatformos Storage-fiókok

Ha Azure Stack felügyeli, a következő többplatformos tárolási fiókok támogatottak az SMB/NFS/REST illesztőfelületek esetében.

|Típus  |Tárfiók  |Megjegyzések  |
|---------|---------|---------|
|Standard     |GPv1: blob blokkolása         |         |
|    |  BLOB Storage: blob blokkolása       | Csak a NAS számára támogatott     |

* Az oldal blobok és Azure Files jelenleg nem támogatottak a Azure Stackban.
* * A gyakori és a rideg szint nem létezik a Azure Stackban. Az adatok feltöltése után a Azure PowerShell használatával helyezheti át az adatok archiválási szintjére. Részletes útmutatásért lépjen a következőre: a [Azure PowerShell használata a blob réteg beállításához]()

## <a name="supported-storage-types"></a>Támogatott tárolótípusok

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>A helyi webes felhasználói felület által támogatott böngészők

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Hálózati portokra vonatkozó követelmények

### <a name="port-requirements-for-azure-stack-edge"></a>A Azure Stack Edge portjának követelményei

A következő táblázat felsorolja azokat a portokat, amelyeket meg kell nyitni a tűzfalon az SMB-, felhő-vagy felügyeleti forgalom engedélyezéséhez. A (z) *in* vagy a *bejövő* tábla a bejövő ügyfél által az eszközhöz való hozzáférést kérő irányt jelöli. A *kimenő vagy kimenő* *állapot arra utal* , hogy az Azure stack Edge-eszköz külsőleg, az üzembe helyezésen túl, például az internet felé irányuló adatokat küld.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>A IoT Edge portjának követelményei

Azure IoT Edge lehetővé teszi a kimenő kommunikációt egy helyszíni peremhálózati eszközről az Azure-felhőbe a támogatott IoT Hub protokollok használatával. A bejövő kommunikációra csak bizonyos esetekben van szükség, amikor az Azure IoT Hub üzeneteket kell leküldenie az Azure IoT Edge eszköznek (például felhőből az eszközre).

Használja a következő táblázatot a port konfigurálásához a Azure IoT Edge futtatókörnyezetet futtató kiszolgálókon:

| Port nem. | Be vagy ki | Port hatóköre | Kötelező | Útmutató |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Ki       | WAN        | Igen      | A kimenő Megnyitás IoT Edge kiépítés esetén. Ez a konfiguráció kézi parancsfájlok vagy Azure IoT Device kiépítési szolgáltatás (DPS) használata esetén szükséges.|

A teljes információ: a [tűzfal és a port konfigurációs szabályai IoT Edge központi telepítéshez](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Tűzfalszabályok URL-mintái

A hálózati rendszergazdák gyakran konfigurálhatják a speciális tűzfalszabályok alapján a bejövő és a kimenő forgalom szűrésére szolgáló URL-mintákat. Az Azure Stack Edge-eszköz és a szolgáltatás más Microsoft-alkalmazásokkal (például Azure Service Bus, Azure Active Directory Access Control, Storage-fiókokkal és Microsoft Update-kiszolgálókkal függ. Az ezekhez az alkalmazásokhoz társított URL-mintákat a tűzfalszabályok konfigurálására lehet használni. Fontos megérteni, hogy az alkalmazásokhoz társított URL-minták megváltoztathatók. Ezekhez a változásokhoz a hálózati rendszergazdának kell megfigyelnie és frissítenie a tűzfal szabályait az Azure Stack Edge-ben és amikor szükséges.

Javasoljuk, hogy a legtöbb esetben a Azure Stack Edge rögzített IP-címei alapján állítsa be a tűzfal szabályait a kimenő forgalomra vonatkozóan. Az alábbi információk segítségével azonban megadhatja a biztonságos környezetek létrehozásához szükséges speciális tűzfalszabályok beállításait is.

> [!NOTE]
> - Az eszköz (forrás) IP-címeit mindig az összes felhőalapú hálózati adapterre kell beállítani.
> - A cél IP-címeket az [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653)értékre kell beállítani.

### <a name="url-patterns-for-gateway-feature"></a>Az átjáró szolgáltatás URL-mintái

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>A számítási funkció URL-mintái

| URL-minta                      | Összetevő vagy funkció                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.microsoft.com<br></br>https:// \* . CDN.mscr.IO | Microsoft Container Registry (kötelező)               |
| https:// \* . azurecr.IO                     | Személyes és harmadik féltől származó tároló-nyilvántartások (opcionális) | 
| https:// \* . Azure-Devices.net              | IoT Hub hozzáférés (kötelező)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>A Azure Government átjárójának URL-mintái

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>A számítási feladatokhoz tartozó URL-minták Azure Government

| URL-minta                      | Összetevő vagy funkció                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.microsoft.com<br></br>https:// \* . CDN.mscr.com | Microsoft Container Registry (kötelező)               |
| https:// \* . Azure-Devices.us              | IoT Hub hozzáférés (kötelező)           |
| https:// \* . azurecr.us                    | Személyes és harmadik féltől származó tároló-nyilvántartások (opcionális) | 

## <a name="internet-bandwidth"></a>Internetes sávszélesség

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Számítási méretezési szempontok

A megoldás fejlesztésével és tesztelésével gondoskodhat arról, hogy elegendő kapacitás legyen a Azure Stack Edge-eszközön, és hogy az eszköz optimális teljesítményű legyen.

Az alábbi tényezőket érdemes figyelembe venni:

- **Tárolók sajátosságai** – gondolja át az alábbiakat.

    - Mi a tároló lábnyoma? Mennyi memóriát, tárterületet és CPU-t használ a tároló?
    - Hány tároló van a munkaterhelésben? Számos könnyű tárolót használhat, és néhány erőforrás-igényes is.
    - Milyen erőforrásokra vannak kiosztva ezek a tárolók, illetve milyen erőforrásokat használnak (a lábnyom)?
    - Hány réteget osztanak meg a tárolók? A tároló-lemezképek egy köteg rétegbe rendezett fájlok. A tároló képénél határozza meg, hogy hány réteget és a megfelelő méreteket kell kiszámítani az erőforrás-felhasználás kiszámításához.
    - Vannak fel nem használt tárolók? Egy leállított tároló továbbra is elegendő lemezterületet igényel.
    - Milyen nyelven íródott a tárolók?
- **A feldolgozott** adatmennyiség mérete – mennyi az adattárolók feldolgozása? Az adatmennyiség lemezterületet vagy az adatmennyiséget fogja feldolgozni a memóriában?
- **Várt teljesítmény** – Mik a megoldás kívánt teljesítmény-jellemzői? 

A megoldás teljesítményének megismeréséhez és pontosításához használhatja a következőt:

- A Azure Portalben elérhető számítási mérőszámok. Nyissa meg az Azure Stack Edge-erőforrást, és válassza a **figyelés > metrikák**lehetőséget. Tekintse meg az **Edge számítási memória használatának** és a **peremhálózati számítási kapacitásának százalékos arányát** , hogy megismerje a rendelkezésre álló erőforrásokat és a felhasznált erőforrásokat.
- A számítási modulok figyeléséhez és hibaelhárításához lépjen a [Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)hibaelhárítása című lépésre.

Végezetül ellenőrizze, hogy az adatkészleten érvényesíti-e a megoldást, és az éles környezetben való üzembe helyezés előtt számszerűsítse Azure Stack Edge teljesítményét.

## <a name="next-step"></a>Következő lépés

- [Az Azure Stack Edge üzembe helyezése](azure-stack-edge-gpu-deploy-prep.md)
