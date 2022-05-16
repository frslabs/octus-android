# Changelog

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
