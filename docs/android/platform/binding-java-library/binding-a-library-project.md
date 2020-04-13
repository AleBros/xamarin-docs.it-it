---
title: Associazione di un progetto di libreria Eclipse
description: Questa procedura dettagliata viene illustrato come utilizzare i modelli di progetto Xamarin.Android per associare un progetto di libreria Android Eclipse.This walkthrough explains how to use Xamarin.Android project templates to bind an Eclipse Android library project.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 2ac402bf423c9f3fe136d1ba31622d915d2e2eef
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027732"
---
# <a name="binding-an-eclipse-library-project"></a>Associazione di un progetto di libreria Eclipse

_Questa procedura dettagliata viene illustrato come utilizzare i modelli di progetto Xamarin.Android per associare un progetto di libreria Android Eclipse.This walkthrough explains how to use Xamarin.Android project templates to bind an Eclipse Android library project._

## <a name="overview"></a>Panoramica

Sebbene. I file AAR stanno diventando sempre più la norma per la distribuzione della libreria Android, in alcuni casi è necessario creare un'associazione per un *progetto di libreria Android.* I progetti di libreria Android sono progetti Android speciali che contengono codice condividabile e risorse a cui possono fare riferimento i progetti di applicazione Android.Android library projects are special Android projects that contain shareable code and resources that can be referenced by Android application projects. In genere, si esegue l'associazione a un progetto di libreria Android quando la libreria viene creata nell'IDE Eclipse.
In questa procedura dettagliata vengono forniti esempi di creazione di un progetto di libreria Android. ZIP dalla struttura di directory di un progetto Eclipse.

I progetti di libreria Android sono diversi dai normali progetti Android in quanto non vengono compilati in un APK e non sono, da soli, distribuibili su un dispositivo. Al contrario, un progetto di libreria Android è destinato a essere referenziato da un progetto di applicazione Android.Instead, an Android library project is meant to be referenced by an Android application project. Quando viene compilato un progetto di applicazione Android, il progetto di libreria Android viene compilato per primo. Il progetto di applicazione Android verrà quindi assorbito nel progetto di libreria Android compilato e includere il codice e le risorse nell'APK per la distribuzione. A causa di questa differenza, la creazione di un'associazione per un progetto di libreria Android è leggermente diversa rispetto alla creazione di un'associazione per un oggetto Java . JAR o . file AAR.

## <a name="walkthrough"></a>Procedura dettagliata

To use an Android library project in a Xamarin.Android Java Binding project it is first necessary to build the Android library project in Eclipse. La schermata seguente mostra un esempio di un progetto di libreria Android dopo la compilazione:The following screenshot shows an example of one Android library project after compilation: 

[![Esempio di progetto di libreria in EclipseExample library project in Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Si noti che il codice sorgente dal progetto di libreria Android è stato compilato in un file temporaneo. JAR denominato **android-mapviewballoons.jar**e che le risorse sono state copiate nella cartella **bin/res/crunch.** 

Una volta che il progetto di libreria Android è stato compilato in Eclipse, può quindi essere associato utilizzando un Xamarin.Android Java Binding progetto. Prima un . È necessario creare un file zip contenente le cartelle **bin** e **res** del progetto di libreria Android. È importante rimuovere la sottodirectory **crunch** intermedia in modo che le risorse risiedano in **bin/res**. La schermata seguente mostra il contenuto di uno di questi . File zip: 

[![Contenuto del progetto di libreria Android .zip](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

Questo. Viene quindi aggiunto al progetto Xamarin.Android Java Binding, come illustrato nella schermata seguente:

[![Zip aggiunto al progetto di associazione Java](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Si noti che l'azione di compilazione del file . Il file zip è stato impostato automaticamente su **LibraryProject.ip**.

Se ci sono . JAR che sono richiesti dal progetto di libreria Android, devono essere aggiunti alla cartella **Jars** del progetto Libreria di associazione Java e l'azione di **compilazione** impostata su **ReferenceJar**. Un esempio di questo può essere visto nella schermata qui sotto: 

[![Azione di compilazione impostata su ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Una volta completati questi passaggi, il progetto Xamarin.Android Java Binding può essere utilizzato come descritto in precedenza in questo documento.

> [!NOTE]
> La compilazione dei progetti di libreria Android in altri IDE non è attualmente supportata. Altri IDE potrebbero non creare la stessa struttura di directory o gli stessi file nella cartella **bin** di Eclipse. 

## <a name="summary"></a>Riepilogo

In questo articolo, abbiamo esaminato il processo di associazione di un progetto di libreria Android.In this article, we walked through the process of binding an Android library project. Abbiamo creato il progetto di libreria Android in Eclipse, quindi abbiamo creato un file zip dal **cestino** e dalle cartelle **res** del progetto di libreria Android. Successivamente, abbiamo usato questo zip per creare un progetto Xamarin.Android Java Binding.Next, we used this zip to create a Xamarin.Android Java Binding project. 
