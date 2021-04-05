---
title: Azure Cloud Services (bővített támogatás) szerepkör-séma | Microsoft Docs
description: A Cloud Services szerepkör-sémájával kapcsolatos információk (kiterjesztett támogatás)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2567f5bb817a34f6274d5e265a266d67a9c81413
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744446"
---
# <a name="azure-cloud-services-extended-support-config-role-schema"></a>Azure Cloud Services (bővített támogatás) konfigurációs szerepkör sémája

A `Role` konfigurációs fájl eleme határozza meg a szolgáltatás egyes szerepköreihez telepítendő szerepkör-példányok számát, a konfigurációs beállítások értékeit, valamint a szerepkörhöz társított tanúsítványok ujjlenyomatai megfelelnek.

Az Azure szolgáltatás konfigurációs sémájával kapcsolatos további információkért lásd a [Cloud Service (bővített támogatás) konfigurációs sémát](schema-cscfg-file.md). További információ az Azure-szolgáltatás definíciós sémájáról: [Cloud Service (bővített támogatás) definíciós sémája](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> szerepkör elem
Az alábbi példa az `Role` elemet és annak alárendelt elemeit mutatja be.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Az alábbi táblázat az elem attribútumait ismerteti `Role` .

| Attribútum | Leírás |
| --------- | ----------- |
| name   | Kötelező. Megadja a szerepkör nevét. A névnek meg kell egyeznie a szolgáltatás definíciós fájljában szereplő szerepkörhöz megadott névvel.|
| vmName | Választható. Megadja a virtuális gép DNS-nevét. A névnek 10 karakternél rövidebbnek kell lennie.|

Az alábbi táblázat az elem alárendelt elemeit ismerteti `Role` .

| Elem | Leírás |
| ------- | ----------- |
| példányszám | Kötelező. A szerepkörhöz telepítendő példányok számát adja meg. A példányok számát az attribútum egész szám határozza meg `count` .|
| Beállítás   | Választható. Megadja a beállítás nevét és értékét egy szerepkörhöz tartozó beállítások gyűjteményében. A beállítás nevét az attribútum egy karakterlánca határozza meg `name` , a beállítás értékét pedig egy karakterlánc adja meg az `value` attribútumhoz.|
| Tanúsítvány | Választható. A szerepkörhöz társítandó szolgáltatás tanúsítványának nevét, ujjlenyomatát és algoritmusát adja meg. A tanúsítvány nevét az attribútum egy karakterlánca határozza meg `name` . A tanúsítvány ujjlenyomatát olyan hexadecimális számokból álló karakterlánc határozza meg, amely nem tartalmaz szóközt az `thumbprint` attribútumhoz. A hexadecimális számoknak számjegyek és nagybetűs alfanumerikus karakterek használatával kell szerepelniük. A tanúsítvány-algoritmust az attribútum karakterlánca határozza meg `thumbprintAlgorithm` .|

## <a name="see-also"></a>Lásd még
A [Cloud Service (bővített támogatás) konfigurációs sémája](schema-cscfg-file.md).