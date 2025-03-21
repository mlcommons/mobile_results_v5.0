######
###### Build mlperf_app.apk with QTI backend support for testing on Android
######

git clone https://github.com/mlcommons/mobile_app_closed
cd mobile_app_closed
git checkout submission_v5.0_qti

# Qualcomm App source code => https://github.com/mlcommons/mobile_app_closed/tree/submission-v5.0_qti

#download the qairt sdk 2.29.0.241129 from this path
https://softwarecenter.qualcomm.com/api/download/software/qualcomm_neural_processing_sdk/v2.29.0.241129.zip (Version 2.29.0.241129)

# Extract the zip file
# copy the extracted qairt folder from zip to mobile_app_closed/mobile_back_qti,
# make sure to set up firebase as instructed in https://github.com/mlcommons/mobile_app_open/blob/master/docs/environment-setup/env-setup-firebase.md

# Build the app
  make OFFICIAL_BUILD=false FLUTTER_BUILD_NUMBER=1 WITH_QTI=1 docker/flutter/android/release


# To build for Stable Diffusion with SD8Elite <optional>

# Step 1: Install QPM3 from https://qpm.qualcomm.com/#/main/tools/details/QPM3

	From terminal, run below commands:

	1. 	qpm-cli --login <username>
	2. 	qpm-cli --license-activate qualcomm_neural_processing_sdk
	3. 	qpm-cli --extract qualcomm_neural_processing_sdk (or)
		qpm-cli --extract <full path to downloaded .qik file>

# Step 2: Download "Notebook for stable diffusion" from QPM. You need to search for "notebook for stable diffusion" in QPM.
# Step 3: Copy include folder from <path_to_notebook>/model/example3/host_linux_target_android_with_MLPerf/include to mobile_back_qti/cpp/backend_qti/StableDiffusionShared/
# Step 4: Copy libStableDiffusionShared.so from <path_to_notebook>/model/example3/host_linux_target_android_with_MLPerf/libs/aarch64-android/ to mobile_back_qti/cpp/backend_qti/StableDiffusionShared/
# Step 5: Replace the "#" symbols in start of the code section in QnnApiHelpers.hpp and JniHelpers.hpp with "//".
# Step 6: Run the command below
# make OFFICIAL_BUILD=true FLUTTER_BUILD_NUMBER=1 WITH_STABLEDIFFUSION=1 WITH_QTI=1 docker/flutter/android/release


# If there is any issue in regards to copying of the apk to output/android-apks, the release apk can be obtained from flutter/build/app/outputs

# Install app to device
adb install output/android-apks/<date>_mlperfbench-<commit_id>-qt.apk
#Open the app once on the device (to create the app's directory)

# Generate the DLCs
# Step 1: cd mobile_app_closed/mobile_back_qti/DLC/
# Step 2: sudo make htp-dlc SNPE_SDK=<path to unzipped qairt sdk 2.29.0.241129>
# Successful execution of make script will genearte necessary DLCs inside mobile_app_closed/output/DLC/mlperf_models

# To generate the bin files for Stable Diffusion <optional>
# Please follow the steps mentioned in mobile_app_closed/mobile_back_qti/DLC/util/StableDiffusion/README.md
# Successful execution of steps mentioned in above README.md will create stable diffusion artifacts inside mobile_app_closed/output/DLC/mlperf_models


# Push the models to the device
adb push mobile_app_closed/output/DLC/mlperf_models /sdcard/Android/data/org.mlcommons.android/mlperfbench/files/

# Push the datatsets to the device (required only for accuracy)
adb push mlperf_datasets /sdcard/Android/data/org.mlcommons.android/mlperfbench/files/

# For Stable Diffusion accuracy <optional>
Copy clip_model_512x512_openai-clip-vit-large-patch14.tflite from mlcommons github path to /sdcard/Android/data/org.mlcommons.android/mlperfbench/files/mlperf_models/stable-diffusion

# select submission mode and press GO
