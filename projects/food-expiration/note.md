# Overview/Questions
Products in circleK probably use a linear, 1D barcode. The UPC type. I am
assuming that the barcodes correlate to a key which is lookedup in CircleK's POS
when items are scanned.

# Notes
-  Producst in CircleK use UPC-A, linear 1D barcodes => therefore they only
contain the GTIN


# POC
- [ ] Catalogue DB (master with product information)  + Expiry DB
    ``` JSON
    {
        ProductId,
        ProductName,
        ProductManufacturer,
        ExpirationPeriod
    }
    ```
- [ ] ExpirationDB
    ```JSON
    {
        ProductId,
        ProductName,
        ProductManufacturer,
        DateReceived,
        ExpirationDate,
    }
    ```
- [ ] Backend (Express or Node)
- [ ] Dashboard (React Web Application)
- [ ] Scanning Tool (Mobile app => React Native? Web Based?)

# Diagrams

# Obsedian Links
[[wikipedia-barcode-introduction-1d-linear]]
[[wesbite-gs1-barcode-upc-a-upc-e]]
[[pdf-gs1-aidc-fresh-foods-sole-at-point-of-sale-implementation-guide-barcodes]]
[[pdf-gs1-aidc-fresh-foods-sole-at-point-of-sale-implementation-guide-barcodes|pdf-gs1-aidc-fresh-foods-sole-at-point-of-sale-implementation-guide-barcodes-gtin-read-bar-codes-application-identifiers]]
