from playwright.sync_api import sync_playwright
import time

URL = "https://demo.owasp-juice.shop/#/register"


def close_popups(page):
    try:
        page.click("button[aria-label='Close Welcome Banner']", timeout=3000)
    except Exception:
        pass
    try:
        page.click("#cookieconsent-banner button", timeout=3000)
    except Exception:
        pass


def fill_registration_form(page, email, password, repeat_password):
    """Заполняет все поля формы регистрации одним набором данных."""
    page.fill("#emailControl", email)
    page.fill("#passwordControl", password)
    page.fill("#repeatPasswordControl", repeat_password)
    page.click("mat-select", force=True)
    page.wait_for_timeout(300)
    page.keyboard.press("ArrowDown")
    page.keyboard.press("Enter")

    page.fill("#securityAnswerControl", "ответ")
    page.wait_for_timeout(500)


def try_register(page, email, password, repeat_password, should_succeed, test_name):

    page.goto(URL)
    close_popups(page)
    page.wait_for_selector("#emailControl")

    fill_registration_form(page, email, password, repeat_password)

    register_button = page.locator("#registerButton")

    if register_button.is_disabled():
        registration_worked = False
    else:
        register_button.click()
        page.wait_for_timeout(1500)
        registration_worked = "login" in page.url

    passed = (registration_worked == should_succeed)
    status = "PASS" if passed else "FAIL"
    print(f"[{status}] {test_name}")
    return passed


def main():
    print("Запуск тестов регистрации Juice Shop...\n")
    results = []

    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False, channel="msedge")
        page = browser.new_page()

        unique_email = f"user{int(time.time())}@test.com"

        results.append(try_register(
            page, email="", password="test1234", repeat_password="test1234",
            should_succeed=False, test_name="Пустой email"
        ))

        results.append(try_register(
            page, email=unique_email, password="test1234", repeat_password="drugoy1",
            should_succeed=False, test_name="Пароли не совпадают"
        ))

        results.append(try_register(
            page, email=f"ok{unique_email}", password="test1234", repeat_password="test1234",
            should_succeed=True, test_name="Корректные данные"
        ))

        browser.close()

    print(f"\nИтог: {sum(results)} из {len(results)} тестов пройдено")


if __name__ == "__main__":
    main()
