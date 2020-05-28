---
title: Xamarin.FormsDomande frequenti
ms.topic: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: edd6cfefe18ff3d5cc97ec58f3bce867f11df7c8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135876"
---
# <a name="xamarinforms-frequently-asked-questions"></a>Xamarin.FormsDomande frequenti

## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[È possibile aggiornare il Xamarin.Forms modello predefinito a un pacchetto NuGet più recente?](update-forms-template.md)
Questa guida usa il Xamarin.Forms modello di libreria .NET standard come esempio, ma lo stesso metodo generale funzionerà anche per il Xamarin.Forms modello di progetto condiviso.

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[Perché la finestra di progettazione XAML di Visual Studio non funziona per Xamarin.Forms i file XAML?](forms-xaml-designer.md)
Xamarin.FormsAttualmente non supporta le finestre di progettazione visiva per i file XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedly"></a>[Errore di compilazione Android: l'attività "LinkAssemblies" non è riuscita in modo imprevisto](android-linkassemblies-error.md)
È possibile che venga visualizzato un messaggio di errore `The "LinkAssemblies" task failed unexpectedly` quando si compila un progetto Novell. Android che usa i moduli. Questo errore si verifica quando il linker è attivo (in genere in una build di *rilascio* per ridurre le dimensioni del pacchetto dell'app); il problema si verifica perché le destinazioni Android non vengono aggiornate al Framework più recente. 

## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["Perché My Xamarin.Forms . Il progetto Android di Maps non riesce con COMPILETODALVIK: errore di livello superiore imprevisto? "](maps-compiletodalvik-error.md)
Questo errore può essere visualizzato nel riquadro degli errori di Visual Studio per Mac o nella finestra di output di compilazione di Visual Studio; nei progetti Android usando Xamarin.Forms . Mappe. Viene in genere risolto aumentando le dimensioni dell'heap Java per il progetto Novell. Android.
