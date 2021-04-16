---
title: azcopy login | Microsoft Docs
description: Ez a cikk az azcopy login parancs referenciainformációit biztosítja.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4861749d66e466bc3302553af8b3ed4919a72e0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503235"
---
# <a name="azcopy-login"></a>azcopy login

Az Azure Storage-Azure Active Directory eléréséhez bejelentkezik.

## <a name="synopsis"></a>Áttekintés

Jelentkezzen be a Azure Active Directory az Azure Storage-erőforrások eléréséhez.

Az Azure Storage-fiókhoz való jogosultsághoz hozzá kell rendelnie a **Storage-blobadatok** közreműködője szerepkört a felhasználói fiókhoz a Storage-fiók, a szülő erőforráscsoport vagy a szülő előfizetés kontextusában.

Ez a parancs az operációs rendszer beépített mechanizmusai segítségével gyorsítótárazza a titkosított bejelentkezési adatokat az aktuális felhasználó számára.

> [!IMPORTANT]
> Ha a parancssor használatával környezeti változót ad meg, az olvasható lesz a parancssori előzményekben. Érdemes lehet törölni a hitelesítő adatokat tartalmazó változókat a parancssori előzményekből. Ha meg kell tartania, hogy a változók ne jelenjenek meg az előzményekben, egy szkript használatával meg kell adnia a felhasználónak a hitelesítő adatait, és be kell állítania a környezeti változót.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Oktatóanyag: Helyszíni adatok áttelepítése felhőalapú tárolóba az AzCopy használatával](storage-use-azcopy-migrate-on-premises-data.md)
- [Adatok átvitele az AzCopy és a Blob Storage használatával](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

## <a name="examples"></a>Példák

Jelentkezzen be interaktívan úgy, hogy az alapértelmezett AAD-bérlőazonosító közös:

```azcopy
azcopy login
```

Jelentkezzen be interaktívan egy adott bérlőazonosítóval:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Jelentkezzen be egy virtuális gép (VM) rendszer által hozzárendelt identitásával:

```azcopy
azcopy login --identity
```

Jelentkezzen be egy virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatásidentitás ügyfél-azonosítójával:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Jelentkezzen be egy virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatásidentitás objektumazonosítójával:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Jelentkezzen be egy virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatásidentitás erőforrás-azonosítójával:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Bejelentkezés szolgáltatásnévként titkos ügyféltitk használatával: Állítsa a környezeti változót AZCOPY_SPA_CLIENT_SECRET titkos ügyfél titkos kódra a titkos kódon alapuló szolgáltatásnév-hitelesítéshez.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Jelentkezzen be szolgáltatásnévként egy tanúsítvánnyal és annak jelszavával:

Állítsa a környezeti AZCOPY_SPA_CERT_PASSWORD a tanúsítvány jelszavára a tanúsítványalapú szolgáltatásnév-hitelesítéshez:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

A `/path/to/my/cert` PEM- vagy PKCS12-fájlok elérési útjaként kezelje a következőt: . Az AzCopy nem éri el a rendszer tanúsítványtárolóját a tanúsítvány beszerzéséhez.

`--certificate-path` A kötelező tanúsítványalapú szolgáltatásnév-hitelesítéskor.

## <a name="options"></a>Beállítások

**--aad-endpoint sztring** A Azure Active Directory végpontot használja. Az alapértelmezett ( https://login.microsoftonline.com) a globális Azure-felhőhöz megfelelő. Ezt a paramétert az országos felhőben való hitelesítéskor állítsa be. A felügyeltszolgáltatás-identitáshoz nem szükséges.

**--application-id string** A felhasználó által hozzárendelt identitás alkalmazásazonosítója. Szolgáltatásnév-hitelesítéshez szükséges.

**--certificate-path sztring** Az SPN-hitelesítés tanúsítványának elérési útja. Tanúsítványalapú szolgáltatásnév-hitelesítéshez szükséges.

**--help**   help a `azcopy login` parancshoz.

**--identity (identitás)**   Jelentkezzen be a virtuális gép identitásával, más néven felügyeltszolgáltatás-identitással (MSI).

**--identity-client-id sztring** A felhasználó által hozzárendelt identitás ügyfél-azonosítója.

**--identity-object-id sztring** a felhasználó által hozzárendelt identitás objektumazonosítója.

**--identity-resource-id sztring** A felhasználó által hozzárendelt identitás erőforrás-azonosítója.

**--service-principal**   Jelentkezzen be egyszerű szolgáltatásnévvel (SPN) egy tanúsítvánnyal vagy titkos okkal. Az ügyfél titkos jelszavát vagy a tanúsítvány jelszavát a megfelelő környezeti változóban kell elhelyezni. Írja be az AzCopy env parancsot a környezeti változók nevének és leírásának a nevére.

**--tenant-id sztring** Azure Active Directory OAuth-eszköz interaktív bejelentkezéséhez használt bérlőazonosító.

## <a name="options-inherited-from-parent-commands"></a>A szülőparancsok által örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--cap-mbps lebegőpontos érték|Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső felsőértéktől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.|
|--output-type string|A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "text".|
|--trusted-microsoft-suffixes sztring   |További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  Az alapértelmezett érték a '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Az itt felsoroltak az alapértelmezett értékhez kerülnek. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
