---
ms.openlocfilehash: 22a8542c0e829db8b889abc2c7fe5f5ab9d19ed4
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527625"
---

Quando si compilano app iOS in Visual Studio e con l'agente di compilazione Mac, il bundle dell'app non viene copiato nuovamente nel computer Windows. Gli strumenti Xamarin per Visual Studio 7.4 aggiungono una nuova proprietà `CopyAppBundle` che consente alle compilazioni CI di copiare nuovamente i bundle delle app in Windows.

Per usare questa funzionalità, aggiungere la proprietà `CopyAppBundle` al file con estensione .csproj sotto il gruppo di proprietà che si vuole applicare alla funzionalità. Ad esempio, di seguito viene illustrato come copiare nuovamente il bundle .app nel computer Windows per una compilazione di tipo **Debug** che ha come destinazione **iPhoneSimulator**:

```xml
<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">
    <CopyAppBundle>true</CopyAppBundle>
</PropertyGroup>
```
