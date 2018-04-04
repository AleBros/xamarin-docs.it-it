---
title: Nozioni di base dell'applicazione
description: Questo documento collegamenti a guide che descrivono i vari concetti necessari per comprendere quando si sviluppano applicazioni Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/17/2015
ms.openlocfilehash: 807cf0e16cafc00483cecfc578367bc110657672
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="application-fundamentals"></a>Nozioni di base dell'applicazione

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[Modelli e idiomi comuni](~/mac/app-fundamentals/patterns.md)

In tutta l'API Apple esposte tramite c#, determinati idiomi e i pattern di avviarsi in modo continuativo. Se si ha esperienza con la programmazione con xamarin, questi possono essere noti. Documentazione spesso farà riferimento a questi modelli e idiomi più volte, in modo da avere una conoscenza approfondita di essi consentono di senso della documentazione di cui che trova.

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[Informazioni sulle API Mac](~/mac/app-fundamentals/mac-apis.md)

Per la maggior parte del tempo allo sviluppo con Xamarin.Mac, può pensare, lettura e scrittura in c# senza preoccuparsi con le API sottostanti Objective-C. Tuttavia, talvolta si necessario leggere la documentazione dell'API di Apple, convertire una risposta dall'Overflow dello Stack a una soluzione per il problema oppure confrontare a un campione esistente.

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[Utilizzo dei file .xib](~/mac/app-fundamentals/xib.md)

In questo articolo viene descritto l'utilizzo con i file .xib creati in Generatore di Xcode interfaccia per creare e gestire le interfacce utente per un'applicazione Xamarin.Mac.

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[.Storyboard/.xib meno progettazione dell'interfaccia utente](~/mac/app-fundamentals/xibless-ui.md)

Questo articolo vengono illustrate la creazione di un Xamarin.Mac interfaccia utente direttamente dal codice c# senza l'utilizzo interfaccia generatore di Xcode con .storyboard o .xib file.

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[Utilizzo di immagini](~/mac/app-fundamentals/image.md)

In questo articolo viene descritto l'utilizzo di immagini e icone in un'applicazione Xamarin.Mac. Vengono illustrate la creazione e mantenere le immagini necessarie per creare l'icona dell'applicazione e l'utilizzo di immagini nel codice c# e nel generatore di interfaccia di Xcode.

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[Data binding e la generazione di codice chiave-valore](~/mac/app-fundamentals/databinding.md)

In questo articolo viene illustrato l'utilizzo chiave-valore di codifica e l'analisi per consentire il data binding a elementi dell'interfaccia utente in interfaccia generatore del Xcode chiave-valore. Questa tecnica è ridurre notevolmente la quantità di codice c# che deve essere scritto per l'applicazione Xamarin.Mac. 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[Utilizzo di database](~/mac/app-fundamentals/databases.md)

In questo articolo viene illustrato l'utilizzo chiave-valore di codifica e l'analisi per consentire il data binding con accesso diretto ai database SQLite per gli elementi dell'interfaccia utente in interfaccia generatore del Xcode chiave-valore. Descrive inoltre utilizzando il ORM SQLite.NET per fornire l'accesso ai dati di SQLite.

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[Utilizzo di copia e Incolla](~/mac/app-fundamentals/copy-paste.md)

In questo articolo viene descritto l'utilizzo con tavolo per fornire copia e Incolla in un'applicazione Xamarin.Mac. Viene illustrato come lavorare con i tipi di dati standard che possono essere condivisi tra più App e come supportare dati personalizzati all'interno di un'app offrono.

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[Un'app Xamarin.Mac sandboxing](~/mac/app-fundamentals/sandboxing.md)

Questo articolo descrive il sandboxing un'applicazione Xamarin.Mac per la versione in App Store. Copre tutti gli elementi che costituiscono il sandboxing: directory contenitore, i diritti, determinato dall'utente, la separazione dei privilegi e autorizzazioni imposizione del kernel.

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[Riproduzione di suoni con AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

In questo articolo viene illustrato come utilizzare una classe helper per controllare la riproduzione di audio usando un AVAudioPlayer.

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[Segnalazione dei bug](~/mac/app-fundamentals/troubleshooting.md)

In alcuni casi si tutti spesso bloccati mentre si lavora in un progetto, l'impossibilità di ottenere un'API per funzionare nel modo desiderato o tentativo di risolvere un bug. È stata fornita alcune risorse necessarie per l'obiettivo di Xamarin è per poter essere eseguita correttamente in scrittura di applicazioni per dispositivi mobili e desktop.
