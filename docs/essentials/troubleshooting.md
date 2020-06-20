---
title: 'Xamarin.Essentials: Risoluzione dei problemi'
description: In questo documento viene descritto come risolvere i problemi riscontrati durante lo sviluppo con la Xamarin.Essentials libreria.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 67a060667cef5f96fa864aa2db3356ac5b9b2dfb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137124"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: Risoluzione dei problemi

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Errore: Conflitto di versioni rilevato per Xamarin.Android.Support.Compat

L'errore seguente può verificarsi quando si aggiornano i pacchetti NuGet (o si aggiunge un nuovo pacchetto) con unXamarin.Forms
progetto che usa Xamarin.Essentials :

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue.
 MyApp -> Xamarin.Essentials 1.3.1 -> Xamarin.Android.Support.CustomTabs 28.0.0.3 -> Xamarin.Android.Support.Compat (= 28.0.0.3)
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

Il problema è la non corrispondenza delle dipendenze per i due pacchetti NuGet. Ciò può essere risolto aggiungendo manualmente una versione specifica della dipendenza (in questo caso **Xamarin.Android.Support.Compat**) in grado di supportare entrambi.

A tale scopo, aggiungere manualmente il NuGet che rappresenta l'origine del conflitto e usare l'elenco **Versione** per selezionare una versione specifica. Attualmente la versione 28.0.0.3 di Novell. Android. support. compat & Novell. Android. support. Core. util NuGet risolverà l'errore.

Fare riferimento al [post di questo blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) per altre informazioni e un video sulle procedure per risolvere il problema.

Se si verificano problemi o si trova un bug, segnalarlo nel [ Xamarin.Essentials repository GitHub](https://github.com/xamarin/Essentials).
