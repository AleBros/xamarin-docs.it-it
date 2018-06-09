---
title: Funzionalità della piattaforma Android
description: In questo articolo viene illustrato come aggiungere funzionalità specifiche di Android per le app xamarin. Forms e si concentra sulla progettazione di materiale.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2016
ms.openlocfilehash: 2eada518586f222d200ec19aeddc65107d7603b3
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242404"
---
# <a name="android-platform-features"></a>Funzionalità della piattaforma Android

## <a name="platform-support"></a>Supporto per piattaforme

Il valore predefinito il progetto Android di xamarin. Forms Usa un stile di versioni precedenti di controllo per il rendering che era comune prima Android 5.0. Le applicazioni compilate con il modello sono `FormsApplicationActivity` come classe di base della loro attività principale.

## <a name="material-design-via-appcompat"></a>Progettazione materiale tramite delle applicazioni

Xamarin. Forms prevede inoltre un `FormsAppCompatActivity` che utilizza **delle applicazioni** le funzionalità fornite da Android per implementare i temi di progettazione di materiale.

Per aggiungere i temi di progettazione di materiale al progetto Android di xamarin. Forms, seguire la [supportano le istruzioni di installazione per applicazioni](appcompat.md)

Ecco il **Todo** esempio con il valore predefinito `FormsApplicationActivity`:

[![](images/before-appcompat-sml.png "Applicazione di esempio di attività senza delle applicazioni")](images/before-appcompat.png#lightbox "l'applicazione di esempio di attività senza delle applicazioni")

E questo è lo stesso codice dopo l'aggiornamento del progetto per utilizzare `FormsAppCompatActivity` (e aggiungere le informazioni sul tema aggiuntivi):

[![](images/post-appcompat-sml.png "Applicazione di esempio di attività con delle applicazioni e i temi")](images/post-appcompat.png#lightbox "applicazione di esempio di attività con delle applicazioni e i temi")

> [!NOTE]
> Quando si utilizza `FormsAppCompatActivity`, [classi di base per alcuni renderer personalizzati Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) sarà diverso.


## <a name="related-links"></a>Collegamenti correlati

- [Aggiungere il supporto di materiale di progettazione](appcompat.md)
