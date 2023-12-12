from datetime import time
import time
from selenium.webdriver.common.by import By
from selenium.webdriver import Firefox
import pytest
from selenium import webdriver
from time import sleep
import pandas as pd

df = pd.read_csv("C:/Users/vinorajm/Desktop/webclientdata_wsp(1).csv")  # using pandas to get data from local file
Uname = df['Uname'][0]
password = df['password'][0]
regid = df['regid'][0]


@pytest.fixture(params=["chrome", "firefox", "edge"], scope="class")  # call multiple browser
def driver_init(request):
    global web_driver
    if request.param == "chrome":
        web_driver = webdriver.Chrome()  # if condition use for call the browser one by one
        print(web_driver.title)
    if request.param == "firefox":
        web_driver = Firefox()
        print(web_driver.title)
    elif request.param == "edge":
        web_driver = webdriver.Edge()
        print(web_driver.title)
    request.cls.driver = web_driver
    yield
    web_driver.quit()


def run_tests(browser):
    pytest.main(["-v", "-s", f"--browser={browser}"])  # set a default browser


if __name__ == "__main__":
    run_tests("firefox")


@pytest.mark.usefixtures("driver_init")  # call driver_init function to this parent class
class BasicTest:  # creating a parent class
    pass


class Test_URL(BasicTest):  # class a child class and call parent class init
    def test_open_url(self):
        web_driver.get(
            'https://us-east-1.webclient-gamma.amazonworkspaces.com/registration')  # to hit a Url link to browser
        sleep(5)
        try:
            web_driver.find_element(By.XPATH,
                                    "/html/body/div[1]/div/div[1]/div/div/div[2]/div/button[1]").click()  # accept cookies
        except:
            print("no cookies")

        time.sleep(3)
        web_driver.find_element(By.CSS_SELECTOR, '#registrationTxt').send_keys(regid)  # registration page
        web_driver.maximize_window()
        web_driver.find_element(By.CSS_SELECTOR, '.styles_main-btn__VuDTB').click()  # sign page
        time.sleep(3)
        web_driver.find_element(By.CSS_SELECTOR, '.styles_main-btn__VuDTB').click()
        time.sleep(10)
        web_driver.find_element(By.XPATH, '//*[@id="wdc_username"]').send_keys(Uname)  # enter username
        web_driver.find_element(By.XPATH, '//*[@id="wdc_password"]').send_keys(password)  # enter password
        web_driver.find_element(By.XPATH, '//*[@id="wdc_login_button"]').click()  # click sign in
        time.sleep(20)
        web_driver.find_element("xpath",
                                '//*[@id="dcv-toolbar-container"]/div/div/div[2]').click()  # after into in-session click drop down
        time.sleep(10)
        #   click disconnect in that drop down and goes to disconnect page
        web_driver.find_element("xpath",
                                '//*[@id="dcv-toolbar-container"]/div/div/div[2]/div/div/div/div/div/div/div/div/div/ul/li[5]').click()
        time.sleep(20)
