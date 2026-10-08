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

## Assignment IV

## Project Description
Selenium Web Automation Testing for Online Shopping Applications
## code
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time


# ============================================================
# BROWSER SETUP
# ============================================================

driver = webdriver.Chrome()
driver.maximize_window()

wait = WebDriverWait(driver, 15)

# Delay between actions
delay = 3


# ============================================================
# TC01 - OPEN SHOPPING WEBSITE
# ============================================================

driver.get("https://www.saucedemo.com/")

time.sleep(delay)

print("\nTC01 - Shopping website opened")

username = wait.until(
    EC.visibility_of_element_located(
        (By.ID, "user-name")
    )
)

time.sleep(1)

username.send_keys("standard_user")

time.sleep(delay)

password = driver.find_element(
    By.ID,
    "password"
)

password.send_keys("secret_sauce")

time.sleep(delay)

login = driver.find_element(
    By.ID,
    "login-button"
)

login.click()

time.sleep(delay)

print("TC01 - Login successful")


# ============================================================
# TC02 - CONFIRMATION ALERT - ACCEPT
# ============================================================

driver.get(
    "https://www.selenium.dev/selenium/web/alerts.html"
)

time.sleep(delay)

links = driver.find_elements(By.TAG_NAME, "a")

for link in links:
    if "test confirm" in link.text.lower():
        link.click()
        break

time.sleep(delay)

alert = wait.until(
    EC.alert_is_present()
)

print("\nTC02 - Confirmation Alert:")
print(alert.text)

time.sleep(delay)

alert.accept()

time.sleep(delay)

print("TC02 - Alert accepted successfully")


# ============================================================
# TC03 - CONFIRMATION ALERT - DISMISS
# ============================================================

driver.get(
    "https://www.selenium.dev/selenium/web/alerts.html"
)

time.sleep(delay)

links = driver.find_elements(By.TAG_NAME, "a")

for link in links:
    if "test confirm" in link.text.lower():
        link.click()
        break

time.sleep(delay)

alert = wait.until(
    EC.alert_is_present()
)

print("\nTC03 - Confirmation Alert:")
print(alert.text)

time.sleep(delay)

alert.dismiss()

time.sleep(delay)

print("TC03 - Cancel selected successfully")


# ============================================================
# TC04 - PROMPT ALERT
# ============================================================

driver.get(
    "https://www.selenium.dev/selenium/web/alerts.html"
)

time.sleep(delay)

links = driver.find_elements(By.TAG_NAME, "a")

for link in links:
    if "prompt happen" in link.text.lower():
        link.click()
        break

time.sleep(delay)

alert = wait.until(
    EC.alert_is_present()
)

print("\nTC04 - Prompt Alert:")
print(alert.text)

time.sleep(delay)

alert.send_keys("Golla Moulidhar")

time.sleep(delay)

print("TC04 - Value entered: Golla Moulidhar")

alert.accept()

time.sleep(delay)

print("TC04 - Prompt submitted successfully")


# ============================================================
# TC05 - MOUSE HOVER
# ============================================================

driver.get(
    "https://www.selenium.dev/selenium/web/mouse_interaction.html"
)

time.sleep(delay)

element = wait.until(
    EC.visibility_of_element_located(
        (By.ID, "hover")
    )
)

time.sleep(2)

print("\nTC05 - Moving mouse to element...")

ActionChains(driver).move_to_element(
    element
).perform()

time.sleep(delay)

print("TC05 - Mouse Hover performed")


# ============================================================
# TC06 - DOUBLE CLICK
# ============================================================

driver.get(
    "https://www.selenium.dev/selenium/web/mouse_interaction.html"
)

time.sleep(delay)

element = wait.until(
    EC.visibility_of_element_located(
        (By.ID, "clickable")
    )
)

time.sleep(2)

print("\nTC06 - Performing double click...")

ActionChains(driver).double_click(
    element
).perform()

time.sleep(delay)

print("TC06 - Double Click performed")


# ============================================================
# TC07 - DRAG AND DROP
# ============================================================

driver.get(
    "https://www.selenium.dev/selenium/web/mouse_interaction.html"
)

time.sleep(delay)

source = wait.until(
    EC.visibility_of_element_located(
        (By.ID, "draggable")
    )
)

