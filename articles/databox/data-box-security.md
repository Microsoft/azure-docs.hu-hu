---
title: A Microsoft Azure Data Box biztonsági áttekintése | Microsoft Docs in data
description: Ismerteti Azure Data Box biztonsági funkcióit az eszközön, a szolgáltatásban és a Data Box található adaton.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: alkohli
ms.openlocfilehash: 4d6c77b3e8920cabc397cdcbc235baefa031e5ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97655492"
---
# <a name="azure-data-box-security-and-data-protection"></a>Az Azure Data Box biztonsági és adatvédelmi szolgáltatásai

A Data Box biztonságos megoldást kínál az adatok védelmére, mivel garantálja, hogy az adatokat csak a jogosult entitások tekinthetik meg, módosíthatják vagy törölhetik. A cikk az Azure Data Box biztonsági szolgáltatásait ismerteti, amelyekkel biztosítható a Data Box megoldás összetevőinek és a bennük tárolt adatoknak a védelme.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Az adatok áramlása az összetevők között

A Microsoft Azure Data Box megoldást négy, egymással együttműködő fő összetevő alkotja:

- **Az Azure-ban üzemeltetett Azure Data Box szolgáltatás** – a felügyeleti szolgáltatás, amellyel eszközmegrendeléseket hozhat létre, konfigurálhatja az eszközöket, majd nyomon követheti a rendelést a teljesítésig.
- **Data Box-eszköz** – az átviteli eszköz, amelyet helyszíni adatai az Azure-ba való importálásához kiküldünk Önnek.
- **Az eszközhöz csatlakoztatott ügyfelek/gazdagépek** – az infrastruktúra védeni kívánt adatokat tartalmazó, a Data Box-eszközhöz csatlakozó ügyfelei.
- **Felhőalapú tároló** – a hely az Azure-felhőben, ahol az adatok tárolása történik. Ez a hely általában a létrehozott Azure Data Box erőforráshoz csatolt Storage-fiók.

A következő ábra az adatok folyamatát mutatja a helyszíni és az Azure közötti Azure Data Box megoldáson keresztül, valamint a különböző biztonsági funkciókat, amelyeket az adatok a megoldáson keresztül áramlanak. Ez a folyamat a Data Box importálási sorrendje.

![Data Box importálás biztonsága](media/data-box-security/data-box-security-import.png)

A következő diagram a Data Box exportálási sorrendjét mutatja.

![Data Box biztonsági exportálás](media/data-box-security/data-box-security-export.png)

Mivel az adatforgalom ezen a megoldáson keresztül zajlik, a rendszer naplózza az eseményeket, és létrehozza a naplókat. További információért keresse fel a következőt:

- [A Azure Data Box importálási rendeléseinek nyomon követése és eseménynaplózása](data-box-logs.md).
- [A Azure Data Box exportálási rendeléseinek nyomon követése és eseménynaplózása](data-box-export-logs.md)

## <a name="security-features"></a>Biztonsági funkciók

A Data Box biztonságos megoldást kínál az adatok védelmére, mivel garantálja, hogy az adatokat csak a jogosult entitások tekinthetik meg, módosíthatják vagy törölhetik. A megoldás biztonsági szolgáltatásai a lemezekre és a rajtuk tárolt adatokat védő szolgáltatásra is vonatkoznak.

### <a name="data-box-device-protection"></a>A Data Box-eszköz védelme

A Data Box-eszköz védelmét az alábbi funkciók biztosítják:

