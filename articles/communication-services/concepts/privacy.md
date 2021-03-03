---
title: Régió rendelkezésre állása és az adattárolás az Azure kommunikációs szolgáltatásokhoz
description: Ismerje meg az Azure kommunikációs szolgáltatások adattárolási és adatkezelési kérdéseit
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/03/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8e08c531cd46d5112138a3feedf97b27307e3dca
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656193"
---
# <a name="region-availability-and-data-residency"></a>Regionális elérhetőség és adattárolási hely

Az Azure kommunikációs szolgáltatás elkötelezett ahhoz, hogy ügyfeleink megfeleljenek az adatvédelmi és személyes adatokra vonatkozó követelményeiknek. A kommunikációs szolgáltatásokat használó fejlesztőknek, akik az alkalmazás használatával közvetlen kapcsolatban állnak az emberekkel, lehetséges, hogy az adataik vezérlői. Mivel az Azure kommunikációs szolgáltatás tárolja ezeket az adattárolást az Ön nevében, valószínűleg az ilyen típusú adatfeldolgozót használjuk. Ez az oldal összefoglalja, hogyan őrzi meg a szolgáltatás az adatait, és hogyan azonosíthatja, exportálhatja és törölheti ezeket az adatfájlokat.

## <a name="data-residency"></a>Adattárolási hely

A kommunikációs szolgáltatások erőforrásának létrehozásakor meg kell adnia egy **földrajzot** (nem Azure-adatközpontot). A kommunikációs szolgáltatások által tárolt összes adat az adott földrajzban megmarad a kommunikációs szolgáltatások által belsőleg kiválasztott adatközpontban. Az adatforgalom más földrajzi területeken is áthaladhat vagy feldolgozható. Ezek a globális végpontok ahhoz szükségesek, hogy nagy teljesítményű, kis késleltetésű felhasználói élményt nyújtsanak a végfelhasználóknak a helyüktől függetlenül.

## <a name="data-residency-and-events"></a>Adattárolás és események

Az Azure kommunikációs szolgáltatásokkal konfigurált Event Grid rendszertémakörek globális helyen lesznek létrehozva. A megbízható kézbesítés támogatásához a globális Event Grid rendszertémakör bármely Microsoft-adatközpontban tárolhatja az eseményeket. Az Azure kommunikációs szolgáltatásokkal való Event Grid konfigurálásakor az esemény adatait Event Grid, amely az Ön felügyelete alá tartozó Azure-erőforrás. Habár az Azure kommunikációs szolgáltatások úgy konfigurálhatók, hogy Azure Event Grid használják, Ön felelős a Event Grid-erőforrás és a benne tárolt adatkezelésért.

## <a name="relating-humans-to-azure-communication-services-identities"></a>Az Azure kommunikációs szolgáltatások identitásával kapcsolatos emberek

Az alkalmazás kezeli az emberi felhasználók és a kommunikációs szolgáltatás identitásának kapcsolatát. Ha egy emberi felhasználóhoz tartozó adattörlést szeretne törölni, törölnie kell az összes, a felhasználóhoz kapcsolódó kommunikációs szolgáltatás identitásával kapcsolatos adatforgalmat.

A kommunikációs szolgáltatási adatmennyiség két kategóriába sorolhatók:
- **API-adatértékek.** Ezeket az adatforgalmat a kommunikációs szolgáltatás API-k hozzák létre és kezelik, amelyek egy tipikus példa csevegési API-kon keresztül felügyelt üzenetek küldésére.
- **Naplók Azure monitor** Ezeket az adatkészleteket a szolgáltatás hozza létre és felügyeli a Azure Monitor adatplatformon keresztül. Ezek az adatok telemetria és mérőszámokat tartalmaznak, amelyek segítenek megérteni a kommunikációs szolgáltatások használatát. Ezt a kommunikációs szolgáltatás API-jai nem kezelik.

## <a name="api-data"></a>API-adatértékek

### <a name="identities"></a>Identitások

Az Azure kommunikációs szolgáltatások megőrzik az identitások könyvtárát, és a [DeleteIdentity](/rest/api/communication/communicationidentity/delete) API használatával távolítják el azokat. Az identitás törlésével visszavonja az összes társított hozzáférési jogkivonatot, és törölni fogja a csevegési üzeneteiket. Az identitás eltávolításával kapcsolatos további információkért [tekintse meg ezt a lapot](../quickstarts/access-tokens.md).

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

A kommunikációs szolgáltatásokkal rendelkező Azure Portal vagy Azure Resource Manager API-k használatával személyes adatai is létrehozhatók. [Ezen a lapon megtudhatja, hogyan kezelheti Azure Resource Manager rendszerekben tárolt személyes információkat.](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>Telefonos szám kezelése

Az Azure kommunikációs szolgáltatások fenntartják a kommunikációs szolgáltatások erőforrásaihoz társított telefonszámok könyvtárát. Ezen API-k használatával lekérheti a telefonszámokat, és törölheti őket:
- `Release Phone Number`

### <a name="chat"></a>Csevegés

A csevegési szálak és üzenetek csak explicit törlés után maradnak meg. A rendszer 30 nap után automatikusan törli A teljesen üresjáratban lévő szálat. Üzenetek beolvasása, listázása, frissítése és törlése [csevegési API](/rest/api/communication/chat/deletechatmessage/deletechatmessage) -k használatával.

- `Get Thread`
- `Get Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>SMS

Az SMS-üzenetek küldését és fogadását a szolgáltatás elmúlóan dolgozza fel, és nem őrzi meg.

### <a name="pstn-voice-calling"></a>PSTN hanghívása

A szolgáltatás elmúló módon dolgozza fel a hang-és video-kommunikációt, és a Azure Monitor-naplóktól eltérő erőforrásban nem őrzi meg az adatait.

### <a name="internet-voice-and-video-calling"></a>Internetes hang-és videohívás-hívás

A szolgáltatás elmúló módon dolgozza fel a hang-és video-kommunikációt, és a Azure Monitor-naplóktól eltérő erőforrásban nem őrzi meg az adatait.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor és Log Analytics

Az Azure kommunikációs szolgáltatások a szolgáltatás működési állapotának és kihasználtságának megértéséhez Azure Monitor naplózási adataiba kerülnek. Ezek a naplók a kommunikációs szolgáltatás identitásait és telefonszámait tartalmazzák. A potenciálisan személyes adatértékek törléséhez [használja ezeket az eljárásokat a Azure monitor](../../azure-monitor/logs/personal-data-mgmt.md). Előfordulhat, hogy [az Azure monitor alapértelmezett megőrzési időtartamát](../../azure-monitor/logs/manage-cost-storage.md)is be szeretné állítani.

## <a name="additional-resources"></a>További források

- [Azure-beli adattulajdonosi kérelmek a GDPR és a CCPA](/microsoft-365/compliance/gdpr-dsr-azure?preserve-view=true&view=o365-worldwide)
- [Microsoft adatvédelmi központ](https://www.microsoft.com/trust-center/privacy/data-location)
- [Azure interaktív Térkép – hol található a vásárlói adataim?](https://azuredatacentermap.azurewebsites.net/)
