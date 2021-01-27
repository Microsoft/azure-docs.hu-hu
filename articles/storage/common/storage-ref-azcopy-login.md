---
title: azcopy bejelentkezés | Microsoft Docs
description: Ez a cikk a azcopy login parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4740870dd2d9748aad55150ce1946e3eb666619
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878358"
---
# <a name="azcopy-login"></a>azcopy login

Bejelentkezik a Azure Active Directoryba az Azure Storage-erőforrások eléréséhez.

## <a name="synopsis"></a>Áttekintés

Az Azure Storage-erőforrások eléréséhez jelentkezzen be Azure Active Directoryba.

Az Azure Storage-fiókjának engedélyezéséhez hozzá kell rendelnie a **Storage blob-adatközreműködői** szerepkört a felhasználói fiókhoz a Storage-fiók, a szülő erőforráscsoport vagy a szülő-előfizetés kontextusában.

Ez a parancs az operációs rendszer beépített mechanizmusaival gyorsítótárazza az aktuális felhasználó titkosított bejelentkezési adatait.

> [!IMPORTANT]
> Ha egy környezeti változót a parancssor használatával állít be, akkor ez a változó a parancssori előzményekben olvasható. Érdemes lehet a parancssori előzményekből származó hitelesítő adatokat tartalmazó változókat törölni. Ahhoz, hogy a változók megjelenjenek az előzményekben, egy parancsfájl használatával megkérheti a felhasználótól a hitelesítő adataikat, és beállíthatja a környezeti változót.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="examples"></a>Példák

Jelentkezzen be interaktív módon az alapértelmezett HRE-bérlői AZONOSÍTÓval a common:

```azcopy
azcopy login
```

Interaktív bejelentkezés egy megadott bérlői AZONOSÍTÓval:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Bejelentkezés egy virtuális gép rendszer által hozzárendelt identitásával (VM):

```azcopy
azcopy login --identity
```

Jelentkezzen be a virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatás identitásának ügyfél-AZONOSÍTÓjának használatával:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Jelentkezzen be a virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatás identitásának azonosítójával:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Jelentkezzen be a virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatás identitásának erőforrás-azonosítójával:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Jelentkezzen be egyszerű szolgáltatásként egy ügyfél titkos kódjával: állítsa be a környezeti változót AZCOPY_SPA_CLIENT_SECRET a titkos kulcshoz tartozó egyszerű szolgáltatás hitelesítő adataihoz.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Jelentkezzen be egyszerű szolgáltatásként tanúsítvány és jelszó használatával:

Állítsa a környezeti változót a tanúsítvány-alapú egyszerű szolgáltatásnév hitelesítéséhez AZCOPY_SPA_CERT_PASSWORD a tanúsítvány jelszavára:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

A rendszer egy `/path/to/my/cert` PEM-vagy PKCS12/pfx-profil-fájl elérési útját kezeli. A AzCopy nem éri el a rendszertanúsítvány-tárolót a tanúsítvány beszerzéséhez.

`--certificate-path` a tanúsítványon alapuló egyszerű szolgáltatás hitelesítése esetén kötelező.

## <a name="options"></a>Beállítások

**--HRE-Endpoint** karakterlánc a használni kívánt Azure Active Directory végpont. Az alapértelmezett érték ( https://login.microsoftonline.com) megfelelő a globális Azure-felhőhöz. Adja meg ezt a paramétert a hitelesítéshez a nemzeti felhőben. Nem szükséges a Managed Service Identityhoz.

**--** a felhasználó által hozzárendelt identitáshoz tartozó alkalmazás-azonosító karakterlánc-alkalmazás azonosítója. Az egyszerű szolgáltatás hitelesítéséhez szükséges.

**--a tanúsítvány-elérésiút-** karakterlánc elérési útja az SPN-hitelesítéshez. A tanúsítványalapú szolgáltatásnév hitelesítéséhez szükséges.

**– Súgó**   a `azcopy login` parancshoz.

**--Identity**   Jelentkezzen be a virtuális gép identitásával, más néven felügyelt szolgáltatás identitásával (MSI).

**--Identity-ügyfél-azonosító** karakterlánc ügyfél-azonosító a felhasználó által hozzárendelt identitáshoz.

**--Identity-Object-ID karakterlánc-** objektum azonosítója a felhasználó által hozzárendelt identitáshoz.

**--Identity-Resource-id** karakterlánc-erőforrás azonosítója a felhasználó által hozzárendelt identitáshoz.

**--szolgáltatás – rendszerbiztonsági tag**   Jelentkezzen be az egyszerű szolgáltatásnév (SPN) használatával egy tanúsítvány vagy egy titkos kód segítségével. Az ügyfél titkos vagy tanúsítványának jelszavát a megfelelő környezeti változóba kell helyezni. Írja be a AzCopy env nevet, és tekintse meg a környezeti változók nevét és leírását.

**--bérlő-azonosító** sztring a OAuth-eszköz interaktív bejelentkezéshez használt Azure Active Directory bérlői azonosító.

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps lebegőpontos|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|
|--megbízható-Microsoft-utótagok karakterlánca   |További tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  Az alapértelmezett érték: "*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
