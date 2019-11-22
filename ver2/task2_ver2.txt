import requests
from bs4 import BeautifulSoup
from openpyxl import Workbook
import time

# Входные данные: число count - количество получаемых постов
# Выходные данные: таблица , в каждой строке которой должны находиться:
# заголовок поста, короткое описание поста, дата публикации, имя автора поста

def get_html(url):
    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:69.0) Gecko/20100101 Firefox/69.0'}
    r = requests.get(url,headers=headers)
    if r.status_code == 200:
        return r.text
    else:
        print(' Problem no OK!!!!')
        print(r.status_code)
        return None
array_write=[]
def data_from_page(html,current_number_post_write,count_posts_for_parsing):
    if html == None:
        print('----------------Error!!!!---------------------')
        return None
    try:
        soup = BeautifulSoup(html, 'lxml')
        posts = soup.find('div', class_='posts_list').find_all('li',id=not None)
        for post in posts:
            name_post=post.find('h2').find('a').text
            short_text=post.find('div',class_='post__text post__text-html js-mediator-article').text
            time_public=post.find('span',class_='post__time').text
            user=post.find('span',class_='user-info__nickname user-info__nickname_small').text
            data=[name_post,short_text,time_public,user]
            current_number_post_write +=1
            if(count_posts_for_parsing<current_number_post_write):
                break
            array_write.append(data)
            print(current_number_post_write,data)
    except Exception as e:
        print(e)
        print('------except !!!')
        return None
    write_data_excel(array_write)
    return current_number_post_write

def write_data_excel(row_need_finish):
    wb = Workbook()
    ws = wb.active
    for row in row_need_finish:
        ws.append(row)
    wb.save("test2.xlsx")

def main():

    count_posts_for_parsing = 45
    current_number_post_write=0

    while(current_number_post_write<count_posts_for_parsing):
        page = (current_number_post_write//20)+1
        url ='https://habr.com/ru/top/yearly/page{}/'.format(page)
        html=get_html(url)
        current_number_post_write=data_from_page(html,current_number_post_write,count_posts_for_parsing)
if __name__ == '__main__':
    main()