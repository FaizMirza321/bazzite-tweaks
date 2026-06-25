# Bazzite Tweaks
General tweaks and fixes for Bazzite OS.

## USB Wake-from-Sleep Fix

Run the following command to create the required system directory for sleep and wake scripts:

```bash
# Create the system directory for sleep/wake scripts
sudo mkdir -p /etc/systemd/system-sleep/
```

Next, open the text editor to create the script file:

```bash
# Create and open the script file in the nano text editor
sudo nano /etc/systemd/system-sleep/bazzite-usb-fix.sh
```

### Script Contents
Paste the following code block entirely inside the `nano` editor, then save (**Ctrl+O**, **Enter**) and exit (**Ctrl+X**):

```bash
#!/bin/bash
if [[ "\${1}" == "post" ]]; then
    # Wait for the system to partially wake up before resetting hardware
    sleep 2
    
    # Unbind the USB controllers to clear the frozen state
    for dev in "0000:11:00.3" "0000:11:00.4" "0000:12:00.0"; do
        echo -n "\$dev" > /sys/bus/pci/drivers/xhci_hcd/unbind 2>/dev/null
    done
    
    sleep 1
    
    # Re-bind the USB controllers to force them to power back up
    for dev in "0000:11:00.3" "0000:11:00.4" "0000:12:00.0"; do
        echo -n "\$dev" > /sys/bus/pci/drivers/xhci_hcd/bind 2>/dev/null
    done
fi
```

## Enable Automation

Finally, apply the correct file permissions to allow the system to execute the script automatically whenever your computer wakes up:

```bash
# Make the script executable so systemd is allowed to run it automatically on wake
sudo chmod 755 /etc/systemd/system-sleep/bazzite-usb-fix.sh
```
