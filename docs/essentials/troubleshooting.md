---
title: 'Xamarin.Essentials: Risoluzione dei problemi'
description: Questo documento descrive come risolvere i problemi riscontrati durante lo sviluppo con la libreria Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: d13589680161de4c9b5d77eef6d5f823cc884136
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671429"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: Risoluzione dei problemi

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Errore: Conflitto di versioni rilevato per Xamarin.Android.Support.Compat

Potrebbe verificarsi l'errore seguente durante l'aggiornamento dei pacchetti NuGet (o l'aggiunta di un nuovo pacchetto) con un progetto Xamarin.Forms che usa Xamarin.Essentials:

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.8.0-preview -> Xamarin.Android.Support.CustomTabs 27.0.2.1 -> Xamarin.Android.Support.Compat (= 27.0.2.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

Il problema è la non corrispondenza delle dipendenze per i due pacchetti NuGet. Ciò può essere risolto aggiungendo manualmente una versione specifica della dipendenza (in questo caso **Xamarin.Android.Support.Compat**) in grado di supportare entrambi.

A tale scopo, aggiungere manualmente il NuGet che rappresenta l'origine del conflitto e usare l'elenco **Versione** per selezionare una versione specifica. Attualmente l'errore viene risolto dalla versione 27.0.2.1 di Xamarin.Android.Support.Compat & Xamarin.Android.Support.Core.Util NuGet.

Fare riferimento al [post di questo blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) per altre informazioni e un video sulle procedure per risolvere il problema.

Se si verificano problemi o si trova un bug segnalarlo nel [repository Xamarin.Essentials GitHub](https://github.com/xamarin/Essentials).
