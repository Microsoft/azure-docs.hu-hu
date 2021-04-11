---
title: Megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványlánc exportálása az ügyfél-hitelesítéshez
titleSuffix: Azure Application Gateway
description: Ismerje meg, hogyan exportálhat megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványláncot az ügyfél-hitelesítéshez az Azure Application Gateway
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 2329dc7426b223ef2c81dd0e2e607bccf73192e6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231456"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>Megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványlánc exportálása ügyfél-hitelesítéssel való használatra
Az ügyféllel való kölcsönös hitelesítés konfigurálásához vagy az ügyfél-hitelesítéshez Application Gateway megköveteli, hogy egy megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványlánc legyen feltöltve az átjáróra. Ha több tanúsítványlánc is van, akkor a láncokat külön kell létrehoznia, és fel kell töltenie őket különböző fájlként a Application Gateway. Ebből a cikkből megtudhatja, hogyan exportálhat egy megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványláncot, amelyet az ügyfél-hitelesítés konfigurációjában használhat az átjárón.  

## <a name="prerequisites"></a>Előfeltételek

A megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványlánc létrehozásához meglévő Ügyféltanúsítvány szükséges. 

## <a name="export-trusted-client-ca-certificate"></a>Megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának exportálása

A megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa szükséges az ügyfél-hitelesítés engedélyezéséhez Application Gatewayon. Ebben a példában egy TLS/SSL-tanúsítványt fogunk használni az ügyféltanúsítvány számára, exportálja a nyilvános kulcsot, majd exportálja a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat a nyilvános kulcsból a megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványainak beszerzéséhez. Ezután összefűzjük az összes ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt egy megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványláncot. 

Az alábbi lépések segítségével exportálhatja a. PEM vagy. cer fájlt a tanúsítványhoz:

### <a name="export-public-certificate"></a>Nyilvános tanúsítvány exportálása 

1. A .cer fájl tanúsítványból történő beszerzéséhez nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Keresse meg a tanúsítványt, amely jellemzően a "Certificates-current User\Personal\Certificates" elemben található, és kattintson a jobb gombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**. Ha nem találja a tanúsítványt a jelenlegi User\Personal\Certificates alatt, előfordulhat, hogy véletlenül megnyitotta a "tanúsítványok – helyi számítógép" kifejezést, és nem a "tanúsítványok – aktuális felhasználó" beállítást. Ha a Tanúsítványkezelőt a jelenlegi felhasználói hatókörben szeretné megnyitni a PowerShell használatával, írja be a *certmgr* a konzol ablakba.

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel: a Tanúsítványkezelő a kiválasztott tanúsítványokkal, valamint az összes feladattal rendelkező környezetfüggő menü, majd a kijelöltek exportálása.](./media/certificates-for-backend-authentication/export.png)

2. A varázslóban kattintson a **Tovább** gombra.
    > [!div class="mx-imgBorder"]
    > ![Tanúsítvány exportálása](./media/certificates-for-backend-authentication/exportwizard.png)

3. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.
    > [!div class="mx-imgBorder"]
    > ![Ne exportálja a titkos kulcsot](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra.
    > [!div class="mx-imgBorder"]
    > ![Base-64 kódolású](./media/certificates-for-backend-authentication/base64.png)

5. A **fájl exportálásához** **tallózással keresse** meg azt a helyet, ahová exportálni kívánja a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **tovább** gombra.

    > [!div class="mx-imgBorder"]
   > ![Képernyőfelvétel: a tanúsítvány-Exportálás varázsló, amelyen az exportálandó fájlt meg kell adni.](./media/certificates-for-backend-authentication/browse.png)

6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

    > [!div class="mx-imgBorder"]
    > ![A fájl exportálásának befejezése után a képernyőképen a tanúsítvány-Exportálás varázsló látható.](./media/certificates-for-backend-authentication/finish.png)

7. A tanúsítvány exportálása sikeresen megtörtént.

    > [!div class="mx-imgBorder"]
    > ![A képernyőkép a tanúsítvány-exportálás varázslót jeleníti meg a sikert jelző üzenettel.](./media/certificates-for-backend-authentication/success.png)

   Az exportált tanúsítvány ehhez hasonlóan néz ki:

    > [!div class="mx-imgBorder"]
    > ![A képernyőképen a tanúsítvány szimbóluma látható.](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (ok) exportálása a nyilvános tanúsítványból

Most, hogy exportálta a nyilvános tanúsítványát, most már exportálnia kell a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (oka) t a nyilvános tanúsítványból. Ha csak legfelső szintű HITELESÍTÉSSZOLGÁLTATÓval rendelkezik, csak a tanúsítványt kell exportálnia. Ha azonban 1 + közbenső hitelesítésszolgáltatóval rendelkezik, ezeket is exportálnia kell. 

1. A nyilvános kulcs exportálását követően nyissa meg a fájlt.

    > [!div class="mx-imgBorder"]
    > ![Engedélyezési tanúsítvány megnyitása](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![tudnivalók a tanúsítványról](./media/mutual-authentication-certificate-management/general.png)

1. A hitelesítésszolgáltató megtekintéséhez válassza a tanúsítvány elérési útja fület.

    > [!div class="mx-imgBorder"]
    > ![tanúsítvány részletei](./media/mutual-authentication-certificate-management/cert-details.png) 

1. Válassza ki a főtanúsítványt, és kattintson a **Tanúsítvány megtekintése** elemre.

    > [!div class="mx-imgBorder"]
    > ![tanúsítvány elérési útja](./media/mutual-authentication-certificate-management/root-cert.png) 

   Ekkor meg kell jelennie a főtanúsítvány adatainak.

    > [!div class="mx-imgBorder"]
    > ![tanúsítvány adatai](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. Válassza a **részletek** lapot, és kattintson a **Másolás fájlba..** . elemre.

    > [!div class="mx-imgBorder"]
    > ![legfelső szintű tanúsítvány másolása](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. Ekkor kibontotta a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány részleteit a nyilvános tanúsítványból. Ekkor megjelenik a **Tanúsítvány exportálása varázsló**. A tanúsítvány exportálása varázsló befejezéséhez kövesse az előző szakaszban (a[nyilvános tanúsítvány exportálása](./mutual-authentication-certificate-management.md#export-public-certificate)) 2-7. lépést. 

1. Most ismételje meg a 2-6. lépést a jelenlegi szakaszból ([CA-tanúsítványok exportálása a nyilvános tanúsítványból](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate)) az összes köztes hitelesítésszolgáltató számára az alap-64 kódolású X. 509 () összes köztes hitelesítésszolgáltatói tanúsítványának exportálásához. CER) formátum.

    > [!div class="mx-imgBorder"]
    > ![köztes tanúsítvány](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    Például megismételheti a 2-6-es lépéseket a *MSIT CAZ2* KÖZTes hitelesítésszolgáltatóról szóló szakaszból a saját tanúsítványként való kinyeréséhez. 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>Az összes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány összefűzése egyetlen fájlba

1. Futtassa az alábbi parancsot a korábban kibontott HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokkal. 

    Windows:
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    Linux:
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    Az eredményül kapott egyesített tanúsítványnak a következőhöz hasonlóan kell kinéznie:
    
    > [!div class="mx-imgBorder"]
    > ![egyesített tanúsítvány](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>Következő lépések

Most már rendelkezik a megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványainak láncával. Ezt hozzáadhatja az ügyfél-hitelesítési konfigurációhoz a Application Gatewayon, hogy engedélyezze a kölcsönös hitelesítést az átjáróval. Lásd: [kölcsönös hitelesítés konfigurálása Application Gateway használatával a portálon](./mutual-authentication-portal.md) vagy a [kölcsönös hitelesítés konfigurálása a Application Gateway és a PowerShell használatával](./mutual-authentication-powershell.md).

