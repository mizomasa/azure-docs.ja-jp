---
title: Anomaly Detector Python クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: a51025245e5b02b89126afd886bc89fb1cefdef4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759821"
---
Python 用 Anomaly Detector クライアント ライブラリを使ってみます。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Anomaly Detector サービスにより、業界、シナリオ、データ量に関係なく、最適なモデルを自動的に使用することで、時系列データ内の異常を検出できます。

Python 用 Anomaly Detector クライアント ライブラリは、次の目的で使用することができます。

* バッチ要求として、時系列データセット全体で異常を検出する
* 時系列で最新のデータ ポイントの異常状態を検出する

[ライブラリのリファレンス ドキュメント](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [パッケージ (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [GitHub でサンプル コードを検索する](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* Anomaly Detector キーとエンドポイント
* [Python 3.x](https://www.python.org/)
* [Pandas データ分析ライブラリ](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>設定

### <a name="create-an-anomaly-detector-resource"></a>Anomaly Detector リソースを作成する

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

 新しい Python ファイルを作成して次のライブラリをインポートします。

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

環境変数としてのキーの変数、時系列データ ファイルへのパスの変数、サブスクリプションの Azure の場所の変数を作成します。 たとえば、「 `westus2` 」のように入力します。 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次を使用してクライアント ライブラリをインストールすることができます。

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>オブジェクト モデル

Anomaly Detector クライアントは、キーを使用して Azure に対する認証を行う [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python)オブジェクトです。 クライアントによる異常検出の方法は 2 とおりあります。[entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)を使用したデータセット全体での検出と [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)を使用した最新のデータ ポイントでの検出) を提供します。 

時系列データは、[Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) オブジェクト内の一連の [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) として送信されます。 `Request` オブジェクトには、データを説明するプロパティ (たとえば、[細分性](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)など) と異常検出のパラメーターが含まれます。 

Anomaly Detector の応答は、使用する方法に応じて、[LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) オブジェクトまたは [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) オブジェクトになります。 

## <a name="code-examples"></a>コード例 

以下のコード スニペットは、Python 用 Anomaly Detector クライアント ライブラリを使用して次のことを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [ファイルから時系列データ セットを読み込む](#load-time-series-data-from-a-file)
* [データ セット全体で異常を検出する](#detect-anomalies-in-the-entire-data-set) 
* [最新のデータ ポイントの異常状態を検出する](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>クライアントを認証する

Azure の場所の変数をエンドポイントに追加し、キーを使用してクライアントを認証します。

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>ファイルから時系列データを読み込む

このクイックスタートのサンプル データを [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) からダウンロードします。
1. ブラウザーで、 **[Raw]\(未加工\)** を右クリックします。
2. **[Save link as]\(名前を付けてリンク先を保存\)** をクリックします。
3. ファイルを .csv ファイルとしてアプリケーション ディレクトリに保存します。

この時系列データは、.csv ファイル形式として書式設定され、Anomaly Detector API に送信されます。

Pandas ライブラリの `read_csv()` メソッドを使用してデータ ファイルを読み込み、データ系列を格納するための空のリスト変数を作成します。 ファイルを反復処理して、データを [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) オブジェクトとして追加します。 このオブジェクトには、タイムスタンプと .csv データ ファイルの行の数値が格納されます。 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

時系列とそのデータ ポイントの[細分性](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (または周期性) を使用して [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) オブジェクトを作成します。 たとえば、「 `Granularity.daily` 」のように入力します。

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>データ セット全体で異常を検出する 

API を呼び出し、クライアントの [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) メソッドを使用して時系列データ全体で異常を検出します。 返された [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) オブジェクトを格納します。 応答の `is_anomaly` 一覧を反復処理して、すべての `true` 値のインデックスを出力します。 これらの値は、異常なデータ ポイントが見つかった場合、そのインデックスに対応します。

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>最新のデータ ポイントの異常状態を検出する

Anomaly Detector API を呼び出し、クライアントの [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) メソッドを使用して最新のデータ ポイントが異常かどうかを判断し、返された [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) オブジェクトを格納します。 応答の `is_anomaly` の値は、そのポイントの異常状態を指定するブール値です。  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>アプリケーションの実行

`python` コマンドとファイル名を使用してアプリケーションを実行します。
 
[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
