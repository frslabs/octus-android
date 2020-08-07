# Changelog

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
