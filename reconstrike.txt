#!/usr/bin/env python3

import os
import subprocess
import time

def clear_screen():
    os.system('clear')

def banner():
    print("="*50)
    print("     ReconStrike - Offensive Recon Toolkit")
    print("="*50)

def main_menu():
    print("\n[1] LAN Scanner (Nmap)")
    print("[2] Wi-Fi Scanner (airmon-ng + airodump-ng)")
    print("[3] Exit\n")
    return input("Select an option: ")

def lan_scanner():
    target = input("\nEnter target IP or subnet (e.g., 192.168.1.0/24): ")
    print(f"\n[+] Running Nmap on {target}...\n")
    try:
        subprocess.run(["nmap", "-sS", "-T4", target])
    except Exception as e:
        print(f"[!] Error: {e}")

def wifi_scanner():
    try:
        print("\n[+] Listing wireless interfaces...\n")
        subprocess.run(["airmon-ng"])
        iface = input("\nEnter interface to use (e.g., wlan0): ")

        print(f"\n[+] Enabling monitor mode on {iface}...")
        subprocess.run(["airmon-ng", "start", iface])
        mon_iface = iface + "mon"

        print(f"\n[+] Starting Wi-Fi scan on {mon_iface} (press Ctrl+C to stop)...\n")
        time.sleep(2)
        subprocess.run(["airodump-ng", mon_iface])

        print(f"\n[+] Stopping monitor mode on {iface}...")
        subprocess.run(["airmon-ng", "stop", mon_iface])

    except KeyboardInterrupt:
        print("\n[!] Scan interrupted by user.")
        subprocess.run(["airmon-ng", "stop", mon_iface])
    except Exception as e:
        print(f"[!] Error: {e}")

def main():
    while True:
        clear_screen()
        banner()
        choice = main_menu()

        if choice == "1":
            lan_scanner()
        elif choice == "2":
            wifi_scanner()
        elif choice == "3":
            print("\n[+] Exiting ReconStrike.")
            break
        else:
            print("[!] Invalid choice. Try again.")
        input("\nPress Enter to return to menu...")

if __name__ == "__main__":
    main()
