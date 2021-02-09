---
title: Microsoft Azure Stack Edge Pro R rendszerre vonatkozó követelmények | Microsoft Docs
description: Ismerkedjen meg az Azure Stack Edge Pro R szoftverrel és hálózati követelményeivel
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: ef81494b6c93f2941cc41b367e566332d6bc7cc9
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831338"
---
# <a name="azure-stack-edge-pro-r-system-requirements"></a>Azure Stack Edge Pro R rendszerkövetelményei

Ez a cikk ismerteti az Azure Stack Edge Pro R-megoldás és az Azure Stack Edge Pro R-hez csatlakozó ügyfelek fontos rendszerkövetelményeit. Javasoljuk, hogy figyelmesen tekintse át az információkat, mielőtt üzembe helyezi a Azure Stack Edge Pro R-t. Az üzembe helyezés és az azt követő művelet során szükség szerint visszatérhet ezekre az adatokra.

Az Azure Stack Edge Pro R rendszerkövetelményei a következők:

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

|Típus  |A(z)  |Megjegyzések  |
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

### <a name="port-requirements-for-azure-stack-edge-pro-r"></a>A portra vonatkozó követelmények Azure Stack Edge Pro R-hez

A következő táblázat felsorolja azokat a portokat, amelyeket meg kell nyitni a tűzfalon az SMB-, felhő-vagy felügyeleti forgalom engedélyezéséhez. A (z)  vagy a *bejövő* tábla a bejövő ügyfél által az eszközhöz való hozzáférést kérő irányt jelöli. A *kimenő vagy kimeneti* állapot arra utal, hogy az Azure stack Edge Pro *R-eszköz* külsőleg, az üzembe helyezésen túl, például az interneten keresztül küldi az adatokat.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>A IoT Edge portjának követelményei

Azure IoT Edge lehetővé teszi a kimenő kommunikációt egy helyszíni peremhálózati eszközről az Azure-felhőbe a támogatott IoT Hub protokollok használatával. A bejövő kommunikációra csak bizonyos esetekben van szükség, amikor az Azure IoT Hub üzeneteket kell leküldenie az Azure IoT Edge eszköznek (például felhőből az eszközre).

Használja a következő táblázatot a port konfigurálásához a Azure IoT Edge futtatókörnyezetet futtató kiszolgálókon:

| Port nem. | Be vagy ki | Port hatóköre | Kötelező | Útmutató |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Ki       | WAN        | Igen      | A kimenő Megnyitás IoT Edge kiépítés esetén. Ez a konfiguráció kézi parancsfájlok vagy Azure IoT Device kiépítési szolgáltatás (DPS) használata esetén szükséges.|

A teljes információ: a [tűzfal és a port konfigurációs szabályai IoT Edge központi telepítéshez](../iot-edge/troubleshoot.md).

## <a name="url-patterns-for-firewall-rules"></a>Tűzfalszabályok URL-mintái

A hálózati rendszergazdák gyakran konfigurálhatják a speciális tűzfalszabályok alapján a bejövő és a kimenő forgalom szűrésére szolgáló URL-mintákat. Az Azure Stack Edge Pro R-eszköz és a szolgáltatás más Microsoft-alkalmazásokkal, például a Azure Service Bus, Azure Active Directory Access Control, a Storage-fiókokkal és a Microsoft Update-kiszolgálókkal függ. Az ezekhez az alkalmazásokhoz társított URL-mintákat a tűzfalszabályok konfigurálására lehet használni. Fontos megérteni, hogy az alkalmazásokhoz társított URL-minták megváltoztathatók. Ezekhez a változásokhoz a hálózati rendszergazdának a Azure Stack Edge Pro R-re vonatkozó tűzfalszabályok figyelésére és frissítésére van szükség.

Javasoljuk, hogy a legtöbb esetben a Azure Stack Edge Pro R fix IP-címek alapján állítsa be a tűzfalszabályok a kimenő forgalomra vonatkozóan. Az alábbi információk segítségével azonban megadhatja a biztonságos környezetek létrehozásához szükséges speciális tűzfalszabályok beállításait is.

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

A megoldás fejlesztésével és tesztelésével gondoskodhat arról, hogy elegendő kapacitás legyen a Azure Stack Edge Pro R-eszközén, és az eszköz optimális teljesítményét kapja meg.

Az alábbi tényezőket érdemes figyelembe venni:

- **Tárolók sajátosságai** – gondolja át az alábbiakat.

    - Hány tároló van a munkaterhelésben? Számos könnyű tárolót használhat, és néhány erőforrás-igényes is.
    - Milyen erőforrásokra vannak kiosztva ezek a tárolók, illetve milyen erőforrásokat használnak?
    - Hány réteget osztanak meg a tárolók?
    - Vannak fel nem használt tárolók? Egy leállított tároló továbbra is elegendő lemezterületet igényel.
    - Milyen nyelven íródott a tárolók?
- **A feldolgozott** adatmennyiség mérete – mennyi az adattárolók feldolgozása? Az adatmennyiség lemezterületet vagy az adatmennyiséget fogja feldolgozni a memóriában?
- **Várt teljesítmény** – Mik a megoldás kívánt teljesítmény-jellemzői? 

A megoldás teljesítményének megismeréséhez és pontosításához használhatja a következőt:

- A Azure Portalben elérhető számítási mérőszámok. Nyissa meg az Azure Stack Edge Pro R-erőforrást, majd lépjen a **figyelés > metrikák** oldalra. Tekintse meg az **Edge számítási memória használatának** és a **peremhálózati számítási kapacitásának százalékos arányát** , hogy megismerje a rendelkezésre álló erőforrásokat és a felhasznált erőforrásokat.
- Az [eszköz PowerShell-felületén keresztül elérhető figyelési parancsok](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).

Végezetül ellenőrizze, hogy az adatkészleten érvényesíti-e a megoldást, és az éles környezetben való üzembe helyezés előtt számszerűsítse Azure Stack Edge Pro R teljesítményét.

## <a name="next-step"></a>Következő lépés

- [Az Azure Stack Edge Pro R üzembe helyezése](azure-stack-edge-pro-r-deploy-prep.md)