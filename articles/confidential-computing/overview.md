---
title: Az Azure bizalmas számítástechnika áttekintése
description: Az Azure bizalmas (ACC) számítástechnika áttekintése
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: c3ef6a764123f52583f081a3b152651b9bb8b8b7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554190"
---
# <a name="confidential-computing-on-azure"></a>Bizalmas számítástechnika az Azure-ban

Az Azure bizalmas számítástechnikai szolgáltatása lehetővé teszi a bizalmas adatok elkülönítését a felhőben történő feldolgozás során. Számos iparág bizalmas számítástechnikai szolgáltatásokkal védi az adataikat bizalmas számítástechnikai szolgáltatásokkal:

- Biztonságos pénzügyi adatkezelés
- A betegek adatainak megvédése
- Gépi tanulási folyamatok futtatása bizalmas adatokon
- Algoritmusok végrehajtása több forrásból származó titkosított adatkészleteken


## <a name="overview"></a>Áttekintés
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Tudjuk, hogy fontos a Felhőbeli adatvédelme. Halljuk az Ön problémáit. Íme néhány kérdés, miszerint ügyfeleinknek lehet a felhőbe áthelyezniük a bizalmas számítási feladatokat: 

- Hogyan győződjön meg arról, hogy a Microsoft nem fér hozzá a nem titkosított adatforgalomhoz?
- Hogyan a vállalaton belül a rendszerjogosultságú rendszergazdák biztonsági fenyegetésének megelőzése?
- Milyen módon lehet megakadályozni, hogy harmadik felek hozzáférjenek a bizalmas ügyféladatok eléréséhez?

A Microsoft Azure segítségével csökkentheti a támadási felületet, így erősebb adatvédelmet nyerhet. Az Azure már számos olyan eszközt kínál, amelyekkel a különböző modelleken keresztül biztosíthatja az [**adatok**](../security/fundamentals/encryption-atrest.md) védelmét, például az ügyféloldali titkosítással és a kiszolgálóoldali titkosítással. Az Azure emellett a biztonságos protokollok, például a TLS [**és a HTTPS protokollon keresztül is**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) titkosítja az adatátviteli mechanizmusokat. Ez az oldal az adattitkosítás harmadik szakaszát mutatja be – a **használatban** lévő adattitkosítást.

## <a name="introduction-to-confidential-computing"></a>A bizalmas számítástechnika bemutatása 

