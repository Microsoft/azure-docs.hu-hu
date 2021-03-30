---
title: Mi az az Azure SignalR Service?
description: Jobban megismerheti, hogy mi a jellemző használati eset az Azure-jelző használata, és megismerheti az Azure-jelző főbb előnyeit.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 92da400616ace9f24da121d81798d5ebd9222e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88921885"
---
# <a name="what-is-azure-signalr-service"></a>Mi az az Azure SignalR Service?

Az Azure SignalR szolgáltatás leegyszerűsíti a valós idejű webes funkciók hozzáadását az alkalmazásokhoz HTTP-n keresztül. A valós idejű funkció lehetővé teszi, hogy a szolgáltatás tartalomfrissítéseket küldjön le a csatlakoztatott ügyfeleknek, például az egyoldalas web- vagy mobilalkalmazásoknak. Így az ügyfelek a kiszolgáló lekérdezése nélkül frissülnek vagy küldenek frissítésekre vonatkozó új HTTP-kéréseket.


A cikk áttekintést nyújt az Azure SignalR szolgáltatásról.

## <a name="what-is-azure-signalr-service-used-for"></a>Mire használják az Azure SignalR szolgáltatást?

A kiszolgálóról az ügyfélnek valós időben történő leküldést igénylő forgatókönyvek esetében az Azure Signaler szolgáltatást használhatja.

A hagyományos valós idejű szolgáltatások, amelyek gyakran igényelnek lekérdezést a kiszolgálóról, az Azure Signaler szolgáltatást is használhatják.

Az Azure Signaler szolgáltatás számos iparágban használatos, minden olyan alkalmazáshoz, amely valós idejű tartalom-frissítést igényel. Felsorolunk néhány példát, amelyek hasznosak az Azure Signaler szolgáltatás használatához:

* **Nagy gyakoriságú adatfrissítések:** játék, szavazás, lekérdezés, aukció.
* **Irányítópultok és monitorozás:** céges irányítópult, pénzügyi piaci adatforgalom, azonnali értékesítési frissítés, többjátékos ranglista és IoT-figyelés.
* **Csevegés:** élő Csevegési csatorna, csevegési robot, online ügyfélszolgálat, valós idejű vásárlási asszisztens, Messenger, játékon belüli csevegés és így tovább.
* **Valós idejű hely a térképen:** logisztikai nyomon követés, kézbesítési állapot követése, szállítási állapot FRISSÍTÉSEI, GPS-alkalmazások.
* **Valós idejű célzási hirdetések:** személyre szabott valós idejű leküldéses hirdetések és ajánlatok, interaktív hirdetések.
* **Együttműködő alkalmazások:** társszerzője, faliújság-alkalmazások és Team Meeting szoftver.
* **Leküldéses értesítések:** közösségi hálózat, e-mail, játék, utazási riasztás.
* **Valós idejű közvetítés:** élő hang/videó közvetítés, élő feliratozás, fordítás, események/Hírek közvetítése.
* **IoT és csatlakoztatott eszközök:** valós idejű IoT mérőszámok, Távvezérlés, valós idejű állapot és hely nyomon követése.
* **Automatizálás:** valós idejű trigger a felsőbb szintű eseményekről.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Milyen előnyökkel jár az Azure Signaler szolgáltatás használata?

**Standard alapú:**

