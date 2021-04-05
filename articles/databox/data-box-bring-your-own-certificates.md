---
title: Saját tanúsítványok használata Azure Data Box/Azure Data Box Heavy eszközökkel
description: Saját tanúsítványok használata a helyi webes felhasználói felület és a blogbejegyzés Data Box vagy Data Box Heavy eszközön való eléréséhez.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: 1836ed57305fd7e168961eb81670b56d4ce296cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545173"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Saját tanúsítványok használata Data Box és Data Box Heavy eszközzel

A rendelés feldolgozása során önaláírt tanúsítványok jönnek létre a helyi webes felhasználói felület és a blob Storage Data Box vagy Data Box Heavy eszközhöz való hozzáféréshez. Ha inkább megbízható csatornán keresztül kommunikál az eszközzel, használhatja a saját tanúsítványait.

A cikk azt ismerteti, hogyan telepítheti saját tanúsítványait, és hogyan térhet vissza az alapértelmezett tanúsítványokhoz, mielőtt visszaadná az eszközt az adatközpontba. Emellett összefoglalja a tanúsítványokra vonatkozó követelményeket is.

## <a name="about-certificates"></a>Információ a tanúsítványokról

A tanúsítvány egy **nyilvános kulcs** és egy olyan entitás (például tartománynév) közötti kapcsolatot biztosít, amelyet egy megbízható harmadik fél (például **egy hitelesítésszolgáltató)** **aláírt** (ellenőrzött).  A tanúsítvány kényelmes módszert biztosít a megbízható nyilvános titkosítási kulcsok terjesztésére. Így a tanúsítványok biztosítják, hogy a kommunikáció megbízható legyen, és titkosított adatokat küldjön a megfelelő kiszolgálóra.

Ha a Data Box-eszköz eredetileg be van állítva, az önaláírt tanúsítványok automatikusan létrejönnek. Igény szerint saját tanúsítványokat is használhat. Ha saját tanúsítványokat szeretne létrehozni, kövesse az alábbi irányelveket.

Data Box vagy Data Box Heavy eszközön kétféle végponti tanúsítvány van használatban:

- BLOB Storage-tanúsítvány
- Helyi felhasználói felületi tanúsítvány

### <a name="certificate-requirements"></a>Tanúsítványkövetelmények

A tanúsítványoknak az alábbi követelményeknek kell megfelelniük:

- Az Endpoint tanúsítványnak formátummal kell rendelkeznie, `.pfx` amely exportálható titkos kulccsal.
- Minden egyes végponthoz, több tartományhoz több végponthoz vagy helyettesítő karakteres végponti tanúsítványhoz egyéni tanúsítványt is használhat.
- A végponti tanúsítvány tulajdonságai hasonlóak egy tipikus SSL-tanúsítvány tulajdonságaihoz.
- `.cer`Az ügyfélszámítógépen szükség van egy der formátumú (filename kiterjesztésű) tanúsítványra.
- A helyi felhasználói felület tanúsítványának feltöltése után újra kell indítania a böngészőt, és törölnie kell a gyorsítótárat. Tekintse meg a böngésző adott utasításait.
- A tanúsítványokat meg kell változtatni, ha az eszköz neve vagy a DNS-tartománynév megváltozik.
- Végponti tanúsítványok létrehozásakor használja az alábbi táblázatot:

  |Típus |Tulajdonos neve (SN)  |Tulajdonos alternatív neve (SAN)  |Tulajdonos neve – példa |
  |---------|---------|---------|---------|
  |Helyi felhasználói felület| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Blob Storage|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |Több-SAN önálló tanúsítvány|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

További információ: a [tanúsítványokra vonatkozó követelmények](../../articles/databox-online/azure-stack-edge-gpu-certificate-requirements.md).

## <a name="add-certificates-to-device"></a>Tanúsítványok hozzáadása az eszközhöz

Saját tanúsítványokat is használhat a helyi webes felhasználói felület eléréséhez és a blob Storage eléréséhez.

> [!IMPORTANT]
> Ha az eszköz neve vagy a DNS-tartomány módosul, új tanúsítványokat kell létrehozni. Az ügyféltanúsítványok és az eszközök tanúsítványait ezután frissíteni kell az új eszköznév és a DNS-tartománnyal.

A saját tanúsítványának az eszközhöz való hozzáadásához kövesse az alábbi lépéseket:

