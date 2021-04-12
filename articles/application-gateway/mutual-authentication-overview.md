---
title: Az Azure-Application Gateway kölcsönös hitelesítésének áttekintése
description: Ez a cikk áttekintést nyújt a Application Gateway kölcsönös hitelesítéséről.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231440"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>Application Gateway (előzetes verzió) kölcsönös hitelesítésének áttekintése

A kölcsönös hitelesítés vagy az ügyfél-hitelesítés lehetővé teszi a Application Gateway számára az ügyfél-küldő kérelmek hitelesítését. Általában csak az ügyfél hitelesíti a Application Gateway; a kölcsönös hitelesítés lehetővé teszi, hogy mind az ügyfél, mind a Application Gateway hitelesítsék egymást. 

> [!NOTE]
> Javasoljuk, hogy a TLS 1,2-et a kölcsönös hitelesítéssel használja, mivel a TLS 1,2 a jövőben lesz felhatalmazva. 

## <a name="mutual-authentication"></a>Kölcsönös hitelesítés

Application Gateway támogatja a tanúsítvány-alapú kölcsönös hitelesítést, ahol feltölthet egy megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (oka) t a Application Gateway, és az átjáró ezt a tanúsítványt fogja használni az átjárónak küldött kérést küldő ügyfél hitelesítéséhez. A IoT használatának és a megnövekedett biztonsági követelményeknek köszönhetően a kölcsönös hitelesítés lehetővé teszi, hogy felügyelje és szabályozza, hogy mely ügyfelek tudnak kommunikálni a Application Gatewayával. 

A kölcsönös hitelesítés konfigurálásához egy megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra van szükség az SSL-profil ügyfél-hitelesítés részeként való feltöltéshez. Ezt követően az SSL-profilt hozzá kell rendelni egy figyelőhöz a kölcsönös hitelesítés konfigurálásának befejezéséhez. A feltöltött ügyféltanúsítványt mindig a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványnak kell lennie. Emellett feltölthet egy tanúsítványláncot is, de a láncnak tartalmaznia kell egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt is, a több köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány mellett. 

Ha például az ügyféltanúsítvány egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, több közbenső HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt és egy levél tanúsítványát tartalmazza, akkor győződjön meg arról, hogy a legfelső szintű hitelesítésszolgáltatói tanúsítvány és az összes köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány fel van töltve egyetlen fájlba Application Gateway. A megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának kibontásával kapcsolatos további információkért lásd: [a megbízható ügyfél hitelesítésszolgáltatói tanúsítványainak kinyerése](./mutual-authentication-certificate-management.md).

Ha legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI és köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal rendelkező tanúsítványláncot tölt fel, a tanúsítványláncot PEM-vagy CER-fájlként kell feltölteni az átjáróra. 

> [!NOTE] 
> A kölcsönös hitelesítés csak Standard_v2 és WAF_v2 SKU-ban érhető el. 

### <a name="certificates-supported-for-mutual-authentication"></a>Kölcsönös hitelesítéshez támogatott tanúsítványok

Application Gateway a következő típusú tanúsítványokat támogatja:

- CA (hitelesítésszolgáltató) tanúsítvány: A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány egy hitelesítésszolgáltató (CA) által kiadott digitális tanúsítvány.
- Önaláírt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok: az ügyfél böngészője nem bízik meg ezekkel a tanúsítványokkal, és figyelmezteti a felhasználót, hogy a virtuális szolgáltatás tanúsítványa nem része egy megbízhatósági láncnak. Az önaláírt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok olyan tesztelési és környezeti környezetekben használhatók, ahol a rendszergazdák vezérelhetik az ügyfeleket, és biztonságosan kihagyhatják a böngésző biztonsági riasztásait. Az éles munkaterhelések soha nem használhatnak önaláírt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat.

A kölcsönös hitelesítés beállításával kapcsolatos további információkért lásd: [a kölcsönös hitelesítés konfigurálása Application Gateway](./mutual-authentication-portal.md)használatával.

> [!IMPORTANT]
> Kölcsönös hitelesítés használata esetén ügyeljen arra, hogy feltöltse a teljes megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványláncot a Application Gatewayba. 

## <a name="additional-client-authentication-validation"></a>További ügyfél-hitelesítés ellenőrzése

