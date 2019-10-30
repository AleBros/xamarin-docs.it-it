---
title: Modifica dei parametri di memoria Java per Android Designer
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 9c9b9f5a205a2eef7db9f27e8d09b10ce65a4318
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027052"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Modifica dei parametri di memoria Java per Android Designer

I parametri di memoria predefiniti usati quando si avvia il processo di `java` per la finestra di progettazione di Android potrebbero essere incompatibili con alcune configurazioni di sistema.

A partire da Xamarin Studio 5.7.2.7 (e versioni successive, Visual Studio per Mac) e Strumenti di Visual Studio per Novell 3.9.344, queste impostazioni possono essere personalizzate in base al progetto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Nuove proprietà di Android designer e corrispondenti opzioni Java

I nomi di proprietà seguenti corrispondono all'opzione della [riga di comando](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html) Java indicata

- **AndroidDesignerJavaRendererMinMemory** -XMS

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX: MaxPermSize

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire la soluzione in Visual Studio.

2. Selezionare ogni progetto Android uno alla volta nel Esplora soluzioni e fare clic su [Mostra tutti i file](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) due volte in ogni progetto. È possibile ignorare i progetti che non contengono file di layout `.axml`. Con questo passaggio si garantisce che ogni directory di progetto contenga un file di `.csproj.user`.

3. Uscire da Visual Studio.

4. Individuare il file di `.csproj.user` per ogni progetto del passaggio 2.

5. Modificare ogni file di `.csproj.user` in un editor di testo.

6. Aggiungere una o tutte le nuove proprietà di memoria di Android designer all'interno di un elemento `<PropertyGroup>`. È possibile usare un `<PropertyGroup>` esistente o crearne uno nuovo. Di seguito è riportato un esempio completo `.csproj.user` file che include tutti i 3 attributi impostati sui rispettivi valori predefiniti:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7. Salvare e chiudere tutti i file di `.csproj.user` aggiornati.

8. Riavviare Visual Studio e riaprire la soluzione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire la soluzione in Visual Studio per Mac per assicurarsi che la directory della soluzione contenga un file di `.userprefs`.

2. Uscire Visual Studio per Mac.

3. Individuare il file di `.userprefs` nella directory della soluzione.

4. Modificare il file di `.userprefs` in un editor di testo.

5. Individuare l'elemento XML esistente con il formato seguente. L'ultima parte del nome di questo elemento corrisponderà al nome del progetto: "AndroidApplication1" in questo esempio:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. Se l'elemento `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` non esiste, crearlo in qualsiasi punto all'interno dell'elemento `<Properties>` di inclusione. Assicurarsi di sostituire "AndroidApplication1" con il nome del progetto.

7. Aggiungere una o tutte le nuove proprietà di memoria di Android designer come attributi nell'elemento. Di seguito è riportato un esempio completo `.userprefs` file che include tutti i 3 attributi impostati sui rispettivi valori predefiniti:

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8. Ripetere i passaggi 5-7 per ogni progetto Android nella soluzione che contiene i file di layout `.axml`. (Ovvero, aggiungere un `<MonoDevelop.Ide.ItemProperties.ProjectName>` elemento per ogni progetto).

9. Salvare e chiudere il file di `.userprefs`.

10. Riavviare Visual Studio per Mac e riaprire la soluzione.

-----
