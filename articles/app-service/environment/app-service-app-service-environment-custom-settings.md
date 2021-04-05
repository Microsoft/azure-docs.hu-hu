---
title: Egyéni beállítások konfigurálása
description: Konfigurálja a teljes Azure App Service környezetre vonatkozó beállításokat. Megtudhatja, hogyan teheti meg Azure Resource Manager-sablonokkal.
author: ccompy
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 5c1e81d02aa35a40a296f04e456be09eeed10331
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226389"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>App Service környezetek egyéni konfigurációs beállításai
## <a name="overview"></a>Áttekintés
Mivel App Service környezetek (ASE-EK) egyetlen ügyfél számára vannak elkülönítve, bizonyos konfigurációs beállítások csak App Service környezetekre alkalmazhatók. Ez a cikk a App Service környezetekhez elérhető különböző testreszabásokat dokumentálja.

Ha nem rendelkezik App Service Environmentval, tekintse meg a [app Service Environment létrehozása](app-service-web-how-to-create-an-app-service-environment.md)című témakört.

App Service Environment testreszabásokat az új **clusterSettings** attribútumban található tömb használatával tárolhatja. Ez az attribútum a *hostingEnvironments* Azure Resource Manager entitás "Properties" szótárában található.

A következő rövidített Resource Manager-sablon a **clusterSettings** attribútumot jeleníti meg:

```json
"resources": [
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "workerPools": [ ...],
        etc...
    }
}
```

A **clusterSettings** attribútum egy Resource Manager-sablonban is szerepelhet a app Service Environment frissítéséhez.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>App Service Environment frissítése a Azure Erőforrás-kezelő használatával
Azt is megteheti, hogy a [Azure erőforrás-kezelő](https://resources.azure.com)használatával frissíti a app Service Environment.  

1. A erőforrás-kezelő területen lépjen a app Service Environment (**előfizetések**  >  **resourceGroups**  >  **providers**  >  **Microsoft. Web**  >  **hostingEnvironments**) csomópontra. Ezután kattintson a frissíteni kívánt App Service Environmentra.
2. A jobb oldali ablaktáblán kattintson az **olvasás/írás** lehetőségre a felső eszköztáron a erőforrás-kezelő interaktív szerkesztésének engedélyezéséhez.  
3. Kattintson a kék **Szerkesztés** gombra, hogy a Resource Manager-sablon szerkeszthető legyen.
4. Görgessen a jobb oldali ablaktábla aljára. A **clusterSettings** attribútum a legalul található, ahol megadhatja vagy frissítheti az értékét.
5. Írja be (vagy másolja és illessze be) a kívánt konfigurációs értékek tömbjét a **clusterSettings** attribútumba.  
6. Kattintson a jobb oldali ablaktábla tetején található zöld **put** gombra, hogy véglegesítse a módosítást a app Service Environment.

A módosítás elküldése azonban körülbelül 30 percet vesz igénybe, és megszorozza a változás életbe léptetéséhez szükséges App Service Environment számával.
Ha például egy App Service Environment négy előtérrel rendelkezik, a konfigurációs frissítés befejezéséhez körülbelül két órát vesz igénybe. A konfiguráció változása közben a rendszer nem végez más skálázási vagy konfigurációs módosítási műveletet a App Service Environment.

## <a name="enable-internal-encryption"></a>Belső titkosítás engedélyezése

A App Service Environment fekete Box-rendszerként működik, ahol a belső összetevők vagy a rendszeren belüli kommunikáció nem látható. A nagyobb átviteli sebesség engedélyezéséhez a titkosítás alapértelmezés szerint nincs engedélyezve a belső összetevők között. A rendszer biztonságban van, mivel a forgalom nem érhető el a figyeléshez vagy a hozzáféréshez. Ha a megfelelőségi követelmény azonban az, hogy az adatok elérési útja teljes titkosítást igényel a végponttól egészen a végéig, a teljes adatelérési út titkosítása clusterSetting is engedélyezhető.  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
Ha a InternalEncryption értéke TRUE (igaz) értékre van állítva, a belső hálózati forgalmat az előtérben és a dolgozók között titkosítja, titkosítja a lapozófájlt, és titkosítja a munkavégző lemezeket. A InternalEncryption-clusterSetting engedélyezése után hatással lehet a rendszer teljesítményére. Ha a módosítást engedélyezi a InternalEncryption engedélyezéséhez, a beadása instabil állapotban lesz, amíg a változást teljes mértékben nem propagálja. A változtatások teljes propagálása eltarthat néhány óráig, attól függően, hogy hány példányt használ a központilag. Azt javasoljuk, hogy a InternalEncryption ne engedélyezze a központilag, amíg használatban van. Ha a InternalEncryption-t aktív használatú benyújtó eszközön kell engedélyeznie, javasoljuk, hogy a művelet befejeződése előtt irányítsa át a forgalmat egy biztonsági mentési környezetbe. 


## <a name="disable-tls-10-and-tls-11"></a>A TLS 1.0 és a TLS 1.1 letiltása

Ha egy alkalmazáson keresztül szeretné kezelni a TLS-beállításokat, akkor használhatja a [TLS-beállítások érvényesítése](../configure-ssl-bindings.md#enforce-tls-versions) dokumentációban ismertetett útmutatást. 

Ha le szeretné tiltani az összes bejövő TLS 1,0-és TLS 1,1-forgalmat egy kiegészítő csomag összes alkalmazásához, a következő **clusterSettings** -bejegyzést állíthatja be:

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

A beállítás neve 1,0, de ha be van állítva, akkor a TLS 1,0 és a TLS 1,1 is letiltja.

## <a name="change-tls-cipher-suite-order"></a>TLS titkosítási csomag sorrendjének módosítása
A benyújtó a titkosítási csomag alapértelmezett értékről történő módosítását támogatja. A több-bérlős szolgáltatásban használt titkosítási algoritmusok alapértelmezett készlete ugyanaz. A titkosító csomagok módosítása hatással van egy teljes App Service központi telepítésre, ami csak az egybérlős bevezetésben lehetséges. A kiegészítő csomaghoz két titkosító csomag szükséges; TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 és TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256. Ha a szolgáltatót a legerősebb és legkevesebb titkosítási csomaggal szeretné üzemeltetni, akkor csak a két szükséges titkosítási algoritmust használja. Ha úgy szeretné konfigurálni a bejelentését, hogy csak a szükséges titkosítási algoritmusokat használja, módosítsa a **clusterSettings** az alább látható módon. 

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
    }
],
```

> [!WARNING]
> Ha helytelen értékek vannak beállítva az SChannel által nem értelmezhető titkosítási csomaghoz, akkor a kiszolgálóval folytatott összes TLS-kommunikáció leállhat. Ebben az esetben el kell távolítania a *FrontEndSSLCipherSuiteOrder* bejegyzést a **clusterSettings** -ből, és el kell küldenie a frissített Resource Manager-sablont, hogy vissza lehessen térni az alapértelmezett titkosítási csomag beállításaihoz.  Körültekintően használja ezt a funkciót.

## <a name="get-started"></a>Bevezetés
Az Azure rövid útmutató Resource Manager-sablon webhelye tartalmaz egy [app Service Environment létrehozásához](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)szükséges alapdefiníciót tartalmazó sablont.

<!-- LINKS -->

<!-- IMAGES -->
