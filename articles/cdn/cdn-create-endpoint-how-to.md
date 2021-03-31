---
title: Azure CDN végpont létrehozása | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre új Azure Content Delivery Network-(CDN-) végpontot, beleértve a speciális beállításokat is.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/12/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 7bbd54ed68863a4704319a25af96b6463ad2377b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539707"
---
# <a name="create-an-azure-cdn-endpoint"></a>Azure CDN végpont létrehozása
Ez a cikk az [Azure Content Delivery Network-(CDN-)](cdn-overview.md) VÉGPONT meglévő CDN-profilban való létrehozásának összes beállítását ismerteti. Miután létrehozott egy profilt és egy végpontot, megkezdheti a tartalom továbbítását az ügyfeleknek. A profil és a végpont létrehozásával kapcsolatos rövid útmutató: gyors üzembe helyezés [Azure CDN profil és végpont létrehozása](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Előfeltételek
CDN-végpont létrehozása előtt létre kell hoznia legalább egy CDN-profilt, amely egy vagy több CDN-végpontot tartalmazhat. Ha rendszerezni szeretné a CDN-végpontokat internetes tartomány, webalkalmazás vagy más feltétel alapján, több profilt is használhat. Mivel a CDN díjszabása a CDN-profil szintjén lesz alkalmazva, több CDN-profilt is létre kell hoznia, ha Azure CDN árképzési szint kombinációját kívánja használni. CDN-profil létrehozásával kapcsolatban tekintse meg [az új CDN-profil létrehozása](cdn-create-new-endpoint.md#create-a-new-cdn-profile)című témakört.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-a-new-cdn-endpoint"></a>Új CDN-végpont létrehozása

1. Az [Azure Portalon](https://portal.azure.com) lépjen a CDN-profilra. Lehetséges, hogy a profilt az előző lépésben az irányítópulton rögzítette. Ha mégsem, akkor a **Minden szolgáltatás**, majd a **CDN-profilok** elemre kattintva megkeresheti. A **CDN-profilok** ablaktáblán válassza ki azt a profilt, amelyhez a végpontot hozzá szeretné adni. 
   
    Megjelenik a CDN-profil ablaktábla.

2. Válassza ki a **Végpont** beállítását.
   
    ![CDN – végpont kiválasztása](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    Megjelenik a **Végpont hozzáadása** oldal.
   
    ![Végpont hozzáadása lap](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. A **Név** mezőben adjon meg egy egyedi nevet a CDN-végpont számára. A rendszer ezt a nevet használja a gyorsítótárazott erőforrások eléréséhez a tartományban _\<endpointname>_ . azureedge.net.

4. A **forrás típusa mezőben** válasszon a következő eredetű típusok közül: 
   - **Tároló** az Azure Storage-hoz
   - Azure Cloud Services **Cloud Service**
   - Az Azure **Webalkalmazása** Web Apps
   - Bármely más nyilvánosan elérhető forrás webkiszolgáló (az Azure-ban vagy máshol üzemeltetett) **Egyéni forrása**

5. A **Origin hostname (forrás neve**) mezőben válassza ki vagy adja meg a forrás-kiszolgáló tartományát. A legördülő listában a 4. lépésben megadott típusú összes rendelkezésre álló forráskiszolgáló látható. Ha az **Egyéni forrás** lehetőséget választotta a forrás típusaként, adja meg az egyéni forráskiszolgáló tartományát.
    
6. A **forrás elérési útja** mezőbe írja be a gyorsítótárazni kívánt erőforrások elérési útját. Ha engedélyezni szeretné az 5. lépésben megadott tartomány bármely erőforrásának gyorsítótárazását, hagyja üresen ezt a beállítást.
    
7. A **Forrás állomásfejléce** mezőben adja meg azt az állomásfejlécet, amelyet az Azure CDN-nek minden egyes kérelemmel el kell küldenie, vagy hagyja meg az alapértelmezett értéket.
   
   > [!NOTE]
   > Bizonyos típusú források – például az Azure Storage és a Web Apps – esetén az állomásfejlécnek egyeznie kell a forrás tartományával. Hacsak a forrás a tartománytól eltérő állomásfejléc használatát nem igényli, hagyja meg az alapértelmezett értéket.
   > 
    
8. A **protokoll** és a **Forrás port** esetében adja meg az erőforrások eléréséhez használandó protokollokat és portokat a forrás-kiszolgálón. Legalább egy protokollt (a HTTP vagy a HTTPS protokollt) ki kell választani. A HTTPS-tartalmak eléréséhez használja a CDN által biztosított tartományt ( _\<endpointname>_ . azureedge.net). 
   
   > [!NOTE]
   > A **forrásport** értéke csak azt a portot határozza meg, amelyet a végpont az információk forrás-kiszolgálóról való lekéréséhez használ. Magát a végpontot a végfelhasználók – a **Forrásport** értékétől függetlenül – csak az alapértelmezett HTTP- és HTTPS-porton (azaz a 80-as és a 443-as porton) érik el.  
   > 
   > Az **Akamai Azure CDN**-profilok végpontjai esetén a forrásportok számára nem áll rendelkezésre a teljes TCP-porttartomány. A nem engedélyezett forrásportok listáját lást: [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Az Akamai Azure CDN engedélyezett forrásportjai).  
   > 
   > Azure CDN egyéni tartományok HTTPS-támogatása nem támogatott a **Akamai-termékek Azure CDN** . További információkért lásd: [HTTPS konfigurálása az Azure CDN egyéni tartományon](cdn-custom-ssl.md).
    
9. A **alkalmazáshoz optimalizált** optimalizálási típushoz válassza ki azt a forgatókönyvet és típust, amelyet a végpontnak teljesíteni kíván. További információ: [optimalizálás Azure CDN a tartalom kézbesítésének típusa](cdn-optimization-overview.md).

    A következő optimalizálási típus beállításai támogatottak a profil típusa szerint:
    - **Azure CDN standard a Microsoft** profiljaiból:
       - [**Általános webes kézbesítés**](cdn-optimization-overview.md#general-web-delivery)

    - **Azure CDN a** Verizon és a **prémium szintű Azure CDN a Verizon** -profiloktól:
       - [**Általános webes kézbesítés**](cdn-optimization-overview.md#general-web-delivery)
       - [**Dinamikus helygyorsítás**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Azure CDN standard a Akamai-** profilokból:
       - [**Általános webes kézbesítés**](cdn-optimization-overview.md#general-web-delivery)
       - [**Általános médiaadatfolyam-továbbítás**](cdn-optimization-overview.md#general-media-streaming)
       - [**Videó igény szerinti médiaadatfolyam-továbbításhoz**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Nagyméretű fájlok letöltése**](cdn-optimization-overview.md#large-file-download)
       - [**Dinamikus helygyorsítás**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Új végpont létrehozásához kattintson a **Hozzáadás** gombra.
   
    A végpont a létrehozás után megjelenik a profil végpontjainak listájában.
    
    ![CDN-végpont](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Mivel némi időre van szükség a regisztráció propagálásához, a végpont nem vehető használatba azonnal: 
    - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
    - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
    - A verizontól a Verizon és a **Azure CDN Premium** **Azure CDN standard** szint esetén a propagálás általában 30 percen belül befejeződik. 
   
    Ha a CDN-tartománynevet úgy kísérli meg használni, hogy a végpont konfigurációja propagálva legyen a jelenléti pontra (POP) szolgáló kiszolgálókra, előfordulhat, hogy HTTP 404-válasz állapotot kap. Ha a végpont létrehozása óta több óra van, és továbbra is 404 választ kap, tekintse meg [a 404 állapotkódot visszaadó Azure CDN-végpontok hibaelhárítása](cdn-troubleshoot-endpoint.md)című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nem szükséges végpontot törölni, válassza ki, majd válassza a **Törlés** lehetőséget. 

## <a name="next-steps"></a>Következő lépések
Az egyéni tartományokkal kapcsolatos további tudnivalókért tekintse meg az egyéni tartomány a CDN-végponthoz való hozzáadásával foglalkozó oktatóanyagot.

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](cdn-map-content-to-custom-domain.md)


