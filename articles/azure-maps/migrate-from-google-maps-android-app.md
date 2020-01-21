---
title: 教學課程：遷移 Android 應用程式 |Microsoft Azure 地圖服務
description: 如何將 Android 應用程式從 Google 地圖遷移至 Microsoft Azure 地圖服務。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 643d48cb931bcec1a8a3385d2ec24a394660c368
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909194"
---
# <a name="migrate-an-android-app-from-google-maps"></a>從 Google 地圖遷移 Android 應用程式

Azure 地圖服務 Android SDK 具有與 Web SDK 非常類似的 API 介面。 如果您曾使用其中一個 SDK 進行開發，則其概念、最佳做法和架構會有許多共通之處，您應該能夠輕鬆地將既有的知識融會貫通。

Azure 地圖服務 Android SDK 支援的最低 Android 版本為 API 21：Android 5.0.0 (Lollipop)。

所有範例均以 Java 提供，但 Kotlin 也可以與 Azure 地圖服務 Android SDK 搭配使用。

另請參閱 [Azure 地圖服務 Android SDK 的操作指南](how-to-use-android-map-control-library.md)，以進一步了解如何使用此 SDK 進行開發。

## <a name="load-a-map"></a>載入地圖

使用 Google 地圖或 Azure 地圖服務在 Android 應用程式中載入地圖時，有許多步驟是相同的。 使用任一種 SDK 時，您都必須：

- 取得 API 或訂用帳戶金鑰，以存取任一平台。
- 將 XML 新增至活動，以指定應呈現地圖的位置，及其版面配置方式。
- 將包含地圖檢視的活動中所有的生命週期方法，轉送至地圖類別中的對應方法。 特別是，您必須轉送下列方法：
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- 等到地圖準備就緒後，再嘗試以程式設計方式加以存取。

**之前：Google 地圖**

若要使用適用於 Android 的 Google 地圖 SDK 來顯示地圖，應執行下列步驟：

1.  確定已安裝 Google Play 服務。
2.  將 Google 地圖服務的相依性新增至模組的 **gradle.build** 檔案： 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  在 **google\_maps\_api.xml** 檔案的應用程式區段內，新增 Google 地圖 API 金鑰：
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  將地圖片段新增至主要活動：

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  在 **MainActivity.java** 檔案中，您必須新增 Google 地圖 SDK 的匯入。 將包含地圖檢視的活動中所有的生命週期方法，轉送至地圖類別中的對應方法。 您可以使用 `getMapAsync(OnMapReadyCallback)` 方法，從地圖片段中擷取 `MapView` 執行個體。 `MapView` 會自動初始化地圖系統和檢視。 編輯 **MainActivity.java** 檔案，如下所示：

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

在應用程式中執行時，地圖控制項會以下列方式載入。

<center>

