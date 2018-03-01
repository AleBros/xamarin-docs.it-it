---
title: Domande frequenti
ms.topic: article
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 95728bf6bf1009db1cc834bf1d9d0be6a8fc5ef5
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="frequently-asked-questions"></a>Domande frequenti


## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[È possibile aggiornare il modello predefinito di xamarin. Forms per un pacchetto NuGet più recente?](update-forms-template.md)
Questa Guida Usa il modello libreria di classi Portabile xamarin. Forms ad esempio, ma lo stesso metodo generale funziona anche per il modello di progetto condiviso xamarin. Forms. 

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[Perché la finestra di progettazione XAML di Visual Studio non funziona per i file XAML di xamarin. Forms?](forms-xaml-designer.md)
Xamarin. Forms attualmente non supporta finestre di progettazione visiva per i file XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedlyandroid-linkassemblies-errormd"></a>[Errore di compilazione Android: l'attività "LinkAssemblies" non è riuscita](android-linkassemblies-error.md)
Si potrebbe visualizzare un messaggio di errore `The "LinkAssemblies" task failed unexpectedly` quando la compilazione di un progetto xamarin che utilizza form. Ciò si verifica quando il linker è attivo (in genere su un *versione* compilazione per ridurre le dimensioni del pacchetto dell'applicazione); e si verifica perché il dispositivo Android non vengono aggiornato per il framework più recente. 


## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["Perché non progetto Xamarin.Forms.Maps Android avrà esito negativo con COMPILETODALVIK: errore imprevisto di livello superiore?"](maps-compiletodalvik-error.md)
Questo errore può verificarsi nel riquadro di errore di Visual Studio per Mac o nella finestra di Output di compilazione di Visual Studio. nei progetti Android utilizzando Xamarin.Forms.Maps. Viene in genere risolto aumentando le dimensioni dell'Heap Java per il progetto xamarin.