- Az eszköz strapabíró tokkal rendelkezik, amely ellenáll az ütéseknek, valamint a kedvezőtlen szállítási és időjárási körülményeknek. 
- A hardverhez vagy a szoftverekhez való illetéktelen hozzáférés észlelése esetén az eszköz nem működtethető.
- Csak Data Box-kompatibilis szoftvert futtat.
- A rendszerindítás zárolt állapotban történik.
- Eszköz-hozzáférés vezérlése az eszköz zárolásának feloldására szolgáló hozzáférési kulcs használatával. Ezt a hitelesítő kulcsot egy titkosítási kulcs védi. Használhatja a saját ügyfél által felügyelt kulcsot a hitelesítő kulcs megvédéséhez. További információ: [az ügyfél által felügyelt kulcsok használata Azure Key Vaultban Azure Data Box](data-box-customer-managed-encryption-key-portal.md).
- Az adatok külső helyre és helyről történő másolásához hozzáférési hitelesítő adatokat kell megadni. A rendszer minden, a Azure Portal **eszköz hitelesítő adatok** lapjához való hozzáférést naplóz a [tevékenység naplófájljaiban](data-box-logs.md#query-activity-logs-during-setup).
- Saját jelszavait használhatja az eszközhöz, és megoszthatja a hozzáférést. További információ: [oktatóanyag: Order Azure Data Box](data-box-deploy-ordered.md).

### <a name="establish-trust-with-the-device-via-certificates"></a>Megbízhatósági kapcsolat létrehozása az eszközzel tanúsítványok használatával

A Data Box eszköz lehetővé teszi saját tanúsítványok használatát, és telepíti azokat, amelyeket a helyi webes felhasználói felülethez és a blob-tárolóhoz való csatlakozáshoz kíván használni. További információ: [saját tanúsítványok használata Data Box és Data Box Heavy eszközökkel](data-box-bring-your-own-certificates.md).

### <a name="data-box-data-protection"></a>A Data Box-adatok védelme

A Data Box szolgáltatás bejövő és kimenő adatainak biztonságát az alábbi szolgáltatások biztosítják:

- Az inaktív adatok 256 bites AES-titkosítással védettek. Magas biztonsági környezetben a szoftveres kettős titkosítást is használhatja. További információ: [oktatóanyag: Order Azure Data Box](data-box-deploy-ordered.md).
- A titkosított protokollok átvitel közben is biztosítják az adatok védelmét. Javasoljuk, hogy az SMB 3,0 titkosítással használja az adatok védelme érdekében az adatkiszolgálókról történő másoláskor.
- Az Azure-ba való feltöltés után az adatok biztonságos törlése az eszközről. Az adattörlés összhangban van a " [a" függelékben szereplő, a NIST 800-88r1 szabványokban található ATA merevlemez-meghajtókra](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)vonatkozó útmutatásokkal. Az adattörlési eseményt a rendszer rögzíti a [megrendelési előzményekben](data-box-logs.md#download-order-history).

### <a name="data-box-service-protection"></a>A Data Box szolgáltatás védelme

A Data Box szolgáltatás biztonságát az alábbi funkciók biztosítják.

- A Data Box szolgáltatáshoz való hozzáféréshez a szervezetnek rendelkeznie kell Data Boxt tartalmazó Azure-előfizetéssel. Az előfizetés szabályozza, hogy az Azure Portal mely szolgáltatásai érhetők el.
- A Data Box szolgáltatás az Azure-ban üzemel, így az Azure biztonsági funkciói védik. A Microsoft Azure által biztosított biztonsági funkciókról a [Microsoft Azure biztonsági és adatkezelési központban](https://www.microsoft.com/TrustCenter/Security/default.aspx) talál további információt.
- Az Data Box rendeléshez való hozzáférés az Azure-szerepkörök használatával szabályozható. További információ: Data Box- [sorrend hozzáférés-vezérlésének beállítása](data-box-logs.md#set-up-access-control-on-the-order)
- A Data Box szolgáltatás a szolgáltatásban lévő eszköz zárolásának feloldásához használt feloldási jelszót tárolja.
- A Data Box szolgáltatás tárolja a megrendelések adatait és állapotát a szolgáltatásban. A megrendelés törlésekor ezek az információk is törlődnek.

## <a name="managing-personal-data"></a>Személyes adatok kezelése

Az Azure Data Box a szolgáltatás alábbi főbb példányaiban gyűjt és jelenít meg személyes adatokat:

- **Értesítési beállítások** – Amikor létrehoz egy megrendelést, a felhasználók e-mail-címeit az értesítési beállítások alatt konfigurálhatja. Ezeket az adatokat az adminisztrátor megtekintheti. A szolgáltatás törli az adatokat, amikor a feladat eléri a végállapotot, illetve ha Ön törli a megrendelést.

- **Rendelés részletei** – a megrendelés létrehozása után a rendszer a Azure Portal tárolja a felhasználók szállítási címét, e-mail-címét és kapcsolattartási adatait. A mentett információk a következők:

  - Kapcsolattartó neve
  - Telefonszám
  - E-mail
  - Utca, házszám
  - City
  - Irányítószám
  - Állapot
  - Ország/tartomány/régió
  - Szállítmányozó fiókszáma
  - Szállítmány nyomkövetési száma

    A Data Box a feladat befejezésekor vagy törlésekor törli a rendelés részleteit.

- **Szállítási cím** – A rendelés feladása után a Data Box szolgáltatás kiadja a szállítási címet külső szállítmányozóknak, például a UPS-nek vagy a DHL-nek. 

További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).


## <a name="security-guidelines-reference"></a>Biztonsági irányelvek – referencia

A Data Box szolgáltatásra a következő biztonsági irányelvek vonatkoznak:

|Irányelv   |Leírás   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Víz és por elleni védelem         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Ellenállás a kedvezőtlen szállítási körülményeknek          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Biztonságos belsővezérlőprogram-frissítés         |
|[2. szintű FIPS 140-2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Adatvédelem         |
|A függelék, ATA-meghajtók esetében a [NIST SP 800 – 88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Adattisztítás         |

## <a name="next-steps"></a>Következő lépések

- A [Data Box használatára vonatkozó előfeltételek](data-box-system-requirements.md) áttekintése.
- A [Data Box korlátjainak](data-box-limits.md) értelmezése.
- Az [Azure Data Box](data-box-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.
