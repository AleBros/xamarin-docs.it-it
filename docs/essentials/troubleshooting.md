---
title: 'Xamarin.Essentials: Risoluzione dei problemi'
description: Questo documento descrive come risolvere i problemi riscontrati durante lo sviluppo con la libreria Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.openlocfilehash: a9546923c9a17aade58d830ea2504406ad09d7d7
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870079"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: Risoluzione dei problemi

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Errore: Conflitto di versioni rilevato per Xamarin.Android.Support.Compat

Potrebbe verificarsi l'errore seguente durante l'aggiornamento dei pacchetti NuGet (o l'aggiunta di un nuovo pacchetto) con un progetto Xamarin.Forms che usa Xamarin.Essentials:

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 1.1.0 -> Xamarin.Android.Support.CustomTabs 28.0.0.1 -> Xamarin.Android.Support.Compat (= 28.0.0.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

Il problema è la non corrispondenza delle dipendenze per i due pacchetti NuGet. Ciò può essere risolto aggiungendo manualmente una versione specifica della dipendenza (in questo caso **Xamarin.Android.Support.Compat**) in grado di supportare entrambi.

A tale scopo, aggiungere manualmente il NuGet che rappresenta l'origine del conflitto e usare l'elenco **Versione** per selezionare una versione specifica. Attualmente l'errore viene risolto dalla versione 28.0.0.1 di Xamarin.Android.Support.Compat & Xamarin.Android.Support.Core.Util NuGet.

Fare riferimento al [post di questo blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) per altre informazioni e un video sulle procedure per risolvere il problema.

Se si verificano problemi o si trova un bug segnalarlo nel [repository Xamarin.Essentials GitHub](https://github.com/xamarin/Essentials).