A bizalmas számítástechnika a bizalmas számítástechnikai [konzorcium](https://confidentialcomputing.io/) (CCC) által meghatározott iparági kifejezés, amely a bizalmas számítástechnika bevezetésének meghatározására és felgyorsítására dedikált alap. A CCC a következőképpen határozza meg a bizalmas számítástechnikai feladatokat: a használatban lévő adatok védelme egy hardveres megbízható végrehajtási környezetben (TEE) végzett számítások végrehajtásával.

A TEE olyan környezet, amely csak a jóváhagyott kód végrehajtását kényszeríti ki. A TEE-ben lévő összes adattal nem lehet beolvasni vagy módosítani az adott környezeten kívüli kódokat. 

### <a name="lessen-the-need-for-trust"></a>A bizalmi kapcsolat szükségességének csökkentése
A munkaterhelések Felhőbeli futtatása megbízhatóságot igényel. Ezt a megbízhatóságot a különböző szolgáltatók számára biztosíthatja, amelyek lehetővé teszik az alkalmazás különböző összetevőit.


**Alkalmazás-szoftvergyártók**: a helyszíni alkalmazások központi telepítésével, nyílt forráskódú vagy házon belüli alkalmazás létrehozásával megbízza a szoftvert.

**Hardvergyártók**: a helyszíni hardver vagy a házon belüli hardver használatával bízza a hardvert. 

**Infrastruktúra-szolgáltatók**: megbízható felhőalapú szolgáltatók vagy saját helyszíni adatközpontok kezelése.


Az Azure bizalmas számítástechnikai szolgáltatásával könnyebben megbízhat a Felhőbeli szolgáltatóban, így a számítási felhő-infrastruktúra különböző szempontjain való megbízhatóságra van szükség. Az Azure bizalmas számítástechnikai szolgáltatásával a gazdagép operációs rendszerének kernele, a hypervisor, a VM-rendszergazda és a gazdagép rendszergazdája is megbízik.

### <a name="reducing-the-attack-surface"></a>A támadási felület csökkentése
A megbízható számítástechnikai bázis (TCB) egy biztonságos környezetet biztosító rendszer hardver-, belső vezérlőprogram-és szoftver-összetevőire vonatkozik. A TCB-ben található összetevők "kritikus" állapotúnak tekintendők. Ha a TCB egyik összetevője biztonságban van, a teljes rendszer biztonsága veszélyben lehet. 

Az alsó TCB nagyobb biztonságot jelent. A különböző sebezhetőségek, kártevők, támadások és rosszindulatú személyek számára kevésbé sok a kockázata. Az Azure bizalmas számítástechnika célja, hogy pólók használatával csökkentse a TCB-t a Felhőbeli munkaterhelésekhez. A pólók csökkentik a TCB-t a megbízható futásidejű bináris fájlokat, kódokat és könyvtárakat. Ha Azure-infrastruktúrát és-szolgáltatásokat használ bizalmas számítástechnikai szolgáltatásokhoz, az összes Microsoftot törölheti a TCB-ből.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Az Azure használata felhőalapú, bizalmas számítástechnikai rendszerekhez <a id="cc-on-azure"></a>

Az Azure bizalmas számítástechnikai részlege lehetővé teszi, hogy a virtualizált környezetekben a bizalmas számítástechnikai funkciókat használja. Mostantól eszközöket, szoftvereket és felhőalapú infrastruktúrát is használhat a biztonságos hardverre való kiépítéshez.  

**Jogosulatlan hozzáférés megakadályozása**: bizalmas adatok futtatása a felhőben. Bízza az Azure-t, hogy a lehető legjobb adatvédelmet biztosítson, kevés a változás a mai naptól.

**Jogszabályi megfelelőség**: Migrálás a felhőbe és az adatok teljes körű ellenőrzése a személyes adatok védelméhez és a szervezeti IP-címek védelméhez szükséges kormányzati rendelkezések teljesítése érdekében.

**Biztonságos és nem megbízható együttműködés**: az iparági szintű munkamennyiséggel kapcsolatos problémák megoldása azáltal, hogy az adatvédelmet több szervezet, akár a versenytársak között, a széles körű adatelemzések és a mélyebb elemzések feloldása érdekében.

**Elkülönített feldolgozás**: egy új hullámot kínál, amely megszünteti a személyes és a vak feldolgozással kapcsolatos felelősséget. A szolgáltató nem tudja lekérni a felhasználói adatkéréseket. 

## <a name="get-started"></a>Első lépések
### <a name="azure-compute"></a>Azure Compute
Hozzon létre alkalmazásokat az Azure-ban bizalmas számítási IaaS-ajánlatokkal.
- Virtual Machines (VM): [DCsv2 sorozat](confidential-computing-enclaves.md)
- Azure Kubernetes (ak): [bizalmas tárolók](confidential-nodes-aks-overview.md) összehangolása

### <a name="azure-security"></a>Azure Security 
Ellenőrizze, hogy a munkaterhelések biztonságosak-e az ellenőrzési módszerek és a hardveres kulcsok felügyelete révén. 
- Igazolás: [Microsoft Azure igazolás (előzetes verzió)](../attestation/overview.md)
- Kulcskezelő: felügyelt HSM (előzetes verzió)

### <a name="develop"></a>Fejlesztés
A bizalmas következtetési keretrendszer használatával megkezdheti a enklávé-kompatibilis alkalmazások fejlesztését és a bizalmas algoritmusok üzembe helyezését.
- Alkalmazások írása DCsv2 virtuális gépeken való futtatásra: [Open-ENKLÁVÉ SDK](https://github.com/openenclave/openenclave)
- Bizalmas ML-modellek a ONNX-futtatókörnyezetben: [bizalmas következtetés (bétaverzió)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>Következő lépések

Helyezzen üzembe egy DCsv2-Series virtuális gépet, és telepítse az OE SDK-t.

> [!div class="nextstepaction"]
> [Bizalmas számítástechnikai virtuális gép üzembe helyezése az Azure Marketplace-en](quick-create-marketplace.md)