---
title: SMS – GYAKORI KÉRDÉSEK
titleSuffix: An Azure Communication Services concept document
description: SMS – GYAKORI KÉRDÉSEK
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: reference
ms.service: azure-communication-services
ms.openlocfilehash: 1ba7c730542adb74356d71f2482cce57e633cb65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646133"
---
# <a name="sms-faq"></a>SMS – GYAKORI KÉRDÉSEK

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>Használható-e az ügyfél vészhelyzeti célokra az Azure kommunikációs szolgáltatásokat?

Az Azure kommunikációs szolgáltatások nem támogatják a Egyesült Államok Text-to-911 funkciókat, azonban lehetséges, hogy a szövetségi kommunikációs Bizottság (FCC) szabályainak megfelelően kell megtennie ezt a kötelezettséget.  Meg kell vizsgálnia, hogy az FCC szöveg-911 szabályai érvényesek-e a szolgáltatásra vagy alkalmazásra. Ahhoz, hogy ezek a szabályok milyen mértékben terjednek ki, a 911 szöveges üzenetek továbbítása felelős a segélyhívó központoknak, amelyek ezt kérik. Szabadon meghatározhatja saját szöveg-911 szállítási modelljét, de az FCC által elfogadott egyik módszer magában foglalja a natív tárcsázó automatikus indítását a felhasználó mobileszközön, hogy 911 szövegeket továbbítson a mögöttes mobil szolgáltatón keresztül.

## <a name="are-there-any-limits-on-sending-messages"></a>Van valamilyen korlát az üzenetek küldésére?

Annak biztosítása érdekében, hogy továbbra is a magas színvonalú szolgáltatást kínáljuk a SLA-kat, az Azure kommunikációs szolgáltatások a díjszabási korlátokat (az egyes primitívek esetében eltérőek). A korláton túli API-kat meghívó fejlesztők 429 HTTP-állapotkód-választ kapnak. Ha a vállalat olyan követelményekkel rendelkezik, amelyek túllépik a díjszabási korlátot, küldjön e-mailt a következő címre: phone@microsoft.com .

Az SMS-re vonatkozó díjszabás:

|Művelet|Hatókör|Időkeret (ek)| Korlát (kérés #) | Üzenet egység percenként|
|---------|-----|-------------|-------------------|-------------------------|
|Üzenet küldése|/Szám|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Hogyan kezeli az Azure kommunikációs szolgáltatások a leválasztást az ingyenes számok esetében?

Az USA-beli díjmentes számokat az USA-beli szolgáltatók felhatalmazzák és érvényesítik.
- Leállítás – ha egy szöveges üzenet címzettje szeretné letiltani a letiltást, a "Leállítás" üzenetet a díjmentes számra küldheti. A szolgáltató a következő alapértelmezett választ küldi le: "hálózati MSG: a" Leállítás "szót választotta, amely letiltja az ebből a számból küldött összes szöveget. A "Leállítás" szöveg visszakapcsolja az üzeneteket. "
- INDÍTÁS/leállítás – ha a címzett egy díjmentes számú szöveges üzenetre szeretne újrafizetni, akkor a "START" vagy a "Leállítás" lehetőséget a díjmentes számra küldheti. A szolgáltató a következő alapértelmezett választ küldi el a START/unstop számára: "hálózati MSG: megválaszolta a" leállítást ", és megkezdi az üzenetek fogadását erről a számról."
- Az Azure kommunikációs szolgáltatás a leállítási üzenetet fogja felderíteni, és letiltja az összes további üzenetet a címzettnek. A kézbesítési jelentés azt jelzi, hogy sikertelen kézbesítés történt az állapotjelző üzenettel, mert a feladó letiltotta a megadott címzettet.
- A Leállítás, a leállítás és az indítási üzenetek vissza lesznek továbbítva Önnek. Az Azure kommunikációs szolgáltatásokkal megfigyelheti és implementálhatja ezeket a letiltásokat, így biztosíthatja, hogy a rendszer ne küldjön olyan üzenetet a címzetteknek, akik kiválasztották a kommunikációt.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Hogyan fogadhatok üzeneteket az Azure kommunikációs szolgáltatásokkal?

Az Azure kommunikációs szolgáltatások ügyfelei a Azure Event Grid használatával fogadhatják a bejövő üzeneteket. Ezt a rövid útmutatót követve beállíthatja [, hogy az](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events) Event-Grid üzeneteket fogadjon.

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>Küldhetek/fogadhatok hosszú üzeneteket (>2048 karakter)?

Az Azure kommunikációs szolgáltatás támogatja a hosszú üzenetek SMS-ben történő küldését és fogadását. Bizonyos vezeték nélküli szolgáltatók és eszközök azonban eltérően működhetnek, ha hosszú üzeneteket fogadnak.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>Hogyan történik az üzenetek küldése a vezetékes telefonszámokra?

A Egyesült Államok az Azure kommunikációs szolgáltatás nem keres vezetékes számokat, és megkísérli elküldeni a kézbesítést a szállítók számára. A rendszer a vezetékes telefonszámokra küldött üzenetekért díjat számít fel. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>Több címzettnek is küldhetek üzeneteket?


Igen, egyetlen kérést több címzett is elvégezhet. Ezt a rövid útmutatót követve több címzettnek [is küldhet](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-csharp) üzeneteket.
