---
title: Domande frequenti
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 5a36c6ab14fdc7bfc5916456670be9c8fe4476ff
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049883"
---
# <a name="frequently-asked-questions"></a>Domande frequenti


## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[È possibile aggiornare il modello predefinito di Xamarin.Forms a un pacchetto NuGet più recente?](update-forms-template.md)
Questa Guida Usa il modello libreria di xamarin. Forms .NET Standard ad esempio, ma lo stesso metodo generale funziona anche per il modello di progetto condiviso xamarin. Forms. 

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[Perché la finestra di progettazione XAML di Visual Studio non funziona per i file XAML di Xamarin.Forms?](forms-xaml-designer.md)
Xamarin. Forms attualmente non supporta finestre di progettazione visiva per i file XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedlyandroid-linkassemblies-errormd"></a>[Errore di compilazione Android: errore imprevisto dell'attività "LinkAssemblies"](android-linkassemblies-error.md)
Si potrebbe visualizzare un messaggio di errore `The "LinkAssemblies" task failed unexpectedly` quando la compilazione di un progetto xamarin che utilizza form. Ciò si verifica quando il linker è attivo (in genere su un *versione* compilazione per ridurre le dimensioni del pacchetto dell'applicazione); e si verifica perché il dispositivo Android non vengono aggiornato per il framework più recente. 


## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["Perché non progetto Xamarin.Forms.Maps Android avrà esito negativo con COMPILETODALVIK: errore imprevisto di livello superiore?"](maps-compiletodalvik-error.md)
Questo errore può verificarsi nel riquadro di errore di Visual Studio per Mac o nella finestra di Output di compilazione di Visual Studio. nei progetti Android utilizzando Xamarin.Forms.Maps. Viene in genere risolto aumentando le dimensioni dell'Heap Java per il progetto xamarin.

