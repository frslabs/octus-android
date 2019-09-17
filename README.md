# OCTUS SDK
![version](https://img.shields.io/badge/version-v2.0.0-blue)

Octus SDK uses advanced deep learning technologies for accurate and fast ID scanning and OCR. Businesses can integrate the Octus SDK into native Android Apps which comes with pre-built screens and configurations. The SDK returns the scanned images, extracted data and error codes. And as a safety measure, the SDK does not store any of the personal data or ID images that are scanned.

For the list of supported documents per country , refer to [Octus Country Specific Supported Documents](SUPPORTED_DOCUMENT_BY_COUNTRY.md)

# Table Of Content

- [Prerequisite](#prerequisite)
- [Android SDK Requirements](#android-sdk-requirements)
- [Download](#download)
  - [Using maven repository](#using-maven-repository)
- [Setup](#setup)
  - [Permissions](#permissions)
- [Quick Start](#quick-start)
  - [Initiating the Octus scanner](#initiating-the-octus-scanner)
  - [Handling the result](#handling-the-result)
- [Octus Result](#octus-result)
- [Octus Parameters](#octus-parameters)
- [Help](#help)

## Prerequisite

You will need a valid license to use the Octus SDK, which can be obtained by contacting `support@frslabs.com` . 

Once you have the license , follow the below instructions for a successful integration of Octus SDK onto your Android Application.

## Android SDK Requirements

**Minimum SDK Version** -  **16** (Ice Cream Sandwich) or higher

**Compile SDK Version** -  **28**  or higher

## Download

#### Using maven repository

Add the following code to your `project` level `build.gradle` file

```groovy
allprojects { 
    repositories { 
        google() 
        jcenter() 
        maven { url "https://jitpack.io" }
        maven { 
            // URL for Octus SDK. 
            url "ENTER_OCTUS_MAVEN_REPO_URL_HERE"                  
            credentials { 
                   username 'repo-username' 
                   password 'repo-password' 
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

And then, add the dependencies
```groovy

// ...

dependencies {
    /* Dependencies for Octus SDK */ 
    implementation 'com.android.support:design:<version above 23.4.0>'      
    implementation 'com.android.support.constraint:constraint-layout:<version above 1.1.3>'
   
    implementation 'com.frslabs.atlas.android.sdk:octus:2.0.0' //Octus SDK Core
    implementation 'com.gemalto.jp2:jp2-android:1.0'
    implementation 'com.rmtheis:tess-two:9.0.0'
    implementation 'com.google.android.gms:play-services-vision:15.0.0'
}
```

## Setup

#### Permissions

Octus requires the camera permission to initiate its scanner

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="your.package.name" >

    <!-- Required by Octus -->
    <uses-permission android:name="android.permission.CAMERA" />

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

        //Initialize the Octus Sdk Config object with the appropriate configurations
        Config octusConfig = new Config.Builder()
                .setLicenseKey(OCTUS_LICENSE_KEY)
                .showInstruction(false)
                .dataPointsAll(false)
                .orientationFlat(false)
                .setScanAlertType(Utility.Alert.VIBRATION)
                .setLanguage(Utility.Language.EN)
                .setDocumentCountry(Country.IN)
                .setDocumentType(Document.VID)
                .setDocumentSubType(Utility.SubType.OCR)
                .setDocumentSide(Utility.Side.FRONT_BACK)
                .aadhaarNumberMasked(false)
                .build();

        //Call the Octus Sdk to start scanning
        Octus.setSdkConfig(octusConfig)
                .enableLogs()
                .initialise(this, this); //Pass the main context here
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
    public void onScanSuccess(ScanResult scanResult) {

        /* Handle the Octus Sdk result here */
        Log.d("OctusSdk Result :", scanResult.toString());

    }

    @Override
    public void onScanFailure(String errorCode) {

        /* Handle the Octus Sdk failure result here */
        Toast.makeText(this, "Error: " + errorCode, Toast.LENGTH_SHORT).show();

    }

    // ...
```



## Octus Result

Result of the scan is obtained from the `ScanResult` instance . Complete Octus result is given below,

```java
  // ...

  @Override
  public void onScanSuccess(ScanResult scanResult) {

        /* Handle the Octus Sdk result here */
        Log.d("OctusSdk Result :", scanResult.toString());

        /* Below values are given for ID card with MRTD & without MRTD */
        String code = scanResult.getCode();
        String documentType = scanResult.getDocumentType();
        String name1 = scanResult.getName1();
        String name2 = scanResult.getName2();
        String idNumber1 = scanResult.getDocumentNumber1();
        String idNumber2 = scanResult.getDocumentNumber2();
        String dob = scanResult.getDateOfBirth();
        String expiry = scanResult.getExpiryDate();
        String gender = scanResult.getGender();
        String address1 = scanResult.getAddress1();
        String address2 = scanResult.getAddress2();
        String address3 = scanResult.getAddress3();
        String address4 = scanResult.getAddress4();
        String city = scanResult.getCity();
        String state = scanResult.getState();
        String idCountry = scanResult.getCountry();
        String idIssCountry = scanResult.getIssuingCountry();

        /* Below values gives the Document Image path */
        String idFacePath = scanResult.getFace();
        String idFrontPhotoPath = scanResult.getPhoto1();
        String idBackPhotopath = scanResult.getPhoto2();

        /* Below values are applicable to Cheque Leaf (India) only */
        String bankAccountNumber = scanResult.getBankAccountNumber();
        String bankAccIfsc = scanResult.getBankIfsCode();
        String gstn = scanResult.getGSTN();

        /* Below values are applicable to Voter ID (India) only */
        String frontConfidenceScore = scanResult.getConfidenceIndexF();
        String backConfidenceScore = scanResult.getConfidenceIndexB();
        String frontIdOcrStatus = scanResult.getFrontIdScanStatus();
        String backIdOcrStatus = scanResult.getConfidenceIndexB();
  }
  
  // ...
```



## Octus Parameters

- `setLicenseKey(String octusLicenseKey)`   ***(Required)***
  
  Accepts the Octus licence key as a `String`
  
  

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

## Help
For any queries/feedback , contact us at `support@frslabs.com` 
