# Netara-OS

<div align="center">
  <img src="netara.svg">
</div>

### Netara OS adalah distribusi Linux keamanan jaringan berbasis Linux Mint 22 Cinnamon, dirancang khusus untuk penetration testing dan analisis keamanan jaringan.
# Upgrade Package Manager
    apt update && apt upgrade -y    

# Hapus Bloatware Linux Mint yang tidak dibutuhkan
    sudo apt-get remove --purge libreoffice* rhythmbox thunderbird simple-scan warpinator transmission-gtk mintchat -y

# Instal Software yang dibutuhkan untuk Netara OS
    apt install wireshark nmap ncat python3-impacket aircrack-ng python3-scapy snort bmon hping3 bettercap iftop nikto lynis -y

# Mematikan Layanan yang tidak perlu
### 1. Bluetooth
    systemctl disable bluetooth.service 

### 2. Printing
    systemctl disable cups.service 
    systemctl disable cups-browsed.service 

### 3. Zero-config
    systemctl disable avahi-daemon.service 
    systemctl disable avahi-daemon.socket

# Copy folder yang sudah didownload dan semua zip sudah di ekstrak ke dalam Cubic
## pindah ke direktori /home
    cd /home
### disini langsung drag and drop saja melalui file explorer (nemo), setelah itu klik `copy` di kanan atas Cubic

# Instal Plymouth
## Memindahkan folder plymouth ke folder global
    sudo cp -r logo-mac-style /usr/share/plymouth/themes
## Menginstal plymouth di sistem
    sudo update-alternatives --install /usr/share/plymouth/themes/default.plymouth default.plymouth /usr/share/plymouth/themes/logo-mac-style/logo-mac-style.plymouth 110
## Memilih sebagai plymouth default
    update-alternatives --config default.plymouth
## Update initramfs
    update-initramfs -u -k all

# Memasang Background (wallpaper dan login)
## Mmeindahkan file background ke folder global
    cp desktop-bg.jpg /usr/share/backgrounds/linuxmint
    cd /usr/share/backgrounds/linuxmint
## Overwrite `default_background.jpg` dengan `desktop-bg.jpg`
    mv desktop-bg.jpg default_background.jpg
## Ubah permission agar bisa dibaca sistem
    chmod 644 default_background.jpg
    
# Mmngubah Logo pada Panel
## Mmeindahkan file logo ke folder global
    cp netara.svg /usr/share/icons/hicolor/scalable/apps
    cd /usr/share/icons/hicolor/scalable/apps
## Overwrite `linuxmint-logo-ring-symbolic.svg` dengan `netara.svg`
    mv netara.svg linuxmint-logo-ring-symbolic.svg
## Ubah permission agar bisa dibaca sistem
    chmod 644 linuxmint-logo-ring-symbolic.svg

# Instal tema
## pindah direktori
    cd /home/Netara-OS-main/WhiteSur-gtk-theme
## Instal tema `WhiteSur`
    ./install.sh -d /usr/share/themes -c dark -s nord

# Instal Icons
## pindah direktori
    cd /home/Netara-OS-main/Tela-circle-icon-theme-master
## Instal Icon `Tela`
    ./install.sh -d /usr/share/icons -c nord

# Firstboot (Apply tema dan skrip otomatisasi)       
## buat folder `autostart`
    mkdir -p /etc/skel/.config/autostart
## Menghapus Mint Welcome
    sudo apt remove --purge mintwelcome -y
    sudo rm -f /etc/skel/.config/autostart/mintwelcome.desktop

## Membuat file `netara.desktop` untuk eksekusi firstboot
    nano /etc/skel/.config/autostart/netara.desktop
### diisi seperti ini
    [Desktop Entry]
    Type=Application
    Name=Netara Setup
    Exec=/usr/local/bin/netara-theme.sh
    Hidden=false
    X-GNOME-Autostart-enabled=true
    Terminal=false
    NoDisplay=true

## Membuat file `netara-theme.sh` untuk Apply tema
    nano /usr/local/bin/netara-theme.sh

