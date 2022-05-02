# OCTUS ANDROID SDK
![version](https://img.shields.io/badge/version-v3.6.1-blue)

Octus SDK uses advanced deep learning technologies for accurate and fast ID scanning and OCR. Businesses can integrate the Octus SDK into native Android Apps which comes with pre-built screens and configurations. The SDK returns the scanned images, extracted data and error codes. And as a safety measure, the SDK does not store any of the personal data or ID images that are scanned.

For the list of supported documents per country , refer to [Octus Country Specific Supported Documents](SUPPORTED_DOCUMENT_BY_COUNTRY.md)

**You can find the latest version and release history [Here](CHANGELOG.md)**

# Table Of Content

- [Prerequisite](#prerequisite)
- [Android SDK Requirements](#android-sdk-requirements)
- [Download](#download)
  - [Using maven repository](#using-maven-repository)
- [Setup](#setup)
  - [Permissions](#permissions)
  - [Proguard rules](#proguard-rules)
- [Quick Start](#quick-start)
  - [Initiating the Octus scanner](#initiating-the-octus-scanner)
  - [Handling the result](#handling-the-result)
- [Octus Result](#octus-result)
- [Octus Error Codes](#octus-error-codes)
- [Octus Parameters](#octus-parameters)
- [Help](#help)

## Prerequisite

You will need a valid license to use the Octus SDK, which can be obtained by contacting `support@frslabs.com` . 

Depending on the license - offline or online - you have opted for, the ping functionality to billing servers will be disabled or enabled. For instance, if you have opted for the offline SDK model, then there will be no server ping needed to our billing server to bill you. However, if you have chosen a transaction based pricing, then after each transaction, a ping request will be made to our billing server. This cannot be overrided by the App. A point to note is that if the ping transaction fails for any reason, the whole transaction will be void without any results from the SDK.

Once you have the license , follow the below instructions for a successful integration of Octus SDK onto your Android Application.

## Android SDK Requirements

**Minimum SDK Version** -  **19** (Kitkat) or higher

**Compile SDK Version** -  **28**  or higher

## Download

#### Androidx Migration 

Existing users using version 2.x.x can continue to use the SDKs until November 2020 if no changes are made to the App. And any new applications you are developing or if you are updating your existing application, then you must use SDK version 3.x.x which is AndroidX compatible. From 01 August 2020 all new applications integrating our SDK must use 3.x.x version. Support for version 2.x.x will cease from 01 November 2020. Please write to us as support@frslabs.com if you need more information on AndroidX release.

#### Using maven repository


Add the following code to your `project` level `build.gradle` file

```groovy
allprojects { 
    repositories { 
        google() 
        jcenter() 
        
        //Maven credentials for the Octus SDK
        maven { 
            // URL for Octus SDK. 
            url "https://octus-android.repo.frslabs.space/"                  
            credentials { 
                   username '<YOUR_USERNAME>' 
                   password '<YOUR_PASSOWRD>' 
            }
       }
       
       /*
        *Include below code only for transaction based billing
        */
        //Maven credentials for the Torus SDK
        maven {
            url "https://torus-android.repo.frslabs.space/"
            credentials {
                username '<YOUR_USERNAME>'
                password '<YOUR_PASSOWRD>'
            }
        }
        
    }
}
```

After that, add the following code to your `app` level `build.gradle` file

```groovy
// ...

defaultConfig { 

    // ...
    
    ndk { 
        abiFilters "armeabi-v7a", "arm64-v8a", "x86", "x86_64" 
    } 

    vectorDrawables.useSupportLibrary true 
    
    renderscriptTargetApi 21 
    renderscriptSupportModeEnabled false 
}

// ...
```

And then, Find the latest version of Octus SDK [Here](CHANGELOG.md) and add the dependencies

For versions 3.X.X and above ,
```groovy

// ...

dependencies {
    /* Dependencies for Octus SDK Using Androidx */ 
    implementation 'com.google.android.material:<lastest version>'
    implementation 'androidx.appcompat:appcompat:<latest version>'
    implementation 'androidx.constraintlayout:constraintlayout:<latest version>'
   
    // ...
    
    /* Core Octus SDK Dependencies */
    implementation 'com.frslabs.android.sdk:octus:3.X.X' // Required . Find latest version at https://github.com/frslabs/octus-android/blob/master/CHANGELOG.md
    implementation 'com.gemalto.jp2:jp2-android:1.0' // Required
    implementation 'com.rmtheis:tess-two:9.1.0' // Required
    implementation 'com.google.android.gms:play-services-vision:15.0.0' // Required
    implementation 'com.frslabs.android.sdk:torus:1.0.0' // Optional - Needed if transaction based billing is enabled
    implementation 'com.google.code.gson:gson:2.8.6' // Optional - Needed if transaction based billing is enabled
    
    // ...
}
```

For versions 2.X.X and below ,
```groovy

// ...

dependencies {
    /* Dependencies for Octus SDK Using support library (Non-AndroidX) */  
    implementation 'com.android.support:design:<version above 23.4.0>'      
    implementation 'com.android.support.constraint:constraint-layout:<version above 1.1.3>'
   
    // ...
    
    /* Core Octus SDK Dependencies */
    implementation 'com.frslabs.android.sdk:octus:2.X.X' // Required , Recent Non-AndroidX version is 2.0.9
    implementation 'com.gemalto.jp2:jp2-android:1.0' // Required
    implementation 'com.rmtheis:tess-two:9.1.0' // Required
    implementation 'com.google.android.gms:play-services-vision:15.0.0' // Required
    implementation 'com.frslabs.android.sdk:torus:1.0.0' // Optional - Needed if transaction based billing is enabled
    implementation 'com.google.code.gson:gson:2.8.6' // Optional - Needed if transaction based billing is enabled
    
    // ...
}
```

## Setup

#### Permissions

Octus requires the camera permission to initiate its scanner

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="your.package.name" >

    <!-- Required by Octus -->
    <uses-permission android:name="android.permission.CAMERA" />

    <!-- Optional - Required if transaction based billing is enabled -->
    <uses-permission android:name="android.permission.INTERNET" />  
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application>
      ...
    </application>

</manifest>
```

## Quick Start

#### Initiating the Octus scanner

Initialize the `Octus` instance with the appropriate configurations to invoke the Octus Sdk

```java
public class MainActivity extends AppCompatActivity implements OctusResultCallback {

    // ...

    /* Enter the Octus license key here */
    private String OCTUS_LICENSE_KEY = "<ENTER_YOUR_LICENSE_KEY_HERE>";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button callSdk = findViewById(R.id.call_sdk);
        callSdk.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                /* Invoke the Octus Sdk */
                callOctusSdk();
            }
        });
    }

   private void callOctusSdk() {
        
        try {
        
              //Initialize the Octus Sdk Config object with the appropriate configurations
              OctusConfig octusConfig = new OctusConfig.Builder()
                      .setLicenseKey(OCTUS_LICENSE_KEY)
                      .showInstruction(false)
                      .setScanMode(Utility.ScanMode.AUTO)
                      .dataPointsAll(false)
                      .orientationFlat(false)
                      .setScanAlertType(Utility.Alert.VIBRATION)
                      .setLanguage(Utility.Language.EN)
                      .setDocumentCountry(Country.IN)
                      .setDocumentType(Document.VID)
                      .setDocumentSubType(Utility.SubType.OCR)
                      .setDocumentSide(Utility.Side.FRONT_BACK)
                      .aadhaarNumberMasked(false)
                      .removeWatermark() // Optional and only for Document.CQL
                      .build();
      
              //Call the Octus Sdk to start scanning
              Octus.setSdkConfig(octusConfig)
                      .enableLogs()
                      .initialise(this, this); //Pass the main context here
                
        }catch (OctusInitException e){
              //Handle exception here
              e.printStackTrace();
        }
    }

    // ...

}
```

For all `parameters` and their possible values, refer [Octus Parameters](#octus-parameters)

#### Handling the result

Your activity must implement `OctusResultCallback` to receive the result.

```java
    // ...

    @Override
    public void onScanSuccess(OctusResult octusResult) {

        /* Handle the Octus Sdk result here */
        Log.d("OctusSdk Result :", octusResult.toString());

    }

    @Override
    public void onScanFailure(String errorCode) {

        /* Handle the Octus Sdk failure result here */
        Toast.makeText(this, "Error: " + errorCode, Toast.LENGTH_SHORT).show();

    }

    // ...
```

For all `errorCode`'s and their meanings refer [Octus Error Codes](#octus-error-codes)


## Octus Result

Result of the scan is obtained from the `OctusResult` instance . Complete Octus result is given below,

```java
  // ...

  @Override
  public void onScanSuccess(OctusResult octusResult) {

        /* Handle the Octus Sdk result here */
        Log.d("OctusSdk Result :", octusResult.toString());

        /* Below values are given for ID card with MRTD & without MRTD */
        String code = octusResult.getCode();
        String documentType = octusResult.getDocumentType();
        String documentCountry = octusResult.getDocumentCountry();
        String documentSubType = octusResult.getDocumentSubType();
        String documentSide = octusResult.getDocumentSide();
        String dataPointAll = octusResult.getDataPointAll();
        String name1 = octusResult.getName1();
        String name2 = octusResult.getName2();
        String idNumber1 = octusResult.getDocumentNumber1();
        String idNumber2 = octusResult.getDocumentNumber2();
        String dob = octusResult.getDateOfBirth();
        String expiry = octusResult.getExpiryDate();
        String gender = octusResult.getGender();
        String address1 = octusResult.getAddress1();
        String address2 = octusResult.getAddress2();
        String address3 = octusResult.getAddress3();
        String address4 = octusResult.getAddress4();
        String city = octusResult.getCity();
        String state = octusResult.getState();
        String idCountry = octusResult.getCountry();
        String idIssCountry = octusResult.getIssuingCountry();

        /* Below values gives the Document Image path */
        String idFacePath = octusResult.getFace();
        String idFrontPhotoPath = octusResult.getPhoto1();
        String idBackPhotopath = octusResult.getPhoto2();

        /* Below values are applicable to Cheque Leaf (India) only */
        String bankAccountNumber = octusResult.getBankAccountNumber();
        String bankAccIfsc = octusResult.getBankIfsCode();
        String gstn = octusResult.getGSTN();

        /* Below values are applicable to Voter ID (India) only */
        String frontConfidenceScore = octusResult.getConfidenceIndexF();
        String backConfidenceScore = octusResult.getConfidenceIndexB();
        String frontIdOcrStatus = octusResult.getFrontIdScanStatus();
        String backIdOcrStatus = octusResult.getConfidenceIndexB();
        
        /* Below values are applicable to Aadhaar Card (India) only */
        String aadhaarMaskStatus = octusResult.getAadhaarMaskStatus();
        
        /* Below values are applicable to MRTD supported documents only */
        String isMRZChecksumValidated = octusResult.getMrzChecksumValidityStatus();
        
  }
  
  // ...
```

Given below are some public methods of `OctusResult` in brief

<div>
<table style="width:100%">
<tr>
<th bgcolor="#F1F1F1" colspan="3">Public Methods</th>
</tr>
<tr>
<td>String</td>
<td>getAadhaarMaskStatus()</td>
<td>
<pre>
Gets the Aadhaar number masking status. Possible values are,

‘XX’ - BOTH SIDES ALREADY MASKED
‘YY’ - BOTH SIDES MASKED
‘YN’ - FRONT MASKED, BACK NOT MASKED
‘NY’ - FRONT NOT MASKED, BACK MASKED 
‘NN’ - BOTH SIDES NOT MASKED
</pre>
</td>
</tr>
</table>
</div>

## Octus Error Codes

Error codes and their meaning are tabulated below

| Code          | Message              |
| ------------- | ------------------- |
| 801  | Scan timed out                |
| 802  | Invalid ID parameters passed  |
| 803  | Camera permission denied      |
| 804  | Scan interrupted              |
| 805  | License expired               |
| 806  | License invalid               |
| 807  | Invalid camera resolution     |
| 811  | QR not detected               |
| 812  | QR parsing failed             |
| 108  | Internet unavailable          |
| 401  | API limit exceeded            |
| 429  | Too many requests             |

## Octus Parameters

- `setLicenseKey(String octusLicenseKey)`   ***(Required)***
  
  Accepts the Octus licence key as a `String`
  
- `setScanMode(Utility.ScanMode scanMode)` ***(Required)***
  
  Sets the scanning mode
  
  | Value          | Effect                 |
  | -------------- | ---------------------- |
  | Utility.ScanMode.AUTO    | Automatically starts scanning as soon as camera preview is ready|
  | Utility.ScanMode.MANUAL  | Displays a button used to start the scan when clicked |

- `setDocumentType(Document documentType)` ***(Required)***
  
  Sets the Document which has to be scanned. Possible values are, 
  
  | Value          | Effect                 |
  | -------------- | ---------------------- |
  | Document.PAN   | Pan Card               |
  | Document.ADR   | Aadhaar Card           |
  | Document.VID   | Voter ID               |
  | Document.NID   | National ID            |
  | Document.PPT   | Passport               |
  | Document.VSA   | Visa                   |
  | Document.DRV   | Driving Licence        |
  | Document.CQL   | Cheque Leaf            |
  | Document.SSN   | Social Security Number |
  | Document.FRM16 | Form 16                |
  | Document.GST   | GST Form               |
  | Document.IMG_ADR | Image Capture Aadhaar  |
  | Document.IMG_ANY | Plain Image capture  |

- `setDocumentCountry(Country country)`  ***(Required)***
  
  Sets the country associated with the Document.
  
  For the complete list of supported countries refer [Country Parameters](COUNTRY_PARAMETERS.md)
  
  
- `setDocumentSubType(Utility.SubType subType)` ***(Required)***  
  
  Sets the Document Sub Type . Majority of the documents support only `Utility.SubType.OCR` as a sub type. 
  
  *Documents where both `Utility.SubType.OCR` and `Utility.SubType.QR_CODE` apply are ,*
  
  - `Document.ADR`
  - `Document.DRV`
  
  *Documents where both `Utility.SubType.MRZ` and `Utility.SubType.OCR` apply are ,*
  
  - `Document.NID`
  
  *Documents where only `Utility.SubType.MRZ` apply are ,*
 
  - `Document.PPT`
  - `Document.VSA`
  
  *Documents where only `Utility.SubType.PDF417` apply are ,*
  - `Document.DRV` For `Country.NG`
  - `Document.VID` For `Country.NG`
  
  
  Possible values for Sub Type are,
  
  | Value            | Effect                         |
  | ---------------- | ------------------------------ |
  | Utility.SubType.OCR | Scans the document in OCR mode |
  | Utility.SubType.QR_CODE | Scans the document in QR mode  |
  | Utility.SubType.MRZ | Scans the document in MRZ mode  |
  | Utility.SubType.PDF417 | Scans the document in PDF417 mode  |

  
- `setLanguage(Utility.Language language)`  ***(Optional)***  *(Defaults to **Utility.Language.EN**)*
  
   Sets the language associated with the Document. Possible values are,
  
  | Value           | Effect  |
  | --------------- | ------- |
  | Utility.Language.EN | English |
  | Utility.Language.FR | French  |
  | Utility.Language.ES | Spanish |
  | Utility.Language.AR | Arabic  |
  | Utility.Language.HI | Hindi   |


- `showInstruction(boolean show)`  ***(Optional)***  *(Defaults to **false**)*
  
  Sets flag to enable/disable the instruction screen prior to scanning. Possible values are,
  
  | Value              | Effect                          |
  | ------------------ | ------------------------------- |
  | true | Enables the instruction screen  |
  | false  | Disables the instruction screen |


- `setScanAlertType(Utility.Alert alertType)`  ***(Optional)***  *(Defaults to **Utility.Alert.SOUND_VIBRATION**)*
  
  Sets the alert type when the sdk returns the result
  
  | Value                          | Effect                                                                    |
  | ------------------------------ | ------------------------------------------------------------------------- |
  | Utility.Alert.SOUND     | Triggers a beep sound after the scan completes                            |
  | Utility.Alert.VIBRATION | Triggers a mild haptic response (Vibration alerting) after scan completes |
  | Utility.Alert.NONE      | Disables any feedback on scan being completed                             |
  | Utility.Alert.SOUND_VIBRATION      | Triggers both beep sound and haptic response after scan completes         |
  

- `setDocumentSide(Utility.Side documentSide)`  ***(Optional)*** *(Defaults to **Utility.Side.FRONT_BACK**)*
  
  Sets the value of the document side to be scanned 
  
  | Value                   | Effect                                               |
  | ----------------------- | ---------------------------------------------------- |
  | Utility.Side.FRONT      | Scans only the Front (Primary) side of the document  |
  | Utility.Side.BACK       | Scans only the Back (Secondary) side of the document |
  | Utility.Side.FRONT_BACK | Scans both Front and back side of the document       |
  

- `dataPointsAll(boolean dataPointCategory)`  ***(Optional)*** *(Defaults to **false**)*
  
  Sets the flag to set the data point category
  
  | Value                  | Effect     |
  | ---------------------- | ---------- |
  | true | Provides the scan result **only** if **all** data points are found |
  | false| Provides the scan result if **atleast one** of the data points are found|
  

- `orientationFlat(boolean isOrientationFlat)`  ***(Optional)*** *(Defaults to **false**)*
  
  Sets the value for which the scanner should lock the orientation with respect to the scan surface . Possible values are,
  
  | Value               | Effect     |
  | ------------------- | ---------- |
  | true  | Scans only when orientation of the phone(camera) is perpendicular(flat) to the scan surface. |
  | false | Scans ignoring the orientation of the phone(camera) to the scan surface. |


- `aadhaarNumberMasked(boolean numberMasked)` ***(Optional)*** *(Defaults to **false**)*  *(Applies ONLY to Document.ADR and COUNTRY.IN)*
  
  Sets the flag to enable/disable aadhaar number masking
  
  | Value              | Effect                        |
  | ------------------ | ----------------------------- |
  | true | Masks the  aadhaar number in the scan result(image) |
  | false | disables masking the aadhaar number in the scan result |

- `removeWatermark()` ***(Optional)*** *(Applies ONLY to Document.CQL)*
  
  Sets flag to remove the watermark over the cheque leaf output image. If method is not called, default behavior is to include the watermark.
  
## Help
For any queries/feedback , contact us at `support@frslabs.com` 
