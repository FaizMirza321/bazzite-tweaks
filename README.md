# Bazzite Tweaks
General tweaks and fixes for Bazzite OS.

## USB Wake-from-Sleep Fix

# Create the system directory for sleep/wake scripts
sudo mkdir -p /etc/systemd/system-sleep/

# Create and open the script file in the nano text editor
sudo nano /etc/systemd/system-sleep/bazzite-usb-fix.sh

# --- PASTE THE CODE BELOW INSIDE NANO, THEN SAVE AND EXIT ---
#!/bin/bash
if [[ "${1}" == "post" ]]; then
    # Wait for the system to partially wake up before resetting hardware
    sleep 2
    
    # Unbind the USB controllers to clear the frozen state
    for dev in "0000:11:00.3" "0000:11:00.4" "0000:12:00.0"; do
        echo -n "$dev" > /sys/bus/pci/drivers/xhci_hcd/unbind 2>/dev/null
    done
    
    sleep 1
    
    # Re-bind the USB controllers to force them to power back up
    for dev in "0000:11:00.3" "0000:11:00.4" "0000:12:00.0"; do
        echo -n "$dev" > /sys/bus/pci/drivers/xhci_hcd/bind 2>/dev/null
    done
fi
# --- END OF NANO FILE ---

# Make the script executable so systemd is allowed to run it automatically on wake
sudo chmod 755 /etc/systemd/system-sleep/bazzite-usb-fix.sh