A SignalR számos, valós idejű webalkalmazások létrehozására használt technológia absztrakciója. A [WebSocket](https://wikipedia.org/wiki/WebSocket) az ideális átviteli technológia, de más technológiákat (például [kiszolgáló által küldött eseményeket (SSE)](https://wikipedia.org/wiki/Server-sent_events) és hosszú lekérdezést) is használ a rendszer, ha egyéb lehetőségek nem érhetők el. A SignalR automatikusan észleli és inicializálja a megfelelő átvitelt, a kiszolgáló és az ügyfél által támogatott szolgáltatások alapján.

**Natív ASP.NET Core támogatás:**

A signaler szolgáltatás natív programozási élményt biztosít a ASP.NET Core és a ASP.NET. Az új Signaler-alkalmazás a Signaler szolgáltatással való fejlesztése vagy a meglévő szignáló alapú alkalmazásról a Signaler szolgáltatásba való Migrálás minimális erőfeszítést igényel.
A signaler szolgáltatás a ASP.NET Core új funkcióját, a kiszolgálóoldali Blazer-t is támogatja.

**Széles körű ügyféltámogatás:**

A signaler szolgáltatás az ügyfelek széles köre, például a web-és mobil böngészők, az asztali alkalmazások, a Mobile apps, a kiszolgálói folyamat, a IoT-eszközök és a játékkonzolok terén működik. A signaler szolgáltatás különböző nyelvű SDK-kat kínál. A natív ASP.NET Core vagy ASP.NET C# SDK-k mellett a Signaler szolgáltatás a JavaScript ügyfél-SDK-t is biztosítja a webes ügyfelek és számos JavaScript-keretrendszer engedélyezéséhez. A Java Client SDK-t Java-alkalmazások is támogatják, beleértve az androidos natív alkalmazásokat is. A signaler szolgáltatás támogatja a REST API és a kiszolgáló nélküli integrációt a Azure Functions és a Event Grid használatával.

**Nagy léptékű Ügyfélkapcsolatok kezelése:**

A signaler szolgáltatás nagyméretű valós idejű alkalmazások számára készült. A signaler szolgáltatás lehetővé teszi, hogy több példány működjön együtt több millió ügyfél-kapcsolatra. A szolgáltatás több globális régiót is támogat a horizontális felskálázáshoz, a magas rendelkezésre álláshoz vagy a vész-helyreállítási célokra.

**Az önkiszolgáló szignáló terhének eltávolítása:**

A saját üzemeltetésű Signaler-alkalmazásokkal összehasonlítva a Signaler szolgáltatásba való váltás eltávolítja a méretezést és az ügyfélkapcsolatokat kezelő hátsó síkok felügyeletének szükségességét. A teljes körűen felügyelt szolgáltatás emellett leegyszerűsíti a webalkalmazásokat, és az üzemeltetési költségeket is csökkenti. A signaler szolgáltatás globálisan elérhető és világszínvonalú adatközpontot és hálózatot kínál, és több millió kapcsolatra, garantált SLA-ra, valamint az Azure standard megfelelőségének és biztonságának biztosítására is lehetőséget nyújt.

![Felügyelt jelző szolgáltatás](./media/signalr-overview/managed-signalr-service.png)

**Széles körű API-kat kínál különböző üzenetkezelési mintákhoz:**

A signaler szolgáltatás lehetővé teszi, hogy a kiszolgáló üzeneteket küldjön egy adott kapcsolatra, minden kapcsolatra vagy egy adott felhasználóhoz tartozó kapcsolatok egy részhalmazára, vagy egy tetszőleges csoportba helyezte őket.

## <a name="how-to-use-azure-signalr-service"></a>Az Azure SignalR szolgáltatás használata

Az Azure Signaler szolgáltatással számos különböző módon lehet programozni, az itt felsorolt minták közül néhányat:

- **[ASP.NET Core SignalR-alkalmazás skálázása](signalr-concept-scale-aspnet-core.md)** – Az Azure SignalR szolgáltatás integrálása egy ASP.NET Core SignalR-alkalmazással több százezer kapcsolatra való horizontális felskálázáshoz.
- **[Kiszolgáló nélküli, valós idejű alkalmazások létrehozása](signalr-concept-azure-functions.md)** – Az Azure SignalR szolgáltatással integrált Azure Functions használata kiszolgáló nélküli, valós idejű alkalmazások létrehozására, többek között JavaScript, C# és Java nyelven.
- **[Üzenetek küldése a kiszolgálóról az ügyfeleknek REST API-n keresztül](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – Az Azure SignalR szolgáltatás biztosít egy REST API-t, amely lehetővé teszi, hogy az alkalmazások bármilyen REST-kompatibilis programnyelven üzeneteket küldjenek a SignalR szolgáltatáshoz csatlakoztatott ügyfeleknek.
