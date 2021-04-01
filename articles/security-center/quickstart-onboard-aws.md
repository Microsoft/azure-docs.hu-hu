---
title: Az AWS-fiók összekötése Azure Security Center
description: Az AWS-erőforrások figyelése Azure Security Center
author: memildin
ms.author: memildin
ms.date: 01/24/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2ad3ab8c39d9f9a29e74bd139fb5ddd1e5faeff9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755149"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>AWS-fiókok összekötése Azure Security Center

A Felhőbeli számítási feladatok gyakran több felhőalapú platformra is kiterjednek, a Cloud Security servicesnek ugyanezt kell tennie.

Azure Security Center védi a munkaterheléseket az Azure-ban, a Amazon Web Services (AWS) és a Google Cloud Platformban (GCP).

Az AWS-fiók Security Centerba való bevezetésével integrálható az AWS Security hub és a Azure Security Center. Security Center így biztosítja a következő felhőalapú környezetek láthatóságát és védelmét:

- Az ügynök automatikus kiépítése (Security Center az [Azure arc](../azure-arc/servers/overview.md) használatával helyezi üzembe a log Analytics-ügynököt az AWS-példányokon)
- Szabályzatkezelés
- Biztonságirés-kezelés
- Beágyazott végpontok észlelése és válasza (EDR)
- Biztonsági konfigurációs beállítások észlelése
- Egyetlen nézet, amely Security Center ajánlásokat és az AWS biztonsági központ eredményeit mutatja
- Az AWS-erőforrások beépítése Security Center biztonságos pontszámának számításaiba
- Az AWS-erőforrások szabályozási megfelelőségi értékelése

Az alábbi képernyőképen megtekintheti az AWS-fiókokat Security Center áttekintő irányítópultján.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Három GCP-projekt szerepel a Security Center áttekintési irányítópultján" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|[Az Azure Defender for Servers](defender-for-servers-introduction.md) használatát igényli|
|Szükséges szerepkörök és engedélyek:|A kapcsolódó Azure-előfizetéshez tartozó **tulajdonos**<br>A **közreműködő** az AWS-fiók összekapcsolását is lehetővé teszi, ha egy tulajdonos megadja az egyszerű szolgáltatásnév adatait|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||



## <a name="connect-your-aws-account"></a>Az AWS-fiók csatlakoztatása

Az AWS Cloud Connector létrehozásához kövesse az alábbi lépéseket. 

### <a name="step-1-set-up-aws-security-hub"></a>1. lépés Az AWS biztonsági központ beállítása:

1. Ha több régióra vonatkozó biztonsági javaslatokat szeretne megtekinteni, ismételje meg az alábbi lépéseket minden érintett régió esetében.

    > [!IMPORTANT]
    > Ha AWS-főkiszolgálói fiókot használ, a következő három lépést megismételve konfigurálja a fő fiókot és az összes kapcsolódó tagot az összes érintett régióban.

    1. Az [AWS-konfiguráció](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)engedélyezése.
    1. Az [AWS biztonsági központ](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html)engedélyezése.
    1. Ellenőrizze, hogy van-e adatfolyam a biztonsági hubhoz.

        A Security hub első engedélyezésekor több óráig is eltarthat, amíg az adathozzáférés elérhetővé válik.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>2. lépés A Security Center hitelesítésének beállítása AWS-ben

Az AWS-ben kétféleképpen engedélyezhető a Security Center hitelesítése:

- **Hozzon létre egy iam-szerepkört a Security Centerhoz** – ez a legbiztonságosabb módszer, és ajánlott
- **AWS-felhasználó a Security Centerhoz** – kevésbé biztonságos megoldás, ha nincs iam engedélyezve

#### <a name="create-an-iam-role-for-security-center"></a>IAM szerepkör létrehozása a Security Centerhoz
1. A Amazon Web Services-konzol **biztonság, identitás & megfelelőség** területén válassza a **iam** lehetőséget.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS-szolgáltatások":::

1. Válassza a **szerepkörök** lehetőséget, és **hozzon létre egy szerepkört**.
1. Válasszon **másik AWS-fiókot**.
1. Adja meg a következő részleteket:

    - **Fiókazonosító** – adja meg a Microsoft-fiók azonosítóját (**158177204117**), ahogyan az a Security Center AWS-összekötő lapján látható.
    - **Külső azonosító megkövetelése** – ki kell választani
    - **Külső azonosító** – adja meg az előfizetés azonosítóját, ahogyan az az AWS-összekötő oldalán látható Security Center 

