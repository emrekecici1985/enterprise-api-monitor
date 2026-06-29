cat << 'EOF' > ~/.service_checker.py
# -*- coding: utf-8 -*-
import sys
import urllib.request
import time

# --- LOGGING COLOR CONSTANTS ---
COLOR_INFO = "\033[1;36m"
COLOR_SUCCESS = "\033[1;32m"
COLOR_FAILURE = "\033[1;31m"
COLOR_RESET = "\033[0m"

def verify_service_status(service_name, target_url):
    try:
        request_wrapper = urllib.request.Request(
            target_url, 
            headers={'User-Agent': 'Enterprise-Health-Monitor/1.0'}
        )
        start_timestamp = time.time()
        
        with urllib.request.urlopen(request_wrapper, timeout=5) as http_response:
            status_code = http_response.getcode()
            execution_time_ms = round((time.time() - start_timestamp) * 1000)
            
            if status_code == 200:
                print(f"{COLOR_SUCCESS}{service_name:<20}:{COLOR_RESET} ONLINE ({execution_time_ms}ms)")
            else:
                print(f"{COLOR_FAILURE}{service_name:<20}:{COLOR_RESET} HTTP_ERROR (Status: {status_code})")
                
    except Exception:
        print(f"{COLOR_FAILURE}{service_name:<20}:{COLOR_RESET} CRITICAL_FAILURE (Unreachable)")

def execute_monitor():
    try:
        print(f"{COLOR_INFO}==============================================={COLOR_RESET}")
        print(f"{COLOR_INFO}        ENTERPRISE INFRASTRUCTURE MONITOR      {COLOR_RESET}")
        print(f"{COLOR_INFO}==============================================={COLOR_RESET}")
        print(f"{COLOR_SUCCESS}Status:{COLOR_RESET} Initializing core dependency checks...\n")
        
        # Core Infrastructure Target Verification
        verify_service_status("Payment Gateway API", "https://api.iyzipay.com")
        verify_service_status("E-Commerce Platform", "https://www.shopify.com")
        verify_service_status("Corporate CRM Service", "https://api.hubapi.com")
        verify_service_status("Global Network Mesh", "https://www.google.com")
        
        print(f"\n{COLOR_INFO}-----------------------------------------------{COLOR_RESET}")
        print(f"{COLOR_INFO}Execution:{COLOR_RESET} Pipeline completed successfully.")
        
    except Exception as runtime_error:
        print(f"{COLOR_FAILURE}Runtime Error:{COLOR_RESET} Execution failed. {str(runtime_error)}")

if __name__ == "__main__":
    execute_monitor()
EOF
chmod +x ~/.service_checker.py

# Bind shortcut link 'l' to target configuration profile
CONFIG_TARGET="$HOME/.bashrc"
[ -f "$HOME/.zshrc" ] && CONFIG_TARGET="$HOME/.zshrc"
sed -i '/alias l=/d' $CONFIG_TARGET
echo "alias l='python3 ~/.service_checker.py'" >> $CONFIG_TARGET
source $CONFIG_TARGET 2>/dev/null

clear
echo -e "\033[1;32m[✓] Enterprise monitoring script initialized successfully.\033[0m"
echo -e "Command token: \033[1;36ml\033[0m\n"

# Trigger initial automation process
python3 ~/.service_checker.py
