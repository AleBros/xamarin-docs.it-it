---
title: Come è possibile copiare i file di output IPA nella cartella di ricezione TFS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 4493b1a0d06e2f44ee9a11a250395f058baa0548
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031027"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>Come è possibile copiare i file di output IPA nella cartella di ricezione TFS?

Aprire il file di `.csproj` per il progetto di app iOS in un editor di testo e quindi aggiungere le righe seguenti alla fine (immediatamente prima del tag di chiusura `</Project>`):

```xml
<PropertyGroup>
    <CreateIpaDependsOn>
        $(CreateIpaDependsOn);
        CopyIpa
    </CreateIpaDependsOn>
</PropertyGroup>

<Target Name="CopyIpa"
    Condition="'$(OutputType)' == 'Exe'
        And '$(ComputedPlatform)' == 'iPhone'
        And '$(BuildIpa)' == 'true'
        And '$(TF_BUILD)' == 'true'">
    <Copy
        SourceFiles="$(OutputPath)$(IpaPackageName)"
        DestinationFolder="$(TF_BUILD_BINARIESDIRECTORY)"
    />
</Target>
```

## <a name="notes"></a>Note

- Si tratta della stessa tecnica generale descritta in è [possibile modificare il percorso di output del file IPA?](~/ios/troubleshooting/questions/ipa-output-path.md). I due punti importanti sono impostare `$(TF_BUILD_BINARIESDIRECTORY)` come cartella di destinazione e aggiungere una condizione aggiuntiva in modo che `CopyIpa` venga eseguito solo per le compilazioni TFS.

- Per una descrizione `TF_BUILD_BINARIESDIRECTORY` vedere [variabili di compilazione predefinite](https://docs.microsoft.com/azure/devops/pipelines/build/variables).

## <a name="additional-references"></a>Riferimenti aggiuntivi

- [Documentazione sull'installazione di TFS per l'uso con Novell](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)
- [Attività di compilazione di Azure DevOps: Xamarin.Android](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-android)
- [Attività di compilazione di Azure DevOps: Xamarin.iOS](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Passaggi successivi

Questo documento illustra il comportamento attuale di Novell 3.11.666 per Visual Studio e Xamarin.iOS 8.10.3 nell'host di compilazione Mac. Per ulteriore assistenza, per contattarci o se il problema persiste anche dopo aver utilizzato le informazioni sopra riportate, vedere [quali sono le opzioni di supporto disponibili per Novell?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché su come archiviare un nuovo bug se necessario .
