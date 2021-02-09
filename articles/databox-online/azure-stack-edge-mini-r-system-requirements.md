---
title: Microsoft Azure Stack Edge mini R rendszerkövetelményei | Microsoft Docs
description: Az Azure Stack Edge mini R szoftver-és hálózatkezelési követelményeinek megismerése
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: 6836777c761fa2cce482cb000b6c86919e6b5613
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834068"
---
# <a name="azure-stack-edge-mini-r-system-requirements"></a>Azure Stack Edge mini R rendszerkövetelményei

Ez a cikk a Microsoft Azure Stack Edge mini R-megoldásához és az Azure Stack Edge mini R-hez csatlakozó ügyfelekhez szükséges fontos rendszerkövetelményeket ismerteti. Javasoljuk, hogy figyelmesen tekintse át az információkat az Azure Stack Edge mini R üzembe helyezése előtt. Az üzembe helyezés és az azt követő művelet során szükség szerint visszatérhet ezekre az adatokra.

A Azure Stack Edge mini R rendszerkövetelményei a következők:

- A **gazdagépek szoftveres követelményei** – a támogatott platformokat, a helyi konfiguráció felhasználói felületének böngészőit, az SMB-ügyfeleket, valamint az eszközt elérő ügyfelekre vonatkozó további követelményeket ismerteti.
- **Az eszköz hálózati követelményei** – információt nyújt a fizikai eszköz működésével kapcsolatos hálózati követelményekről.

## <a name="supported-os-for-clients-connected-to-device"></a>Az eszközhöz csatlakoztatott ügyfelek támogatott operációs rendszere

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Az eszközt elérő ügyfelek által támogatott protokollok

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-edge-storage-accounts"></a>Támogatott Edge Storage-fiókok

