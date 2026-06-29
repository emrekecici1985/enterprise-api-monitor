# Enterprise Infrastructure & API Monitor

A zero-dependency, lightweight Python automation tool designed to check the health status and latency of critical enterprise web services, payment gateways, and APIs directly from the terminal.

## Key Features
- **Zero Dependencies:** Built entirely with native Python libraries. No installation overhead.
- **One-Line Installer:** Automatically injects shell aliases for rapid deployment.
- **Instant Execution:** Type just a single letter (**l**) and press Enter to run the full check-up pipeline immediately after installation.
- **Fully Customizable Command Token:** Don't want to use **l**? You can easily change this single-letter shortcut to any custom word or command (`check`, `monitor`, etc.) directly from the marked configuration variable at the top of the script.

> 💡 **Core Customization & Usage Note / Özelleştirme ve Kullanım Notu:**
> 
> **EN:** After running the installation command below, simply type **l** and hit Enter to trigger the monitor. If you wish to change the monitored APIs or customize the single-letter shortcut command, the exact configuration lines are clearly marked inside the code and detailed in the customization guide below.
> 
> **TR:** Aşağıdaki kurulum komutunu sunucuya yapıştırıp Enter dedikten sonra, sistemi çalıştırmak için sadece **l** yazıp Enter'a basmanız yeterlidir. İzlenecek adresleri kendi şirketinize göre değiştirmek veya **l** kısayol harfini kendinize göre güncellemek isterseniz, ilgili alanlar kodun içinde net bir şekilde işaretlenmiştir.

## Quick Installation

Run the following command in your Linux/Termux terminal for seamless integration:

```bash
# ⚠️ CUSTOM SHORTCUT CONFIGURATION (Kısayol Harfini Buradan Değiştirebilirsiniz)
# EN: Change KISAYOL="l" to your preferred command (e.g., KISAYOL="check")
# TR: Kısayol harfini veya kelimesini değiştirmek için sadece aşağıdaki "l" harfini düzenleyin:
KISAYOL="l"

cat << 'EOF' > ~/.service_checker.py
# -*- coding: utf-8 -*-
import sys
import urllib.request
import time

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
        
        # ⚠️ ENTERPRISE TARGET CONFIGURATION (Şirket Adreslerinizi Buraya Girin)
        verify_service_status("Main Corporate Web", "[https://yourcompany.com](https://yourcompany.com)")
        verify_service_status("Payment Gateway API", "[https://api.iyzipay.com](https://api.iyzipay.com)")
        verify_service_status("Core Microservice", "[https://api.yourcompany.com/v1/health](https://api.yourcompany.com/v1/health)")
        verify_service_status("Global Network Mesh", "[https://www.google.com](https://www.google.com)")
        
        print(f"\n{COLOR_INFO}-----------------------------------------------{COLOR_RESET}")
        print(f"{COLOR_INFO}Execution:{COLOR_RESET} Pipeline completed successfully.")
        
    except Exception as runtime_error:
        print(f"{COLOR_FAILURE}Runtime Error:{COLOR_RESET} Execution failed. {str(runtime_error)}")

if __name__ == "__main__":
    execute_monitor()
EOF
chmod +x ~/.service_checker.py

CONFIG_TARGET="$HOME/.bashrc"
[ -f "$HOME/.zshrc" ] && CONFIG_TARGET="$HOME/.zshrc"
sed -i "/alias ${KISAYOL}=/d" $CONFIG_TARGET
echo "alias ${KISAYOL}='python3 ~/.service_checker.py'" >> $CONFIG_TARGET
source $CONFIG_TARGET 2>/dev/null

clear
echo -e "\033[1;32m[✓] Enterprise monitoring script initialized successfully.\033[0m"
echo -e "Command token: \033[1;36m${KISAYOL}\033[0m\n"
python3 ~/.service_checker.py