### diisi seperti ini
    #!/bin/bash
    # Netara Theme - Hanya apply tema
    
    sleep 3
    
    # Apply tema
    gsettings set org.cinnamon.theme name 'WhiteSur-Dark-nord'
    gsettings set org.cinnamon.desktop.wm.preferences 'WhiteSur-Dark-nord'
    gsettings set org.cinnamon.desktop.interface gtk-theme 'WhiteSur-Dark-nord'
    gsettings set org.cinnamon.desktop.interface icon-theme 'Tela-circle-nord'
    gsettings set org.cinnamon.desktop.interface cursor-theme 'DMZ-Black'
    gsettings set org.gnome.desktop.interface cursor-theme 'DMZ-Black'
    
    gsettings set org.cinnamon panels-enabled "['1:0:top']"
    gsettings set org.cinnamon panels-height "['1:29']"
    
    gsettings set org.cinnamon enabled-applets "['panel1:left:0:menu@cinnamon.org',
    'panel1:left:1:grouped-window-list@cinnamon.org', 'panel1:center:0:calendar@cinnamon.org',
    'panel1:right:0:systray@cinnamon.org', 'panel1:right:1:xapp-status@cinnamon.org',
    'panel1:right:2:notifications@cinnamon.org', 'panel1:right:3:printers@cinnamon.org',
    'panel1:right:4:removable-drives@cinnamon.org', 'panel1:right:5:keyboard@cinnamon.org',
    'panel1:right:6:favorites@cinnamon.org', 'panel1:right:7:network@cinnamon.org',
    'panel1:right:8:sound@cinnamon.org', 'panel1:right:9:power@cinnamon.org']"
    
    # Hapus .desktop file (netara.desktop)
    rm -f "$HOME/.config/autostart/netara.desktop"
    
    # === DETEKSI MODE ===
    # Jika LIVE OS -> exit (tidak jalankan firstboot)
    if [ "$USER" = "mint" ] || mount | grep -q "/cdrom" || [ -d /lib/live/mount ]; then
        echo "Live OS detected - Skipping firstboot"
        exit 0
    fi
    
    # Jika INSTALLED SYSTEM -> jalankan firstboot di terminal yang kelihatan
    echo "Installed system - Launching firstboot..."
    sleep 2
    
    # Jalankan firstboot di terminal terpisah
    if command -v gnome-terminal >/dev/null; then
        gnome-terminal --title="Netara First Boot" -- bash -c "/usr/local/bin/netara-firstboot.sh"
    elif command -v x-terminal-emulator >/dev/null; then
        x-terminal-emulator -title "Netara First Boot" -e "bash -c '/usr/local/bin/netara-firstboot.sh'"
    else
        # Fallback: jalankan langsung (tapi mungkin gak keliatan terminal)
        /usr/local/bin/netara-firstboot.sh
    fi
    
    exit 0

### Ubah permission `+x` agar bisa dieksekusi
    chmod +x /usr/local/bin/netara-theme.sh

## Buat file `netara-firstboot.sh` untuk skrip otomatisasi
    nano /usr/local/bin/netara-firstboot.sh

