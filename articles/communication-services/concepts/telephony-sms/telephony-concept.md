---
title: PSTN telefonos integrációs fogalmak az Azure kommunikációs szolgáltatásokhoz
description: Megtudhatja, hogyan integrálhatja az Azure kommunikációs szolgáltatások alkalmazásában a PSTN-hívások funkcióit.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d5e4920dcc422e848266f35c8a59175b5149b924
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492744"
---
# <a name="telephony-concepts"></a>Telefonos fogalmak

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Az Azure kommunikációs szolgáltatások az ügyfél-kódtárak használatával telefonos és PSTN-alkalmazásokat adhatnak hozzá az alkalmazásaihoz. Ez az oldal a legfontosabb telefonos fogalmakat és képességeket foglalja össze. Tekintse meg a [hívó könyvtárat](../../quickstarts/voice-video-calling/calling-client-samples.md) , ahol további információkat tudhat meg az ügyféloldali kódtár nyelveiről és képességeiről.

## <a name="overview-of-telephony"></a>A telefonos szolgáltatások áttekintése
Ha a felhasználók hagyományos telefonszámmal működnek, a hívásokat a PSTN (nyilvános hálózati kapcsoló) hanghívása könnyíti meg. PSTN-hívások létrehozásához és fogadásához a telefonos funkciókat kell hozzáadnia az Azure kommunikációs szolgáltatások erőforrásaihoz. Ebben az esetben a jelzés és a média az IP-alapú és a PSTN-alapú technológiák kombinációját használja a felhasználók összekapcsolásához. A kommunikációs szolgáltatások két különálló módszert biztosítanak a PSTN-hálózat eléréséhez: az Azure Cloud Calling és a SIP felületet.

### <a name="azure-cloud-calling"></a>Azure Cloud Calling

Az alkalmazáshoz vagy szolgáltatáshoz való PSTN-kapcsolat egyszerű módja, ebben az esetben a Microsoft a távközlési szolgáltató. A Microsofttól közvetlenül is vásárolhat számokat. Az Azure Cloud Calling egy felhőalapú telefonos megoldás a kommunikációs szolgáltatásokhoz. Ez a legegyszerűbb lehetőség, amely összeköti az ACS-t a nyilvános kapcsolású telefonos hálózattal (PSTN), hogy lehetővé tegye a telefonálást a vezetékes és a mobil telefonok számára világszerte. Ezzel a beállítással a Microsoft PSTN-szolgáltatóként működik, ahogy az alábbi ábrán is látható:

![Azure Cloud Calling diagram.](../media/telephony-concept/azure-calling-diagram.png)

Ha a következőre válaszol az "igen" értékre, akkor az Azure Cloud Calling a legmegfelelőbb megoldás:
- Az Azure Cloud Calling az Ön régiójában érhető el.
- Nem kell megőriznie a jelenlegi PSTN-szolgáltatót.
- Microsoft által felügyelt hozzáférést kíván használni a PSTN-hez.

Ezzel a beállítással:
- A számokat közvetlenül a Microsofttól szerezheti be, és a világ különböző pontjain hívhat telefonokat.
- Nincs szükség helyszíni központi telepítés telepítésére vagy karbantartására, mivel az Azure Cloud Call az Azure kommunikációs szolgáltatásokból is működik.
- Megjegyzés: ha szükséges, dönthet úgy, hogy a támogatott munkamenet-vezérlőt (SBC) a SIP-felületen keresztül összekapcsolja a külső gyártótól származó PBX, analóg eszközökkel és más, az SBC által támogatott külső telefonos eszközökkel való együttműködéshez.

Ehhez a lehetőséghez folyamatos kapcsolat szükséges az Azure kommunikációs szolgáltatásokhoz.

### <a name="sip-interface"></a>SIP-interfész

Ezzel a beállítással összekapcsolhatja az örökölt helyszíni telefonos szolgáltatásokat és az Azure kommunikációs szolgáltatásokhoz való választást. Ha az Azure Cloud Calling nem érhető el az Ön országában vagy régiójában, akkor is lehetővé teszi az ACS-alkalmazások számára a PSTN-alapú telefonálási funkciókat. 

![SIP-csatoló diagramja](../media/telephony-concept/sip-interface-diagram.png)

Ha a következő kérdések bármelyikére Igennel válaszol, akkor a SIP Interface a legmegfelelőbb megoldás:

- A PSTN-hívási képességekkel szeretné használni az ACS-t.
- Meg kell őriznie a jelenlegi PSTN-szolgáltatót.
- Azt szeretné, hogy az útválasztást az Azure Cloud Calling szolgáltatásban megjelenő hívásokkal is össze lehessen állítani.
- A harmadik féltől származó PBX és/vagy berendezésekkel, például a külső személyhívókkal, az analóg eszközökkel és így tovább kell működnie.

Ezzel a beállítással:

- A saját támogatott SBC-t az Azure kommunikációs szolgáltatásokhoz is összekapcsolhatja anélkül, hogy további helyszíni szoftverre lenne szükség.
- Az ACS-vel bármilyen telefonos szolgáltatót használhat.
- Dönthet úgy, hogy konfigurálja és felügyeli ezt a lehetőséget, vagy konfigurálhatja és felügyelheti a szolgáltató vagy partner (megkérdezheti, hogy a szolgáltató vagy a partner biztosítja ezt a lehetőséget).
- A telefonos berendezések, például a külső PBX és az analóg eszközök, valamint az ACS közötti együttműködés konfigurálható.

Ehhez a beállításhoz a következők szükségesek:

- Megszakítás nélküli kapcsolódás az Azure-hoz.
- Támogatott SBC üzembe helyezése és karbantartása.
- Egy harmadik féltől származó szolgáltatóval kötött szerződés. (Kivéve, ha központilag van telepítve a harmadik féltől származó PBX-hez, analóg eszközökhöz vagy más telefonos berendezésekhez a kommunikációs szolgáltatásokban lévő felhasználók számára.)

## <a name="next-steps"></a>Következő lépések

### <a name="conceptual-documentation"></a>Fogalmi dokumentáció

- [Telefonszám-típusok az Azure kommunikációs szolgáltatásokban](./plan-solution.md)
- [A SIP-interfész tervezése](./sip-interface-infrastructure.md)
- [Díjszabás](../pricing.md)

### <a name="quickstarts"></a>Rövid útmutatók

- [Telefonszám beszerzése](../../quickstarts/telephony-sms/get-phone-number.md)
- [Telefonos hívás](../../quickstarts/voice-video-calling/pstn-call.md)
