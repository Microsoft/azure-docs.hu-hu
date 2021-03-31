---
title: Microsoft Azure Data Box Disk biztonsági funkciók | AdatMicrosoft Docs
description: A Azure Data Box Disk által megvalósított biztonsági szolgáltatásokat ismerteti, amelyek lehetővé teszik nagy mennyiségű adatok átvitelét az Azure-ba
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: 59cce6d89bc863ad489a8ed37c7f284d3085c775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "79117269"
---
# <a name="azure-data-box-disk-security-and-data-protection"></a>Biztonság és adatvédelem Azure Data Box Disk

A cikk az Azure Data Box Disk biztonsági szolgáltatásait ismerteti, amelyekkel biztosítható a Data Box megoldás összetevőinek és bennük tárolt adatoknak a védelme. 

## <a name="data-flow-through-components"></a>Az adatok áramlása az összetevők között

A Microsoft Azure Data Box megoldást négy, egymással együttműködő fő összetevő alkotja:

- **Az Azure-ban üzemeltetett Azure Data Box szolgáltatás** – a felügyeleti szolgáltatás, amellyel lemezmegrendeléseket hozhat létre, konfigurálhatja a lemezeket, majd nyomon követheti a rendelést a teljesítésig.
- **Data Box-lemezek** – a fizikai lemezek, amelyeket helyszíni adatai az Azure-ba való importálásához kiküldünk Önnek. 
- **A lemezekhez csatlakoztatott ügyfelek/gazdagépek** – az infrastruktúra védeni kívánt adatokat tartalmazó, a Data Box-lemezekre USB-kapcsolaton keresztül csatlakozó ügyfelei.
- **Felhőalapú tároló** – a hely az Azure-felhőben, ahol az adatok tárolása történik. Ez általában a létrehozott Azure Data Box-erőforráshoz csatolt tárfiók.

A következő ábra mutatja az adatok áramlását az Azure Data Box Disk megoldásban a helyszíni rendszerekről az Azure-ba.

![A Data Box Disk biztonsága](media/data-box-disk-security/data-box-disk-security-1.png)

## <a name="security-features"></a>Biztonsági funkciók

A Data Box Disk biztonságos megoldást kínál az adatok védelmére, mivel garantálja, hogy az adatokat csak a jogosult entitások tekinthetik meg, módosíthatják vagy törölhetik. A megoldás biztonsági szolgáltatásai a lemezekre és a rajtuk tárolt adatokat védő szolgáltatásra is vonatkoznak.

### <a name="data-box-disk-protection"></a>A Data Box Disk védelme

A Data Box Disk biztonságát az alábbi szolgáltatások biztosítják:

- Folyamatos AES-128 bites BitLocker-titkosítás a lemezeken.
- A lemezek biztonságos frissítése.
- A lemezek szállítása zárolt állapotban történik, és kizárólag a Data Box Disk feloldóeszközével oldhatók fel. A feloldóeszköz a Data Box Disk szolgáltatás portálján érhető el.

### <a name="data-box-disk-data-protection"></a>A Data Box Disk-adatok védelme

A Data Box Disk szolgáltatás bejövő és kimenő adatainak biztonságát az alábbi szolgáltatások biztosítják:

- Az adatok folyamatos BitLocker-titkosítása.
- Az adatok biztonságos törlése a lemezről, miután az Azure-ba való adatfeltöltés befejeződött. Az adatok törlése a NIST 800-88r1 szabványoknak megfelelően történik.

### <a name="data-box-service-protection"></a>A Data Box szolgáltatás védelme

A Data Box szolgáltatás biztonságát az alábbi funkciók biztosítják.

- A Data Box Disk szolgáltatáshoz való hozzáféréshez a szervezetnek olyan Azure-előfizetéssel kell rendelkeznie, amely tartalmazza a Data Box Disket. Az előfizetés szabályozza, hogy az Azure Portal mely szolgáltatásai érhetők el.
- A Data Box szolgáltatás az Azure-ban üzemel, így az Azure biztonsági funkciói védik. A Microsoft Azure által biztosított biztonsági funkciókról a [Microsoft Azure biztonsági és adatkezelési központban](https://www.microsoft.com/TrustCenter/Security/default.aspx) talál további információt.
- A Data Box Disk tárolja a lemezek hozzáférési kulcsát, amellyel a lemezek zárolása a szolgáltatásban feloldható. 
- A Data Box Disk szolgáltatás tárolja a megrendelések adatait és állapotát a szolgáltatásban. A megrendelés törlésekor ezek az információk is törlődnek.

## <a name="managing-personal-data"></a>Személyes adatok kezelése

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Az Azure Data Box Disk a szolgáltatás alábbi főbb példányaiban gyűjt és jelenít meg személyes adatokat:

- **Értesítési beállítások** – Amikor létrehoz egy megrendelést, a felhasználók e-mail-címeit az értesítési beállítások alatt konfigurálhatja. Ezeket az adatokat az adminisztrátor megtekintheti. A szolgáltatás törli az adatokat, amikor a feladat eléri a végállapotot, illetve ha Ön törli a megrendelést.

- **Megrendelés részletei** – miután létrejött a megrendelés, a felhasználók szállítási címét, e-mail-címét és kapcsolattartási adatait az Azure Portal tárolja. A mentett információk a következők:

  - Kapcsolattartó neve
  - Telefonszám
  - E-mail
  - Utca, házszám
  - City
  - Irányítószám
  - Állapot
  - Ország/tartomány/régió
  - Meghajtóazonosító
  - Szállítmányozó fiókszáma
  - Szállítmány nyomkövetési száma

    A Data Box a feladat befejezésekor vagy törlésekor törli a rendelés részleteit.

- **Szállítási cím** – A rendelés feladása után a Data Box szolgáltatás kiadja a szállítási címet külső szállítmányozóknak, például a UPS-nek vagy a DHL-nek. 

További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).


## <a name="next-steps"></a>Következő lépések

- A [Data Box Disk rendszerkövetelményeinek](data-box-disk-system-requirements.md) áttekintése.
- A [Data Box Disk korlátainak](data-box-disk-limits.md) megismerése.
- Az [Azure Data Box Disk](data-box-disk-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.
