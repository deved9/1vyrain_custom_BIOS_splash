# Custom BIOS splash screen
- Both Windows and Linux is used, each step is marked with (W) or (L)
- Requirements:
    - Secondary PC is needed for the last step (recommended having python installed)
    - USB flash drive

1. (W if using exe/both if using ISO) Update with original Lenovo update tool
    - [Example for T430](https://pcsupport.lenovo.com/us/cs/products/laptops-and-netbooks/thinkpad-t-series-laptops/thinkpad-t430/downloads/ds029252)
    - Follow instructions in BIOS_LOGO.TXT
    - contents of BIOS_LOGO.TXT for T430 bios

```
GUIDELINES FOR CUSTOM START UP IMAGE:
1. THE TWO IMAGE FILE SIZE ARE LIMITED TO 30KB.
2. RESOLUTION OF THE IMAGE
 LOGO1.* --> LESS THAN 640X480
 LOGO2.* --> LESS THAN 1366X768
3. VALID FORMATS FOR THE IMAGE ARE AS FOLLOWS:
 BITMAP (.BMP) FILE FORMAT
 JPEG (.JPG) FILE FORMAT
 GIF (.GIF) FILE FORMAT
4. LOGO2 IS DISPLAYED WHEN 'OS OPTIMIZED DEFAULTS' IS SET AS 'ENABLED' IN SETUP MENU. OTHERWISE, LOGO1 IS DISPLAYED.
```

2. (W) Downgrade BIOS with [IVprep](https://github.com/n4ru/IVprep)

3. (L) Apply battery/keyboard patches as needed with [Thinkpad-ec](https://github.com/hamishcoleman/thinkpad-ec)

4. (L) Read back the BIOS region with flashrom
    - Kernel boot parameter ```iomem=relaxed``` needs to be set. 
        - [How to](https://askubuntu.com/questions/19486/how-do-i-add-a-kernel-boot-parameter)
    - Use  ```flashrom -p internal --ifd -i bios -r BIOS.rom ```

5. (L) Chop off first 8M of the rom 
    - Use ```dd if=BIOS.rom of=4M.rom bs=1M skip=8 ```  

6. (L) Apply patches to BIOS file
    - Use [UEFIPatch](https://github.com/LongSoft/UEFITool/releases/tag/0.28.0) with [thinkpad-patches](https://github.com/digmorepaka/thinkpad-firmware-patches) for patches
    - Run ``` ./UEFIPatch <path to 4M.rom> <path to patchlist> -o <name of patched rom>```
  
7. (L) Write as a custom binary using 1vyrain
    - Copy patched BIOS file to secondary PC
    - Download [1vyrain ISO](https://1vyra.in/) and create bootable USB
        - Use ```sudo dd if=<path to ISO> of=<path to USB drive> status=progress conv=fsync``` 
    - Boot up said USB
    - When asked, select "flash custom image from URL"
    - Spin up HTTP server from the folder where BIOS.rom using secondary PC
        - ``` cd <path to folder with rom> ``` 
        - ``` python -m http.server ``` to run HTTP server from your current directory
        - URL is ```<hosts local IP>/<name of patched rom>```
        - I recommend trying to download the file on the same PC as a test
    - Enter URL into the 1vyrain prompt
8. Profit
