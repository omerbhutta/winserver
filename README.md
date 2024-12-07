# Set Up a Free Windows Server with RDP 
After extensive research, I discovered reverse tunneling tools that can help in this situation. One such tool is "NGROK".

### What is NGROK? 
> Ngrok is a multiplatform tunnelling, reverse proxy software that establishes secure tunnels from a public endpoint such as internet to a locally running network service while capturing all traffic for detailed inspection and replay.
>Enter ngrok, a very cool, lightweight tool that creates a secure tunnel on your local machine along with a public URL you can use for browsing your local site. When ngrok is running, it listens on the same port that you're local web server is running on and proxies external requests to your local machine.

However, there are few limitations in NGROK as well. The biggest one is that its not completely free. And if you're using the free version of NGROK, the tunnel link expires after every 8 hours, and you'll have to restart the NGROK service again manually, and again the tunnel link will change. 

This repository provides a step-by-step guide to obtaining a free Windows Server with RDP using GitHub Jobs and an NGROK tunnel. Follow the instructions to set up a free Windows VPS server through GitHub and access it via Remote Desktop Connection.

## Steps To Create Windows Server
* Sign Up a GitHub Account : https://github.com/
* Create a Private Repository, Go to repository settings > Secrets and variables > Actions > New Repository Secrets
* Sign Up a Ngrok Account : https://ngrok.com/
* Copy the auth key from ngrok and add to github repository secrets
* Setup New Workflow Manually the Put the following code in main.yml and commit changes 
```
name: CI

on: [push, workflow_dispatch]

jobs:
  build:

    runs-on: windows-latest

    steps:
    - name: Download
      run: Invoke-WebRequest https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-windows-amd64.zip -OutFile ngrok.zip
    - name: Extract
      run: Expand-Archive ngrok.zip
    - name: Auth
      run: .\ngrok\ngrok.exe authtoken $Env:NGROK_AUTH_TOKEN
      env:
        NGROK_AUTH_TOKEN: ${{ secrets.NGROK_AUTH_TOKEN }}
    - name: Enable TS
      run: Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server'-name "fDenyTSConnections" -Value 0
    - run: Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
    - run: Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1
    - run: Set-LocalUser -Name "runneradmin" -Password (ConvertTo-SecureString -AsPlainText "Coff33Blue$" -Force)
    - name: Create Tunnel
      run: .\ngrok\ngrok.exe tcp 3389

```
* Run The WorkFlow and take note of credentials (runneradmin:Coff33Blue$)
* Get the ngrok endpoint url and use it as ip or address in Remote Desktop Connection with credentials

If you have a better solution, your contributions are welcomed. :)