target = wait.until(
    EC.visibility_of_element_located(
        (By.ID, "droppable")
    )
)

time.sleep(2)

print("\nTC07 - Dragging product...")

ActionChains(driver).drag_and_drop(
    source,
    target
).perform()

time.sleep(delay)

print("TC07 - Drag and Drop performed")


# ============================================================
# TC08 - EXPLICIT WAIT
# ============================================================

driver.get(
    "https://www.saucedemo.com/"
)

time.sleep(delay)

username = wait.until(
    EC.visibility_of_element_located(
        (By.ID, "user-name")
    )
)

username.send_keys("standard_user")

time.sleep(2)

password = driver.find_element(
    By.ID,
    "password"
)

password.send_keys("secret_sauce")

time.sleep(2)

login = driver.find_element(
    By.ID,
    "login-button"
)

login.click()

time.sleep(delay)

print("\nTC08 - Login completed")

product = wait.until(
    EC.visibility_of_element_located(
        (By.CLASS_NAME, "inventory_item")
    )
)

time.sleep(delay)

print("TC08 - Product results loaded successfully")

print("Product:")
print(product.text)

time.sleep(delay)


# ============================================================
# TC09 - CLICKABLE WAIT
# ============================================================

add = wait.until(
    EC.element_to_be_clickable(
        (By.ID, "add-to-cart-sauce-labs-backpack")
    )
)

time.sleep(2)

print("\nTC09 - Adding product to cart...")

add.click()

time.sleep(delay)

print("TC09 - Product added to cart")

cart = wait.until(
    EC.element_to_be_clickable(
        (By.CLASS_NAME, "shopping_cart_link")
    )
)

time.sleep(2)

cart.click()

time.sleep(delay)

print("TC09 - Cart opened")

checkout = wait.until(
    EC.element_to_be_clickable(
        (By.ID, "checkout")
    )
)

time.sleep(2)

checkout.click()

time.sleep(delay)

print("TC09 - Checkout page opened")

first_name = wait.until(
    EC.visibility_of_element_located(
        (By.ID, "first-name")
    )
)

time.sleep(2)

first_name.send_keys("Golla")

time.sleep(2)

driver.find_element(
    By.ID,
    "last-name"
).send_keys("Moulidhar")

time.sleep(2)

driver.find_element(
    By.ID,
    "postal-code"
).send_keys("600001")

time.sleep(delay)

driver.find_element(
    By.ID,
    "continue"
).click()

time.sleep(delay)

place_order = wait.until(
    EC.element_to_be_clickable(
        (By.ID, "finish")
    )
)

print("TC09 - Place Order button is clickable")

time.sleep(delay)

place_order.click()

time.sleep(delay)

print("TC09 - Order submitted successfully")


# ============================================================
# TC10 - ORDER CONFIRMATION ALERT
# ============================================================

driver.get(
    "https://www.saucedemo.com/"
)

time.sleep(delay)

username = wait.until(
    EC.visibility_of_element_located(
        (By.ID, "user-name")
    )
)

username.send_keys("standard_user")

time.sleep(2)

password = driver.find_element(
    By.ID,
    "password"
)

password.send_keys("secret_sauce")

time.sleep(2)

login = driver.find_element(
    By.ID,
    "login-button"
)

login.click()

time.sleep(delay)

print("\nTC10 - Login completed")

add = wait.until(
    EC.element_to_be_clickable(
        (By.ID, "add-to-cart-sauce-labs-backpack")
    )
)

time.sleep(2)

add.click()

time.sleep(delay)

print("TC10 - Product added to cart")

cart = wait.until(
    EC.element_to_be_clickable(
        (By.CLASS_NAME, "shopping_cart_link")
    )
)

time.sleep(2)

cart.click()

time.sleep(delay)

print("TC10 - Cart opened")

checkout = wait.until(
    EC.element_to_be_clickable(
        (By.ID, "checkout")
    )
)

time.sleep(2)

checkout.click()

time.sleep(delay)

first_name = wait.until(
    EC.visibility_of_element_located(
        (By.ID, "first-name")
    )
)

first_name.send_keys("Golla")

time.sleep(2)

driver.find_element(
    By.ID,
    "last-name"
).send_keys("Moulidhar")

time.sleep(2)

driver.find_element(
    By.ID,
    "postal-code"
).send_keys("600001")

time.sleep(delay)

