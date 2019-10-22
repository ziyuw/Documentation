
---
title: Quickstart for Cloud Recording
description: 
platform: Linux Java
updatedAt: Tue Oct 22 2019 01:42:06 GMT+0800 (CST)
---
# Quickstart for Cloud Recording
<div class="alert note">The service for the Cloud Recording SDK will be phased out according to the following schedule:<li>From November 15, the service for the Cloud Recording SDK will no longer be maintained, but you can still use the Cloud Recording SDK before the service termination.</li><li>From Decemeber 15, the service for the Cloud Recording SDK will be terminated. After the service termination, you can no longer use the Cloud Recording SDK.</li></div>

This page shows how to integrate the Agora Cloud Recording SDK to record a call or live broadcast.

> The Agora Cloud Recording SDK joins a channel as a dummy client. It needs to join the same channel and use the same App ID and channel profile as the Agora Native/Web SDK.

## Prerequisites

Ensure that you meet the following requirements:

- Open the following TCP ports: 4447, 8913, and 9700.
- Open the following UDP ports: 53 and 4444.
- Operating system (physical or virtual):
  - Ubuntu 14.04+ x64
  - CenOS 6.5+ (7.0 recommended) x64
- Set up the Java environment and install OpenJDK 1.6+.
- Contact [sales-us@agora.io](mailto:sales-us@agora.io) to enable the Agora Cloud Recording service.
- Deploy a third-party cloud storage. Agora Cloud Recording supports [Amazon S3](https://aws.amazon.com/s3/?nc1=h_ls), [Alibaba Cloud](https://www.alibabacloud.com/product/oss), and [Qiniu Cloud](https://www.qiniu.com/en/products/kodo).

## Integrate the SDK

1. Download the [Agora Cloud Recording SDK](http://download.agora.io/acrsdk/release/Agora_Cloud_Recording_JAVA_SDK_for_Linux_FULL_v1_0_0.tar.gz) package.
1. Add the `agora-cloud-recording-sdk.jar` and `libagora-cloud-recording-java.so` files in the `lib` folder of the SDK package to your project and ensure that these files are connected to your project.
2. Import the `agora-cloud-recording-sdk.jar` file：
```bash
import io.agora.cloud_recording.<class name>
```

## Enable cloud recording
A complete cloud recording session includes the following steps:

1. [Create an instance](#create)

2. [Start recording](#start) (join the channel)

3. [Stop recording](#stop) (leave the channel)

4. [Release resources](#release)

Agora Cloud Recording supports automatic recording only, which means the recording session starts when the instance joins the channel and stops when the instance leaves the channel. 
For one cloud recording instance, the recording ends when it leaves the channel. To start recording again, you must create another instance.

### <a name="create"></a> Create an instance

```java
public static CloudRecorder createCloudRecorderInstance(ICloudRecordingEventHandler handler)
```

Call the [createCloudRecorderInstance](../../en/cloud-recording/cloud_recording_api_java.md) method  to create a Cloud Recording instance and connect it with your app. You can create multiple instances to record simultaneously.

> When an instance is no longer needed, call the [release](../../en/cloud-recording/cloud_recording_api_java.md) method to release all resources related to the recording.

### <a name="start"></a> Start cloud recording

```java
public RecordingErrorCode startCloudRecording(
    String appId,
    String channelName,
    String token,
    int uid,
    RecordingConfig config,
    CloudStorageConfig storageConfig)
```

After creating a Cloud Recording instance, call the [startCloudRecording](../../en/cloud-recording/cloud_recording_api_java.md) method to start recording. In this method, fill in the following parameters:

- appId: (Mandatory) The App ID of the channel to be recorded.
- channelName: (Mandatory) The name of the channel to be recorded.
- token: (Optional) The token used in the channel to be recorded. See [Use Security Keys](../../en/cloud-recording/token.md).
- uid: (Mandatory) The UID of the recording client. A 32-bit unsigned integer ranging from 1 to (2<sup>32</sup>-1) that is unique in the channel. Do not set it as 0. **Agora Cloud Recording does not support string user accounts. Ensure that the recording channel uses integer UIDs.**
- config: (Optional) The recording configuration. See [`RecordingConfig`](../../en/cloud-recording/cloud_recording_api_java.md) for details.
- storageConfig: (Mandatory). The third-party cloud storage configuration. See [`CloudStorageConfig`](../../en/cloud-recording/cloud_recording_api_java.md) for details.

> - Ensure that the settings of the App ID and channel name are exactly the same as those of the Agora Native/Web SDK for the channel to be recorded.
> - If the Agora Native/Web SDK uses Token, ensure you set the `token` parameter.

After calling the  [`startCloudRecording`](../../en/cloud-recording/cloud_recording_api_java.md)  method, the SDK starts recording when detecting other users in the channel and triggers the [`onRecordingStarted`](../../en/cloud-recording/cloud_recording_api_java.md) callback to inform the app.

### <a name="stop"></a> Stop cloud recording

```java
public RecordingErrorCode stopCloudRecording()
```

Call the [`stopCloudRecording`](../../en/cloud-recording/cloud_recording_api_java.md) method to stop recording and the SDK triggers the [`onRecordingStopped`](../../en/cloud-recording/cloud_recording_api_java.md) callback.

>To start recording again after calling the [`stopCloudRecording`](../../en/cloud-recording/cloud_recording_api_java.md) method, create another instance.

If the [`stopCloudRecording`](../../en/cloud-recording/cloud_recording_api_java.md) method is not called, the SDK automatically leaves the channel and stops recording when no user is in the channel for more than 30 seconds by default, and triggers the `RecordingErrorRecorderExit` callback to inform the app of the abnormal exit.

### <a name="release"></a> Release resources

```java
public RecordingErrorCode release(boolean cancelCloudRecording)
```

Call the [`release`](../../en/cloud-recording/cloud_recording_api_java.md) method to destroy the `CloudRecorder` instance and release all recording resources. 

In this method, the `cancelCloudRecording` parameter is set as `false` by default, and you should use this default setting. If an [error](../../en/cloud-recording/cloud_recording_api_java.md) occurs and you need to restart the recording service, set the `cancelCloudRecording` parameter as `true`. 

> To use Cloud Recording again, you must call the  [`createCloudRecorderInstance`](../../en/cloud-recording/cloud_recording_api_java.md) method to create a new instance. 

## Manage and upload the recorded files

After the recording starts, the Agora server automatically splits the recorded content into multiple TS files and keeps uploading them to the third-party cloud storage until the recording stops.

### Recording ID

The recording ID is the unique identification of a recording. Each recording instance has a unique recording ID.

After creating the recording instance, you can call the [`getRecordingId`](../../en/cloud-recording/cloud_recording_api_java.md) method to get the recording ID. You can also get the recording ID from any of the callbacks.

### Playlist of the recorded files

Each recording instance has an M3U8 file, which is a playlist pointing to all the split TS files of the recording. You can use the M3U8 file to play and manage the recorded files.

The name of the M3U8 file consists of the recording ID and the channel name. For example,  `recording_id_channelName.M3U8`.

### Upload the recorded files

The Agora server automatically uploads the recorded files. Pay attention to the following callbacks.

During the recording, the SDK triggers the following callback once every minute:

- [`onRecordingUploadingProgress`](../../en/cloud-recording/cloud_recording_api_java.md): Reports the upload progress (%) of the recorded files. 

After the recording stops, the SDK triggers one of the following callbacks:

- [`onRecordingUploaded`](../../en/cloud-recording/cloud_recording_api_java.md): Occurs when all the recorded files are uploaded to the third-party cloud storage. 
- [onRecordingBackedUp](../../en/cloud-recording/cloud_recording_api_java.md): Occurs when some of the recorded files fail to upload to the third-party cloud storage and upload to Agora Cloud Backup instead. Agora Cloud Backup automatically uploads these files to your cloud storage. If you cannot [play the recorded files](../../en/cloud-recording/cloud_recording_onlineplay.md) after five minutes, contact Agora technical support.

You can get the M3U8 filename in any of these callbacks.

## Error codes

The SDK might trigger the following callbacks when an [error](../../en/cloud-recording/cloud_recording_api_java.md) occurs:

- `onRecorderFailure`: Occurs when an error occurs in the recorder component. You do not need to do anything about this.
- `onUploaderFailure`: Occurs when an error occurs in the uploader component. You do not need to do anything about this.
- `onRecordingFatalError`: Occurs when the SDK encounters an error that cannot be recovered automatically. You need to call the `Release` method to release all resources and check the status of the Agora Cloud Recording server according to the returned error code. 
