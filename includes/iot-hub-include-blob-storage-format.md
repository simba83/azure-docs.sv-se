---
title: inkludera fil
description: inkludera fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b20c97bf919073b01f564b59b3a55eccb7614005
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89042976"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Data kan skrivas till Blob Storage i formatet [Apache Avro](https://avro.apache.org/) , som är standard, eller JSON (för hands version). 
>    
> Funktionen för att koda JSON-format är i för hands version i alla regioner där IoT Hub är tillgänglig, förutom östra USA, västra USA och Västeuropa. Kodnings formatet kan bara anges när Blob Storage-slutpunkten konfigureras. Det går inte att ändra formatet för en slut punkt som redan har kon figurer ATS. När du använder JSON-kodning måste du ange contentType till JSON och contentEncoding till UTF-8 i meddelande systemets egenskaper. 
>
> Mer detaljerad information om hur du använder en Blob Storage-slutpunkt finns i [rikt linjer för routning till lagring](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).
>