driver.find_element(
    By.ID,
    "continue"
).click()

time.sleep(delay)

finish = wait.until(
    EC.element_to_be_clickable(
        (By.ID, "finish")
    )
)

time.sleep(2)

finish.click()

time.sleep(delay)

print("TC10 - Order completed")

confirmation = wait.until(
    EC.visibility_of_element_located(
        (By.CLASS_NAME, "complete-header")
    )
)

time.sleep(delay)

print("TC10 - Order confirmation page displayed")
print("Message:", confirmation.text)

time.sleep(delay)


# ============================================================
# CREATE CONFIRMATION ALERT
# ============================================================

driver.execute_script("""
    setTimeout(function() {
        alert('Order confirmed successfully!');
    }, 1000);
""")

time.sleep(2)

alert = wait.until(
    EC.alert_is_present()
)

print("TC10 - Confirmation Alert:")
print(alert.text)

time.sleep(delay)

alert.accept()

time.sleep(delay)

print("TC10 - Confirmation alert handled successfully")


# ============================================================
# ALL TEST CASES COMPLETED
# ============================================================

print("\n====================================")
print("ALL 10 TEST CASES COMPLETED")
print("====================================")

input("\nPress Enter to close browser...")

driver.quit()
```



## Assignment V - Xpath
## DESCRPITION
Imagine an online student registration form.

The automation must:

Open the registration page.
Enter student name.
Enter password.
Enter additional information.
Select dropdown.
Select checkbox.
Select radio button.
Click Submit.
Verify successful submission.

## CODE
```python

from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time


# ==========================================================
# TC01 - Launch Chrome and Open Registration Page
# ==========================================================

driver = webdriver.Chrome()
driver.maximize_window()

wait = WebDriverWait(driver, 15)

driver.get("https://expertdecision.onrender.com/register")

print("TC01 - Registration page opened successfully")


# ==========================================================
# TC02 - Enter Full Name
# ==========================================================

name = wait.until(
    EC.visibility_of_element_located(
        (By.XPATH, "//input[@placeholder='John Doe']")
    )
)

name.clear()
name.send_keys("Golla Moulidhar")

print("TC02 - Full Name entered successfully")


# ==========================================================
# TC03 - Enter Email
# ==========================================================

email = wait.until(
    EC.visibility_of_element_located(
        (By.XPATH, "//input[@placeholder='john@company.com']")
    )
)

email.clear()
email.send_keys("koppalanaveen7@gmail.com")

print("TC03 - Email entered successfully")


# ==========================================================
# TC04 - Enter Password
# ==========================================================

password = wait.until(
    EC.visibility_of_element_located(
        (By.XPATH, "//input[@type='password'][1]")
    )
)

password.clear()
password.send_keys("Naveen0320@")

print("TC04 - Password entered successfully")


# ==========================================================
# TC05 - Enter Confirm Password
# ==========================================================

confirm_password = wait.until(
    EC.visibility_of_element_located(
        (By.ID, "confirm_password")
    )
)

confirm_password.clear()
confirm_password.send_keys("Naveen0320@")

print("TC05 - Confirm Password entered successfully")


# ==========================================================
# TC06 - Select Employee Role
# ==========================================================

employee = wait.until(
    EC.element_to_be_clickable(
        (By.XPATH, "//label[@for='roleEmployee']")
    )
)

driver.execute_script(
    "arguments[0].scrollIntoView({block: 'center'});",
    employee
)

time.sleep(1)

employee.click()

print("TC06 - Employee role selected successfully")


# ==========================================================
# TC07 - Click Continue / Submit
# ==========================================================

continue_button = wait.until(
    EC.element_to_be_clickable(
        (By.XPATH, "//button[@id='submitBtn']")
    )
)

driver.execute_script(
    "arguments[0].scrollIntoView({block: 'center'});",
    continue_button
)

time.sleep(1)

continue_button.click()

print("TC07 - Continue button clicked successfully")


# ==========================================================
# TC08 - Wait for Email Verification Page
# ==========================================================

time.sleep(3)

print("TC08 - Email verification page opened")


# ==========================================================
# Keep Browser Open
# ==========================================================

input("\nPress Enter to close the browser...")


# ==========================================================
# Close Browser
# ==========================================================

driver.quit()

print("Browser closed successfully")
```
