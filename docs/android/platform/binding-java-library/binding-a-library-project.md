---
title: Associazione di un progetto di libreria Eclipse
description: Questa procedura dettagliata illustra come usare i modelli di progetto Novell. Android per associare un progetto di libreria Android per Eclipse.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 2ac402bf423c9f3fe136d1ba31622d915d2e2eef
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027732"
---
# <a name="binding-an-eclipse-library-project"></a>Associazione di un progetto di libreria Eclipse

_Questa procedura dettagliata illustra come usare i modelli di progetto Novell. Android per associare un progetto di libreria Android per Eclipse._

## <a name="overview"></a>Panoramica

Anche se. I file AAR stanno diventando sempre più la norma per la distribuzione della libreria Android, in alcuni casi è necessario creare un'associazione per un *progetto di libreria Android*. I progetti di libreria Android sono progetti Android speciali che contengono codice condivisibile e risorse a cui possono fare riferimento i progetti di applicazioni Android. In genere, si esegue l'associazione a un progetto di libreria Android quando la libreria viene creata nell'IDE di Eclipse.
Questa procedura dettagliata fornisce esempi su come creare un progetto di libreria Android. ZIP dalla struttura di directory di un progetto Eclipse.

I progetti di libreria Android sono diversi dai progetti Android normali perché non vengono compilati in un file APK e non sono, in modo autonomo, distribuibile in un dispositivo. Un progetto di libreria Android viene invece usato come riferimento da un progetto di applicazione Android. Quando viene compilato un progetto di applicazione Android, viene compilato per primo il progetto di libreria Android. Il progetto di applicazione Android verrà quindi assorbito nel progetto di libreria Android compilato e includerà il codice e le risorse nell'APK per la distribuzione. A causa di questa differenza, la creazione di un'associazione per un progetto di libreria Android è leggermente diversa rispetto alla creazione di un'associazione per un Java. JAR o. File AAR.

## <a name="walkthrough"></a>Procedura dettagliata

Per usare un progetto di libreria Android in un progetto di binding Java per Novell. Android, è necessario innanzitutto compilare il progetto di libreria Android in Eclipse. La schermata seguente mostra un esempio di un progetto di libreria Android dopo la compilazione: 

[progetto di libreria di esempio ![in Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Si noti che il codice sorgente del progetto di libreria Android è stato compilato in un oggetto temporaneo. File JAR denominato **Android-mapviewballoons. jar**e che le risorse sono state copiate nella cartella **bin/res/Crunch** . 

Dopo che il progetto di libreria Android è stato compilato in Eclipse, può essere associato usando un progetto di binding Java Novell. Android. Primo oggetto a. È necessario creare il file ZIP contenente le cartelle **bin** e **res** del progetto di libreria Android. È importante rimuovere la sottodirectory **Crunch** corrispondente in modo che le risorse risiedano in **bin/res**. Lo screenshot seguente mostra il contenuto di uno di questi. File ZIP: 

[Contenuto ![del file di libreria Android Project. zip](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

Questo. Il file ZIP viene quindi aggiunto al progetto di binding Java Novell. Android, come illustrato nello screenshot seguente:

[![zip aggiunto al progetto di associazione Java](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Si noti che l'azione di compilazione di. Il file ZIP è stato impostato automaticamente su **LibraryProjectZip**.

Se ne esiste uno. I file JAR necessari per il progetto di libreria Android devono essere aggiunti alla cartella **jar** del progetto di libreria di binding Java e all'azione di **compilazione** impostata su **ReferenceJar**. Un esempio può essere illustrato nello screenshot seguente: 

[![azione di compilazione impostata su ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Una volta completati questi passaggi, è possibile usare il progetto di associazione Java Novell. Android, come descritto in precedenza in questo documento.

> [!NOTE]
> La compilazione dei progetti di libreria Android in altri IDE non è supportata in questo momento. Altri IDE non possono creare la stessa struttura di directory o i file nella cartella **bin** come Eclipse. 

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato il processo di associazione di un progetto di libreria Android. Il progetto di libreria Android è stato compilato in Eclipse, quindi è stato creato un file zip dalle cartelle **bin** e **res** del progetto di libreria Android. Successivamente, è stato usato questo file zip per creare un progetto di binding Java per Novell. Android. 
