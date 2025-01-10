# project
import os
import socket
import requests
from bs4 import BeautifulSoup

def banner():
    print("""
    ========================================
    Little Brother - Information Gathering Tool
    ========================================
    Running on Kali Linux
    """)

def menu():
    print("""
    1. User Lookup
    2. IP Address Analysis
    3. Website Information
    4. Exit
    """)

def user_lookup():
    username = input("Enter username to search: ")
    platforms = {
        "Twitter": f"https://twitter.com/{username}",
        "GitHub": f"https://github.com/{username}",
        "Instagram": f"https://www.instagram.com/{username}",
    }

    for platform, url in platforms.items():
        print(f"Checking {platform}...")
        try:
            response = requests.get(url, timeout=5)
            if response.status_code == 200:
                print(f"[+] Found: {url}")
            else:
                print(f"[-] Not found on {platform}")
        except requests.RequestException:
            print(f"[-] Error connecting to {platform}")

def ip_analysis():
    ip = input("Enter IP address to analyze: ")
    try:
        response = requests.get(f"https://ipinfo.io/{ip}/json")
        if response.status_code == 200:
            data = response.json()
            print(f"IP: {data.get('ip')}")
            print(f"City: {data.get('city')}")
            print(f"Region: {data.get('region')}")
            print(f"Country: {data.get('country')}")
            print(f"Organization: {data.get('org')}")
            print(f"Location: {data.get('loc')}")
        else:
            print("[-] Unable to fetch IP information")
    except requests.RequestException:
        print("[-] Error connecting to IP information service")

def website_info():
    url = input("Enter website URL (e.g., https://example.com): ")
    try:
        response = requests.get(url, timeout=5)
        if response.status_code == 200:
            print(f"[+] Website: {url}")
            print(f"Status Code: {response.status_code}")
            print(f"Server: {response.headers.get('Server')}")
            soup = BeautifulSoup(response.text, 'html.parser')
            title = soup.find('title').text if soup.find('title') else "No title found"
            print(f"Title: {title}")
        else:
            print(f"[-] Unable to access website: {url}")
    except requests.RequestException:
        print("[-] Error connecting to the website")

def main():
    while True:
        banner()
        menu()
        choice = input("Select an option: ")

        if choice == "1":
            user_lookup()
        elif choice == "2":
            ip_analysis()
        elif choice == "3":
            website_info()
        elif choice == "4":
            print("Exiting Little Brother. Goodbye!")
            break
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    if os.name != "posix":
        print("This tool is designed to run on Linux systems.")
        exit(1)
    main()
