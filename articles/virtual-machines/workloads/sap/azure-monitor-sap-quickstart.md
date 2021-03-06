---
title: Distribuera Azure Monitor för SAP-lösningar med Azure Portal
description: Distribuera Azure Monitor för SAP-lösningar med Azure Portal
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines
ms.date: 08/17/2020
ms.reviewer: cynthn
ms.openlocfilehash: 4b0e54c434f68a8a26f49b6ba0c1010e93d36ad6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739887"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Distribuera Azure Monitor för SAP-lösningar med Azure Portal

Azure Monitor för resurser för SAP-lösningar kan skapas via [Azure Portal](https://azure.microsoft.com/features/azure-portal). Den här metoden ger ett webbläsarbaserat användar gränssnitt för att distribuera Azure Monitor för SAP-lösningar och konfigurera providrar.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på Azure-portalen på https://portal.azure.com

## <a name="create-monitoring-resource"></a>Skapa övervaknings resurs

1. Välj **Azure Monitor för SAP-lösningar** från **Azure Marketplace** .

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Bilden visar hur du väljer Azure Monitor för SAP-lösningar från Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Ange de värden som krävs på fliken **grundläggande** . Om det är tillämpligt kan du använda en befintlig Log Analytics-arbetsyta.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Bilden visar hur du väljer Azure Monitor för SAP-lösningar från Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. När du väljer ett virtuellt nätverk måste du kontrol lera att de system som du vill övervaka kan kontaktas i det virtuella nätverket. 

   > [!IMPORTANT]
   > Om du väljer **dela** för data delning med Microsoft kan våra support team tillhandahålla ytterligare support.

## <a name="configure-providers"></a>Konfigurera providers

### <a name="sap-hana-provider"></a>SAP HANA Provider 

1. Välj fliken **Provider** för att lägga till de providrar som du vill konfigurera. Du kan lägga till flera providers efter en annan eller lägga till dem efter att du har distribuerat övervaknings resursen. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Bilden visar hur du väljer Azure Monitor för SAP-lösningar från Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Välj **Lägg till provider** och välj **SAP HANA** i list rutan. 

   > [!IMPORTANT]
   > Se till att SAP HANA-providern har kon figurer ATS för SAP HANA Master-noden.

3. Mata in den privata IP-adressen för HANA-servern.

4. Ange namnet på den databas klient som du vill använda. Du kan välja vilken klient som helst, men vi rekommenderar att du använder **SYSTEMDB** eftersom det möjliggör en bredare matris av övervaknings områden. 

5. Mata in det SQL-portnummer som är associerat med din HANA-databas. Port numret måste ha formatet **[3]**  +  **[instance #]**  +  **[13]** . Till exempel 30013. 

6. Ange det databas användar namn som du vill använda. Se till att databas användaren har tilldelats de **övervaknings** -och **katalog Läs** rollerna. 

7. När du är färdig väljer du **Lägg till provider** . Fortsätt att lägga till ytterligare providrar vid behov eller Välj **Granska + skapa** för att slutföra distributionen.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Bilden visar hur du väljer Azure Monitor för SAP-lösningar från Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Pacemaker-Provider (hög tillgänglighets kluster)

1. Välj **kluster för hög tillgänglighet (pacemaker)** i list rutan. 

   > [!IMPORTANT]
   > Om du vill konfigurera pacemaker-providern (High-Availability Cluster) kontrollerar du att ha_cluster_provider är installerat på varje nod. Mer information finns i [ha kluster export verktyget](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Mata in Prometheus-slutpunkten i form av http://IP:9664/metrics . 
 
3. Mata in system-ID (SID), värdnamn och kluster namn.

4. När du är färdig väljer du **Lägg till provider** . Fortsätt att lägga till ytterligare providrar vid behov eller Välj **Granska + skapa** för att slutföra distributionen.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Bilden visar hur du väljer Azure Monitor för SAP-lösningar från Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server Provider

1. Innan du lägger till Microsoft SQL Server-providern ska du köra följande skript i SQL Server Management Studio för att skapa en användare med de behörigheter som krävs för att konfigurera providern.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Välj **Lägg till provider** och välj **Microsoft SQL Server** i list rutan. 

3. Fyll i fälten med information som är kopplad till din Microsoft SQL Server. 

4. När du är färdig väljer du **Lägg till provider** . Fortsätt att lägga till ytterligare providrar vid behov eller Välj **Granska + skapa** för att slutföra distributionen.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Bilden visar hur du väljer Azure Monitor för SAP-lösningar från Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Monitor för SAP-lösningar](azure-monitor-overview.md)
