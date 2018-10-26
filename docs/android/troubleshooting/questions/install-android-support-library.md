---
title: Come è è possibile installare manualmente le librerie di supporto Android necessarie dai pacchetti deselezionati?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 84ee33fe174c01656144e55bc3cbba7c773950fd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120645"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Come è è possibile installare manualmente le librerie di supporto Android necessarie dai pacchetti deselezionati?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Procedure di esempio per Xamarin.Android.Support.v4 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Scaricare il pacchetto NuGet deselezionati desiderato (ad esempio tramite l'installazione con Gestione pacchetti NuGet).

Uso `ildasm` per verificare quale versione di **android_m2repository.zip** il pacchetto NuGet è necessario:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```
Output di esempio:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Scaricare **android\_m2repository.zip** da Google usando l'URL restituito da **ildasm**. In alternativa, è possibile controllare quale versione del _Android Support Repository_ attualmente installata in Android SDK Manager:

!["Visualizzazione di Android Support Repository versione 32 installato android SDK Manager"](install-android-support-library-images/sdk-extras.png)

Se la versione corrisponda a quello che necessario per il pacchetto NuGet, quindi non devi scaricare nulla di nuovo. È possibile invece comprimere nuovamente esistente **m2repository** directory che si trova sotto **extras\\android** nel _percorso SDK_ (come illustrato nella parte superiore di Android Finestra di gestione SDK).

Calcolare l'hash MD5 dell'URL restituito da **ildasm**. Formattare la stringa risultante per l'uso senza spazi e lettere maiuscole. Ad esempio, modificare il `$url` variabile come necessario e quindi eseguire le seguenti 2 righe (in base [originale C# codice da xamarin. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) in PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```
Output di esempio:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copia **android\_m2repository.zip** nel **% LOCALAPPDATA %\\Xamarin\\zips\\**  cartella. Rinominare il file per utilizzare l'hash MD5 dall'hash MD5 precedente calcolo del passaggio. Ad esempio:

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

(Facoltativo) Decomprimere il file in **% LOCALAPPDATA %\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\contenuto\\**  (creazione di un **contenuto\\m2repository** sottodirectory). Se si ignora questo passaggio, quindi la prima compilazione che usa la libreria richiederà un po' più tempo perché è necessario completare questo passaggio.
Il numero di versione della sottodirectory (**23.4.0.0** in questo esempio) non è piuttosto quello utilizzato per la versione del pacchetto NuGet. È possibile usare `ildasm` per trovare il numero di versione corretto:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```
Output di esempio:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Scaricare il pacchetto NuGet deselezionati desiderato (ad esempio tramite l'installazione con Gestione pacchetti NuGet).

Fare doppio clic sul _Xamarin.Android.Support.v4_ assembly sotto il _riferimenti_ sezione del progetto Android in Visual Studio per Mac aprire l'assembly nel Browser di Assembly. Verificare che il _Language_ elenco a discesa è impostata su _C#_ e selezionare il primo livello _Xamarin.Android.Support.v4_ assembly dall'albero di navigazione del Browser di Assembly . Individuare il `SourceUrl` in una delle proprietà il `IncludeAndroidResourcesFrom` o `JavaLibraryReference` attributi:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Scaricare **android\_m2repository.zip** da Google usando la `SourceUrl` restituito da **ildasm**. In alternativa, è possibile controllare quale versione del _Android Support Repository_ attualmente installata in Android SDK Manager:

!["Visualizzazione di Android Support Repository versione 32 installato android SDK Manager"](install-android-support-library-images/sdk-extras.png)

Se la versione corrisponda a quello che necessario per il pacchetto NuGet, quindi non devi scaricare nulla di nuovo. È possibile invece comprimere nuovamente esistente **m2repository** directory che si trova sotto **extras/android** nel _percorso SDK_ (come illustrato nella parte superiore della finestra di Android SDK Manager) .

Calcolare l'hash MD5 dell'URL restituito da **ildasm**. Formattare la stringa risultante per l'uso senza spazi e lettere maiuscole. Ad esempio, modificare la stringa URL come necessario e quindi eseguire il comando seguente in una **terminal** prompt dei comandi:

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Un'altra opzione consiste nell'usare la `csharp` interprete per eseguire [lo stesso C# codice che usa xamarin. Android stesso](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
A tale scopo, modificare il `url` variabile come necessario e quindi eseguire il comando seguente in un **terminal** prompt dei comandi:

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```
Output di esempio:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copia **android\_m2repository.zip** per il **$HOME/.local/share/Xamarin/zips/** cartella. Rinominare il file per utilizzare l'hash MD5 dall'hash MD5 precedente calcolo del passaggio. Ad esempio:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

(Facoltativo) Decomprimere il file in: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(creazione di un **contenuto/m2repository** sottodirectory). Se si ignora questo passaggio, quindi la prima compilazione che usa la libreria richiederà un po' più tempo perché è necessario completare questo passaggio.

Il numero di versione della sottodirectory (**23.4.0.0** in questo esempio) non è piuttosto quello utilizzato per la versione del pacchetto NuGet. Come nel **ildasm** passaggio in precedenza, è possibile usare il Browser di Assembly in Visual Studio per Mac per trovare il numero di versione corretto. Cercare il `Version` in una delle proprietà il `IncludeAndroidResourcesFrom` o `JavaLibraryReference` attributi:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----


## <a name="additional-references"></a>Riferimenti aggiuntivi

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – che sia impreciso "Download non riuscito. È necessario scaricare {0} e inserirlo il {1} directory. " e "installare il pacchetto: '{0}' disponibile nel programma di installazione SDK" messaggi di errore correlati ai pacchetti deselezionati

### <a name="next-steps"></a>Passaggi successivi

Questo documento illustra il comportamento corrente a partire da agosto 2016. La tecnica descritta in questo documento non fa parte della suite di test stabile per Xamarin, quindi potrebbe interrompere in futuro.

Per ulteriore assistenza, contattare il supporto tecnico o se il problema rimane anche dopo che usano le informazioni sopra riportate, vedi [le opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché come archiviare un nuovo bug se necessario.

