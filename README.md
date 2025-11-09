<div align="center">
  
# ProtonVPN-IPs

🔒 An automatically updated list of IP addresses associated with the widely used free and privacy-focused VPN provider, ProtonVPN.

![GitHub Workflow Status](https://img.shields.io/github/actions/workflow/status/tn3w/ProtonVPN-IPs/main.yml?label=Build&style=for-the-badge)

### IPInfo Category
[IPSet](https://github.com/tn3w/IPSet) | [ProtonVPN-IPs](https://github.com/tn3w/ProtonVPN-IPs) | [TunnelBear-IPs](https://github.com/tn3w/TunnelBear-IPs)

</div>

## 📊 Data Files

The repository maintains six regularly updated data files:

1. `protonvpn_logicals.json` - Contains the raw response from ProtonVPN's API, including detailed information about all logical servers and their configurations.

2. `protonvpn_ips.json` - A JSON array containing only the unique exit IP addresses used by ProtonVPN servers. This is a simplified version of the data focusing only on the IP addresses.

3. `protonvpn_ips.txt` - A plain text file with one IP address per line, making it easy to use in scripts or other tools that expect a simple list format.

4. `protonvpn_subdomains.json` - A JSON array containing unique subdomains used by ProtonVPN servers.

5. `protonvpn_entry_ips.json` - A JSON array containing the unique entry IP addresses used by ProtonVPN servers.

6. `protonvpn_entry_ips.txt` - A plain text file with one IP address per line, making it easy to use in scripts or other tools that expect a simple list format.

## 🛠️ Usage Examples

### Checking if an IP address is a ProtonVPN IP

#### Python Example - Check Exit IP

```python
import json
import netaddr

def is_protonvpn_exit_ip(ip_to_check, json_path='protonvpn_ips.json'):
    """Check if an IP address is a ProtonVPN exit IP"""
    try:
        # Validate IP address format
        netaddr.IPAddress(ip_to_check)
        
        # Load the ProtonVPN IPs list
        with open(json_path, 'r') as f:
            protonvpn_ips = json.load(f)
            
        # Check if IP is in the list
        return ip_to_check in protonvpn_ips
    except netaddr.AddrFormatError:
        print(f"Error: {ip_to_check} is not a valid IP address")
        return False
    except Exception as e:
        print(f"Error: {e}")
        return False

# Usage example
ip = "84.247.50.181"  # Example IP address
if is_protonvpn_exit_ip(ip):
    print(f"{ip} is a ProtonVPN exit IP")
else:
    print(f"{ip} is not a ProtonVPN exit IP")
```

#### Python Example - Check Entry IP

```python
import json
import netaddr

def is_protonvpn_entry_ip(ip_to_check, json_path='protonvpn_entry_ips.json'):
    """Check if an IP address is a ProtonVPN entry IP"""
    try:
        # Validate IP address format
        netaddr.IPAddress(ip_to_check)
        
        # Load the ProtonVPN IPs list
        with open(json_path, 'r') as f:
            protonvpn_ips = json.load(f)
            
        # Check if IP is in the list
        return ip_to_check in protonvpn_ips
    except netaddr.AddrFormatError:
        print(f"Error: {ip_to_check} is not a valid IP address")
        return False
    except Exception as e:
        print(f"Error: {e}")
        return False

# Usage example
ip = "146.70.120.210"  # Example IP address
if is_protonvpn_entry_ip(ip):
    print(f"{ip} is a ProtonVPN entry IP")
else:
    print(f"{ip} is not a ProtonVPN entry IP")
```

#### Bulk IP Checking (Python)

```python
import json
import netaddr
from typing import List, Dict

def check_multiple_ips(ips_to_check: List[str]) -> Dict[str, Dict[str, bool]]:
    """Check multiple IPs against both entry and exit IP lists"""
    # Load IP lists
    try:
        with open('protonvpn_ips.json', 'r') as f:
            exit_ips = set(json.load(f))
        
        with open('protonvpn_entry_ips.json', 'r') as f:
            entry_ips = set(json.load(f))
            
        # Check each IP
        results = {}
        for ip in ips_to_check:
            try:
                netaddr.IPAddress(ip)  # Validate IP format
                results[ip] = {
                    'is_exit_ip': ip in exit_ips,
                    'is_entry_ip': ip in entry_ips
                }
            except netaddr.AddrFormatError:
                results[ip] = {'error': f"Invalid IP address format"}
                
        return results
    
    except Exception as e:
        return {'error': str(e)}

# Example usage
ips = ["146.70.120.210", "84.247.50.181", "192.168.1.1"]
results = check_multiple_ips(ips)

for ip, status in results.items():
    print(f"IP: {ip}")
    if 'error' in status:
        print(f"  Error: {status['error']}")
    else:
        print(f"  ProtonVPN Exit IP: {'Yes' if status['is_exit_ip'] else 'No'}")
        print(f"  ProtonVPN Entry IP: {'Yes' if status['is_entry_ip'] else 'No'}")
```

## 🔄 Token Authentication

The project uses Proton API authentication tokens to fetch VPN server data. It now supports automatic token refresh, which helps maintain uninterrupted access to the API even when tokens expire.

### Required Secrets

For GitHub Actions to work properly, set the following repository secrets:

- `AUTH_PM_UID`: Your Proton account UID (not changed by token refresh)
- `AUTH_TOKEN`: Initial authentication token
- `REFRESH_TOKEN`: Token used to refresh authentication credentials
- `SESSION_ID`: Your session ID
- `GH_TOKEN`: GitHub fine grained token with `Secrets: Read and Write` and `Contents: Read and Write` permissions used to update repository secrets

The workflow automatically refreshes tokens when needed and updates repository secrets accordingly.

### How to Obtain Authentication Secrets

To export ALL ProtonVPN server IPs, you need to extract authentication tokens from your ProtonVPN account. Follow these steps:

#### Quick Reference Summary

| Secret | Source | Where to Find | Required |
|--------|--------|---------------|----------|
| `AUTH_PM_UID` | ProtonVPN Browser Session | Extract from cookie name `AUTH-{uid}` or `x-pm-uid` header | ✅ Yes |
| `AUTH_TOKEN` | ProtonVPN Browser Session | Cookie value for `AUTH-{uid}` | ✅ Yes |
| `REFRESH_TOKEN` | ProtonVPN Browser Session | Cookie value for `REFRESH-{uid}` | ✅ Yes |
| `SESSION_ID` | ProtonVPN Browser Session | Cookie value for `Session-Id` | ✅ Yes |
| `GH_TOKEN` | GitHub Settings | Personal Access Token (Fine-grained) | ✅ Yes |

#### Step 1: Get ProtonVPN Authentication Tokens

1. **Open Your Browser**: Use a browser with developer tools (Chrome, Firefox, Edge, etc.)

2. **Open Developer Tools**: 
   - Press `F12` or `Ctrl+Shift+I` (Windows/Linux) or `Cmd+Option+I` (Mac)
   - Go to the **Network** tab

3. **Log in to ProtonVPN**:
   - Navigate to [account.protonvpn.com](https://account.protonvpn.com)
   - Log in with your ProtonVPN credentials
   - Make sure you have an active ProtonVPN subscription (Free or Paid)

4. **Capture the API Request**:
   - In the Network tab, filter by `XHR` or `Fetch`
   - Look for a request to `logicals` or any request to `account.protonvpn.com/api/`
   - If you don't see any requests, refresh the page or navigate to the VPN Settings section

5. **Extract the Cookies**:
   - Click on any API request to `account.protonvpn.com`
   - Go to the **Headers** section
   - Scroll down to **Request Headers**
   - Find the `Cookie` header and copy its value
   
6. **Parse the Cookie Values**:
   
   The Cookie header contains several values. Extract these:
   
   - **AUTH_PM_UID**: Look for `x-pm-uid` in the request headers, OR extract the UID from the cookie name `AUTH-{uid}`. For example, if you see `AUTH-abc123def456`, then `AUTH_PM_UID=abc123def456`
   
   - **AUTH_TOKEN**: From the Cookie header, find `AUTH-{uid}={token_value}`. Copy the `{token_value}` part. For example:
     ```
     AUTH-abc123def456=eyJhbGc...rest_of_token
     ```
     Your `AUTH_TOKEN` is `eyJhbGc...rest_of_token`
   
   - **REFRESH_TOKEN**: From the Cookie header, find `REFRESH-{uid}={refresh_token_value}`. Copy the `{refresh_token_value}` part.
   
   - **SESSION_ID**: From the Cookie header, find `Session-Id={session_value}`. Copy the `{session_value}` part.

#### Example Cookie Header Breakdown

If your Cookie header looks like this:
```
AUTH-abc123def456=eyJhbGciOiJIUzI1NiIs...; REFRESH-abc123def456=rt_xyz789abc...; Session-Id=sess_123456789...
```

Extract as follows:
- `AUTH_PM_UID` = `abc123def456`
- `AUTH_TOKEN` = `eyJhbGciOiJIUzI1NiIs...`
- `REFRESH_TOKEN` = `rt_xyz789abc...`
- `SESSION_ID` = `sess_123456789...`

#### Step 2: Create GitHub Fine-Grained Token

To allow the workflow to update secrets automatically:

1. Go to **GitHub Settings** → **Developer settings** → **Personal access tokens** → **Fine-grained tokens**
2. Click **Generate new token**
3. Configure the token:
   - **Token name**: `ProtonVPN-IPs-Secrets-Updater`
   - **Expiration**: Choose your preferred duration (90 days recommended)
   - **Repository access**: Select "Only select repositories" and choose your fork
   - **Permissions**:
     - **Repository permissions**:
       - `Contents`: Read and Write
       - `Secrets`: Read and Write
4. Click **Generate token** and copy the token value

#### Step 3: Set GitHub Repository Secrets

1. Go to your repository on GitHub
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Add each of the following secrets:
   - `AUTH_PM_UID` = Your extracted UID
   - `AUTH_TOKEN` = Your extracted auth token
   - `REFRESH_TOKEN` = Your extracted refresh token
   - `SESSION_ID` = Your extracted session ID
   - `GH_TOKEN` = Your GitHub fine-grained token

#### Step 4: Test the Workflow

1. Go to **Actions** tab in your repository
2. Select the **ProtonVPN IP Scraper** workflow
3. Click **Run workflow** → **Run workflow**
4. Wait for the workflow to complete
5. Check the results:
   - `protonvpn_ips.json` - All exit IPs
   - `protonvpn_entry_ips.json` - All entry IPs
   - `protonvpn_logicals.json` - Complete server data

### Important Notes

- **Token Expiration**: The `AUTH_TOKEN` and `SESSION_ID` expire regularly. The workflow uses `REFRESH_TOKEN` to automatically obtain new tokens, so you don't need to manually update them.
- **REFRESH_TOKEN Expiration**: If the `REFRESH_TOKEN` expires (typically after 180 days), you'll need to repeat Step 1 to obtain new tokens.
- **Account Requirements**: You need an active ProtonVPN account. Free accounts work, but paid accounts have access to more servers.
- **Security**: Never share your tokens publicly. They provide access to your ProtonVPN account.
- **All Servers**: This setup will export IPs from ALL servers your account has access to. Free accounts see fewer servers than paid accounts.

### Troubleshooting

**Issue: Workflow fails with "Failed to fetch data from ProtonVPN"**
- Solution: Your tokens may have expired. Follow Step 1 again to get fresh tokens.

**Issue: Only getting a few IPs**
- Solution: Free accounts have limited server access. Upgrade to a paid plan to see all servers.

**Issue: "Failed to refresh authentication tokens"**
- Solution: Your `REFRESH_TOKEN` may be invalid or expired. Extract fresh tokens from the browser.

**Issue: Can't find the Cookie header**
- Solution: Make sure you're logged in and looking at requests to `account.protonvpn.com`. Try navigating to different pages in the ProtonVPN dashboard to trigger API calls.

## 🔬 Technical Implementation

### Exit IP Discovery (`main.py`)

The exit IP discovery process implements authenticated API interaction with ProtonVPN's official endpoints:

1. **Authentication Flow**:
   - Implements cookie-based authentication using `AUTH-{uid}` and `Session-Id` tokens
   - Handles token refresh via `/api/auth/refresh` endpoint on token expiration
   - Extracts new tokens from HTTP response headers (`Set-Cookie`)

2. **API Integration**:
   - Fetches server data from `account.protonvpn.com/api/vpn/logicals` endpoint
   - Uses HTTP headers including `x-pm-appversion`, `x-pm-uid` for API versioning
   - Dynamically identifies latest app version from manifest.json

3. **Data Processing**:
   - Traverses the nested JSON structure (`LogicalServers[].Servers[].ExitIP`)
   - Performs deduplication using Python sets (`set()`)
   - Combines API data with local base data for enhanced coverage
   - Serializes to both JSON and plain text formats

### Entry IP Discovery (`entry_ips.py`)

The entry IP discovery process employs passive reconnaissance techniques:

1. **Subdomain Enumeration**:
   - Leverages Certificate Transparency logs via crt.sh API
   - Implements retry logic (10 attempts with 30s intervals) for API resilience
   - Filters and normalizes subdomains

2. **DNS Resolution**:
   - Uses socket library with specific address family parameters (AF_INET, AF_INET6)
   - Directly resolves both IPv4 (A records) and IPv6 (AAAA records)
   - Utilizes concurrent execution (`ThreadPoolExecutor`) for parallel DNS resolution

3. **IP Processing**:
   - Employs set data structures for efficient deduplication
   - Maintains mixed IPv4/IPv6 collections in output
   - Handles network errors gracefully with specific exception handling

## 📜 License
Copyright 2025 TN3W

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.