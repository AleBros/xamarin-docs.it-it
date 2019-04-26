---
title: Associazione di un progetto di libreria Eclipse
description: Questa procedura dettagliata illustra come usare i modelli di progetto xamarin. Android per associare un progetto di libreria Android di Eclipse.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a5887748eda8af89b9215e3e121db592dfa73935
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957656"
---
# <a name="binding-an-eclipse-library-project"></a>Associazione di un progetto di libreria Eclipse

_Questa procedura dettagliata illustra come usare i modelli di progetto xamarin. Android per associare un progetto di libreria Android di Eclipse._


## <a name="overview"></a>Panoramica

Anche se. I file AAR sempre più stanno diventando la norma per la distribuzione di libreria Android, in alcuni casi è necessario creare un'associazione per un *progetto di libreria Android*. Progetti di libreria Android sono i progetti Android speciali che contengono codice condivisibile e risorse che è possono farvi riferimento dai progetti di applicazione Android. In genere, si associa un progetto di libreria Android quando viene creata la raccolta nell'IDE di Eclipse.
Questa procedura dettagliata vengono forniti esempi di come creare un progetto di libreria Android. Codice postale dalla struttura di directory di un progetto di Eclipse.

Progetti di libreria Android sono diversi dai progetti Android regolari, in quanto non sono compilati in un file APK e non lo sono, in modo indipendente, distribuibili in un dispositivo. Al contrario, un progetto di libreria Android è progettato per farvi riferimento da un progetto di applicazione Android. Quando viene compilato un progetto di applicazione Android, viene innanzitutto compilato il progetto di libreria Android. Il progetto di applicazione Android verrà quindi assorbire il momento nel progetto di libreria Android compilate e includere il codice e le risorse nel file APK per la distribuzione. A causa di questa differenza, creazione di un'associazione per un progetto di libreria Android è leggermente diversa rispetto alla creazione di un'associazione per un oggetto Java. File JAR di o. File AAR.



## <a name="walkthrough"></a>Procedura dettagliata

Per usare un progetto di libreria Android in un progetto xamarin. Android Java Binding è necessario innanzitutto compilare il progetto di libreria Android in Eclipse. Lo screenshot seguente mostra un esempio di un progetto di libreria Android dopo la compilazione: 

[![Progetto libreria di esempio in Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Si noti che a un elemento temporaneo sia stato compilato il codice sorgente dal progetto di libreria Android. File di JAR denominato **android mapviewballoons.jar**, e che le risorse siano state copiate i **bin/res/masticare** cartella. 

Una volta compilato il progetto di libreria Android in Eclipse, può quindi essere associato usando un progetto xamarin. Android Binding di linguaggio. Prima una. È necessario creare file con estensione ZIP che contiene il **bin** e **res** cartelle del progetto di libreria Android. È importante rimuovere il tipo di protezione **masticare** sottodirectory in modo che le risorse si trovano in **bin/res**. Lo screenshot seguente mostra il contenuto di una tale. File con estensione ZIP: 

[![Contenuto del file zip del progetto libreria Android](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

In questo. File ZIP viene quindi aggiunto al progetto xamarin. Android Java Binding, come illustrato nello screenshot seguente:

[![Codice postale aggiunto al progetto Java di associazione](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Si noti che l'azione di compilazione di. File ZIP è stato impostato automaticamente su **LibraryProjectZip**.

Se presenti. File con estensione JAR necessari per il progetto di libreria Android, devono essere aggiunte alle **Jars** cartella del progetto libreria di Binding Java e il **azione di compilazione** impostato su **ReferenceJar**. Un esempio di questo può essere visualizzato nello screenshot seguente: 

[![Impostare su ReferenceJar azione di compilazione](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Dopo aver completato questi passaggi, il progetto xamarin. Android Java Binding è utilizzabile come descritto in precedenza in questo documento.

> [!NOTE]
> La compilazione di progetti di libreria Android in altri IDE non è supportata in questo momento. Altri IDE non è possibile creare la stessa struttura di directory o file nei **bin** cartella Eclipse. 


## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato il processo di associazione di un progetto di libreria Android. È stato compilato il progetto di libreria Android in Eclipse, quindi è stato creato un file zip dal **bin** e **res** cartelle del progetto di libreria Android. Successivamente, abbiamo utilizzato questo file zip per creare un progetto xamarin. Android Java Binding. 

