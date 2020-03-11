---
title: AndroidX
description: Come iniziare a sviluppare app con AndroidX usando Novell. Android.
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: ad6ea2f68fc01183f7ed42e85094f6be5fb3d9f9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78292453"
---
# <a name="androidx-with-xamarin"></a>AndroidX con Novell

_Come iniziare a sviluppare app con AndroidX usando Novell. Android._

AndroidX è un miglioramento importante per la libreria di supporto Android originale, che non viene più mantenuta. I pacchetti **AndroidX** sostituiscono completamente la libreria di supporto Android fornendo la parità di funzionalità e le nuove librerie che è possibile usare nelle applicazioni Android.

AndroidX include le funzionalità seguenti:

- Tutti i pacchetti all'interno di AndroidX hanno ora uno spazio dei nomi coerente che inizia con `androidx`. Ciò significa che tutti i pacchetti della libreria di supporto Android vengono mappati a un pacchetto di `androidx.*` corrispondente.
- i pacchetti `androidx` vengono gestiti e aggiornati separatamente. Ciò significa che è possibile aggiornare le librerie AndroidX indipendentemente l'una dall'altra.
- A partire da V28 della libreria di supporto Android, non saranno più disponibili versioni. Tutto lo sviluppo verrà incluso in `androidx`.

![Logo di AndroidX](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>Requisiti

L'elenco seguente è necessario per usare le funzionalità di AndroidX nelle app basate su Novell:

- **Visual Studio** -in Windows Update per visual studio 2019 versione 16,4 o successiva. In macOS eseguire l'aggiornamento a Visual Studio 2019 per Mac versione 8,4 o successiva.
- **Novell. Android** : Novell. Android 10,0 o versione successiva deve essere installato con Visual Studio (Novell. Android viene installato automaticamente come parte del carico di lavoro **sviluppo di applicazioni per dispositivi mobili con .NET** in Windows e installato come parte del **programma di installazione di Visual Studio per Mac**)
- **Java Developer Kit** : lo sviluppo di Novell. Android 10,0 richiede JDK 8. La distribuzione di Microsoft OpenJDK viene installata automaticamente come parte di Visual Studio.
- **Android SDK** Android SDK API 28 o versione successiva deve essere installato tramite il gestore di Android SDK.

## <a name="get-started"></a>Attività iniziali

Per iniziare a usare AndroidX, è possibile includere qualsiasi [pacchetto NuGet AndroidX](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22) all'interno del progetto Android. Altre informazioni sull'installazione e l'uso di un pacchetto in [Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio) o [Visual Studio per Mac](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac)

## <a name="behavior-changes"></a>Modifiche funzionali

Poiché AndroidX è una riprogettazione della libreria di supporto Android, include passaggi di migrazione che influiranno sulle applicazioni Android compilate con la libreria di supporto Android.

### <a name="package-name-change"></a>Modifica del nome del pacchetto
I nomi dei pacchetti sono stati modificati tra i pacchetti obsoleti e nuovi. Di seguito è riportato un esempio di queste modifiche:

| Precedente                    | Nuovo                    |
| ---------------------- | ---------------------- |
| Android. support. * *     | androidx. @             |
| Android. Design. * *      | com. Google. Android. Material. @ |
| Android. support. test. * * | androidx. test. @       |
| Android. Arch. * *        | androidx. @             |
| Android. Arch. Persistence. room. * * | androidx. room. @ |
| Android. Arch. Persistence. * * | androidx. sqlite. @ |

Per ulteriori informazioni sulla denominazione dei pacchetti, [vedere la documentazione seguente](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings).

## <a name="migration-tooling"></a>Strumenti di migrazione

Per l'applicazione è necessario tenere presenti tre passaggi di migrazione.

1. Se l'applicazione include **spazi dei nomi della libreria di supporto per Android e si desidera eseguirne la migrazione negli spazi dei nomi AndroidX**, è possibile usare gli strumenti dell'IDE **migrate to AndroidX** per gestire la maggior parte degli scenari dello spazio dei nomi. 

Abilitare il **Migrator AndroidX** tramite **strumenti > opzioni > Novell > le impostazioni Android** all'interno di Visual Studio 2019 (è possibile ignorare questo passaggio in Visual Studio per Mac).

![Abilita Migrator AndroidX](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

Fare clic con il pulsante destro del mouse sul progetto ed **eseguire la migrazione a AndroidX**.

![Eseguire la migrazione a AndroidX](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> È necessario apportare alcune modifiche manuali dello spazio dei nomi per gli scenari che lo strumento non copre. Mentre verrà mappato il pacchetto corretto, è consigliabile esaminare i mapping ufficiale degli [artefatti](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings) e i [mapping delle classi](https://developer.android.com/jetpack/androidx/migrate/class-mappings) per facilitare la migrazione del progetto.

2. Se l'applicazione include **tutte le dipendenze di cui non è stata eseguita la migrazione allo spazio dei nomi AndroidX**, è necessario usare la [libreria di supporto Android per AndroidX il pacchetto di migrazione.](https://www.nuget.org/packages/Xamarin.AndroidX.Migration)
3. Se l'applicazione **non include alcuna dipendenza che richiede la migrazione dello spazio dei nomi AndroidX**, è possibile usare [attualmente le librerie AndroidX in NuGet](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22).

## <a name="troubleshooting"></a>Risoluzione dei problemi

- Alcuni pacchetti di architettura in AndroidX sono in conflitto con le versioni della libreria di supporto. Per risolvere questo problema, è necessario usare la versione AndroidX di questi pacchetti e rimuovere la versione della libreria di supporto. Se, ad esempio, si fa riferimento a `Xamarin.Android.Arch.Work.Runtime` nel progetto, si verifica un conflitto con i tipi del pacchetto di `AndroidX.Work` appena aggiunto.

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato AndroidX e spiega come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Novell. Android con AndroidX. Viene fornita una panoramica della AndroidX. Sono inclusi collegamenti alla documentazione API e agli argomenti per sviluppatori Android che consentono di iniziare a creare app usando AndroidX. Sono state inoltre evidenziate le modifiche più importanti del comportamento AndroidX e gli argomenti per la risoluzione dei problemi che potrebbero influito sulle app esistenti.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a AndroidX | Novell Show](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [AndroidX](https://developer.android.com/jetpack/androidx)
- [Repository GitHub Novell AndroidX](https://github.com/xamarin/AndroidX)
- [Repository GitHub per la migrazione di Novell AndroidX](https://github.com/xamarin/XamarinAndroidXMigration)
