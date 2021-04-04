---
title: Egyéni tartománynév konfigurálása Cloud Services (klasszikus) | Microsoft Docs
description: A DNS-beállítások konfigurálásával megtudhatja, hogyan teheti elérhetővé Azure-alkalmazásait vagy-adatait az interneten egy egyéni tartományon.  Ezek a példák a Azure Portal használják.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: bced2345473dbcbb5b9adf0269de0bef0549e862
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742369"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service-classic"></a>Egyéni tartománynév konfigurálása Azure Cloud Service-hez (klasszikus)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Felhőalapú szolgáltatás létrehozásakor az Azure a **cloudapp.net** altartományához rendeli hozzá. Ha például a Cloud Service neve "contoso", a felhasználók az alkalmazáshoz hasonló URL-címen érhetik el az alkalmazást `http://contoso.cloudapp.net` . Az Azure egy virtuális IP-címet is hozzárendel.

Az alkalmazást ugyanakkor saját tartománynevén is közzéteheti, például **contoso.com**. Ez a cikk bemutatja, hogyan foglalhat le vagy konfigurálhat egyéni tartománynevet a Cloud Service webes szerepköreihez.

Már ismeri a CNAME és A rekordokat? [Ugorja át a magyarázatot](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Az ebben a feladatban ismertetett eljárások az Azure Cloud Servicesra vonatkoznak. App Services esetében lásd: [meglévő egyéni DNS-név leképezése az Azure web Appsra](../app-service/app-service-web-tutorial-custom-domain.md). A Storage-fiókok esetében tekintse [meg az Egyéni tartománynév konfigurálása az Azure Blob Storage-végponthoz](../storage/blobs/storage-custom-domain-name.md)című témakört.
> 
> 

<p/>

> [!TIP]
> Gyorsabb használat – az új Azure [interaktív](https://support.microsoft.com/kb/2990804)bemutató használatával!  Az Azure Cloud Services vagy az Azure websites egy beépülő modullal társítja az egyéni tartománynevet, és biztonságossá teszi a kommunikációt (TLS).
> 
> 

## <a name="understand-cname-and-a-records"></a>A CNAME és A rekordok ismertetése
A CNAME (vagy alias Records) és A Records is lehetővé teszi, hogy a tartománynevet egy adott kiszolgálóhoz társítsa (vagy ebben az esetben a szolgáltatásban), de másképp működnek. Az Azure Cloud Services használatával olyan rekordokat is figyelembe kell vennie, amelyeket érdemes megfontolnia, mielőtt döntene.

### <a name="cname-or-alias-record"></a>CNAME vagy alias rekord
A CNAME rekord egy *adott* tartományt (például **contoso.com** vagy **www \. contoso.com**) rendel egy kanonikus tartománynévhez. Ebben az esetben a kanonikus tartománynév az Azure által üzemeltetett alkalmazás **[SajátPr]. cloudapp. net** tartományának neve. A létrehozást követően a CNAME aliast hoz létre a következőhöz: **[SajátPr]. cloudapp. net**. A CNAME bejegyzés a **[SajátPr]. cloudapp. net** szolgáltatás automatikus IP-címére lesz feloldva, tehát ha a felhőalapú szolgáltatás IP-címe megváltozik, nem kell semmilyen műveletet végrehajtania.

> [!NOTE]
> Egyes tartományi regisztrátorok csak CNAME rekordok (például www- \. contoso.com), és nem gyökérszintű nevek (például a contoso.com) használata esetén teszik lehetővé az altartományok leképezését. A CNAME-rekordokkal kapcsolatos további információkért tekintse meg a regisztrátor által biztosított dokumentációt, [a wikipedia bejegyzését a CNAME rekordon](https://en.wikipedia.org/wiki/CNAME_record), vagy az [IETF tartományneveket – megvalósítási és specifikációs](https://tools.ietf.org/html/rfc1035) dokumentumot.

### <a name="a-record"></a>A-rekord
Az *a* rekord leképezi a tartományt, például a **contoso.com** vagy a **www \. contoso.com**, *vagy egy helyettesítő karaktert* (például **\* . contoso.com**) egy IP-címhez. Azure Cloud Service esetén a szolgáltatás virtuális IP-címe. Tehát a rekordok CNAME rekordon keresztüli fő előnye, hogy egy olyan bejegyzést használhat, amely helyettesítő karaktert (például \* *_. contoso.com)_* használ, amely több altartományhoz (például **mail.contoso.com**, **login.contoso.com** vagy **www \. contso.com**) érkező kéréseket fog kezelni.

> [!NOTE]
> Mivel egy rekord statikus IP-címekre van leképezve, nem tudja automatikusan feloldani a felhőalapú szolgáltatás IP-címének módosításait. A felhőalapú szolgáltatás által használt IP-címet a rendszer először az üres tárolóhelyre való üzembe helyezéskor (éles környezetben vagy előkészítéskor) osztja ki. Ha törli a tárolóhely üzembe helyezését, az Azure felszabadítja az IP-címet, és a tárolóhely jövőbeli telepítései új IP-címet kapnak.
> 
> Kényelmesen, egy adott üzembe helyezési pont IP-címe (éles vagy átmeneti környezet) megmarad az átmeneti és éles környezetek közötti váltáskor, vagy egy meglévő üzemelő példány helyben történő frissítésének végrehajtásakor. A műveletek végrehajtásával kapcsolatos további információkért lásd: [a Cloud Services kezelése](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>CNAME-rekord hozzáadása az egyéni tartományhoz
CNAME rekord létrehozásához hozzá kell adnia egy új bejegyzést a DNS-táblában az egyéni tartományhoz a regisztrátor által biztosított eszközök használatával. Az egyes regisztrátorok hasonló, de némileg eltérő módon határozzák meg a CNAME rekordot, de a fogalmak ugyanazok.

1. A módszerek egyikével megkeresheti a felhőalapú szolgáltatáshoz rendelt **. cloudapp.net** tartománynevet.

   * Jelentkezzen be a [Azure Portalba], válassza ki a felhőalapú szolgáltatást, tekintse meg az **Áttekintés** szakaszt, és keresse meg a **webhely URL-címét** .

       ![a webhely URL-címét bemutató gyors áttekintés szakasz][csurl]

       **OR**
   * Telepítse és konfigurálja az [Azure PowerShellt](/powershell/azure/), majd használja a következő parancsot:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Mentse az egyik metódus által visszaadott URL-címben használt tartománynevet, mivel CNAME rekord létrehozásakor szüksége lesz rá.
2. Jelentkezzen be a DNS-regisztráló webhelyére, és nyissa meg a lapot a DNS kezeléséhez. Keresse meg a helyek **tartománynevet**, **DNS**-t vagy **névkiszolgáló-kezelést** jelölő hivatkozásait vagy területeit.
3. Itt megtalálhatja a CNAME elem kiválasztásának vagy megadásának helyét. Előfordulhat, hogy ki kell választania a bejegyzéstípust a legördülő listából, vagy a speciális beállítások lapra kell lépnie. A következő szavakat kell megkeresnie: **CNAME**, **alias** vagy **altartomány**.
4. A CNAME tartomány vagy altartomány aliasnevét is meg kell adnia, például a **www** -t, ha létre szeretne hozni egy aliast a **www- \. customdomain.com**. Ha létre szeretne hozni egy aliast a gyökértartomány számára, akkor előfordulhat, hogy a " **\@** " szimbólum jelenik meg a regisztrátor DNS-eszközeiben.
5. Ezt követően meg kell adnia egy kanonikus állomásnév nevét, amely ebben az esetben az alkalmazás **cloudapp.net** tartománya.

A következő CNAME rekord például továbbítja a **www \. contoso.com** és **contoso.cloudapp.net** közötti összes forgalmat, a telepített alkalmazás egyéni tartománynevét:

| Alias/állomásnév/altartomány | Kanonikus tartomány |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> A **www \. contoso.com** látogatói soha nem fogják látni a valódi gazdagépet (contoso.cloudapp.net), így a továbbítási folyamat láthatatlan a végfelhasználó számára.
> 
> A fenti példa csak a **www** altartományon belüli forgalomra vonatkozik. Mivel a helyettesítő karakterek nem használhatók CNAME rekordokkal, minden tartományhoz/altartományhoz létre kell hoznia egy CNAME-t. Ha az altartományokból, például a *. contoso.com-ból szeretne forgalmat irányítani a cloudapp.net-címre, beállíthat egy URL- **átirányítási** vagy **URL-cím-továbbítási** bejegyzést a DNS-beállításokban, vagy létrehozhat egy rekordot.

## <a name="add-an-a-record-for-your-custom-domain"></a>Rekord hozzáadása az egyéni tartományhoz
Rekord létrehozásához először meg kell keresnie a felhőalapú szolgáltatás virtuális IP-címét. Ezután vegyen fel egy új bejegyzést az egyéni tartomány DNS-táblájába a regisztrátor által biztosított eszközök használatával. Minden regisztrálóhoz hasonló, de némileg eltérő módon kell megadni egy rekordot, de a fogalmak ugyanazok.

1. A felhőalapú szolgáltatás IP-címének lekéréséhez használja az alábbi módszerek egyikét.

   * Jelentkezzen be a [Azure Portalba], válassza ki a felhőalapú szolgáltatást, tekintse meg az **Áttekintés** szakaszt, és keresse meg a **nyilvános IP-címek** bejegyzést.

       ![a VIP-t bemutató gyors áttekintés szakasz][vip]

       **OR**
   * Telepítse és konfigurálja az [Azure PowerShellt](/powershell/azure/), majd használja a következő parancsot:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Mentse az IP-címet, mert egy rekord létrehozásakor szüksége lesz rá.
2. Jelentkezzen be a DNS-regisztráló webhelyére, és nyissa meg a lapot a DNS kezeléséhez. Keresse meg a helyek **tartománynevet**, **DNS**-t vagy **névkiszolgáló-kezelést** jelölő hivatkozásait vagy területeit.
3. Itt megtalálhatja A rekordok kiválasztásának vagy megadásának helyét. Előfordulhat, hogy ki kell választania a bejegyzéstípust a legördülő listából, vagy a speciális beállítások lapra kell lépnie.
4. Válassza ki vagy adja meg azt a tartományt vagy altartományt, amely ezt A rekordot fogja használni. Válassza például a **www** lehetőséget, ha létre szeretne hozni egy aliast a **www \. customdomain.com**. Ha helyettesítő karaktert szeretne létrehozni az összes altartományhoz, írja be a "* * * * *" értéket. Ez minden olyan altartományt magában foglal, mint például a **mail.customdomain.com**, a **login.customdomain.com** és a **www \. customdomain.com**.

    Ha létre szeretne hozni egy rekordot a gyökértartomány számára, akkor előfordulhat, hogy a " **\@** " szimbólum jelenik meg a regisztrátor DNS-eszközeiben.
5. Adja meg a felhőalapú szolgáltatás IP-címét a megadott mezőben. Ez társítja az a rekordban használt tartományi bejegyzést a felhőalapú szolgáltatás központi telepítésének IP-címével.

A következő rekord például a **contoso.com** -ről a **137.135.70.239**-re irányuló összes forgalmat továbbítja a telepített alkalmazás IP-címére:

| Gazdagép neve/altartománya | IP-cím |
| --- | --- |
| \@ |137.135.70.239 |

Ez a példa egy rekord létrehozását mutatja be a gyökértartomány számára. Ha olyan helyettesítő karaktert szeretne létrehozni, amely az összes altartományra kiterjed, akkor a "* * * * *" altartományként kell megadnia a következőt: "* * * * *".

> [!WARNING]
> Alapértelmezés szerint az Azure-beli IP-címek dinamikusak. Érdemes egy [fenntartott IP-címet](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) használni annak biztosítására, hogy az IP-cím ne változzon.
> 
> 

## <a name="next-steps"></a>Következő lépések
* [A Cloud Services felügyelete](cloud-services-how-to-manage-portal.md)
* [CDN-tartalom leképezése egyéni tartományra](../cdn/cdn-map-content-to-custom-domain.md)
* [A felhőalapú szolgáltatás általános konfigurációja](cloud-services-how-to-configure-portal.md).
* Ismerje meg, hogyan [helyezhet üzembe egy felhőalapú szolgáltatást](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja a [TLS/SSL-tanúsítványokat](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure Portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png