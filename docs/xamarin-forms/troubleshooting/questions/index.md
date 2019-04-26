---
title: Domande frequenti su xamarin. Forms
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 24e2ae456e478585f30aa704917f66bb0bf11da9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247791"
---
# <a name="xamarinforms-frequently-asked-questions"></a>Domande frequenti su xamarin. Forms

## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[È possibile aggiornare il modello predefinito di Xamarin.Forms a un pacchetto NuGet più recente?](update-forms-template.md)
Questa Guida Usa il modello di libreria xamarin. Forms .NET Standard come esempio, ma lo stesso metodo generale funzioneranno anche per il modello di progetto condiviso xamarin. Forms.

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[Perché la finestra di progettazione XAML di Visual Studio non funziona per i file XAML di Xamarin.Forms?](forms-xaml-designer.md)
Xamarin. Forms attualmente non supporta finestre di progettazione visiva per i file XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedlyandroid-linkassemblies-errormd"></a>[Errore di compilazione Android: Errore imprevisto dell'attività "LinkAssemblies"](android-linkassemblies-error.md)
Si può vedere un messaggio di errore `The "LinkAssemblies" task failed unexpectedly` quando la compilazione di un progetto xamarin. Android che utilizza form. Ciò si verifica quando il linker è attivo (in genere in un *rilascio* compilazione per ridurre le dimensioni del pacchetto dell'app); e ciò avviene perché gli obiettivi di Android non sono aggiornati per il framework più recente. 

## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["Perché non il progetto Android verifica riesce con COMPILETODALVIK: ERRORE PRIMO LIVELLO IMPREVISTO?"](maps-compiletodalvik-error.md)
Questo errore potrebbe essere presente il riquadro errori di Visual Studio per Mac o nella finestra di Output di compilazione di Visual Studio. nei progetti Android tramite verifica. Viene in genere risolto aumentando le dimensioni dell'Heap di Java per il progetto xamarin. Android.
