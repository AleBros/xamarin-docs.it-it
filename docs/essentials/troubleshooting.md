---
title: 'Xamarin.Essentials: risoluzione dei problemi'
description: Questo documento descrive come risolvere i problemi riscontrati durante lo sviluppo con la libreria Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 06/26/2018
ms.openlocfilehash: 3dba315aec2475cb334110ba7555f773f4165aa1
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066734"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: risoluzione dei problemi

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Errore: Conflitto di versione rilevato per Xamarin.Android.Support.Compat

Potrebbe verificarsi l'errore seguente durante l'aggiornamento dei pacchetti NuGet (o l'aggiunta di un nuovo pacchetto) con un progetto xamarin. Forms che utilizza Xamarin.Essentials:

```
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.7.0.17-preview -> Xamarin.Android.Support.CustomTabs 27.0.2 -> Xamarin.Android.Support.Compat (= 27.0.2) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

Il problema si verifica dipendenze non corrispondente per due NuGets. Ciò può essere risolto aggiungendo manualmente una versione specifica della dipendenza (in questo caso **Xamarin.Android.Support.Compat**) che può supportare entrambi.

A tale scopo, aggiungere NuGet che rappresenta l'origine del conflitto manualmente e usare la **versione** elenco per selezionare una versione specifica. Attualmente versione 27.0.2 di Xamarin.Android.Support.Compat NuGet risolverà l'errore.

Fare riferimento a [questo post di blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) per altre informazioni e un video su come risolvere il problema.

Se verificano problemi o trova un bug segnalarlo nel [repository Xamarin.Essentials GitHub](http://github.com/xamarin/Essentials).
