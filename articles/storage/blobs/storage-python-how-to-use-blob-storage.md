---
title: "如何使用 Python 中的 Azure Blob 儲存體 (物件儲存體) | Microsoft Docs"
description: "使用 Azure Blob 儲存體 (物件儲存體) 在雲端中儲存非結構化資料。"
services: storage
documentationcenter: python
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 0348e360-b24d-41fa-bb12-b8f18990d8bc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 2/24/2017
ms.author: tamram
ms.openlocfilehash: ae5ad68929a6779ed4944de82a609321a5c4b5ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-blob-storage-from-python"></a>如何使用 Python 的 Azure Blob 儲存體
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Overview
Azure Blob 儲存體是可將非結構化的資料儲存在雲端作為物件/blob 的服務。 Blob 儲存體可以儲存任何類型的文字或二進位資料，例如文件、媒體檔案或應用程式安裝程式。 Blob 儲存體也稱為物件儲存體。

本文將示範如何使用 Blob 儲存體執行一般案例。 這些範例是以 Python 所撰寫，並使用 [Microsoft Azure Storage SDK for Python (適用於 Python 的 Microsoft Azure 儲存體 SDK)]。 所涵蓋的案例包括「上傳、列出、下載」及「刪除」Blob。

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>下載並安裝 Azure Storage SDK for Python

Azure Storage SDK for Python 需要有 Python 2.7、3.3、3.4、3.5 或 3.6，而且提供 4 種不同的封裝：`azure-storage-blob`、`azure-storage-file`、`azure-storage-table` 和 `azure-storage-queue`。 在本教學課程中，我們將使用 `azure-storage-blob` 封裝。
 
### <a name="install-via-pypi"></a>透過 PyPi 安裝

若要透過 Python Package Index (PyPI) 安裝，請輸入：

```bash
pip install azure-storage-blob
```


> [!NOTE]
> 如果您要從 Azure 儲存體 Azure Storage SDK for Python 0.36 版或更早版本升級，您將必須先使用 `pip uninstall azure-storage` 解除安裝，因為我們將不再以單一封裝的方式發行 Storage SDK for Python。
> 
> 

如需替代安裝方法，請瀏覽 [Github 上的 Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/)。

## <a name="create-a-container"></a>建立容器
根據您想要使用的 Blob 類型，建立 **BlockBlobService**、**AppendBlobService** 或 **PageBlobService** 物件。 下列程式碼會使用 **BlockBlobService** 物件。 將下列內容新增至您想要在其中以程式設計方式存取 Azure 區塊 Blob 儲存體之任何 Python 檔案內的頂端附近。

```python
from azure.storage.blob import BlockBlobService
```

下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 **BlockBlobService** 物件。  將 'myaccount' 和 'mykey' 取代為您的帳戶名稱和金鑰。

```python
block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')
```

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

在下列的程式碼範例中，如果容器不存在，您可以使用 **BlockBlobService** 物件建立容器。

```python
block_blob_service.create_container('mycontainer')
```

根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰 (如先前所做) 才能從此容器下載 Blob。 若要讓所有人都能使用容器中的 blob，您可以使用下列程式碼建立容器，並傳遞公用存取等級。

```python
from azure.storage.blob import PublicAccess
block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)
```

或者，您也可以在建立容器之後使用下列程式碼修改它。

```python
block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)
```

做此變更之後，網際網路上的任何人都可以看到公用容器中的 Blob，但只有您能修改或刪除它們。

## <a name="upload-a-blob-into-a-container"></a>將 Blob 上傳至容器
若要建立區塊 Blob 和上傳資料，請使用 **create\_blob\_from\_path**、**create\_blob\_from\_stream**、**create\_blob\_from\_bytes** 或 **create\_blob\_from\_text** 方法。 這些是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化動作。

**create\_blob\_from\_path** 會從指定的路徑上傳檔案的內容，**create\_blob\_from\_stream** 會從已開啟的檔案/串流上傳內容。 **create\_blob\_from\_bytes** 會上傳位元組陣列，**create\_blob\_from\_text** 會使用指定的編碼 (預設為 UTF-8) 上傳指定的文字值。

下列範例會將 **sunset.png** 檔案的內容上傳至 **myblockblob** Blob 中。

```python
from azure.storage.blob import ContentSettings
block_blob_service.create_blob_from_path(
    'mycontainer',
    'myblockblob',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png')
            )
```

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob
若要列出容器中的 Blob，請使用 **list\_blobs** 方法。 這個方法會傳回產生器。 下列程式碼會將容器中每個 blob 的 **name** 輸出到主控台。

```python
generator = block_blob_service.list_blobs('mycontainer')
for blob in generator:
    print(blob.name)
```

## <a name="download-blobs"></a>下載 Blob
若要從 blob 下載資料，請使用 **get\_blob\_to\_path****get\_blob\_to\_stream**、**get\_blob\_to\_bytes** 或 **get\_blob\_to\_text**。 這些是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化動作。

下列範例示範如何使用 **get\_blob\_to\_path** 下載 **myblockblob** Blob 的內容，並將其儲存至 **out-sunset.png** 檔案。

```python
block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')
```

## <a name="delete-a-blob"></a>刪除 Blob
最後，若要刪除 Blob，請呼叫 **delete_blob**。

```python
block_blob_service.delete_blob('mycontainer', 'myblockblob')
```

## <a name="writing-to-an-append-blob"></a>寫入附加 Blob
附加 Blob 已針對附加作業 (例如紀錄) 最佳化。 如同區塊 Blob，附加 Blob 亦由區塊組成，但當您將新區塊加入附加 Blob 時，它一律會附加到 Blob 結尾。 您無法更新或刪除附加 Blob 中的現有區塊。 附加 Blob 的區塊識別碼不會公開顯示，因為該識別碼適用於區塊 Blob。

附加 Blob 中的每個區塊大小都不同，最大為 4 MB，而附加 Blob 可包含高達 50,000 個區塊。 因此，附加 Blob 的大小上限稍高於 195 GB (4 MB X 50,000 個區塊)。

下列範例中建立了新的附加 Blob，並附加一些資料，以模擬簡單的記錄作業。

```python
from azure.storage.blob import AppendBlobService
append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

# The same containers can hold all types of blobs
append_blob_service.create_container('mycontainer')

# Append blobs must be created before they are appended to
append_blob_service.create_blob('mycontainer', 'myappendblob')
append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')
```

## <a name="next-steps"></a>後續步驟
了解 Blob 儲存體的基礎概念之後，請使用下列連結深入了解。

* [Python 開發人員中心](https://azure.microsoft.com/develop/python/)
* [Azure 儲存體服務 REST API](http://msdn.microsoft.com/library/azure/dd179355)
* [Azure 儲存體團隊部落格]
* [Microsoft Azure Storage SDK for Python (適用於 Python 的 Microsoft Azure 儲存體 SDK)]

[Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python (適用於 Python 的 Microsoft Azure 儲存體 SDK)]: https://github.com/Azure/azure-storage-python
