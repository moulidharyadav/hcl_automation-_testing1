## Project Description

This project automates Amazon product search and cart functionality using Python and Selenium.
## code
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException
import time


driver = webdriver.Chrome()
wait = WebDriverWait(driver, 30)

driver.get("https://www.amazon.in")
driver.maximize_window()

print("Amazon opened")

# ============================================================
# 1. ACCOUNT & MOBILE LOGIN
# ============================================================

signin = wait.until(
    EC.element_to_be_clickable(
        (By.ID, "nav-link-accountList")
    )
)
signin.click()
print("PASS: Account & Lists clicked")
time.sleep(2)

mobile_number = "9342608036"

try:
    mobile_box = wait.until(
        EC.presence_of_element_located(
            (By.ID, "ap_email")
        )
    )
except TimeoutException:
    try:
        mobile_box = wait.until(
            EC.presence_of_element_located(
                (By.ID, "ap_email_login")
            )
        )
    except TimeoutException:
        mobile_box = wait.until(
            EC.presence_of_element_located(
                (
                    By.XPATH,
                    "//input[contains(@placeholder,'mobile') "
                    "or contains(@placeholder,'email')]"
                )
            )
        )

mobile_box.clear()
mobile_box.send_keys(mobile_number)
print("PASS: Mobile number entered")
time.sleep(0.5)

# Click Continue
try:
    continue_button = wait.until(
        EC.element_to_be_clickable(
            (By.ID, "continue")
        )
    )
except TimeoutException:
    try:
        continue_button = wait.until(
            EC.element_to_be_clickable(
                (
                    By.XPATH,
                    "//input[@type='submit' "
                    "and contains(@value,'Continue')]"
                )
            )
        )
    except TimeoutException:
        continue_button = wait.until(
            EC.element_to_be_clickable(
                (
                    By.XPATH,
                    "//button[contains(.,'Continue')]"
                )
            )
        )

continue_button.click()
print("PASS: Continue clicked")
time.sleep(2)

# ============================================================
# 2. MANUAL PASSKEY STEP
# ============================================================

print()
print("======================================================")
print("MANUAL PASSKEY REQUIRED")
print("Complete the Amazon Passkey manually in the browser.")
print("======================================================")
print()

try:
    passkey_button = WebDriverWait(driver, 8).until(
        EC.element_to_be_clickable(
            (
                By.XPATH,
                "//*[contains("
             "translate(normalize-space(.),"
                "'ABCDEFGHIJKLMNOPQRSTUVWXYZ',"
                "'abcdefghijklmnopqrstuvwxyz'),"
                "'sign in with a passkey'"
                ")]"
            )
        )
    )
    driver.execute_script(
        "arguments[0].scrollIntoView({block:'center'});",
        passkey_button
    )
    time.sleep(0.5)
    passkey_button.click()
    print("PASS: Passkey option selected")
except TimeoutException:
    print("INFO: Passkey option was not automatically found.")
    print("Complete the passkey manually in the browser.")

print("Waiting for Amazon login...")
try:
    WebDriverWait(driver, 300).until(
        EC.presence_of_element_located(
            (By.ID, "twotabsearchtextbox")
        )
    )
    print("PASS: Amazon login completed")
except TimeoutException:
    print("FAIL: Amazon login was not completed.")
    input("Press Enter to close browser...")
    driver.quit()
    exit()

# ============================================================
# 3. SEARCH & ADD PRODUCT TO CART
# ============================================================

driver.switch_to.new_window("tab")
driver.get("https://www.amazon.in")
print("Search tab opened")

search = wait.until(
    EC.presence_of_element_located(
        (By.ID, "twotabsearchtextbox")
    )
)

product = "ASUS TUF Gaming laptop"
search.send_keys(product)
search.send_keys(Keys.ENTER)
print("ASUS TUF Gaming laptop searched")

time.sleep(7)

print("Finding first search result...")
first_product = wait.until(
    EC.presence_of_element_located(
        (
            By.XPATH,
            "(//h2[@aria-label])[1]"
        )
    )
)

product_name = first_product.get_attribute("aria-label")
print("First product found:")
print(product_name)

print("Finding Add to Cart button...")

add_cart = driver.execute_script("""
    var title = arguments[0];
    var parent = title;

    for (var i = 0; i < 12; i++) {
        if (!parent) {
            break;
        }

        var elements = parent.querySelectorAll("button, input, a");

        for (var j = 0; j < elements.length; j++) {
            var el = elements[j];

            var text = (
                el.innerText ||
                el.value ||
                el.getAttribute("aria-label") ||
                ""
            ).toLowerCase();

            if (text.includes("add to cart")) {
                return el;
            }
        }

        parent = parent.parentElement;
    }

    return null;
""", first_product)

if add_cart:
    print("Add to Cart button found")
    driver.execute_script(
        "arguments[0].scrollIntoView({block:'center'});",
        add_cart
    )
    time.sleep(1)
    driver.execute_script(
        "arguments[0].click();",
        add_cart
    )
    print("SEARCHED PRODUCT ADDED TO CART")
else:
    print("Add to Cart button not found")
    input("Press Enter to close browser...")
    driver.quit()
    exit()

time.sleep(5)
print("Cart updated")

# ============================================================
# 4. VIEW CART
# ============================================================

driver.switch_to.new_window("tab")
driver.get("https://www.amazon.in/gp/cart/view.html")
print("Cart tab opened")

time.sleep(5)
print("Cart opened successfully")

input("Press Enter to close browser...")
driver.quit()
```
# ============================================================
## output:-
<img width="1822" height="975" alt="image" src="https://github.com/user-attachments/assets/9243109f-8845-402a-a0bf-615075df8fa1" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c6940982-5b38-4298-b0bb-d7342b599570" />
<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/c247fff2-1766-4a74-9e67-fb0a2c444fe5" />
<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/3541cf4f-a8fd-43a1-b2cb-6f3b8cf29992" />



