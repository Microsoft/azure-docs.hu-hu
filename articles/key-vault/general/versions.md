---
title: Azure Key Vault verziók
description: A Azure Key Vault különböző verziói
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 2396aac67a17352987d9d6e3b49535c19ffe8917
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792378"
---
# <a name="key-vault-versions"></a>Key Vault-verziók

A Azure Key Vault újdonságai. Az új funkciók és Újdonságok az [Azure updates Key Vault csatornán](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)is bejelentve lesznek.

## <a name="june-2020"></a>2020. június

A Key Vault Azure Monitor jelenleg előzetes verzióban érhető el.  A Azure Monitor a Key Vault kérelmek, a teljesítmény, a hibák és a késés egységes áttekintésével biztosítja a kulcstartók átfogó figyelését. További információ: [Azure monitor for Key Vault (előzetes verzió).](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="may-2020"></a>2020. május

A "saját kulcs használata" (BYOK) Key Vault már általánosan elérhető. Tekintse meg a [Azure Key Vault BYOK specifikációját](../keys/byok-specification.md), és Ismerje meg, hogyan [importálhat HSM-védelemmel ellátott kulcsokat a Key Vaultba (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>2020. március

A privát végpontok már előzetes verzióban érhetők el. Az Azure Private link Service lehetővé teszi, hogy a virtuális hálózat privát végpontján keresztül hozzáférhessen Azure Key Vault és az Azure által üzemeltetett ügyfél-és partneri szolgáltatásokhoz.  Ismerje meg, hogyan [integrálhatja Key Vaulteit az Azure Private-hivatkozással](private-link-service.md).

## <a name="2019"></a>2019

- A következő generációs Azure Key Vault SDK-k kiadása. A használatra vonatkozó példákért tekintse meg a [Python](../secrets/quick-create-python.md), a [.net](../secrets/quick-create-net.md), a [Java](../secrets/quick-create-java.md)és a [Node.js](../secrets/quick-create-node.md) Azure Key Vault titkos útmutatóit
- Új Azure-szabályzatok a Key Vault-tanúsítványok kezeléséhez. Tekintse [meg a Key Vault Azure Policy beépített definícióit](../policy-reference.md).
- A Azure Key Vault virtuálisgép-bővítmény már általánosan elérhető.  Lásd: [Key Vault virtuálisgép-bővítmény a Linux](../../virtual-machines/extensions/key-vault-linux.md) rendszerhez és [Key Vault virtuálisgép-bővítmény a Windowshoz](../../virtual-machines/extensions/key-vault-windows.md).
- Az Azure Key Vault-alapú, eseményvezérelt titkok kezelése már elérhető a Azure Event Gridban. További információ: [a Event Grid sémája az eseményekhez a Azure Key Vault] (.. /.. /Event-Grid/Event-Schema-Key-Vault.MD], és megtudhatja, hogyan [fogadhat és reagálhat a Key Vault-értesítésekre Azure Event Grid](event-grid-tutorial.md)használatával.

## <a name="2018"></a>2018

Ebben az évben megjelent új funkciók és integrációk:

- Integráció a Azure Functionssal. A Key Vault-műveletek [Azure Functionsét](../../azure-functions/index.yml) kihasználó példát a [titkos kód elforgatásának automatizálása](../secrets/tutorial-rotation.md)című témakörben talál. 
- [Integráció a Azure Databrickssal](/azure/databricks/scenarios/store-secrets-azure-key-vault). Ezzel a Azure Databricks mostantól két típusú titkos hatókört támogat: a Azure Key Vault-alapú és a Databricks-alapú biztonsági mentést. További információ: [Azure Key Vault-alapú titkos kulcs hatókörének létrehozása](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Azure Key Vault virtuális hálózati szolgáltatási végpontok](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Az idei évben megjelent új funkciók:

- Felügyelt Storage-fiókok kulcsai. A Storage-fiók kulcsainak funkció az Azure Storage-nal való egyszerűbb integrációt teszi ki. További információért tekintse meg az Áttekintés témakört, amely a [felügyelt Storage-fiókok kulcsairól nyújt áttekintést](../secrets/overview-storage-keys.md).
- Helyreállítható törlés. A Soft-delete funkció javítja a kulcstartók és a Key Vault-objektumok adatvédelmi védelmét. További információért lásd az áttekintést [ismertető témakört.](./soft-delete-overview.md)

## <a name="2015"></a>2015

Az idei évben megjelent új funkciók:
- Tanúsítványkezelő. Szolgáltatásként hozzáadva a GA 2015-06-01-es verziójához, 2016. szeptember 26-án.

Az általános elérhetőség (2015-06-01-es verzió) a 2015. június 24-én jelent meg. Ebben a kiadásban a következő módosítások történtek: 
- Törölje a "use" mezőt az eltávolításhoz.
- Adatok lekérése a kulcsról – "használat" mező eltávolítva.
- Kulcs importálása egy tárolóba – "használat" mező eltávolítva.
- Egy kulcs visszaállítása – "használat" mező eltávolítva.     
- A "RSA_OAEP" elem "RSA-OAEP" értékre módosult RSA-algoritmusokhoz. Lásd: [a kulcsok, a titkok és a tanúsítványok ismertetése](about-keys-secrets-certificates.md).    
 
A második előzetes verzió (2015-02-01-es verzió – előzetes verzió) bejelentve: 2015. április 20. További információ: [REST API Update](/archive/blogs/kv/rest-api-update) blog post. A következő feladatok frissültek:
 
- Egy tár által hozzáadott többoldalas támogatásban lévő kulcsok listázása a művelethez.
- Egy kulccsal felvett művelet verzióinak listázása a kulcsok verzióinak listázásához.  
- Egy tár által hozzáadott tördelési támogatással rendelkező titkok listázása.
- Egy titkos hozzáadási művelet verzióinak listázása egy titkos kulcs verzióinak listázásához.  
- Minden művelet – új létrehozott/frissített időbélyegek az attribútumokhoz.  
- Hozzon létre egy titkos tartalmú tartalmat a titkokhoz.
- Hozzon létre egy kulccsal hozzáadott címkéket opcionális információként.
- Hozzon létre egy titkos címkével kiegészített címkéket opcionális információként.
- Kulcshoz hozzáadott címkék frissítése opcionális információként.
- Titkos címkék frissítése opcionális információként.
- A titkok maximális mérete 10 K és 25 K közötti értékre módosult. Lásd: [kulcsok, titkos kódok és tanúsítványok](about-keys-secrets-certificates.md).    
 
## <a name="2014"></a>2014
 
Az első előzetes verzió (2014-12-08-es verzió – előzetes verzió) a 2015. január 8-án jelent meg.  
 
## <a name="next-steps"></a>Következő lépések

- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-certificates.md)
- [Kulcsok](../keys/index.yml)
- [Titkos kódok](../secrets/index.yml)
- [Tanúsítványok](../certificates/index.yml)