1. Lépjen a   >  **tanúsítványok** kezelése gombra.

   A **név** az eszköz nevét jeleníti meg. A **DNS-tartomány** a DNS-kiszolgáló tartománynevét jeleníti meg.

   A képernyő alján a jelenleg használatban lévő tanúsítványok láthatók. Új eszköz esetén megjelenik a rendelés feldolgozása során létrehozott önaláírt tanúsítványok.

   ![Data Box eszköz tanúsítványok lapja](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. Ha módosítania kell a **nevet** (eszköznév) vagy a **DNS-tartományt** (az eszköz DNS-kiszolgálójának tartománya), tegye meg most a tanúsítvány hozzáadása előtt. Ezután válassza az **Alkalmaz** lehetőséget.

   A tanúsítványt meg kell változtatni, ha az eszköz neve vagy a DNS-tartománynév megváltozik.

   ![Új eszköznév és DNS-tartomány alkalmazása Data Box](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. Tanúsítvány hozzáadásához válassza a tanúsítvány **hozzáadása** lehetőséget a **tanúsítvány hozzáadása** panel megnyitásához. Ezután válassza ki a **tanúsítvány típusát** : **blob Storage** vagy **helyi webes felhasználói felület**.

   ![Tanúsítványok hozzáadása a Data Box eszköz tanúsítványok lapján](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. Válassza ki a tanúsítványfájl ( `.pfx` formátumban), és adja meg a tanúsítvány exportálásakor beállított jelszót. Ezután válassza az **érvényesítés & Hozzáadás** lehetőséget.

   ![BLOB-végponti tanúsítvány Data Boxba való felvételének beállításai](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   A tanúsítvány sikeres hozzáadása után a **tanúsítványok** képernyő az új tanúsítvány ujjlenyomatát jeleníti meg. A tanúsítvány állapota **érvényes**.

   ![A sikeresen felvett érvényes új tanúsítvány](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. A tanúsítvány részleteinek megtekintéséhez válassza ki a tanúsítvány nevét, majd kattintson rá. A tanúsítvány egy év után lejár.

   ![Data Box eszköz tanúsítványának részleteinek megtekintése](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. Ha módosította a helyi webes felhasználói felület tanúsítványát, újra kell indítania a böngészőt, majd a helyi webes felhasználói felületet. Ez a lépés az SSL-gyorsítótárral kapcsolatos problémák elkerüléséhez szükséges.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. Telepítse az új tanúsítványt a használni kívánt ügyfélszámítógépen a helyi webes felhasználói felület eléréséhez. Útmutatásért lásd alább a [tanúsítványok importálása az ügyfélre](#import-certificates-to-client)című szakaszt.


## <a name="import-certificates-to-client"></a>Tanúsítványok importálása az ügyfélbe

Miután hozzáadta a tanúsítványt a Data Box eszközhöz, importálnia kell a tanúsítványt az eszköz eléréséhez használt ügyfélszámítógépre. Importálja a tanúsítványt a megbízható legfelső szintű hitelesítésszolgáltató tárolóba a helyi gépen.

Ha tanúsítványt szeretne importálni egy Windows-ügyfélen, kövesse az alábbi lépéseket:

1. A Fájlkezelőben kattintson a jobb gombbal a tanúsítványfájl ( `.cer` formátum) elemre, majd válassza a **tanúsítvány telepítése** lehetőséget. Ez a művelet elindítja a tanúsítvány importálása varázslót.

    ![1. tanúsítvány importálása](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. Az **áruház helye** területen válassza a **helyi számítógép** lehetőséget, majd kattintson a **tovább** gombra.

    ![A tanúsítvány importálása varázslóban válassza a helyi gép tároló helye lehetőséget.](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. Válassza **a minden tanúsítvány tárolása a következő tárolóban** lehetőséget, válassza a **megbízható legfelső szintű hitelesítésszolgáltató** lehetőséget, majd kattintson a **tovább** gombra.

   ![Válassza ki a megbízható legfelső szintű hitelesítésszolgáltatók tárolót a tanúsítvány importálása varázslóban.](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. Tekintse át a beállításokat, és kattintson a **Befejezés gombra**. Egy üzenet jelzi, hogy az importálás sikeres volt.

   ![Tekintse át a tanúsítvány beállításait, és fejezze be a tanúsítvány importálása varázslót](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>Visszaállítása az alapértelmezett tanúsítványokra

Mielőtt visszaadja az eszközt az Azure-adatközpontnak, vissza kell állítania a rendelés feldolgozásakor generált eredeti tanúsítványokat.

Az alábbi lépéseket követve visszaállíthatja a rendelés feldolgozása során létrehozott tanúsítványokat:

1. Lépjen a   >  **tanúsítványok** kezelése elemre, és válassza a **tanúsítványok visszaállítása** lehetőséget.

   A tanúsítványok visszaállítása a rendelés feldolgozása során létrehozott önaláírt tanúsítványok használatára. A saját tanúsítványok el lesznek távolítva az eszközről.

   ![A tanúsítványok visszavonása beállítás a Data Box eszköz tanúsítványainak kezelése elemnél](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. A tanúsítvány-visszaállítás sikeres befejezése után lépjen a **Leállítás vagy az újraindítás** lehetőségre, és válassza az **Újraindítás** lehetőséget. Ez a lépés az SSL-gyorsítótárral kapcsolatos problémák elkerüléséhez szükséges.

   ![A helyi webes felhasználói felület leállítása vagy újraindítása a tanúsítványok Data Box eszközön való visszaállítása után](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   Várjon néhány percet, majd jelentkezzen be újra a helyi webes felhasználói felületen.
