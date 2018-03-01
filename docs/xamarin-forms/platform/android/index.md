---
title: "Funzionalità della piattaforma Android"
description: "Aggiunta di funzionalità specifiche di Android per App xamarin. Forms"
ms.topic: article
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2016
ms.openlocfilehash: d68d84671028ded14b4b885f2c134656fc639f9e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="android-platform-features"></a>Funzionalità della piattaforma Android

## <a name="platform-support"></a>Supporto per piattaforme

Il valore predefinito il progetto Android di xamarin. Forms Usa un stile di versioni precedenti di controllo per il rendering che era comune prima Android 5.0. Le applicazioni compilate con il modello sono `FormsApplicationActivity` come classe di base della loro attività principale.

## <a name="material-design-via-appcompat"></a>Progettazione materiale tramite delle applicazioni

Xamarin. Forms prevede inoltre un `FormsAppCompatActivity` che utilizza **delle applicazioni** le funzionalità fornite da Android per implementare i temi di progettazione di materiale.

Per aggiungere i temi di progettazione di materiale al progetto Android di xamarin. Forms, seguire la [supportano le istruzioni di installazione per applicazioni](appcompat.md)

Ecco il **Todo** esempio con il valore predefinito `FormsApplicationActivity`:

[ ![](images/before-appcompat-sml.png "Applicazione di esempio di attività senza delle applicazioni")](images/before-appcompat.png "l'applicazione di esempio di attività senza delle applicazioni")

E questo è lo stesso codice dopo l'aggiornamento del progetto per utilizzare `FormsAppCompatActivity` (e aggiungere le informazioni sul tema aggiuntivi):

[ ![](images/post-appcompat-sml.png "Applicazione di esempio di attività con delle applicazioni e i temi")](images/post-appcompat.png "applicazione di esempio di attività con delle applicazioni e i temi")

> [!NOTE]
> **Nota**: quando si utilizza `FormsAppCompatActivity`, [classi di base per alcuni renderer personalizzati Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) sarà diverso.


## <a name="related-links"></a>Collegamenti correlati

- [Aggiungere il supporto di materiale di progettazione](appcompat.md)
