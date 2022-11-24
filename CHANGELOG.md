# Changelog
‼ ATTENTION ‼ → BREAKING CHANGE introduced at Octus SDK `v3.8.0`. We have introduced a new license format. If you are using versions prior to `v3.8.0` and intend to update to `v3.8.0` or above please contact support@frslabs.com for an updated license.

## **v3.8.4** - *24-11-2022*
- ANR bug fixes

## **v3.8.3** - *21-11-2022*
- Bug fixes

## **v3.8.2** - *11-10-2022*
- General improvements for E-Mandate Category 2 - Page 1, Page 2 Documents
- Added Error Codes

## **v3.8.1** - *10-08-2022*
- Added New document types,
  - `Document.E_MANDATE_CAT2_PAGE1` - E-Mandate Category 2 - Page 1 Document 
    - Returns the following results,
      - fully cropped document via `octusResult.getPhoto1()` 
      - document cropped at perforated line via `octusResult.getPhoto2()`
  - `Document.E_MANDATE_CAT2_PAGE2` - E-Mandate Category 2 - Page 2 Document
    - Returns the following results,
      - fully cropped document via `octusResult.getPhoto1()`
- Minor Bug fixes

## **v3.8.0** - *15-07-2022*
- Added support for new license format
- SDK now targets API 32

`NOTE:` BREAKING CHANGE introduced at Octus SDK `v3.8.0`. We have introduced a new license format. Contact support@frslabs.com for an updated license.

## **v3.7.2** - *22-06-2022*
### New
- Added [`skipDocumentAlternateCaptureMode(boolean skipMode)`](README.md#octus-parameters) to OctusConfig 
### Changes
- Improved E-Mandate document detection capabilities

## **v3.7.1** - *10-06-2022*
### Changes
- Added manual capture flow for E-Mandate document (When Auto Capture fails)
- Improved E-Mandate document processing logic, updated related UI and UX
- Added `Passport File number` to response (use `octusResult.getDocumentNumber2()` to retrieve) for the following configuration
```js
Country.IN
Document.PPT
Utility.Side.FRONT_BACK
Utility.SubType.MRZ
```

## **v3.6.6** - *30-05-2022*
### Changes
- Added Flash and Progress Bar UI components for E-mandate document
- Added custom rectangular UI component for visually assisting user when scanning E-Mandate document

## **v3.6.5** - *26-05-2022*
### New
- [`setScanTimeLimit(timeInSec)`](README.md#octus-parameters) feature for the document scan time limit (in sec)
### Fixed
- Fix scan timeout error after successfully scanned

## **v3.6.4** - *20-05-2022*
### Changes
- Improved E-mandate bottom part crop (Identifies and stops at perforated line)
- Updated E-mandate specific informational text messages to assist user
- Updated implementation to support material design library `v1.0.0+`

## **v3.6.3** - *17-05-2022*
### Fix
- Updated behaviour of Aadhaar mask flag to return Aadhaar Number instead of VID (as `documentNumber2` in the result)  if enabled.

## **v3.6.2** - *16-05-2022*
### Changes
- Updated text for visual feedback when scanning Cheque leaf

## **v3.6.1** - *02-05-2022*
### New
- `removeWatermark()` feature for removal of watermark in Cheque leaf
- New document type `E_MANDATE_CAT1`
### Changes
- Updated `minSdkVersion` to `19`

## **v3.6.0** - *02-12-2021*
### Fixed
- Union of india DL - dates & address issue fixed.

## **v3.5.0** - *30-08-2021*
### Fixed
- date of birth and address issue for new voter id card.

## **v3.4.0** - *24-05-2021*
### Fixed
- Camera API2 frame color issue resolved.

## **v3.3.0** - *02-05-2021*
### Added
Updated responses to scanning front and back of ID cards. The responses for frontIdScanStatus and backIdScanStatus are improved with response codes of Success, Fail or NA (Not Applicable). For instance
1)    If you let the user scan only the front, you will get the responses for frontIdScanStatus and backIdScanStatus as Success and NA.
2)    If you let the user scan both front and back, and if both are scanned successfully, you will get the responses for frontIdScanStatus and backIdScanStatus as Success and Success.
3)    If you let the user scan both front and back, and if front has scanned successfully and back has failed, you will get the responses for frontIdScanStatus and backIdScanStatus as Success and Fail.
Note that for conditions 2 and 3 above to be satisfied, you will need to set dataPointAll='False (meaning that the scanner will return values even if one value is scanned from the front or back of the ID). 

## **v3.2.0** - *15-08-2020*
### Added
- Torus v1.0.0 integrated 

## **v3.1.0** - *07-08-2020*
### Added
- ExideLife barcode support.

## **v3.0.0** - *01-08-2020*
### Bug fixes
- Fixed Licence key end url issue.
### New
- Migrated Octus from support library to AndroidX

## **v2.0.9** - *03-02-2020*
### Bug fixes
- Fixed UI issue caused due to redundant theme.

## **v2.0.8** - *10-01-2020*
### Added
- Improvements in OCR of scanned images
### Bug fixes
- SDK starts scanning of ID dcuments prior to camera permission granted or denied .This issue has been fixed.


## **v2.0.7** - *19-12-2019*
### Added
- Passport MRZ validation added.
- Added String isMRZChecksumValidated = octusResult.getMrzChecksumValidityStatus();
- The responses are yes and no (yes is validation success and no is validation has failed)