![簡單的 Google 地圖](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**之後：Azure 地圖服務**

若要使用適用於 Android 的 Azure 地圖服務 SDK 來顯示地圖，必須執行下列步驟：

1. 開啟頂層 **build.gradle** 檔案，並將下列程式碼新增至 [所有專案]  、[存放庫區塊]  區段：

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新 **app/build.gradle**，並於其中新增下列程式碼：
    
    1. 確定專案的 **minSdkVersion** 是 API 21 或更新版本。

    2. 將下列程式碼新增至 Android 區段：

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 更新您的相依性區塊，並且為最新的 Azure 地圖服務 Android SDK 新增實作相依性程式行：

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Azure 地圖服務 Android SDK 會定期進行升級和強化。 您可以參閱[開始使用 Android 地圖控制項](how-to-use-android-map-control-library.md)文件，以取得最新的 Azure 地圖服務實作版本號碼。 此外，您也可以將版本號碼從 "0.2" 設定為 "0+"，使其一律指向最新版本。
    
    4. 移至工具列中 [檔案]  ，然後按一下 [同步處理專案與 Gradle 檔案]  。
3. 將地圖片段新增至主要活動 (res \> layout \> activity\_main.xml)：
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. 在 **MainActivity.java** 檔案中，您將必須：
    
    * 新增 Azure 地圖服務 SDK 的匯入
    * 設定您的 Azure 地圖服務驗證資訊
    * 在 **onCreate** 方法中取得地圖控制項執行個體

    使用 `setSubscriptionKey` 或 `setAadProperties` 方法全域設定 `AzureMaps` 類別的驗證資訊，而無須在每個檢視上新增您的驗證資訊。 

    地圖控制項包含其本身用來管理 Android OpenGL 生命週期的生命週期方法，此方法必須直接從其所屬的活動呼叫。 若要讓您的應用程式正確地呼叫地圖控制項的生命週期方法，您必須在包含地圖控制項的活動中覆寫下列生命週期方法，並呼叫各自的地圖控制項方法。 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    編輯 **MainActivity.java** 檔案，如下所示：
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

執行應用程式時，地圖控制項會以下列方式載入。

<center>

![簡單的 Azure 地圖服務](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

請注意，Azure 地圖服務控制項支援更高倍數的縮小，而更能提供涵蓋全世界的檢視。

> [!TIP]
> 如果在 Windows 中使用 Android 模擬器，則地圖可能會因為與 OpenGL 和軟體加速圖形轉譯發生衝突，而不會呈現。 下列方法在部分情況下可解決此問題。 開啟 [AVD 管理員]，然後選取要編輯的虛擬裝置。 在 [模擬的效能]  區段中，將 [圖形]  選項設定為 [硬體]  。

## <a name="localizing-the-map"></a>將地圖當地語系化

如果您的受眾分散在多個國家/地區或使用不同語言，當地語系化便很重要。

**之前：Google 地圖**

您可以在設定地圖的內容檢視之前新增下列程式碼，藉以在主要活動的 `onCreate` 方法中設定地圖的語言。 下列程式碼會使用語言代碼 "fr" 將語言限定為法文。

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

以下是語言設定為 "fr" 的 Google 地圖範例。

<center>

![Google 地圖當地語系化](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**之後：Azure 地圖服務**

Azure 地圖服務提供三種不同的方式供您設定地圖的語言和區域檢視。 第一個選項是全域使用靜態 `setLanguage` 和 `setView` 方法，將語言和區域檢視資訊傳入 `AzureMaps` 類別中。 這會在應用程式中載入的所有 Azure 地圖服務控制項間設定預設語言和區域檢視。 下列程式碼會使用語言代碼 "fr-FR" 將語言限定為法文。

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

第二個選項是將語言和檢視資訊傳入地圖控制項 XML 中。

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

第三個選項是使用地圖 `setStyle` 方法，以程式設計方式設定地圖的語言和區域檢視。 此方法可隨時執行，以變更地圖的語言和區域檢視。

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

以下是語言設定為 "fr-FR" 的 Azure 地圖服務範例。

<center>

![Azure 地圖服務當地語系化](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

如需支援的語言和區域檢視的完整清單，請參閱[這裡](supported-languages.md)。

## <a name="setting-the-map-view"></a>設定地圖檢視

您可以藉由呼叫適當的方法，以程式設計方式將 Azure 地圖服務和 Google 地圖中的動態地圖移至新的地理位置。 下列範例說明如何讓地圖顯示衛星空照圖影像，並將地圖置中於某個位置，其座標為 (經度：35.0272、緯度：-111.0225)，並在 Google 地圖中將縮放層級變更為 15。

> [!NOTE]
> Google 地圖所使用的地圖底圖維度為 256 像素，而 Azure 地圖服務則使用較大的 512 像素地圖底圖。 在載入和 Google 地圖相同的地圖區域時，這種設定會讓 Azure 地圖服務減少所需的網路要求數目。 不過，由於地圖底圖金字塔在地圖控制項中的運作方式所致，如果在 Azure 地圖服務中使用較大的地圖底圖，則表示若要達到與 Google 地圖中的地圖相同的可檢視區域，您就必須在使用 Azure 地圖服務時，將 Google 地圖中所用的縮放層級減 1。

**之前：Google 地圖**

您可以使用 `moveCamera` 方法以程式設計方式移動「Google 地圖」地圖控制項的觀景窗，這種方法可讓您指定地圖的中心點和縮放層級。 `setMapType` 方法可用來變更顯示的地圖類型。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Google 地圖設定的檢視](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**之後：Azure 地圖服務**

如前所述，若要在 Azure 地圖服務中達到相同的可檢視區域，請將 Google 地圖中使用的縮放層級減一，在此案例中，請使用縮放層級 14。

初始地圖檢視可在地圖控制項的 XML 屬性中設定。

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

地圖檢視可使用地圖 `setCamera` 和 `setStyle` 方法，以程式設計方式進行更新。

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Azure 地圖服務設定的檢視](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**其他資源：**

- [支援的地圖樣式](supported-map-styles.md)

## <a name="adding-a-marker"></a>新增標記

點資料常會使用地圖上的影像呈現於地圖上。 這些影像通常稱為標記、圖釘、釘選或符號。 下列範例會將點資料呈現為地圖上的標記，其座標為 (緯度：51.5、經度：-0.2)。

**之前：Google 地圖**

使用 Google 地圖時，會使用地圖 `addMarker` 方法來新增標記。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Google 地圖標記](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，您可以先將點資料新增至資料來源，然後將該資料來源連線至符號圖層，藉以在地圖上呈現點資料。 資料來源可將地圖控制項中的空間資料管理最佳化，而符號圖層則會指定如何呈現作為影像和/或文字的點資料。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![Azure 地圖服務的標記](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>新增自訂標記

自訂影像可用來代表地圖上的位置點。 下列範例中使用的影像會使用自訂影像在地圖上顯示某個位置點 (緯度：51.5、經度：-0.2) 並位移標記位置，讓圖釘圖示的尖端對準地圖上的正確位置。

<center>

![黃色圖釘影像](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

在這兩個範例中，上述影像均新增至應用程式資源的可繪製資料夾。

**之前：Google 地圖**

使用 Google 地圖時，您可以透過標記的 `icon` 選項來載入自訂影像，以用於標記。 若要將影像點對齊座標，可以使用 `anchor` 選項。 錨點相對於影像的維度，在此案例中為0.2 單位寬，1 個單位高。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Google 地圖的自訂標記](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**之後：Azure 地圖服務**

Azure 地圖服務中的符號圖層也支援自訂影像，但影像必須先載入至地圖資源，並獲派唯一識別碼。 符號圖層接著便可以參考此識別碼。 您可以使用 `iconOffset` 選項來位移符號，使其對齊影像上的正確位置點。 請注意，圖示位移以像素為單位。 根據預設，位移會相對於影像的底部中央，但您可以使用 `iconAnchor` 選項加以調整。 這個範例會將 `iconAnchor` 選項設定為 `"center"`，並使用圖示位移將影像向右移動五個像素，同時向上移動 15 個像素，以對齊圖釘影像的點。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Azure 地圖服務的自訂標記](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>新增聚合線條

聚合線條可用來代表地圖上的一條線或路徑。 下列範例說明如何在地圖上建立虛線聚合線條。

**之前：Google 地圖**

使用 Google 地圖時，可以使用 `PolylineOptions` 類別來建立聚合線條，並使用 `addPolyline` 方法將其新增至地圖。 筆觸色彩可使用 `color` 選項來設定，筆觸寬度可使用寬度選項進行設定，而筆觸虛線陣列則可以使用 `pattern` 選項來設定。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Google 地圖的聚合線條](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，聚合線條稱為 LineString 或 MultiLineString 物件。 這些物件可新增至資料來源，並使用線條圖層來加以呈現。 請注意，筆觸寬度和虛線陣列的「像素」單位會與 Azure 地圖服務 Web SDK 一致，在這兩個 SDK 中使用相同的值會產生相同的結果。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Azure 地圖服務的聚合線條](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>新增多邊形

多邊形可用來代表地圖上的區域。 下列範例說明如何根據地圖的中心座標，建立會形成三角形的多邊形。

**之前：Google 地圖**

使用 Google 地圖時，可以使用 `PolygonOptions` 類別來建立多邊形，並使用 `addPolygon` 方法將其新增至地圖。 您可以使用 `fillColor` 和 `strokeColor` 選項來設定填滿和筆觸色彩，筆觸寬度則可使用 `strokeWidth` 選項來設定。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Google 地圖的多邊形](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，MultiPolygon 物件可新增至資料來源，並使用圖層在地圖上加以呈現。 多邊形的區域可在多邊形圖層中呈現。 多邊形的外框則可使用線條圖層來呈現。 請注意，筆觸寬度和虛線陣列的「像素」單位會與 Azure 地圖服務 Web SDK 一致，在這兩個 SDK 中使用相同的值會產生相同的結果。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Azure 地圖服務的多邊形](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>覆蓋地圖底圖圖層

地圖底圖圖層 (在 Google 地圖中也稱為影像覆蓋) 可讓您將已分解成較小地圖底圖影像、並與地圖底圖系統契合的大型影像覆蓋在一起。 在覆蓋圖層影像或非常大型的資料集時，常會使用這種方式。

下列範例會覆蓋來自愛荷華州立大學 Iowa Environmental Mesonet 的氣象雷達地圖底圖圖層。 地圖底圖的大小為 256 像素。

**之前：Google 地圖**

使用 Google 地圖時，可以使用 `TileOverlayOptions` 類別將地圖底圖圖層覆蓋在地圖之上，並使用 `addTileLauer` 方法將其新增至地圖。 若要讓地圖底圖變成半透明狀，可將 `transparency` 選項設定為 0.2 或 20% 透明。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![Google 地圖的地圖底圖圖層](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**之後：Azure 地圖服務**

您可在 Azure 地圖服務中將地圖底圖圖層新增至地圖中，其方式與任何其他圖層大致相同。 具有 x、y、縮放預留位置 (分別為 `{x}`、`{y}`、`{z}`) 的格式化 URL 可用來讓圖層知道要於何處存取地圖底圖。 Azure 地圖服務中的地圖底圖圖層也支援 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 預留位置。 若要讓地圖底圖圖層變成半透明狀，可使用不透明度值 0.8。 請注意，不透明度和透明度雖然類似，但應使用倒數。 若要在兩者之間轉換，只需將一減去其值即可。

> [!TIP]
> 在 Azure 地圖服務中，您可以輕鬆地將圖層放到其他圖層下面來呈現，其中也包括基底地圖圖層。 地圖底圖圖層通常最好是放到地圖標籤下面來呈現，以方便辨識。 `map.layers.add` 方法會採用第二個參數，也就是要在其下面插入新圖層的目標圖層所具有的識別碼。 若要在地圖標籤下面插入地圖底圖圖層，您可以使用下列程式碼：`map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Azure 地圖服務的地圖底圖圖層](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>顯示流量

Azure 地圖服務和 Google 地圖都可以覆蓋交通資料。

**之前：Google 地圖**

使用 Google 地圖時，可以藉由將 true 傳入地圖的 `setTrafficEnabled` 方法中，將交通流量資料覆蓋在地圖之上。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Google 地圖的交通](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**之後：Azure 地圖服務**

Azure 地圖服務提供數個不同的選項來顯示交通。 交通事件 (例如道路封閉和事故) 可在地圖上顯示為圖示。 車流量 (以色彩編碼的道路) 可以在地圖上覆蓋，且色彩可修改為以相對於速限、相對於平常的預期延遲或絕對延遲作為依據。 Azure 地圖服務中的事件資料會每分鐘更新一次，車流資料則會每兩分鐘更新一次。

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure 地圖服務的交通](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>後續步驟

深入了解 Azure 地圖服務 Android SDK。

> [!div class="nextstepaction"]
> [如何使用 Android 的地圖控制項](how-to-use-android-map-control-library.md)
