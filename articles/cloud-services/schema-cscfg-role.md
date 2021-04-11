---
title: Azure Cloud Services (klasszikus) szerepkör-séma | Microsoft Docs
description: A szolgáltatás konfigurációs fájljának szerepkör eleme határozza meg, hogy az egyes szerepkörök, konfigurációs értékek és tanúsítványok ujjlenyomatai megfelelnek hány szerepkör-példány telepíthető.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd46a37ada1fb96797faee6c8491359561e065d7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934155"
---
# <a name="azure-cloud-services-classic-config-role-schema"></a>Azure Cloud Services (klasszikus) konfigurációs szerepkör sémája

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A `Role` konfigurációs fájl eleme határozza meg a szolgáltatás egyes szerepköreihez telepítendő szerepkör-példányok számát, a konfigurációs beállítások értékeit, valamint a szerepkörhöz társított tanúsítványok ujjlenyomatai megfelelnek.

További információ az Azure szolgáltatás konfigurációs sémájáról: [Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md). Az Azure szolgáltatás definíciós sémájával kapcsolatos további információkért lásd: [Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> Szerepkör elem
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

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)