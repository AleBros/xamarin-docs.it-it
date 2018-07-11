---
title: 'Xamarin.Essentials: risoluzione dei problemi'
description: Questo documento descrive come risolvere i problemi riscontrati durante lo sviluppo con la libreria Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 06/26/2018
ms.openlocfilehash: 8cb18ab029d2fd161c60fda7e130f319b8f0c3ab
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947374"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: risoluzione dei problemi

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Errore: Conflitto di versioni rilevato per Xamarin.Android.Support.Compat

Potrebbe verificarsi l'errore seguente quando si aggiornano i pacchetti NuGet (o l'aggiunta di un nuovo pacchetto) con un progetto xamarin. Forms che utilizza Xamarin.Essentials:

```
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.8.0-preview -> Xamarin.Android.Support.CustomTabs 27.0.2.1 -> Xamarin.Android.Support.Compat (= 27.0.2.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

Il problema è senza corrispondenza delle dipendenze per i due pacchetti NuGet. Può essere risolto aggiungendo manualmente una versione specifica della dipendenza (in questo caso **Xamarin.Android.Support.Compat**) che può supportare entrambi.

A tale scopo, aggiungere il NuGet che rappresenta l'origine del conflitto manualmente e usare la **versione** elenco per selezionare una versione specifica. Attualmente versione 27.0.2.1 Xamarin.Android.Support.Compat & Xamarin.Android.Support.Core.Util NuGet risolverà l'errore.

Fare riferimento a [questo post di blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) per altre informazioni e un video sulle procedure per risolvere il problema.

Se verificano problemi o trova un bug segnalarlo sul [repository Xamarin.Essentials GitHub](http://github.com/xamarin/Essentials).
