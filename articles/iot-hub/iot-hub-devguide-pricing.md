---
title: Az Azure IoT Hub díjszabásának ismertetése | Microsoft Docs
description: Fejlesztői útmutató – információ arról, hogy a mérés és a díjszabás hogyan működik IoT Hub többek között a bevált példákkal.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 4c7382f84522333b6aae0d79941aae8f2147a12f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "81729147"
---
# <a name="azure-iot-hub-pricing-information"></a>Az Azure IoT Hub díjszabási információi

Az [Azure IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub) a IoT hub különböző SKU-ról és díjszabásáról nyújt általános információt. Ez a cikk további részleteket tartalmaz arról, hogy a különböző IoT Hub funkciók hogyan vannak kiértékelve üzenetekként IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Díjak/művelet

| Művelet | Számlázási információk | 
| --------- | ------------------- |
| Identitásjegyzék műveletei <br/> (létrehozás, lekérés, Listázás, frissítés, törlés) | Nem számítunk fel díjat. |
| Az eszközről a felhőbe irányuló üzenetek | Az üzenetek sikeres elküldését 4 KB-os adattömbök terhelik IoT Hub. Például egy 6 KB-os üzenet után 2 üzenet kerül kiszámlázásra. |
| Felhőből az eszközre irányuló üzenetek | Az üzenetek sikeres elküldése 4 KB-os adattömbökben történik, például egy 6 KB-os üzenet után 2 üzenet kerül kiszámlázásra. |
| Fájlfeltöltés | Az Azure Storage-ba való fájlátvitel IoT Hub nem mérhető. A fájlátvitel kezdeményezése és a befejezési üzenetek a 4 KB-os növekményekben elküldött üzenetként lesznek felszámítva. Például egy 10 MB-os fájl átvitele két üzenetként történik az Azure Storage-költség mellett. |
| Közvetlen metódusok | A metódusok sikeres kérelmeit 4 KB-os adattömbökben számítjuk fel, a válaszokat pedig 4 KB-os adattömbökben, további üzenetként számítjuk fel. A leválasztott eszközökre irányuló kérelmeket 4 KB-os adattömbökben lévő üzenetként számítjuk fel. Például egy 4 KB-os törzstel rendelkező metódus, amely az eszköz törzsével kapcsolatos választ eredményez, két üzenetként lesz felszámítva. Egy 6 KB-os törzstel rendelkező metódus, amely az eszköz 1 KB-os válaszát eredményezi, a rendszer két üzenetként számítja fel a kérést, valamint egy másik üzenetet a válaszhoz. |
| Eszközök és modulok kettős olvasások | Az eszközről vagy a modulból érkező, illetve a megoldás hátterében található kettős olvasások az 512 bájtos adattömbökben lévő üzenetekként lesznek felszámítva. Például egy 6 KB-os dupla beolvasása 12 üzenetként lesz felszámítva. |
| Eszköz-és modul-különálló frissítések (címkék és tulajdonságok) | Az eszközről vagy a modulból érkező, illetve a megoldás hátsó részén lévő frissítések az 512 bájtos adattömbökben lévő üzenetként lesznek felszámítva. Például egy 6 KB-os dupla beolvasása 12 üzenetként lesz felszámítva. |
| Eszközök és modulok kettős lekérdezései | A lekérdezések az eredmény méretétől függően az 512 bájtos tömbökben lesznek felszámítva. |
| Feladatműveletek <br/> (létrehozás, frissítés, listázás, törlés) | Nem számítunk fel díjat. |
| Feladatok eszközönkénti műveletek | A feladatok műveleteit (például a kettős frissítéseket és a metódusokat) a rendszer a szokásos módon számítja fel. Például egy 1000-es metódust eredményező, 1 KB-os kérelmeket és üres törzsű válaszokat tartalmazó feladatok díja 1000 üzenet. |
| Életben tartási üzenetek | AMQP-vagy MQTT-protokollok használatakor a rendszer a kapcsolat létrehozásához és az egyeztetés során megjelenő üzenetek kiváltásához szükséges üzeneteket nem terheli. |

> [!NOTE]
> A rendszer minden méretet számításba vesz, figyelembe véve a hasznos adatok méretét bájtban (a protokollok kialakítása figyelmen kívül lesz hagyva). A tulajdonságokat és a törzset tartalmazó üzenetek esetében a méretet a rendszer a protokolltól független módon számítja ki. További információ: [IoT hub üzenet formátuma](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Példa #1

Egy eszköz percenként 1 1 KB-os eszközről a felhőbe küldött üzenetet küld IoT Hubra, amelyet aztán a Azure Stream Analytics olvas. A megoldás háttérbe állítása 10 percenként meghívja a (512 bájtos adattartalommal rendelkező) metódust az eszközön egy adott művelet elindításához. Az eszköz 200 bájtos eredménnyel válaszol a metódusra.

Az eszköz a következőket használja:

* Egy üzenet * 60 perc * 24 óra = 1440 üzenet naponta az eszközről a felhőbe irányuló üzenetekhez.
* Két kérelem plusz válasz * 6 alkalommal/óra * 24 óra = 288 üzenet a metódusokhoz.

Ez a számítás naponta összesen 1728 üzenetet biztosít.

## <a name="example-2"></a>Példa #2

Az eszközök óránként 1 100 KB-os eszközről a felhőbe irányuló üzenetet küldenek. Emellett az eszköz 2. és 1 KB-os hasznos adatait is frissíti négy óránként. A megoldás háttérbe (naponta egyszer) beolvassa a 14 KB-os eszközt, és frissíti azt 512 bájtos adattartalommal a konfigurációk módosításához.

Az eszköz a következőket használja:

* 25 (100 KB/4 KB) üzenet * 24 óra az eszközről a felhőbe irányuló üzenetekhez.
* Két üzenet (1 KB/0,5 KB) * napi hat alkalommal az eszköz Twin frissítéseihez.

Ez a számítás naponta összesen 612 üzenetet biztosít.

A megoldás háttér-végpontja 28 üzenetet (14 KB/0,5 KB) használ a különálló eszközök olvasásához, valamint egy üzenet frissítéséhez, összesen 29 üzenetet.

Összességében az eszköz és a megoldás háttér 641 üzenetet használ naponta.
