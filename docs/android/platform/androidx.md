---
title: AndroidX
description: Come iniziare a sviluppare app con AndroidX usando Xamarin.Android.
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: ad6ea2f68fc01183f7ed42e85094f6be5fb3d9f9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292453"
---
# <a name="androidx-with-xamarin"></a>AndroidX con Xamarin

_Come iniziare a sviluppare app con AndroidX usando Xamarin.Android._

AndroidX è un importante miglioramento per l'originale Android Support Library, che non è più mantenuto. I pacchetti **AndroidX** sostituiscono completamente la libreria di supporto Android fornendo parità di funzionalità e nuove librerie che è possibile utilizzare nelle applicazioni Android.

AndroidX include le seguenti funzionalità:

- Tutti i pacchetti all'interno di `androidx`AndroidX ora hanno uno spazio dei nomi coerente a partire da . Ciò significa che tutti i `androidx.*` pacchetti della libreria di supporto Android eseguono il mapping a un pacchetto corrispondente.
- `androidx`pacchetti vengono gestiti e aggiornati separatamente. Ciò significa che è possibile aggiornare le librerie AndroidX indipendentemente l'una dall'altra.
- A partire dalla v28 della libreria di supporto Android, non ci saranno più versioni. Tutto lo sviluppo `androidx` sarà incluso in invece.

![AndroidX Logo](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>Requisiti

L'elenco seguente è necessario per utilizzare le funzionalità AndroidX nelle app basate su Xamarin:

- **Visual Studio** - In Windows update a Visual Studio 2019 versione 16.4 o successiva. In macOS, eseguire l'aggiornamento a Visual Studio 2019 per Mac versione 8.4 o successiva.
- **Xamarin.Android** - Xamarin.Android 10.0 o versione successiva deve essere installato con Visual Studio (Xamarin.Android viene installato automaticamente come parte del carico di lavoro **di sviluppo per dispositivi mobili con .NET** in Windows e installato come parte del programma di installazione di Visual Studio per **Mac**)
- **Java Developer Kit** - Xamarin.Android 10.0 sviluppo richiede JDK 8. La distribuzione di Microsoft di OpenJDK viene installata automaticamente come parte di Visual Studio.
- **Android SDK:** Android SDK API 28 o versione successiva deve essere installato tramite Android SDK Manager.

## <a name="get-started"></a>Introduzione

Puoi iniziare con AndroidX includendo qualsiasi [pacchetto AndroidX NuGet](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22) all'interno del tuo progetto Android. Altre informazioni sull'installazione e l'uso di un pacchetto in Visual Studio o [Visual Studio per MacLearn](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac) more about installing and using a package in [Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio) or Visual Studio for Mac

## <a name="behavior-changes"></a>Modifiche funzionali

Poiché AndroidX è una riprogettazione della libreria di supporto Android, include passaggi di migrazione che influiranno sulle applicazioni Android create con la libreria di supporto Android.Because AndroidX is a redesign of the Android Support Library, it includes migration steps that will affect Android applications built with the Android Support Library.

### <a name="package-name-change"></a>Modifica del nome del pacchetto
I nomi dei pacchetti sono stati modificati tra il vecchio e il nuovo pacchetto. Di seguito è riportato un esempio di queste modifiche:

| Vecchio                    | Nuovo                    |
| ---------------------- | ---------------------- |
| androide.supporto.     | androidx. @             |
| androide.      | com.google.android.materiale. @ |
| androide.supporto.test. | androidx.test. @       |
| android.arch.        | androidx. @             |
| android.arch.persistence.room. | androidx.room. @ |
| android.arch.persistence. | androidx.sqlite. @ |

Per ulteriori informazioni sulla denominazione dei pacchetti, [vedere la documentazione seguente.](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings)

## <a name="migration-tooling"></a>Strumenti di migrazione

Esistono tre passaggi di migrazione che è consigliabile conoscere per l'applicazione.

1. Se l'applicazione include gli spazi dei nomi della libreria di supporto **Android e si desidera eseguirne la migrazione agli spazi dei nomi AndroidX,** è possibile utilizzare gli strumenti **Migra a IDE Di meditta** per gestire la maggior parte degli scenari dello spazio dei nomi. 

Abilitare **AndroidX Migrator** tramite **Strumenti > Opzioni > Xamarin > Impostazioni Android** all'interno di Visual Studio 2019 (è possibile ignorare questo passaggio in Visual Studio per Mac).

![Abilita AndroidX Migrator](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

Fare clic con il pulsante destro del mouse sul progetto e **scegliere Esegui migrazione a AndroidX**.

![Eseguire la migrazione ad AndroidXMigrate to AndroidX](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> Sarà necessario apportare alcune modifiche manuali dello spazio dei nomi per gli scenari che lo strumento non copre. Mentre verrà eseguito il mapping del pacchetto corretto per l'utente, è consigliabile dare un'occhiata [ai mapping](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings) ufficiali degli artefatti e dei mapping di [classi](https://developer.android.com/jetpack/androidx/migrate/class-mappings) per facilitare la migrazione del progetto.

2. Se l'applicazione include **dipendenze che non sono state migrate nello spazio dei nomi AndroidX**, sarà necessario utilizzare la libreria di supporto Android al pacchetto di [migrazione AndroidX.](https://www.nuget.org/packages/Xamarin.AndroidX.Migration)
3. Se l'applicazione non include dipendenze che richiedono la **migrazione dello spazio dei nomi AndroidX**, è possibile utilizzare le [librerie AndroidX in NuGet oggi.](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22)

## <a name="troubleshooting"></a>Risoluzione dei problemi

- Alcuni pacchetti di architettura all'interno di AndroidX saranno in conflitto con le versioni della libreria di supporto. Per risolvere questo problema, è necessario utilizzare la versione AndroidX di questi pacchetti e rimuovere la versione della libreria di supporto. Ad esempio, se si `Xamarin.Android.Arch.Work.Runtime` fa riferimento al progetto, questo andrà `AndroidX.Work` in conflitto con i tipi del pacchetto appena aggiunto.

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto AndroidX e ha spiegato come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Xamarin.Android con AndroidX. Ha fornito una panoramica di ciò che è AndroidX. Includeva collegamenti alla documentazione dell'API e agli argomenti di Android Developer per aiutarti a iniziare a creare app con AndroidX. Ha inoltre evidenziato le modifiche più importanti al comportamento di AndroidX e gli argomenti di risoluzione dei problemi che potrebbero influire sulle app esistenti.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione ad AndroidX Lo spettacolo di Xamarin](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [AndroidX](https://developer.android.com/jetpack/androidx)
- [Xamarin AndroidX GitHub Repository](https://github.com/xamarin/AndroidX)
- [Xamarin AndroidX Migrazione GitHub Repository](https://github.com/xamarin/XamarinAndroidXMigration)
