---
title: 'Xamarin.Essentials: risoluzione dei problemi'
description: Questo documento descrive come risolvere i problemi riscontrati durante lo sviluppo con la libreria Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 2bd537a782b7090207b09ca02c5dfe5c4422a9ad
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "77545133"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: risoluzione dei problemi

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Errore: Conflitto di versioni rilevato per Xamarin.Android.Support.Compat

Potrebbe verificarsi l'errore seguente durante l'aggiornamento dei pacchetti NuGet (o l'aggiunta di un nuovo pacchetto) con un progetto Xamarin.Forms che usa Xamarin.Essentials:

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 1.3.1 -> Xamarin.Android.Support.CustomTabs 28.0.0.3 -> Xamarin.Android.Support.Compat (= 28.0.0.3) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

Il problema è la non corrispondenza delle dipendenze per i due pacchetti NuGet. Ciò può essere risolto aggiungendo manualmente una versione specifica della dipendenza (in questo caso **Xamarin.Android.Support.Compat**) in grado di supportare entrambi.

A tale scopo, aggiungere manualmente il NuGet che rappresenta l'origine del conflitto e usare l'elenco **Versione** per selezionare una versione specifica. Attualmente la versione 28.0.0.3 di Xamarin.Android.Support.Compat & Xamarin.Android.Support.Core.Util NuGet risolverà questo errore.

Fare riferimento al [post di questo blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) per altre informazioni e un video sulle procedure per risolvere il problema.

Se si verificano problemi o si trova un bug segnalarlo nel [repository Xamarin.Essentials GitHub](https://github.com/xamarin/Essentials).
