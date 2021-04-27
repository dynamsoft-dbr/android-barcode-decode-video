# Real-time barcode scanning from Android camera stream

How to build a simple Android barcode scanning app with Android camera API and Dynamsoft Barcode Reader.

[Learn more about Dynamsoft Barcode Reader SDK >](http://www.dynamsoft.com/Products/Dynamic-Barcode-Reader.aspx)

## What You Should Know
- [![](https://img.shields.io/badge/Download-Offline%20SDK-orange)](https://www.dynamsoft.com/barcode-reader/downloads)
- [![](https://img.shields.io/badge/Get-30--day%20FREE%20Trial%20License-blue)](https://www.dynamsoft.com/customer/license/trialLicense/?product=dbr)

## Resources

- [Dynamsoft Barcode Reader for Android API Documentation](https://www.dynamsoft.com/help/Barcode-Reader-Android/index.html)

- [More resources](https://www.dynamsoft.com/Products/Barcode-Reader-Resources.aspx)

## Barcode scanning from Android camera stream

Call `startDecodingFrame()` to initialize the environment and call `appendFrame()` repeatedly to add the preview images.

```java
cameraView.addFrameProcessor(new FrameProcessor() {
            @SuppressLint("NewApi")
            @Override
            public void process(@NonNull final Frame frame) {
                try {
                    if (isDetected && isCameraOpen) {
                        YuvImage yuvImage = new YuvImage(frame.getData(), ImageFormat.NV21,
                                frame.getSize().getWidth(), frame.getSize().getHeight(), null);
                        if (width == 0) {
                            width = yuvImage.getWidth();
                            height = yuvImage.getHeight();
                            stride = yuvImage.getStrides()[0];
                            reader.setErrorCallback(errorCallback, null);
                            reader.setTextResultCallback(textResultCallback, null);
                            reader.setIntermediateResultCallback(intermediateResultCallback, null);
                            // start a new thread to decode barcodes
                            reader.startFrameDecoding(10, 10, width, height, stride, EnumImagePixelFormat.IPF_NV21, "");
                        } else {
                            PublicRuntimeSettings s  = reader.getRuntimeSettings();
                            // add new image frames for barcode scanning
                            int frameid = reader.appendFrame(yuvImage.getYuvData());
                            Log.i("FrameId", frameid + "");
                        }
                        if (bUpateDrawBox) {
                            bUpateDrawBox = false;
                            Message message = handler.obtainMessage();
                            Rect imageRect = new Rect(0, 0, width, height);
                            message.obj = imageRect;
                            message.what = 0x001;
                            handler.sendMessage(message);
                        }
                        isDetected = true;
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
```
