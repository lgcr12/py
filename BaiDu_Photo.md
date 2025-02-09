import requests
import os
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium import webdriver
import urllib.parse

#将查找内容转换成utf-8编码
def encode_text(text):
    # 使用 urllib.parse.quote 进行 URL 编码
    encoded_text = urllib.parse.quote(text, encoding='utf-8')
    return encoded_text

#获取图片下载地址
def get_pic_url(html_url):
    opt=webdriver.ChromeOptions()
    opt.add_argument('--headless')
    driver = webdriver.Chrome(options=opt)
    driver.get(url=html_url)
    print("成功打开网页")
    # 等待图片加载完成
    WebDriverWait(driver, 10).until(
        EC.visibility_of_all_elements_located((By.XPATH, ".//img[@class='main_img img-hover']")))

    # 定位所有图片元素
    pics = driver.find_elements(By.XPATH, ".//img[@class='main_img img-hover']")

    # 确保保存目录存在
    save_dir = text
    if not os.path.isdir(save_dir):
        os.mkdir(save_dir)

    # 逐个处理图片元素
    for index, pic in enumerate(pics):
        # 获取图片链接
        pic_url = pic.get_attribute("src")
        print("成功获取图片链接")
        try:
            download_image(pic_url, save_dir, index)
        except Exception as e:
            print(f"下载图片失败：{e}")
    driver.quit()

#下载图片
def download_image(pic_url, save_dir, index):
    # 请求图片链接获取图片内容
    response = requests.get(pic_url)
    response.raise_for_status()  # 检查请求是否成功

    # 确定保存路径，使用图片索引来生成唯一的文件名
    save_path = os.path.join(save_dir, f"{text}_{index+1}.jpg")

    # 写入图片内容到文件
    with open(save_path, "wb") as f:
        f.write(response.content)
        print(f"成功下载图片{index+1}")

if __name__ == "__main__":
    print("请输入想查找的图片")
    text = input()
    name = encode_text(text)
    url = f'https://image.baidu.com/search/index?tn=baiduimage&ipn=r&ct=201326592&cl=2&lm=-1&st=-1&fm=result&fr=&sf=1&fmq=1716365971765_R&pv=&ic=&nc=1&z=&hd=&latest=&copyright=&se=1&showtab=0&fb=0&width=&height=&face=0&istype=2&dyTabStr=MCwxLDMsMiw2LDQsNSw3LDgsOQ%3D%3D&ie=utf-8&ctd=1716365971767%5E00_1425X237&sid=&word={name}'
    get_pic_url(url)