### <a name="verify-client-certificate-dn"></a>Ügyféltanúsítvány megkülönböztető hitelesítésének ellenőrzése

Lehetősége van arra, hogy ellenőrizze az ügyféltanúsítvány azonnali kiállítóját, és csak azt engedélyezze a Application Gateway számára, hogy megbízzon a kiállítóban. Ez a beállítás alapértelmezés szerint ki van kapcsolva, de a portál, a PowerShell vagy az Azure CLI használatával engedélyezheti. 

Ha úgy dönt, hogy engedélyezi a Application Gateway az ügyféltanúsítvány azonnali kiállítójának ellenőrzéséhez, az alábbi módon állapíthatja meg, hogy az ügyféltanúsítvány kiállítójának megkülönböztető nevét a rendszer kinyerje a feltöltött tanúsítványokból. 
* **1. forgatókönyv:** Tanúsítványlánc tartalma: főtanúsítvány – köztes tanúsítvány – levél típusú tanúsítvány 
    * A *köztes tanúsítvány* tulajdonosának neve az a Application Gateway, amelyet az ügyféltanúsítvány kiállítói megkülönböztető neveként fog kinyerni, és a rendszer ellenőrzi. 
* **2. forgatókönyv:** Tanúsítványlánc tartalma: főtanúsítvány – intermediate1 tanúsítvány – intermediate2 tanúsítvány – levél tanúsítványa
    * Az *Intermediate2-tanúsítvány* tulajdonosának neve a kinyert ügyfél-tanúsítvány kiállítójának megkülönböztető neve lesz, és a rendszer ellenőrzi a-t. 
* **3. forgatókönyv:** Tanúsítványlánc tartalma: főtanúsítvány – levél típusú tanúsítvány 
    * A *Főtanúsítvány* tulajdonosának neve kibontásra kerül, és az ügyfél-tanúsítvány kiállítójának DN-ként lesz használatban.
* **4. forgatókönyv:** Ugyanazon a fájlban több tanúsítványlánc azonos hosszúságú. Az 1. lánc a főtanúsítvány intermediate1 tanúsítványát tartalmazza. A 2. lánc a főtanúsítvány intermediate2 tanúsítványát tartalmazza. 
    * Az *Intermediate1-tanúsítvány* tulajdonosának neve az ügyféltanúsítvány KIÁLLÍTÓjának DN-ként lesz kibontva.  
* **5. forgatókönyv:** Több, különböző hosszúságú tanúsítványlánc található ugyanabban a fájlban. Az 1. lánc a főtanúsítvány intermediate1 tanúsítványát tartalmazza. A 2. lánc a főtanúsítvány intermediate2 tanúsítvány-intermediate3 tanúsítványát tartalmazza. 
    * Az *Intermediate3-tanúsítvány* tulajdonosának neve az ügyféltanúsítvány KIÁLLÍTÓjának DN-ként lesz kibontva. 

> [!IMPORTANT]
> A fájlok csak egy tanúsítványlánc feltöltését javasoljuk. Ez különösen akkor fontos, ha engedélyezi az ügyféltanúsítvány megkülönböztető hitelesítésének ellenőrzését. Ha több tanúsítványláncot tölt fel egyetlen fájlban, akkor a négy vagy öt forgatókönyvben fog megjelenni, és a sorban később, amikor a bemutatott ügyféltanúsítvány nem egyezik a láncokból kinyert ügyféltanúsítvány kiállítójának DN Application Gatewayával. 

A megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványainak kinyerésével kapcsolatos további információkért lásd: [a megbízható ügyfél hitelesítésszolgáltatói tanúsítványainak kinyerése](./mutual-authentication-certificate-management.md).

## <a name="server-variables"></a>Kiszolgálói változók 

A kölcsönös hitelesítéssel további kiszolgálói változók találhatók, amelyek segítségével átadhatja az ügyféltanúsítvány információit a Application Gateway mögött lévő háttér-kiszolgálókra. További információ arról, hogy mely kiszolgálói változók érhetők el, és hogyan használhatók ezek a [kiszolgálók változói](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview).

## <a name="next-steps"></a>Következő lépések

A kölcsönös hitelesítés megismerése után lépjen a [Application Gateway konfigurálása kölcsönös hitelesítéssel a PowerShellben](./mutual-authentication-powershell.md) , és hozzon létre egy Application Gateway a kölcsönös hitelesítés használatával. 