A következő Edge Storage-fiókok támogatottak az eszköz REST-felületével. Az Edge Storage-fiókok az eszközön jönnek létre. További információ: [Edge Storage-fiókok](azure-stack-edge-j-series-manage-storage-accounts.md#about-edge-storage-accounts)

|Típus  |A(z)  |Megjegyzések  |
|---------|---------|---------|
|Standard     |GPv1: blob blokkolása         |         |


* Az oldal blobok és Azure Filesek jelenleg nem támogatottak.

## <a name="supported-local-azure-resource-manager-storage-accounts"></a>Támogatott helyi Azure Resource Manager Storage-fiókok

Ezek a Storage-fiókok a helyi Azure Resource Managerhoz való csatlakozáskor az eszköz helyi API-kon keresztül érhetők el. A következő Storage-fiókok támogatottak:

|Típus  |A(z)  |Megjegyzések  |
|---------|---------|---------|
|Standard     |GPv1: blob blokkolása, oldal Blobja         | Az SKU típusa Standard_LRS        |
|Prémium   |GPv1: blob blokkolása, oldal Blobja         |Az SKU típusa Premium_LRS         |


## <a name="supported-storage-types"></a>Támogatott tárolótípusok

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>A helyi webes felhasználói felület által támogatott böngészők

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Hálózati portokra vonatkozó követelmények

### <a name="port-requirements-for-azure-stack-edge-mini-r"></a>A Azure Stack Edge mini R-re vonatkozó portokra vonatkozó követelmények

A következő táblázat felsorolja azokat a portokat, amelyeket meg kell nyitni a tűzfalon az SMB-, felhő-vagy felügyeleti forgalom engedélyezéséhez. A (z)  vagy a *bejövő* tábla a bejövő ügyfél által az eszközhöz való hozzáférést kérő irányt jelöli. A *kimenet* vagy a *kimenő* érték arra utal, hogy az Azure stack Edge mini R-eszköz az üzembe helyezésen túl az adatokat, például az interneten keresztül küldi el az adatokat.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>A IoT Edge portjának követelményei

Azure IoT Edge lehetővé teszi a kimenő kommunikációt egy helyszíni peremhálózati eszközről az Azure-felhőbe a támogatott IoT Hub protokollok használatával. A bejövő kommunikációra csak bizonyos esetekben van szükség, amikor az Azure IoT Hub üzeneteket kell leküldenie az Azure IoT Edge eszköznek (például felhőből az eszközre).

Használja a következő táblázatot a port konfigurálásához a Azure IoT Edge futtatókörnyezetet futtató kiszolgálókon:

| Port nem. | Be vagy ki | Port hatóköre | Kötelező | Útmutató |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Ki       | WAN        | Igen      | A kimenő Megnyitás IoT Edge kiépítés esetén. Ez a konfiguráció kézi parancsfájlok vagy Azure IoT Device kiépítési szolgáltatás (DPS) használata esetén szükséges.|

A teljes információ: a [tűzfal és a port konfigurációs szabályai IoT Edge központi telepítéshez](../iot-edge/troubleshoot.md).

## <a name="url-patterns-for-firewall-rules"></a>Tűzfalszabályok URL-mintái

A hálózati rendszergazdák gyakran konfigurálhatják a speciális tűzfalszabályok alapján a bejövő és a kimenő forgalom szűrésére szolgáló URL-mintákat. A Azure Stack Edge mini R-eszköz és a szolgáltatás más Microsoft-alkalmazásokkal, például a Azure Service Bus, Azure Active Directory Access Control, a Storage-fiókokkal és a Microsoft Update-kiszolgálókkal függ. Az ezekhez az alkalmazásokhoz társított URL-mintákat a tűzfalszabályok konfigurálására lehet használni. Fontos megérteni, hogy az alkalmazásokhoz társított URL-minták megváltoztathatók. Ezekhez a változásokhoz a hálózati rendszergazdának a Azure Stack Edge mini R-re vonatkozó tűzfalszabályok figyelésére és frissítésére van szükség.

Javasoljuk, hogy a legtöbb esetben az Azure Stack Edge mini R fix IP-címek alapján állítsa be a tűzfal szabályait a kimenő forgalomra vonatkozóan. Az alábbi információk segítségével azonban megadhatja a biztonságos környezetek létrehozásához szükséges speciális tűzfalszabályok beállításait is.

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

A megoldás fejlesztésével és tesztelésével gondoskodhat arról, hogy elegendő kapacitás legyen a Azure Stack Edge mini R-eszközén, és az eszköz optimális teljesítményét kapja meg.

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

- A Azure Portalben elérhető számítási mérőszámok. Nyissa meg az Azure Stack Edge-erőforrást, és válassza a **figyelés > metrikák** lehetőséget. Tekintse meg az **Edge számítási memória használatának** és a **peremhálózati számítási kapacitásának százalékos arányát** , hogy megismerje a rendelkezésre álló erőforrásokat és a felhasznált erőforrásokat.
- Az eszköz PowerShell-felületén keresztül elérhető figyelési parancsok, például:

    - `dkr` statisztika a tároló (k) erőforrás-használati statisztikáinak élő streambe való beszerzéséhez. A parancs támogatja a PROCESSZORt, a memóriahasználat, a memória korlátját és a hálózati IO-metrikákat.
    - `dkr system df` a felhasznált lemezterület mennyiségére vonatkozó információk lekérése. 
    - `dkr image [prune]` a fel nem használt rendszerképek tisztítása és a szabad terület felszabadítása.
    - `dkr ps --size` egy futó tároló hozzávetőleges méretének megtekintéséhez. 

    Az elérhető parancsokkal kapcsolatos további információkért keresse fel a [ Kubernetes-problémák hibakeresése](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)című témakört.

Végezetül ellenőrizze, hogy az adatkészleten érvényesíti-e a megoldást, és az éles környezetben való üzembe helyezés előtt számszerűsítse a teljesítményt Azure Stack Edge mini R-ben.

## <a name="next-step"></a>Következő lépés

- [Az Azure Stack Edge mini R üzembe helyezése](azure-stack-edge-placeholder.md)
