---
title: Caselle di controllo Distribuisci disabilitate in Configuration Manager
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: edf471f1d9a2ee4adc11f09e0c7b7ad3cf6f78f1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014246"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Caselle di controllo Distribuisci disabilitate in Configuration Manager

Dal momento che Novell 3,5, i progetti Xamarin.iOS vengono distribuiti automaticamente ogni volta che si preme il pulsante **Start** della barra degli strumenti o si sceglie la voce di menu **debug > Avvia debug** . Sarà comunque necessario impostare il progetto di app Xamarin.iOS desiderato come progetto di **avvio** prima di eseguire uno di questi comandi.

Per questo motivo, le caselle di controllo **Distribuisci** sono intenzionalmente disabilitate nel Configuration Manager di Visual Studio per i progetti Xamarin.iOS:

![](deploy-checkboxes-images/configuration.png "Visual Studio Configuration Manager showing the 'Deploy' checkbox disabled for a Xamarin.iOS project in Xamarin 3.5")

Questa modifica elimina un errore che può essere visualizzato nelle versioni precedenti di Novell (versione 3,3 e precedenti) quando il progetto di app Xamarin.iOS non è stato impostato per la distribuzione:

![](deploy-checkboxes-images/error.png "Error dialog: The project iPhoneApp1 needs to be deployed before it can be started. Verify the project is selected to be deployed in the Solution Configuration Manager.")