1. Kattintson a **Tovább** gombra.
1. Az **engedélyezési házirendek csatolása** szakaszban válassza ki a következő házirendeket:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Opcionálisan hozzáadhat címkéket. A Címkék hozzáadása a felhasználóhoz nem befolyásolja a kapcsolódást.
1. Kattintson a **Tovább** gombra.

1. A szerepkörök listában válassza ki a létrehozott szerepkört.

1. Mentse az Amazon-erőforrás nevét (ARN) később. 

#### <a name="create-an-aws-user-for-security-center"></a>AWS-felhasználó létrehozása Security Centerhoz 
1. Nyissa meg a **felhasználók** lapot, és válassza a **felhasználó hozzáadása** elemet.
1. A **részletek** lépésben adjon meg Security Center felhasználónevet, és győződjön meg arról, hogy az AWS hozzáférési típushoz a **programozott hozzáférés** lehetőséget választotta. 
1. Válassza a **következő engedélyek** lehetőséget.
1. Válassza a **meglévő szabályzatok közvetlen csatolása** lehetőséget, és alkalmazza a következő házirendeket:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Kattintson a **Tovább gombra: címkék**. Opcionálisan hozzáadhat címkéket. A Címkék hozzáadása a felhasználóhoz nem befolyásolja a kapcsolódást.
1. Válassza az **Áttekintés** lehetőséget.
1. Mentse az automatikusan generált **hozzáférési kulcs azonosítóját** és a **titkos elérési kulcs** CSV-fájlját később.
1. Tekintse át az összegzést, és kattintson a **felhasználó létrehozása** lehetőségre.


### <a name="step-3-configure-the-ssm-agent"></a>3. lépés Az SSM-ügynök konfigurálása

Az AWS Systems Manager szükséges a feladatok AWS-erőforrásokon keresztüli automatizálásához. Ha a EC2 példányai nem rendelkeznek az SSM-ügynökkel, kövesse az Amazon megfelelő utasításait:

