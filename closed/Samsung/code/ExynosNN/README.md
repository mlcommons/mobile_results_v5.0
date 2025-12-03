# Instructions for building mlperf app with Samsung Exynos 2600 backend support 

## Clone the repo 
* Branch: https://github.com/mlcommons/mobile_app_closed/tree/samsung-exynos2600-backend 
* Commit ID: `0344a6c06a99ee385e2c341aa7e755d7c4838323`
```
git clone https://github.com/mlcommons/mobile_app_closed 
cd mobile_app_closed
git checkout samsung-exynos2600-backend
```

## Copy libraries
from: [https://github.com/mlcommons/mobile_back_samsung/tree/samsung_exynos2600_backend/samsung_libs](https://github.com/mlcommons/mobile_back_samsung/tree/samsung_exynos2600_backend/samsung_libs) 

to: `mobile_back_samsung/samsung/lib/internal/`

## Build the app
```make WITH_SAMSUNG=1 flutter/android/release```

## Push app to device
```
adb install `output/android-apks/2025-MM-DD_mlperfbench--.apk`
```

## Launch the app once to create the cache folder
```
adb shell "am start -n org.mlcommons.android.mlperfbench/org.mlcommons.android.mlperfbench.MainActivity"
```

## NNC models will be downloaded automatically and app will validate checksums 
NNCs released here: [https://github.com/mlcommons/mobile_models/releases/tag/v5.0-samsung-exynos2600](https://github.com/mlcommons/mobile_models/releases/tag/v5.0-samsung-exynos2600)
```
MD5 (mnv4_large.nnc)            = 6d86899c52a88ae24c025a327bd7e3a0
MD5 (mnv4_large_offline.nnc)    = dbef7a1c6d56e2437d89085a3a38d7bf
MD5 (mobile_bert.nnc)           = 5b7c8b635697c909693264034fcc5898
MD5 (od.nnc)                    = 43a7e0faa0ab1e8a86e774947792e36d
MD5 (sd_dec.nnc)                = 0d961ff0471472b2903594f497e3064c
MD5 (sd_enc.nnc)                = 9470f2195a2b2eee0c0e90d5fd3853fe
MD5 (sd_unet.nnc)               = f600698aab63398291f64f3c49d99b2e
MD5 (sm_uint8.nnc)              = 43814a29b2e63719af67a30e8b5efc0c
MD5 (sr.nnc)                    = 5ff526a6a30f781fdc4be310df43ac5e
MD5 (np.bin)                    = c50807d72ce221cf08a2248a6ac3c48e
MD5 (te.bin)                    = 798b772155a69de5df44b304327bb3cc
MD5 (gt.bin)                    = f41c1130809647fbccd76707b2f14305
```

## Prepare accuracy/validation dataset and put into mlperf_datasets.zip 

## Push the datatsets to the device (for accuracy measurement)
```
adb push mlperf_datasets.zip "/sdcard/Android/data/org.mlcommons.android.mlperfbench/files/"

adb shell "cd `/sdcard/Android/data/org.mlcommons.android.mlperfbench/files/` && unzip mlperf_datasets.zip"
```

## Now you can launch the app, select submission mode and press GO
