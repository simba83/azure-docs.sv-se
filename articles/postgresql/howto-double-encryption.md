---
title: Infrastruktur dubbel kryptering – Azure Portal – Azure Database for PostgreSQL
description: Lär dig hur du konfigurerar och hanterar infrastruktur Double Encryption för din Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: ea486b534ac3e703849ddb3922d7c3a428dd076b
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242236"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Infrastruktur dubbel kryptering för Azure Database for PostgreSQL

Lär dig hur du använder den här inställningen för att konfigurera och hantera infrastruktur dubbel kryptering för din Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>Skapa en Azure Database for PostgreSQL-server med Double Infrastructure Encryption – Portal

Följ dessa steg om du vill skapa en Azure Database for MySQL-server med Double Infrastructure Encryption från Azure Portal:

1. Välj **skapa en resurs** (+) i det övre vänstra hörnet i portalen.

2. Välj **databaser**  >  **Azure Database for PostgreSQL**. Du kan också ange PostgreSQL i sökrutan för att hitta tjänsten. Aktiverat distributions alternativet för **enskild server** .

   :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Azure Database for PostgreSQL på menyn":::

3. Ange grundläggande information om servern. Välj **ytterligare inställningar** och aktivera kryss rutan **infrastruktur med dubbla kryptering** för att ange parametern.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png" alt-text="Azure Database for PostgreSQL val":::

4. Välj **Granska + skapa** för att etablera servern.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png" alt-text="Azure Database for PostgreSQL Sammanfattning":::

5. När servern har skapats kan du verifiera infrastrukturens dubbla kryptering genom att kontrol lera statusen i bladet **data krypterings** Server.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png" alt-text="Azure Database for MySQL validering":::

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>Skapa en Azure Database for PostgreSQL-server med dubbel kryptering med infrastruktur – CLI

Följ dessa steg om du vill skapa en Azure Database for MySQL-server med Double Infrastructure Encryption från CLI:

I det här exemplet skapas en resurs grupp med namnet `myresourcegroup` på `westus` platsen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
I följande exempel skapas en PostgreSQL 11-server i västra USA som heter `mydemoserver` i din resurs grupp `myresourcegroup` med Server Administratörs inloggning `myadmin` . Det här är **4:e generationens server för** **generell användning** med 2 **virtuella kärnor**. Detta kommer också att aktivera infrastruktur dubbel kryptering för servern som skapats. Ersätt `<server_admin_password>` med ditt eget värde.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>Nästa steg

Mer information om data kryptering finns i [Azure Database for PostgreSQL data infrastruktur Double Encryption](concepts-Infrastructure-double-encryption.md).