- [Az SSM-ügynök telepítése és konfigurálása Windows-példányokon](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Az SSM-ügynök telepítése és konfigurálása az Amazon EC2 Linux-példányokon](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>4. lépés: Az Azure-ív előfeltételeinek befejezése
1. Győződjön meg arról, hogy a megfelelő [Azure-erőforrás-szolgáltatók](../azure-arc/servers/agent-overview.md#register-azure-resource-providers) vannak regisztrálva:
    - Microsoft. HybridCompute
    - Microsoft. GuestConfiguration

1. Hozzon létre egy egyszerű szolgáltatást a méretezéshez. A bevezetéshez használni kívánt előfizetés **tulajdonosaként** hozzon létre egy egyszerű szolgáltatásnevet az Azure arc bevezetéséhez az [egyszerű szolgáltatásnév létrehozása az üzembe helyezéshez](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)című témakörben leírtak szerint.


### <a name="step-5-connect-aws-to-security-center"></a>5. lépés Az AWS összekötése Security Center

1. A Security Center menüjében válassza a **több Felhőbeli összekötők** lehetőséget.
1. Válassza az **AWS-fiók hozzáadása** lehetőséget.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="AWS-fiók hozzáadása gomb a Security Center multi Cloud Connectors oldalán":::
1. Adja meg a beállításokat az **AWS-hitelesítés** lapon:
    1. Adja meg az összekötő **megjelenítendő nevét** .
    1. Győződjön meg arról, hogy az előfizetés helyes. Ez az előfizetés tartalmazza az összekötő és az AWS Security hub javaslatait.
    1. A hitelesítési lehetőségtől függően a [2. lépésben választott. Security Center hitelesítésének beállítása AWS-ben](#step-2-set-up-authentication-for-security-center-in-aws):
        - Válassza a  **szerepkör** bejelölése és az ARN beillesztése a [következőre: iam szerepkör létrehozása a Security Centerhoz](#create-an-iam-role-for-security-center)lehetőséget.
            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Az ARN-fájl beillesztése a Azure Portal AWS-kapcsolatok varázslójának megfelelő mezőjébe":::

            OR

        - Válassza ki a **hitelesítő adatokat** , majd illessze be a **hozzáférési kulcsot** és a **titkos kulcsot** az [AWS-felhasználó létrehozása a Security Centerhoz](#create-an-aws-user-for-security-center)című. csv fájlból.
1. Kattintson a **Tovább** gombra.
1. Adja meg a beállításokat az **Azure arc konfiguráció** lapján:

    Security Center felveszi a csatlakoztatott AWS-fiókban lévő EC2-példányokat, és az SSM-t használja az Azure-ív bevezetéséhez. 

    > [!TIP]
    > A támogatott operációs rendszerek listájáért lásd: [milyen operációs rendszerek támogatottak a EC2-példányok esetében?](#what-operating-systems-for-my-ec2-instances-are-supported) a gyakori kérdések között.

    1. Válassza ki azt az **erőforráscsoportot** és **Azure-régiót** , amelyet a felderített AWS-EC2s a kijelölt előfizetésbe fog bevezetni.
    1. Adja meg az Azure-ív egyszerű szolgáltatás- **azonosítóját** és az **egyszerű szolgáltatás ügyfél-titkos kulcsát** , az itt leírtak szerint [: egyszerű szolgáltatás létrehozása a méretezéshez](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Ha a gép egy proxykiszolgálón keresztül csatlakozik az internethez, adja meg a proxykiszolgáló IP-címét, vagy azt a nevet és portszámot, amelyet a gép használ a proxykiszolgálóhoz való kommunikációhoz. Adja meg az értéket a formátumban ```http://<proxyURL>:<proxyport>```
    1. Válassza az **Áttekintés + létrehozás** lehetőséget.

        Az összegző információk áttekintése

        A címkék szakaszban minden olyan Azure-címkét listázunk, amely a saját vonatkozó adataival automatikusan létrejön az Azure-ban. 

        További információ az Azure-címkék [használatáról címkék használatával az Azure-erőforrások és a felügyeleti hierarchia rendszerezéséhez](../azure-resource-manager/management/tag-resources.md).

### <a name="step-6-confirmation"></a>6. lépés Visszaigazolás

Az összekötő sikeres létrehozása után az AWS biztonsági központ megfelelően van konfigurálva:

- Security Center megvizsgálja az AWS EC2-példányok környezetét, beépítve azokat az Azure-ívet, lehetővé téve a Log Analytics-ügynök telepítését és a veszélyforrások elleni védelem és biztonsági javaslatok biztosítását. 
- Az ASC szolgáltatás 6 óránként megvizsgálja az új AWS EC2-példányokat, és a konfigurációnak megfelelően bekészíti őket.
- Az AWS CIS standard a Security Center szabályozási megfelelőségi irányítópultján jelenik meg.
- Ha a biztonsági központ házirendje engedélyezve van, a javaslatok a Security Center Portálon és a megfelelőségi megfelelőségi irányítópulton 5-10 perccel a bevezetést követően jelennek meg.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="AWS-erőforrások és javaslatok Security Center ajánlásai oldalon":::



## <a name="monitoring-your-aws-resources"></a>Az AWS-erőforrások monitorozása

A fentiekben leírtaknak megfelelően a Azure Security Center biztonsági javaslatai oldalon az AWS-erőforrásokat, valamint az Azure-és GCP-erőforrásokat is megtekintheti egy igazi, többfelhős nézetben.

Ha az erőforrás típusa szerint szeretné megtekinteni az összes aktív javaslatot az erőforrásokhoz, használja a Security Center eszköz leltározási lapját, és szűrje az AWS-erőforrástípus, amelyben érdekli:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="A tárgyieszköz-leltár oldal erőforrástípus-szűrője, amely az AWS-beállításokat jeleníti meg"::: 


## <a name="aws-in-security-center-faq"></a>AWS Security Center – gyakori kérdések

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Milyen operációs rendszereket támogat a EC2-példányok?

Támogatott operációs rendszer automatikus bevezetéshez az Azure arc for AWS-gépekhez

- Ubuntu 16,04 – az SSM-ügynök előre telepítve van, alapértelmezés szerint
- Ubuntu 18,04 – az SSM-ügynök előre telepítve van, alapértelmezés szerint
- A Windows Server-SSM ügynök előre telepítve van, alapértelmezés szerint
- CentOS Linux 7 – az SSM-et manuálisan vagy külön kell telepíteni
- SUSE Linux Enterprise Server (SLES) 15 (x64) – az SSM-et manuálisan kell telepíteni vagy külön előkészíteni
- Red Hat Enterprise Linux (RHEL) 7 (x64) – az SSM-et manuálisan kell telepíteni vagy külön előkészíteni


## <a name="next-steps"></a>Következő lépések

Az AWS-fiók csatlakoztatása a Azure Security Centerban elérhető többfelhős élmény része. A kapcsolódó információk a következő oldalon találhatók:

- [A GCP-fiókok összekapcsolásának Azure Security Center](quickstart-onboard-gcp.md)