### diisi seperti ini
    #!/bin/bash
    # Netara OS - First Boot Setup (Install Only)
    
    # Fungsi tampilkan banner
    show_banner() {
        clear
        echo ""
        echo "╔═══════════════════════════════════════════════════════════════╗"
        echo "║                                                               ║"
        echo "║    ███╗   ██╗███████╗████████╗ █████╗ ██████╗  █████╗         ║"
        echo "║    ████╗  ██║██╔════╝╚══██╔══╝██╔══██╗██╔══██╗██╔══██╗        ║"
        echo "║    ██╔██╗ ██║█████╗     ██║   ███████║██████╔╝███████║        ║"
        echo "║    ██║╚██╗██║██╔══╝     ██║   ██╔══██║██╔══██╗██╔══██║        ║"
        echo "║    ██║ ╚████║███████╗   ██║   ██║  ██║██║  ██║██║  ██║        ║"
        echo "║    ╚═╝  ╚═══╝╚══════╝   ╚═╝   ╚═╝  ╚═╝╚═╝  ╚═╝╚═╝  ╚═╝        ║"
        echo "║                                                               ║"
        echo "║            Network Security Operating System                  ║"
        echo "║                                                               ║"
        echo "╚═══════════════════════════════════════════════════════════════╝"
        echo ""
        echo "                     First Boot Setup"
        echo "════════════════════════════════════════════════════════════════"
        echo ""
    }
    
    # Fungsi tampilkan ALL installed software di terminal
    show_installed_software() {
        echo "┌─────────────────────────────────────────────────────────────┐"
        echo "│           NETARA OS - INSTALLED SECURITY TOOLS              │"
        echo "├─────────────────────────────────────────────────────────────┤"
    
    # Network Analysis
    echo "│ 🔍 NETWORK ANALYSIS:"
    check_and_echo "Wireshark" "wireshark"
    check_and_echo "Nmap" "nmap"
    check_and_echo "Ncat" "ncat"
    check_and_echo "TCPDump" "tcpdump"
    
    # Wireless Security
    echo "│"
    echo "│ 📶 WIRELESS SECURITY:"
    check_and_echo "Aircrack-ng" "aircrack-ng"
    check_and_echo "Bettercap" "bettercap"
    
    # Packet Crafting
    echo "│"
    echo "│ 🛠️  PACKET CRAFTING:"
    check_and_echo "Scapy" "python3-scapy"
    check_and_echo "Hping3" "hping3"
    
    # Intrusion Detection
    echo "│"
    echo "│ 🛡️  INTRUSION DETECTION:"
    check_and_echo "Snort" "snort"
    
    # Python Tools
    echo "│"
    echo "│ 🐍 PYTHON TOOLS:"
    check_and_echo "Impacket" "python3-impacket"
    
    # Web Security
    echo "│"
    echo "│ 🌐 WEB SECURITY:"
    check_and_echo "Nikto" "nikto"
    
    # Network Monitoring 
    echo "│"
    echo "│ 📊 NETWORK MONITORING:"
    check_and_echo "Iftop" "iftop"
    check_and_echo "Bmon" "bmon"          
    
    # Security Audit (BARU)
    echo "│"
    echo "│ 🔐 SECURITY AUDIT:"
    check_and_echo "Lynis" "lynis"         
    
    echo "└─────────────────────────────────────────────────────────────┘"
    echo ""
    }
    
    # Fungsi helper check tool dan echo
    check_and_echo() {
        name="$1"
        pkg="$2"
    
    if dpkg -l | grep -q "^ii.*$pkg"; then
        echo "│   ✓ $name"
    else
        echo "│   ✗ $name"
    fi
    }

    # Fungsi tampilkan quick commands untuk SEMUA tools
    show_quick_commands() {
        echo "┌─────────────────────────────────────────────────────────────┐"
        echo "│                QUICK COMMANDS - ALL TOOLS                   │"
        echo "├─────────────────────────────────────────────────────────────┤"
    
    echo "│ 🔍 NETWORK ANALYSIS:"
    echo "│   \$ sudo wireshark"
    echo "│   \$ sudo nmap -sV 192.168.1.0/24"
    echo "│   \$ sudo tcpdump -i eth0 -w capture.pcap"
    
    echo "│"
    echo "│ 📶 WIRELESS SECURITY:"
    echo "│   \$ sudo airmon-ng start wlan0"
    echo "│   \$ sudo airodump-ng wlan0mon"
    echo "│   \$ sudo bettercap -iface wlan0mon"
    
    echo "│"
    echo "│ 🛠️  PACKET CRAFTING:"
    echo "│   \$ sudo hping3 -S target -p 80 -c 5"
    echo "│   \$ python3 -c \"from scapy.all import *; send(IP(dst='target')/ICMP())\""
    
    echo "│"
    echo "│ 🛡️  INTRUSION DETECTION:"
    echo "│   \$ sudo snort -A console -q -c /etc/snort/snort.conf -i eth0"
    
    echo "│"
    echo "│ 🌐 WEB SECURITY:"
    echo "│   \$ nikto -h http://target.com"
    echo "│   \$ nikto -h https://target.com -ssl"
    
    echo "│"
    echo "│ 📊 NETWORK MONITORING:"
    echo "│   \$ sudo iftop -i eth0"
    echo "│   \$ bmon -p eth0"                   
    echo "│   \$ bmon -r 2"                      
    
    echo "│"
    echo "│ 🔐 SECURITY AUDIT:"
    echo "│   \$ sudo lynis audit system"        
    echo "│   \$ sudo lynis audit system --quick"
    echo "│   \$ lynis show version"
    
    echo "│"
    echo "│ 🐍 PYTHON TOOLS:"
    echo "│   \$ python3 -m impacket.examples.smbclient"
    echo "│   \$ python3 -m impacket.examples.secretsdump"
    
    echo "└─────────────────────────────────────────────────────────────┘"
    echo ""
    }
    
    # ===== MAIN EXECUTION =====
    
    # Tampilkan banner
    show_banner
    sleep 1
    
    # LOG FILE
    LOG="$HOME/netara-firstboot.log"
    echo "=== Netara OS First Boot - $(date) ===" > $LOG
    echo "" >> $LOG
    
    # 1. UPDATE SYSTEM
    
    # ===== TANYA APT UPGRADE =====
    echo "════════════════════════════════════════════════════════════════"
    echo "               SYSTEM UPDATE CHECK"
    echo "════════════════════════════════════════════════════════════════"
    echo ""
    echo "Do you want to update system packages now? [Y/n]"
    echo "(Recommended for security updates)"
    read -p "Your choice: " -n 1 -r
    echo ""
    
    if [[ $REPLY =~ ^[Yy]$ ]] || [[ -z "$REPLY" ]]; then
        echo "Starting system update..."
        echo "System update: STARTED" >> $LOG
    
    echo "┌─────────────────────────────────────────────────────────────┐" | tee -a $LOG
    echo "│                     SYSTEM UPDATE                           │" | tee -a $LOG
    echo "├─────────────────────────────────────────────────────────────┤" | tee -a $LOG
    echo "│ Updating package database..." | tee -a $LOG
    sudo apt update >> $LOG 2>&1
    echo "│ Upgrading installed packages..." | tee -a $LOG
    sudo apt upgrade -y >> $LOG 2>&1
    echo "│ Cleaning up..." | tee -a $LOG
    sudo apt autoremove -y >> $LOG 2>&1
    echo "│ ✓ System updated successfully!" | tee -a $LOG
    echo "└─────────────────────────────────────────────────────────────┘" | tee -a $LOG
    echo "" | tee -a $LOG
    
    UPDATE_DONE=true
    echo "System update: COMPLETED" >> $LOG
    else
        echo "⚠️  System update skipped."
        echo "You can update later with: sudo apt update && sudo apt upgrade"
        echo ""
    
    echo "System update: SKIPPED by user" >> $LOG
    UPDATE_DONE=false
    fi
    
    echo "" >> $LOG
    sleep 1
    
    echo "🔧 Checking kernel security configuration..."
    
    REQUIRED_PARAMS=("pti=on" "spectre_v2=retpoline" "slab_nomerge")
    CURRENT_PARAMS=$(cat /proc/cmdline)
    
    MISSING=()
    for param in "${REQUIRED_PARAMS[@]}"; do
        if [[ ! "$CURRENT_PARAMS" =~ $param ]]; then
            MISSING+=("$param")
        fi
    done
    
    if [ ${#MISSING[@]} -gt 0 ]; then
        echo "⚠️  Missing kernel parameters: ${MISSING[*]}"
        echo "Applying Netara OS security configuration..."
    
    # Backup dengan timestamp
    BACKUP_FILE="/etc/default/grub.backup.$(date +%Y%m%d_%H%M%S)"
    sudo cp /etc/default/grub "$BACKUP_FILE"
    echo "Backup created: $BACKUP_FILE"
    
    # Fix grub config dengan logging
    echo "Updating GRUB configuration..."
    
    # Log file untuk update-grub
    GRUB_LOG="/tmp/netara-grub-update.log"
    
    # Update GRUB config
    sudo sed -i 's|GRUB_CMDLINE_LINUX_DEFAULT=".*"|GRUB_CMDLINE_LINUX_DEFAULT="quiet splash pti=on spectre_v2=retpoline slab_nomerge"|' /etc/default/grub
    
    # Update GRUB dengan logging
    echo "Running update-grub (log: $GRUB_LOG)..."
    sudo update-grub > "$GRUB_LOG" 2>&1
    
    # Cek apakah sukses
    if [ $? -eq 0 ]; then
        echo "✅ Security parameters applied"
        echo "   Log: $GRUB_LOG"
        echo "🔄 Reboot required: sudo reboot"
        
        # Show summary dari log
        echo ""
        echo "Update summary:"
        grep -E "(Generating|Found|Adding|done)" "$GRUB_LOG" | tail -5
    else
        echo "❌ Failed to update GRUB"
        echo "   Check log: $GRUB_LOG"
        echo "   Restoring backup..."
        sudo cp "$BACKUP_FILE" /etc/default/grub
    fi
    else
        echo "✅ All security parameters active"
    fi
    sleep 1

    # Tampilkan installed software (SEMUA tools termasuk baru)
    show_installed_software
    sleep 2
    
    # 2. Tampilkan quick commands untuk SEMUA tools
    show_quick_commands
    sleep 2
    
    # 3. BUAT FILE PANDUAN DI DESKTOP
    echo "┌─────────────────────────────────────────────────────────────┐"
    echo "│         CREATING DESKTOP GUIDE                              │"
    echo "└─────────────────────────────────────────────────────────────┘"
    echo ""
    
    GUIDE_FILE="$HOME/Desktop/Netara-Tools-Guide.txt"
    
    cat > "$GUIDE_FILE" << 'EOF'
    ╔═══════════════════════════════════════════════════════════════╗
    ║                NETARA OS - TOOLS GUIDE                       ║
    ║                Quick Reference Manual                        ║
    ╚═══════════════════════════════════════════════════════════════╝
    
    📅 Generated on: $(date)
    
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    🔧 INSTALLED SECURITY TOOLS
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    
    • NETWORK ANALYSIS:
      └─ Nmap, Wireshark, Ncat, TCPdump
    
    • WIRELESS SECURITY:
      └─ Aircrack-ng, Bettercap
    
    • PACKET CRAFTING:
      └─ Scapy, Hping3
    
    • INTRUSION DETECTION:
      └─ Snort
    
    • WEB SECURITY:
      └─ Nikto
    
    • NETWORK MONITORING:
      └─ Iftop, Bmon
    
    • SECURITY AUDIT:
      └─ Lynis
    
    • PYTHON TOOLS:
      └─ Impacket
    
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    🚀 QUICK COMMANDS
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    
    🔍 NETWORK SCANNING:
      sudo nmap -sV 192.168.1.0/24
      sudo nmap -A -T4 target.com
      sudo wireshark
      sudo tcpdump -i eth0 -w capture.pcap
    
    📶 WIRELESS TESTING:
      sudo airmon-ng start wlan0
      sudo airodump-ng wlan0mon
      sudo bettercap -iface wlan0mon
    
    🌐 WEB SECURITY:
      nikto -h http://target.com
      nikto -h https://target.com -ssl
    
    🛡️ SECURITY AUDIT:
      sudo lynis audit system
      sudo lynis audit system --quick
    
    🐍 PYTHON TOOLS:
      python3 -c "from scapy.all import *; send(IP(dst='target')/ICMP())"
      python3 -m impacket.examples.smbclient
    
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    📖 USEFUL TIPS
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    
    1. Always use 'sudo' for network operations
    2. Save scan results for documentation:
       sudo nmap -sV target.com -oN scan_results.txt
    3. Check tool versions:
       nmap --version
       wireshark --version
    4. Update tools regularly:
       sudo apt update && sudo apt upgrade
    5. Find configuration files in /etc/
       - Snort: /etc/snort/snort.conf
       - Lynis: /etc/lynis/default.prf
    
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    📞 SUPPORT & RESOURCES
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    
    • GitHub: https://github.com/zktkcla/Netara-OS
    • Documentation: Check /usr/share/doc/ for each tool
    • Man pages: man nmap, man wireshark, etc.
    
    ⚠️  DISCLAIMER: Use these tools only on systems you own
       or have explicit permission to test.
    
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    🎯 Happy Ethical Hacking!
       - Netara OS Development Team
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    EOF
    
    # Set permissions
    chmod 644 "$GUIDE_FILE"
    
    echo "✅ Created: $GUIDE_FILE"
    echo "📁 Location: Desktop/Netara-Tools-Guide.txt"
    echo ""
    
    # Log ke file
    echo "Desktop guide created: $GUIDE_FILE" >> $LOG
    
    sleep 2
    
    # 4. CLEANUP
    echo "Cleaning up autostart entry..." | tee -a $LOG
    rm -f "$HOME/.config/autostart/netara.desktop"
    
    # 5. NOTIFICATION
    if command -v notify-send >/dev/null 2>&1; then
        notify-send -i security-high \
            "Netara OS Ready" \
            -t 6000
    fi
    
    # 6. FINAL MESSAGE
    echo ""
    echo "════════════════════════════════════════════════════════════════"
    echo "                  NETARA OS - SETUP COMPLETE!                   "
    echo "════════════════════════════════════════════════════════════════"
    echo ""
    echo "✅ 10+ Security Tools Installed:"
    echo "   • Network Analysis: wireshark, nmap, tcpdump"
    echo "   • Wireless: aircrack-ng, bettercap"
    echo "   • Packet Crafting: scapy, hping3"
    echo "   • Intrusion Detection: snort"
    echo "   • Web Security: nikto"
    echo "   • Network Monitoring: iftop, bmon"
    echo "   • Security Audit: lynis"
    echo "   • Python Tools: impacket"
    echo ""
    echo "📋 Log file: $LOG"
    echo "🖥️  System ready for Network Security & Threat Analysis"
    echo ""
    echo "════════════════════════════════════════════════════════════════"
    echo "        Happy Ethical Hacking! - Netara OS Development Team"
    echo "════════════════════════════════════════════════════════════════"
    echo ""
    echo ""
    echo "This terminal will close in 15 seconds..."
    echo "Or press Ctrl+C to close now."
    
    # Tunggu 15 detik
    sleep 15
    
    exit 0
### ubah permission menjadi `+x` untuk `netara-firstboot.sh`
    chmod +x /usr/local/bin/netara-firstboot.sh
### ubah permission menjadi `644` untuk `netara.desktop`
    chmod 644 /etc/skel/.config/autostart/netara.desktop

## Kursor
### Buat direktori `default`
    mkdir -p /etc/skel/.icons/default
#### Buat file `index.theme`
    nano /etc/skel/.icons/default/index.theme
#### Diisi seperti ini
    [Icon Theme]
    Inherits=DMZ-Black

### Buat direktori `gtk-3.0`
    mkdir -p /etc/skel/.config/gtk-3.0
#### Buat file `settings.ini`
    nano /etc/skel/.config/gtk-3.0/settings.ini
#### Diisi seperti ini    
    [Settings]
    gtk-cursor-theme-name=DMZ-Black
    gtk-cursor-theme-size=24

### Buat file `.Xresources`
    nano /etc/skel/.Xresources
#### Diisi seperti ini
    Xcursor.theme: DMZ-Black
    Xcursor.size: 24

### Buat file `slick-greeter.conf`
    nano /etc/lightdm/slick-greeter.conf
#### Diisi seperti ini
    [Greeter]
    cursor-theme-name=DMZ-Black
    cursor-theme-size=24

# Slideshow Instalasi Netara OS
## Pindah ke direktori /ubiq 
    cd /home/Netara-OS-main/ubiq
## Copy file ke `/usr/share/ubiquity-slideshow/slides/screenshots`
    cp helpNe.png softwareNe.png webNe.png welcomeNe.png /usr/share/ubiquity-slideshow/slides/screenshots
## Copy file ke `/usr/share/ubiquity-slideshow/slides/icons`
    cp nmap.png scapy.png wireshark.png /usr/share/ubiquity-slideshow/slides/icons
## Pindah direktori `/slides`
    cd /usr/share/ubiquity-slideshow/slides/
## Hapus 4 file ini
    rm office.html customize.html photos.html windows.html
# Pengeditan .html
`welcome.html`, `software.html` (jadi aplikasi yang diinstal), `web.html` (ilangin netflix sama youtube), `help.html`, `chat.html` ubah kata `Linux Mint` menjadi `Netara OS`

## Teks welcome: 
    Welcome and thank you for choosing Netara OS. This slideshow will show you around while the system is being installed on your computer.
    
## Teks software (installed app):
    Netara OS includes Nmap for network discovery and vulnerability scanning, Wireshark for deep packet analysis and traffic inspection, and Scapy for custom packet crafting and network testing. Complete toolkit for penetration testing and security research.

## Teks help:
    If you have questions, encounter issues, or want to contribute to Netara OS development, visit our GitHub repository. Netara OS is an open-source security distribution built for penetration testers and security researchers. Find documentation, report bugs, request features, or join the development at: https://github.com/zktkcla/Netara-OS

# modifikasi background pilihan awal live os, dilakukan diluar cubic, lewat terminal host

## Ke direktori /Netara-OS-main
## Copy `nah.png` ke `/isolinux`
    cp nah.png ~/Downloads/remaster/custom-disk/isolinux
## Pindah ke direktori `/isolinux`
    cd ~/Downloads/remaster/custom-disk/isolinux
## Overwirte `splash.png` dengan  `nah.png`
    mv nah.png splash.png
## Ubah permission menjadi `644` untuk `splash.png`
    sudo chmod 644 splash.png

# Ubah `/etc/os-release`
    nano /etc/os-release
`HANYA ubah NAME, VERSION, DAN PRETTY-NAME`

# Ubah konfigurasi grub
    nano /etc/default/grub
## Tambah kernel boot parameter
    pti=on spectre_v2=retpoline slab_nomerge

# Saat berada di grub.cfg dan live.cfg cubic
    ubah `Linux Mint` Mmenjadi `Netara OS`

