
## **1. Open Both Files**

- Open your **original BIOS dump** (e.g., `mydump.bin`) in HxD.
    
- Open the **donor file** (e.g., a known-good boot block or ME region) in a second HxD window.
    

## **2. Find the Correct Offsets**

- **Boot Block:**
    
    - Offset: `0xFF0000` to `0xFFFFFF` (last 64KB of a 16MB file)
        
- **ME Region:**
    
    - Offset: `0x005000` to `0x50FFFF` (about 5MB region)
        

## **3. Select and Copy the Donor Region**

- In the donor file’s HxD window:
    
    - Press `Ctrl+G` (Go To) and enter the **start offset** (e.g., `FF0000` for boot block).
        
    - Click at that location, then hold `Shift` and press `Ctrl+End` to select from that point to the end (for the boot block), or use `Ctrl+E` (Select Block) and enter the **end offset** (e.g., `FFFFFF`).
        
    - Press `Ctrl+C` to copy the selected bytes.
        

## **4. Select the Same Region in Your Dump**

- In your original BIOS dump’s HxD window:
    
    - Press `Ctrl+G` and enter the **start offset** (e.g., `FF0000`).
        
    - Use `Ctrl+E` (Select Block) and enter the **end offset** (e.g., `FFFFFF`).
        
    - The region will be highlighted.
        

## **5. Paste and Overwrite**

- With the region highlighted, press `Ctrl+V` to paste and overwrite the selection.
    
- HxD will replace the selected bytes with the donor region.
    

## **6. Save the Modified File**

- Save the modified BIOS dump under a new name (e.g., `mydump_mod.bin`) to preserve your original backup.

## **7. Rinse and repeat**
- Do the same for the ME region if required.

## **8. Flash Bios Chip**
- Follow The guide to flash new .bin to bios chip on your motherboard. 

## **Quick Reference Table**

| Region     | Start Offset | End Offset | Size  |
| ---------- | ------------ | ---------- | ----- |
| Boot Block | 0xFF0000     | 0xFFFFFF   | 64 KB |
| ME Region  | 0x005000     | 0x50FFFF   | ~5 MB |

## **Tips**

- **Always keep a backup** of your original dump.
- Use the **“Go To” (Ctrl+G)** and **“Select Block” (Ctrl+E)** features for precision in HxD.
- Make sure the donor region is from a compatible source (same board family and version).
- This doesnt change the MAC or Serial number, they are kept elsewhere in the .bin file, specifically between 511DDO and 511E00. The Mac first, then followed by the serial number. 
    

---

## **Example (Boot Block Replacement)**

1. **In donor file:**
    
    - `Ctrl+G` → `FF0000`
    - `Ctrl+E` → End offset `FFFFFF`
    - `Ctrl+C`
        
2. **In your dump:**
    
    - `Ctrl+G` → `FF0000`
    - `Ctrl+E` → End offset `FFFFFF`
    - `Ctrl+V`
        
3. **Save as new file.**
    Do the same with ME but for region 0x005000 to 0x50FFFF
