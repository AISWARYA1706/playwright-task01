from playwright.sync_api import Page, expect

class LoginPage:
    def _init_(self, page: Page):
        self.page = page
        self.username_input = page.locator("input[name='email']")
        self.password_input = page.locator("input[name='password']")
        self.login_button = page.locator("button[type='submit']")
        self.error_message = page.locator("text=Invalid")

    def load(self, url):
        self.page.goto(url)

    def login(self, username, password):
        self.username_input.fill(username)
        self.password_input.fill(password)
        self.login_button.click()

    def validate_username_field(self):
        expect(self.username_input).to_be_visible()

    def validate_password_field(self):
        expect(self.password_input).to_be_visible()

    def validate_submit_button(self):
        expect(self.login_button).to_be_enabled()

        conftest.py
        import pytest
from playwright.sync_api import sync_playwright

@pytest.fixture()
def browser_context():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        context = browser.new_context()
        page = context.new_page()
        yield page
        context.close()
        browser.close()

        test_login.py

        import pytest
from pages.login_page import LoginPage
from pages.dashboard_page import DashboardPage

ZEN_PORTAL_URL = "https://www.guvi.in/zen"  # Update this

VALID_USER = "your_valid_email@example.com"
VALID_PASS = "your_valid_password"

INVALID_PASS = "wrong123"

def test_validate_fields(browser_context):
    login = LoginPage(browser_context)
    login.load(ZEN_PORTAL_URL)
    login.validate_username_field()
    login.validate_password_field()
    login.validate_submit_button()

def test_successful_login(browser_context):
    login = LoginPage(browser_context)
    login.load(ZEN_PORTAL_URL)
    login.login(VALID_USER, VALID_PASS)

    dashboard = DashboardPage(browser_context)
    dashboard.verify_logout_button()
    dashboard.logout()

def test_unsuccessful_login(browser_context):
    login = LoginPage(browser_context)
    login.load(ZEN_PORTAL_URL)
    login.login(VALID_USER, INVALID_PASS)

    assert login.error_message.is_visible(), "Error message not displayed"
