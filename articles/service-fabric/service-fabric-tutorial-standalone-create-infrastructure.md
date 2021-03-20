---
title: Infrastruktúra létrehozása az AWS-fürthöz
description: Ez az oktatóanyag ismerteti, hogyan állíthatja be az AWS-infrastruktúrát Service Fabric-fürt futtatásához.
ms.topic: tutorial
ms.date: 05/11/2018
ms.custom: mvc
ms.openlocfilehash: c7a18b0dcdc04bdf66ac4b36ce7376ee018eb238
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842903"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>Oktatóanyag: AWS-infrastruktúra létrehozása Service Fabric-fürt futtatásához

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban egy, az AWS-en futtatott önálló fürtöt hoz létre, majd telepít rá egy alkalmazást.

Ez az oktatóanyag egy sorozat első része. Ebben a cikkben a Service Fabric önálló fürtjének futtatásához szükséges AWS-erőforrásokat hozza létre. A további cikkekben telepíteni fogja az önálló Service Fabric-csomagot, majd egy mintaalkalmazást a fürtön, végül pedig törli a fürtöt.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * EC2-példányok készletének létrehozása
> * A biztonsági csoport módosítása
> * Bejelentkezés az egyik példányba
> * A példány előkészítése a Service Fabrichez

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy AWS-fiókra lesz szüksége.  Ha még nem rendelkezik fiókkal, először létre kell egyet hoznia az [AWS-konzolon](https://aws.amazon.com/).

## <a name="create-ec2-instances"></a>EC2-példányok létrehozása

Jelentkezzen be az AWS-konzolba > írja be a **EC2** kifejezést a keresőmezőbe > **EC2 virtuális kiszolgálók a felhőben**

![Keresés az AWS-konzolon][aws-console]

Válassza a **Launch Instance** (Példány indítása) lehetőséget, és a következő képernyőn válassza a Microsoft Windows Server 2016 Base melletti **Select** (Kiválasztás) lehetőséget.

![EC2-példány kiválasztása][aws-ec2instance]

Válassza a **t2.medium** lehetőséget, majd válassza a **Next: Configure Instance Details** (Tovább: Példány részleteinek konfigurálása) elemet, és a következő képernyőn módosítsa a példányok számát `3` értékre, majd válassza az **Advanced Details** (Speciális részletek) lehetőséget a szakasz kibontásához.

A Service Fabricben a virtuális gépek összekapcsolásához az infrastruktúrát futtató virtuális gépeknek ugyanazokkal a hitelesítő adatokkal kell rendelkezniük.  Két gyakori módja van a konzisztens hitelesítő adatok elérésének: csatlakoztassa mindet ugyanahhoz a tartományhoz, vagy állítsa be ugyanazt a rendszergazdai jelszót az összes virtuális gépen.  Ebben az oktatóanyagban egy felhasználói adatok szkripttel állítja be, hogy az összes EC2-példány ugyanazt a jelszót használja.  Éles környezetben a gazdagépek Windows-tartományhoz való csatlakozása biztonságosabb.

Írja be a következő szkriptet a felhasználói adatok mezőbe a konzolon:

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

A PowerShell-szkript beírása után válassza a **Review and Launch** (Áttekintés és indítás) lehetőséget

![EC2 áttekintés és indítás][aws-ec2configure2]

Az áttekintési képernyőn válassza a **Launch** (Indítás) elemet.  Ezután módosítsa a legördülő listát **Proceed without a key pair** (Folytatás kulcspár nélkül) értékre, és jelölje be a jelölőnégyzetet, amely azt jelzi, hogy ismeri a jelszót.

![AWS-kulcspár kiválasztása][aws-keypair]

Végül válassza a **Launch Instances** (Példányok indítása), majd a **View Instances** (Példányok megtekintése) elemet.  Létrehozta a Service Fabric-fürt alapját, most pedig el kell végeznie végső beállítást magukon a példányokon, hogy előkészítse őket a Service Fabric konfigurációjára.

## <a name="modify-the-security-group"></a>A biztonsági csoport módosítása

A Service Fabric használatához nyitva kell lennie bizonyos számú portnak a fürt gazdagépei között. Ha meg kívánja nyitni ezeket a portokat az AWS-infrastruktúrában, válassza ki az egyik létrehozott példányt. Ezután válassza ki a biztonsági csoport nevét, például: **launch-wizard-1**. Most válassza az **Inbound** (Bejövő) fület.

Csak az ugyanabban a biztonsági csoportban lévő gazdagépek számára nyissa meg ezeket a portokat, hogy ne az egész világnak legyenek megnyitva. Jegyezze fel a biztonsági csoport azonosítóját, a példában ez **sg-c4fb1eba**.  Ezután válassza az **Edit** (Szerkesztés) elemet.

A következő lépésben adja hozzá a négy szabályt a szolgáltatás függőségeinek biztonsági csoportjához, majd három továbbit magához a Service Fabrichez. Az első szabály az ICMP-forgalom engedélyezése az alapvető kapcsolat-ellenőrzésekhez. A többi szabály megnyitja az SMB és a távoli beállításjegyzék engedélyezéséhez szükséges portokat.

Az első szabályhoz válassza az **Add Rule** (Szabály hozzáadása) lehetőséget, majd a legördülő menüből válassza az **All ICMP - IPv4** (Minden ICMP – IPv4) elemet. Válassza ki az egyéni mező melletti beviteli mezőt, és írja be a fenti biztonságicsoport-azonosítót.

Az utolsó három függőségnél hasonló a végrehajtandó folyamat.  Válassza az **Add Rule** (Szabály hozzáadása) lehetőséget, a legördülő listából válassza a **Custom TCP Rule** (Egyéni TCP-szabály) elemet, és a porttartományhoz adja meg mindegyik szabálynál a `135`, a `137-139` vagy a `445` értéket. Végül írja be a biztonsági csoport azonosítóját a Source (Forrás) mezőbe.

![Biztonsági csoport portjai][aws-ec2securityports]

Most, hogy a függőségek portjai nyitva vannak, ugyanezt kell tennie a Service Fabric kommunikációs portjaival. Válassza az **Add Rule** (Szabály hozzáadása) lehetőséget, a legördülő listából válassza a **Custom TCP Rule** (Egyéni TCP-szabály) elemet, a porttartományhoz adja meg a `20001-20031` értéket, és írja be a biztonsági csoportot a Source (Forrás) mezőbe.

Ezután adjon hozzá egy szabályt a rövid élettartamú porttartományhoz.  Válassza az **Add Rule** (Szabály hozzáadása) lehetőséget, a legördülő listából válassza a **Custom TCP Rule** (Egyéni TCP-szabály) elemet, és a porttartományhoz adja meg a `20606-20861` értéket. Végül írja be a biztonsági csoport azonosítóját a Source (Forrás) mezőbe.

A két utolsó szabállyal nyissa meg a Service Fabricet az egész világ számára, hogy a személyi számítógépéről kezelhesse a Service Fabric-fürtöt. Válassza az **Add Rule** (Szabály hozzáadása) lehetőséget, a legördülő listából válassza a **Custom TCP Rule** (Egyéni TCP-szabály) elemet, és a porttartományhoz adja meg a `19000-19003` vagy a `19080-19081` értéket, majd a Source (Forrás) legördülő menüben válassza az Anywhere (Bárhol) értéket.

Végezetül csak meg kell nyitnunk a 8080-as portot, hogy láthassa az üzembe helyezett alkalmazást. Válassza az **Add Rule** (Szabály hozzáadása) lehetőséget, a legördülő listából válassza a **Custom TCP Rule** (Egyéni TCP-szabály) elemet, és a porttartományhoz adja meg a `8080` értéket, majd a Source (Forrás) legördülő menüben válassza az Anywhere (Bárhol) értéket.

Most már minden szabály meg van adva. Kattintson a **Mentés** gombra.

## <a name="connect-to-an-instance-and-validate-connectivity"></a>Kapcsolódás egy példányhoz és a kapcsolat ellenőrzése

A biztonsági csoport lapján válassza a bal oldali menüből az **Instances** (Példányok) lehetőséget.  Válassza ki a létrehozott példányokat, és jegyezze fel a magánhálózati IP-címüket. Az alábbi példák a `172.31.21.141` és a `172.31.20.163` címeket használják.

Miután megvan az összes IP-cím, válassza ki az egyik példányt, amelyikhez csatlakozni szeretne. Ehhez kattintson a jobb gombbal a példányra, és válassza a **Connect** (Csatlakozás) lehetőséget.  Innen töltheti le az adott példány RDP-fájlját.  Válassza a **Download Remote Desktop File** (Távoli asztali fájl letöltése) lehetőséget, majd nyissa meg a letöltött fájlt, hogy távoli asztali (RDP-) kapcsolatot létesítsen a példánnyal.  Amikor a rendszer kéri, adja meg a jelszót (`serv1ceF@bricP@ssword`).

![Távoli asztali fájl letöltése][aws-rdp]

Miután sikeresen csatlakozott a példányhoz, ellenőrizze, hogy van-e közöttük kapcsolat, és megoszthat-e fájlokat.  Miután begyűjtötte az összes példány IP-címét, válasszon ki egyet, amelyhez jelenleg nem csatlakozik. Lépjen a **Start** menüre, írja be a `cmd` sztringet, és válassza a **Parancssor** lehetőséget.

Ezekben a példákban az RDP-kapcsolat a következő IP-címhez jött létre: 172.31.21.141. Ekkor minden csatlakozási teszt a másik IP-címmel történik: 172.31.20.163.

A kapcsolat alapvető működésének teszteléséhez használja a ping parancsot.

```
ping 172.31.20.163
```

Ha a kimenetben a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` szöveg ismétlődik négyszer, akkor a példányok közötti kapcsolat működik.  Most ellenőrizze az SMB-megosztás működését a következő paranccsal:

```
net use * \\172.31.20.163\c$
```

A parancs kimenetének a következőnek kell lennie: `Drive Z: is now connected to \\172.31.20.163\c$.`.

## <a name="prep-instances-for-service-fabric"></a>Példányok előkészítése a Service Fabrichez

Ha ezt az alapoktól hozta létre, akkor el kell végeznie néhány további lépést.  Nevezetesen ellenőriznie kell, hogy fut-e a távoli beállításjegyzék, engedélyeznie kell az SMB-t, és meg kell nyitnia az SMB-hez és a távoli beállításjegyzékhez szükséges portokat.

Ennek megkönnyítése érdekében beágyazta az összes munkát, amikor a felhasználói adatokat tartalmazó szkripttel indította a példányokat.

Az SMB engedélyezéséhez ezt a PowerShell-parancsot használta:

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

A tűzfalban lévő portok megnyitására ez a PowerShell-parancs szolgál:

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>Következő lépések

A sorozat első részében megismerte, hogyan indíthat el három EC2-példányt, és hogyan konfigurálhatja őket a Service Fabric telepítéséhez:

> [!div class="checklist"]
> * EC2-példányok készletének létrehozása
> * A biztonsági csoport módosítása
> * Bejelentkezés az egyik példányba
> * A példány előkészítése a Service Fabrichez

A sorozat második részében konfigurálni fogja a Service Fabricet a fürtben.

> [!div class="nextstepaction"]
> [A Service Fabric telepítése](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png