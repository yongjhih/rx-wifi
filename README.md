# RxWifi

[![CircleCI](https://circleci.com/gh/yongjhih/rx-wifi.svg?style=shield)](https://circleci.com/gh/yongjhih/rx-wifi)
[![codecov](https://codecov.io/gh/yongjhih/rx-wifi/branch/master/graph/badge.svg)](https://codecov.io/gh/yongjhih/rx-wifi)

## Usage

Scan:

```kt
RxWifi.scan(context).subscribe { scanResult ->
  println(scanResult)
}
```

Connect:

```kt
RxWifi.connects(context, ssid, password)
   .subscribe({
     println(it)
   }, { e -> {
    // ssid not found
    // auth failure
    // connection failure
    // no internet?
   })
```

## Installation

```gradle
repositories {
    jcenter()
    maven { url "https://jitpack.io" }
}

dependencies {
    compile 'com.github.yongjhih.rx-wifi:rx-wifi:-SNAPSHOT'
}
```

## Known Issue - Connection failure to a no-internet Wi-Fi with smart network routing and mobile data enabled

```kt
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
    val connectivityManager = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
    val netCallback = object : ConnectivityManager.NetworkCallback() {
        override fun onAvailable(network: Network) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP && Build.VERSION.SDK_INT < Build.VERSION_CODES.M) {
                ConnectivityManager.setProcessDefaultNetwork(network)
            } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                connectivityManager.bindProcessToNetwork(network)
            }
        }
    }
    connectivityManager.registerNetworkCallback(NetworkRequest.Builder()
            .addTransportType(NetworkCapabilities.TRANSPORT_WIFI)
            .removeCapability(NetworkCapabilities.NET_CAPABILITY_INTERNET)
            .build(), netCallback)
}

// connectivityManager.unregisterNetworkCallback(netCallback)
```

## Bonus

rx-connectivity:

```java
RxConnectivity.defaultNetwork(context, NetworkRequest.Builder()
            .addTransportType(NetworkCapabilities.TRANSPORT_WIFI)
            .removeCapability(NetworkCapabilities.NET_CAPABILITY_INTERNET)
            .build())
        .subscribe()
```

rx-connectivity-kotlin:

```kt
connectivityManager.defaultNetwork(NetworkRequest.Builder()
            .addTransportType(NetworkCapabilities.TRANSPORT_WIFI)
            .removeCapability(NetworkCapabilities.NET_CAPABILITY_INTERNET)
            .build())
        .subscribe()
```

rx-receiver:

```kt
RxReceiver.receives(context, intentFilter).subscribe()
```

rx-receiver-kotlin:

```kt
intentFilter.receives(context).subscribe()
```

rx-receiver-local (v4):

```kt
RxReceiverLocal.receives(context, intentFilter).subscribe()
```

rx-receiver-local-kotlin (v4):

```kt
intentFilter.receivesLocal(context).subscribe()
```

rx-wifi-kotlin:

```kt
wifiManager.scan(context).subscribe()
```

p.s.:

* It not works on Samsung Note Edge, Android 6.0.1
* It does work on Nexus Pixel, Android 7.1.1 (can also reproduce)
* It does work on Nexus 5X, Android 7.1.1
* It does work on Nexus 5, Android 6.0.1

## LICENSE

Apache 2.0
