---
layout: post
title:  "Rails Active Storage"
date:   2019-08-02 19:23:11
categories: Rails Active_Storage
---

## Tips
* A tmp file will be uploaded and recorded in `active_storage_blobs` table before entering controller business code.(This file can be thought as a backup file. This file will not be deleted if the model record is deleted.)
### For create
* If validation failed before save, the file will still uploaded and recorded in `active_storage_blobs` table.
The metadata value is  `{"identified":true}`, the same as the tmp file.
* If the file is uploaded and record is saved successfully, the metadata value will be marked as `{"identified":true,"analyzed":true}`. 
### For update
* When updating, the old blob file and record in `active_storage_blobs` table will be deleted.
* A new record will be inserted into `active_storage_blobs` table and be related to `active_storage_attachments`.
