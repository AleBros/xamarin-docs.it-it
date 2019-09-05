---
title: Caselle di controllo Distribuisci disabilitate in Configuration Manager
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: conceptdev
ms.author: crdun
ms.date: 12/02/2016
ms.openlocfilehash: 82ff1a684ffad75a301f0db6b0f8e3116be6746d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285057"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Caselle di controllo Distribuisci disabilitate in Configuration Manager

Dal momento che Novell 3,5, i progetti Novell. iOS vengono distribuiti automaticamente ogni volta che si preme il pulsante **Start** della barra degli strumenti o si sceglie la voce di menu **debug > Avvia debug** . Sarà comunque necessario impostare il progetto di app Novell. iOS desiderato come progetto di **avvio** prima di eseguire uno di questi comandi.

Per questo motivo, le caselle di controllo **Distribuisci** sono intenzionalmente disabilitate nel Configuration Manager di Visual Studio per i progetti Novell. iOS:

![](deploy-checkboxes-images/configuration.png "Visual Studio Configuration Manager la visualizzazione della casella di controllo ' Distribuisci ' disabilitata per un progetto Novell. iOS in Novell 3,5")

Questa modifica elimina un errore che può essere visualizzato nelle versioni precedenti di Novell (versione 3,3 e precedenti) quando il progetto di app Novell. iOS non è stato impostato per la distribuzione:

![](deploy-checkboxes-images/error.png "Finestra di dialogo di errore: Il progetto iPhoneApp1 deve essere distribuito prima di poter essere avviato. Verificare che il progetto sia selezionato per la distribuzione nella soluzione Configuration Manager.")
