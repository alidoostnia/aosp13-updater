Embedding the Updater with AOSP 13
-------------------------
You need to do the following actions to incorporate the Updater app in an AOSP project:

 - To include the Updater app in your standard build process, add the following code to your `base_product.mk` file (or any other file you ensure is in the mainstream build process):
```
PRODUCT_PACKAGES += \
    fs_config_dirs_product \
    fs_config_files_product \
    group_product \
    ModuleMetadata \
    passwd_product \
    product_compatibility_matrix.xml \
    product_manifest.xml \
    selinux_policy_product \
    Updater \
```

 - You need to modify `init.rc` in `/system/core/rootdir`, and add the following local path in your device. So, the Updater app will be able to use a local storage with encryption option to store and modify the downloaded file.
```
    mkdir /data/lineageos_updates 0770 system cache encryption=Require
    chown system cache /data/lineageos_updates
    chmod 0770 /data/lineageos_updates
```
 - After each boot, the system needs a permissive policy to permit the switch between A/B partitions and other related SELinux permissions.

If you have any problem regarding the incorporation the Lineage updater app into an AOSP project, please feel free to contact us. 

Server requirements
-------------------
The app sends `GET` requests to the URL defined by the `updater_server_url`
resource and expects as response a JSON with the following structure:
```json
{
  "response": [
    {
      "datetime": 1230764400,
      "filename": "ota-package.zip",
      "id": "5eb63bbbe01eeed093cb22bb8f5acdc3",
      "romtype": "nightly",
      "size": 314572800,
      "url": "https://example.com/ota-package.zip",
      "version": "15.1"
    }
  ]
}
```

The `datetime` attribute is the build date expressed as UNIX timestamp.  
The `filename` attribute is the name of the file to be downloaded.  
The `id` attribute is a string that uniquely identifies the update.  
The `romtype` attribute is the string to be compared with the `ro.build.type` property.  
The `size` attribute is the size of the update expressed in bytes.  
The `url` attribute is the URL of the file to be downloaded.  
The `version` attribute is the string to be compared with the `ro.build.version.release` property.  

Additional attributes are ignored.
