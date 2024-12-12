# VNC STunnel setup
Scripts to setup an stunnel for encrypted vnc connections (or other applications)

VNC is a tool for remote desktop access, but it typically lacks built-in encryption after the password authentication.

The `vnc-server-setup` script creates an stunnel on the server side, which acts as a middleman and accepts remote connections with encryption, it then forwards the connection to the local vnc server.

The `vnc-client-setup` script creates an stunnel client on the connecting server which forwards connections to `localhost:5900` to the remote stunnel server with encryption.

# VNC Encrypted Tunnel Setup
These scripts create a secure tunnel for connecting to your VNC server instead of directly using the VNC port. 
## Assumptions
- VNC is running on port `5900`.
## Setup on the Server Hosting VNC
  1. **Make the Server Setup Script Executable** ```bash sudo chmod +x ./vnc-server-setup ```
  2. **Run the Server Setup Script** ```bash sudo ./vnc-server-setup -server localhost -remoteport 5901 -localport 5900 ```
**Parameters:**
 - `-server localhost`: Specifies that the vnc server is running on `localhost`.
 - `-remoteport 5901`: The port on which the server will accept encrypted connections.
 - `-localport 5900`: The local VNC port to which the traffic will be forwarded.

> **Note:** Ensure that the `vnc-server-setup` script is located in the current directory. This command sets up a server that accepts encrypted connections on port `5901` and forwards them to the VNC server running on port `5900`.

## Setup on the Client Machine
  1. **Make the Client Setup Script Executable** ```bash sudo chmod +x ./vnc-client-setup ```
  2. **Run the Client Setup Script** ```bash sudo ./vnc-client-setup -server <server-ip> -remoteport 5901 -localport 5902 ```
**Parameters:**
 - `-server <server-ip>`: Replace `<server-ip>` with the IP address of your VNC server.
 - `-remoteport 5901`: The port on the server to connect to (should match the server's `-remoteport`).
 - `-localport 5900`: The local port on the client machine to forward the connection.

## Connecting with a VNC Client
After setting up the tunnel: 
1. Open your preferred VNC client. 
2. Connect to `localhost:5900` instead of `<server-ip>:5900`.
This connection will be securely tunneled to the VNC server running on the remote machine. 
## Summary
 - **Server Side:**
 - Runs `vnc-server-setup` to accept encrypted connections on `5901` and forward them to `5900`.
 - **Client Side:**
 - Runs `vnc-client-setup` to forward `localhost:5902` to the server's `5901`.
 - **VNC Client:** - Connects to `localhost:5900` to securely access the VNC server.

## Prerequisites
 - Ensure that both `vnc-server-setup` and `vnc-client-setup` scripts are present in their respective directories and have executable permissions.
 - Replace `<server-ip>` with the actual IP address of your VNC server.
 - Make sure that the necessary ports (`5900`, `5901`) are open and not blocked by firewalls on both the server and client machines.

## Notes
 - Closing the old port `5900` on the vnc server ensures you do not accidentally connect to the unencrypted port.
 - A vnc client may still warn you that the connection is unencrypted. You may ignore this message as the vnc client is unaware of the stunnel setup.
 - The `vnc-server-setup` creates a new service `stunnel-vnc-client.service` and overwrites `/etc/stunnel/x11vnc.conf` if it exists.
 - The `vnc-client-setup` creates a new service if it does not exist `stunnel-vnc-server.service` and creates or appends to `/etc/stunnel/x11vnc.conf`. So you can add multiple remote servers to your client by running the script again.*
> *Running the client setup with the same aruguments adds duplicate entries in `/etc/stunnel/vnc-client.conf`. You can edit the file manually if any issues occur or you want to modify an entry.
## Additional Information
For more advanced configurations or troubleshooting, refer to the documentation of the scripts or the VNC server/client you are using.

# Contributing
Contributions are welcome! Please open an issue or submit a pull request for any improvements or bug fixes.
# Contact
For any questions or support, please open an issue on this repository.

*This README was generated with the help of ChatGPT.*
