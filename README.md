# Cloudflare Tunnel  (No Port Forwarding)

**Port forwarding** is the traditional way to share a local app over the internet. You set a rule on your router that opens a port and sends incoming public traffic straight to a specific device’s local IP address.

It works, but it exposes your home or network IP address to the open web. Modern tools like **Cloudflare Tunnel** skip this completely by making an outgoing connection instead, so you don't have to open any ports at all. 

---
### Prerequisites

*  A system with Docker installed.
*  Traefik

---
### Download docker-compose.yml

```
git clone https://github.com/TejaNune1998/cloudflared_tunnel.git
```

---
### Directory Structure

```
/opt/docker_files/cloudflared/
|
└── docker-compose.yml
```
---
## Deploy Cloudflared Tunnel

### Step 1: Create and Initialize the Tunnel

1. **Create Tunnel in Dashboard:**
    - Open the **Cloudflare dashboard** and search for  **Tunnels**.
    - Click **Create a Tunnel** and enter a name (e.g., `miniverse`).

2. **Retrieve Connector Token:**
    - In the setup screen, select **Docker** as the Operating System environment.
    - Copy the generated `TUNNEL_TOKEN` from the provided Docker command.

3. **Deploy Container:**
    - Paste the token into local `docker-compose.yml` file for the `cloudflared` service.
    - Ensure `cloudflared` is attached to **Traefik** network (e.g., `proxy`).
    - Start the container (`docker compose up -d`) and verify that the tunnel status displays **Connected** in Cloudflare.
    
4. **Configure Public Hostname Route:**
    - Return to **Tunnels**, select newly tunnel, and navigate to the **Route** tab.
    - Click **Add Route** , select **Published Applications** and update as below 

        - **Subdomain:** `*`  
        - **Domain:** Choose your respective `yourdomain.com`
        - **Path:** _(Leave empty)_
        - **Service URL:** `https://traefik`
    
	    - Expand **Additional Application Settings**:
	        - Under **TLS**: 
		        - update `Origin Server Name` with for which domain your traefik container is obtaining certficate for. *(e.g *.yourdomain.com.co)* 
		
5. **Map Root DNS Record:**
    
    - Copy the **Tunnel ID** from **Tunnel details**.
    - Go to **Cloudflare Dashboard** -->**DNS** --> **Records** and click **Add Record**:
        - **Type:** `CNAME`
        - **Name:** `*`
        - **Target:** `<TUNNEL_UUID>.cfargotunnel.com`
        - **Proxy Status:** **Proxied** (Orange Cloud)

### Step 2: Expose Internal Services via Cloudflare Tunnel

>*Note: Make sure either add application details in **traefik's config.yml** file or use **traefik labels**.*

To expose an individual service (such as Hugo) through Traefik and the tunnel:

 **Add DNS CNAME Record:**
    - In **Cloudflare Dashboard** --> **DNS** --> **Records**, click **Add Record**:
        - **Type:** `CNAME`
        - **Name:** `app-name`
        - **Target:** *yourdomain.com*
        - **Proxy Status:** **Proxied** (Orange Cloud)
