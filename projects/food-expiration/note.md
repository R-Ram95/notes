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


## APIs
What functions do we need?
-  create a new item in the catalogue
-  delete item(s) from catalogue
-  modify item(s) from catalogue
-  get all item(s) from catalogue (paginated?)
-  add item to expiration table
-  remove item from expiration table
-  get items that are about to expire from the exiration table
-  get all items from the expiration table (paginated?)
-  delete item(s) from the expiration table

# Diagrams

# Obsedian Links
[[wikipedia-barcode-introduction-1d-linear]]
[[wesbite-gs1-barcode-upc-a-upc-e]]
[[pdf-gs1-aidc-fresh-foods-sole-at-point-of-sale-implementation-guide-barcodes]]
[[pdf-gs1-aidc-fresh-foods-sole-at-point-of-sale-implementation-guide-barcodes|pdf-gs1-aidc-fresh-foods-sole-at-point-of-sale-implementation-guide-barcodes-gtin-read-bar-codes-application-identifiers]]
