---
title: Associazione di un progetto libreria di Eclipse
description: Questa procedura dettagliata viene illustrato come utilizzare i modelli di progetto xamarin per associare un progetto di libreria Android Eclipse.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 1c3c33f1b958aff5818b26e4408e60f449b46f41
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="binding-an-eclipse-library-project"></a>Associazione di un progetto libreria di Eclipse

_Questa procedura dettagliata viene illustrato come utilizzare i modelli di progetto xamarin per associare un progetto di libreria Android Eclipse._


## <a name="overview"></a>Panoramica

Sebbene. I file AAR stanno diventando sempre più la norma per la distribuzione di libreria Android, in alcuni casi è necessario creare un'associazione per un *progetto di libreria Android*. I progetti di libreria Android sono progetti Android speciali contenenti codice condivisibile e risorse che è possibile fare riferimento dai progetti di applicazione Android. In genere, si associa un progetto di libreria Android quando la creazione della raccolta nell'IDE di Eclipse.
Questa procedura dettagliata vengono forniti esempi di come creare un progetto di libreria Android. Codice postale dalla struttura di directory di un progetto Eclipse.

Progetti di libreria Android sono diversi dai progetti Android regolari non sono compilati in un file APK e non sono, in modo indipendente, distribuibili in un dispositivo. Al contrario, un progetto di libreria Android è progettato per essere a cui fa riferimento un progetto di applicazione Android. Quando viene compilato un progetto di applicazione Android, è innanzitutto compilare il progetto di libreria Android. Il progetto di applicazione Android verrà quindi angolare nel progetto di libreria Android compilate e includere il codice e risorse nel file APK per la distribuzione. A causa di questa differenza, creazione di un'associazione per un progetto di libreria Android è leggermente diversa rispetto alla creazione di un'associazione per un linguaggio. File JAR o. File AAR.



## <a name="walkthrough"></a>Procedura dettagliata

Per utilizzare un progetto di libreria Android in un progetto xamarin Java associazione è necessario innanzitutto compilare il progetto di libreria Android in Eclipse. Nella schermata seguente viene illustrato un esempio di un progetto di libreria Android dopo la compilazione: 

[![Progetto di libreria di esempio in Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Si noti che il codice sorgente dal progetto di libreria Android sia stato compilato per una variabile temporanea. File JAR denominato **android mapviewballoons.jar**, e che le risorse sono state copiate i **res/bin/elaborare** cartella. 

Dopo avere compilato il progetto di libreria Android in Eclipse, si può quindi essere associato usando un progetto xamarin Binding di linguaggio. Primo una. È necessario creare il file ZIP che contiene il **bin** e **res** cartelle del progetto di libreria Android. È importante rimuovere il tipo di protezione **elaborare** sottodirectory in modo che le risorse si trovano in **bin/res**. La schermata seguente mostra il contenuto di un tale. File ZIP: 

[![Contenuto di file ZIP di progetto di libreria Android](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

Questo. File ZIP viene quindi aggiunto al progetto xamarin Java associazione, come illustrato nella schermata seguente:

[![Aggiunto al progetto Java Binding ZIP](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Si noti che l'azione di compilazione del. File ZIP è stato impostato automaticamente su **LibraryProjectZip**.

Se sono presenti. File JAR necessari per il progetto di libreria Android, essi devono essere aggiunti al **file JAR** nella cartella del progetto libreria di Binding di linguaggio e **azione di compilazione** impostato su **ReferenceJar**. Un esempio di questo oggetto può essere visualizzato nella schermata seguente: 

[![Impostare su ReferenceJar azione di compilazione](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Dopo aver completati questi passaggi, il progetto xamarin Java Binding utilizzabile come descritto in precedenza in questo documento.

> [!NOTE]
> In questo momento non è possibile compilare i progetti di libreria Android in altri IDE. Altri IDE non è possibile creare la stessa struttura di directory o file di **bin** cartella Eclipse. 


## <a name="summary"></a>Riepilogo

In questo articolo, abbiamo esaminato in dettaglio il processo di associazione di un progetto di libreria Android. È stato compilato il progetto di libreria Android in Eclipse, quindi è stato creato un file zip dal **bin** e **res** cartelle del progetto di libreria Android. Successivamente, questo zip è utilizzato per creare un progetto xamarin Binding di linguaggio. 

