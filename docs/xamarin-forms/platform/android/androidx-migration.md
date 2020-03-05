---
title: Migrazione di AndroidX in Novell. Forms
description: Questo articolo spiega perché AndroidX esiste e come eseguire la migrazione a AndroidX nell'app Novell. Forms.
ms.prod: xamarin
ms.assetid: 98884003-E65A-4EB4-842D-66CFE27344A4
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 01/22/2020
ms.openlocfilehash: 13fb802dec326cdb82bac8825ca84343ef85b13e
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292919"
---
# <a name="androidx-migration-in-xamarinforms"></a>Migrazione di AndroidX in Novell. Forms

AndroidX sostituisce la libreria di supporto Android. Questo articolo spiega perché AndroidX esiste, come influisca su Novell. Forms e come eseguire la migrazione dell'applicazione per l'uso delle librerie AndroidX.

## <a name="history-of-androidx"></a>Cronologia di AndroidX

La libreria di supporto Android è stata creata per fornire funzionalità più recenti nelle versioni precedenti di Android. Si tratta di un livello di compatibilità che consente agli sviluppatori di usare funzionalità che potrebbero non esistere in tutte le versioni del sistema operativo Android e hanno fallback normali per le versioni precedenti. La libreria di supporto include anche le classi di praticità e supporto, gli strumenti di debug e di utilità e le classi sofisticate che dipendono da altre classi di librerie di supporto per funzionare.

Sebbene la libreria di supporto fosse originariamente un singolo file binario, è cresciuta e sviluppata in una suite di librerie, che sono quasi essenziali per lo sviluppo di app moderne. Di seguito sono riportate alcune funzionalità di uso comune della libreria di supporto:

- Classe di supporto `Fragment`.
- `RecyclerView`, utilizzato per la gestione di elenchi lunghi.
- Supporto di Multidex per le app con più di 65.536 metodi.
- Classe `ActivityCompat`.

AndroidX è una sostituzione per la libreria di supporto, che non viene più gestita. tutto lo sviluppo della nuova libreria verrà eseguito nella libreria AndroidX. AndroidX è una libreria riprogettata che usa il controllo delle versioni semantico, i nomi dei pacchetti più chiari e un supporto migliore per i modelli di architettura delle applicazioni comuni. AndroidX versione 1.0.0 è l'equivalente binario della versione della libreria di supporto 28.0.0. Per un elenco completo dei mapping delle classi dalla libreria di supporto a AndroidX, vedere [mapping delle classi della libreria di supporto](https://developer.android.com/jetpack/androidx/migrate/class-mappings) in Developer.Android.com.

Google ha creato un processo di migrazione denominato Jetifier con AndroidX. Il Jetifier controlla il bytecode jar durante il processo di compilazione e Rimappa i riferimenti alla libreria di supporto, sia nel codice dell'app che nelle dipendenze, al rispettivo equivalente di AndroidX.

In un'app Novell. Forms, così come in un'app Java Android, è necessario eseguire la migrazione delle dipendenze jar in AndroidX. Tuttavia, è necessario eseguire la migrazione anche delle associazioni Novell per puntare ai file jar sottostanti corretti. Novell. Forms ha aggiunto il supporto per la migrazione automatica di AndroidX nella versione 4,5.

Per altre informazioni su AndroidX, vedere [Panoramica di AndroidX](https://developer.android.com/jetpack/androidx) in Developer.Android.com.

## <a name="automatic-migration-in-xamarinforms"></a>Migrazione automatica in Novell. Forms

Per eseguire automaticamente la migrazione a AndroidX, un progetto Novell. Forms deve:

- Destinazione API Android versione 29 o successiva.
- Usare Novell. Forms versione 4,5 o successiva.

Dopo aver confermato queste impostazioni nel progetto, compilare l'app Android in Visual Studio 2019. Durante il processo di compilazione, il linguaggio intermedio (IL) viene controllato e le dipendenze della libreria di supporto e le associazioni vengono scambiate con le dipendenze AndroidX. Se l'applicazione dispone di tutte le dipendenze di AndroidX necessarie per la compilazione, non si noterà alcuna differenza nel processo di compilazione.

> [!NOTE]
> È necessario mantenere i riferimenti alla libreria di supporto nel progetto. Questi vengono usati per compilare l'applicazione prima che il processo di migrazione esami il risultato il e trasforma le dipendenze.

Se vengono rilevate dipendenze AndroidX che non fanno parte del progetto, viene restituito un errore di compilazione che indica i pacchetti AndroidX mancanti. Di seguito è riportato un errore di compilazione di esempio:

```
Could not find 37 AndroidX assemblies, make sure to install the following NuGet packages:
- Xamarin.AndroidX.Lifecycle.LiveData
- Xamarin.AndroidX.Browser
- Xamarin.Google.Android.Material
- Xamarin.AndroidX.Legacy.Supportv4
You can also copy and paste the following snippit into your .csproj file:
 <PackageReference Include="Xamarin.AndroidX.Lifecycle.LiveData" Version="2.1.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Browser" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.Google.Android.Material" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Legacy.Support.V4" Version="1.0.0-rc1" />
```

I pacchetti NuGet mancanti possono essere installati tramite Gestione pacchetti NuGet in Visual Studio o installati modificando il file Android. csproj per includere gli elementi `PackageReference` XML elencati nell'errore.

Una volta risolti i pacchetti mancanti, la ricompilazione del progetto carica i pacchetti mancanti e il progetto viene compilato usando le dipendenze AndroidX anziché le dipendenze della libreria di supporto.

> [!NOTE]
> Se il progetto e le dipendenze del progetto non fanno riferimento alle librerie di supporto Android, il processo di migrazione non esegue alcuna operazione e non viene eseguito.

## <a name="related-links"></a>Collegamenti correlati

- [Panoramica della libreria di supporto Android](https://developer.android.com/topic/libraries/support-library/index) su Developer.Android.com
- [Panoramica di AndroidX](https://developer.android.com/jetpack/androidx) in Developer.Android.com
