# SagePowerPalStandalone
Powerpal python script which locally connects via bluetooth and outputs the results in terminal

Check my repositories for one that will work natively within HomeAssistant. Coming soon.



Powerpal BLE Standalone — Quick Reference
How it works
Scans for your Powerpal by MAC address — keeps retrying every 15s until found

Connects via BLE and authenticates using your pairing code

Syncs time and sets the measurement interval (default 1 minute)

Streams readings — every minute you get: timestamp, pulse count, kWh, and average watts

Auto-reconnects when the Powerpal drops the connection (~every 6-8 min, normal behaviour)

Config (top of the file) - most of the these details can be found in the official Powerpal app once paired. You need to remove that pairing before using this.
python
POWERPAL_MAC     = "D8:3F:AE:A6:FD:XX"  # your device MAC
PAIRING_CODE     = 411XXX                # your 6-digit code
PULSES_PER_KWH   = 3200                  # your meter's pulse rate -this was mine.
INTERVAL_MINUTES = 1                     # 1-15 minutes
LIVE_PULSE       = False                 # True for per-pulse instantaneous watts
Installation (Debian, one-time)
bash
# Create virtual environment
python3 -m venv ~/powerpal-env
source ~/powerpal-env/bin/activate
pip install bleak

# Pair the device (one-time only)
bluetoothctl
> scan on
> pair D8:3F:AE:A6:FD:XX  #MAC ADDRESS OF POWERPAL
> trust D8:3F:AE:A6:FD:XX
> quit


Running


source ~/powerpal-env/bin/activate
python sagepp-standalone.py
> 
To run automatically on boot


# Create a systemd service
sudo nano /etc/systemd/system/powerpal.service
text
[Unit]
Description=Powerpal BLE Client
After=bluetooth.target

[Service]
User=root
ExecStart=/root/powerpal-env/bin/python /root/sagepp-standalone.py
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
bash
sudo systemctl enable --now powerpal
