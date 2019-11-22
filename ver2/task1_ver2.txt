import requests
from bs4 import BeautifulSoup
from openpyxl import Workbook

# Выходные данные: таблица , в каждой строке которой должны находиться:
# Полное юридическое наименование, Руководитель, Дата регистрации, Статус, ИНН, КПП

def get_html(url):
    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:69.0) Gecko/20100101 Firefox/69.0'}
    r = requests.get(url,headers=headers)
    #time.time(0.3)
    if r.status_code == 200:
        return r.text
    else:
        print(' Beda no OK!!!!')
        print(r.status_code)
        return None

def data_from_page(html):
    if html == None:
        print('----------------Error!!!!---------------------')
        return None
    try:
        soup = BeautifulSoup(html, 'lxml')
        trs = soup.find('table', class_='tt').find_all('tr')
        divs=soup.find_all('div',class_='c2m')
        for div in divs:
            temp=div.find('p')
            if (temp.find('i').text == 'Полное юридическое наименование:'):
                name_company = temp.find('a').text
                print(name_company)
                break
        print(len(trs))
        for tr in trs:
        #if (len(trs)==7):
            data=[]
            if tr.find_all('td')[0].find('i').text == 'Руководитель:':
                name_leader = tr.find_all('td')[1].text
                print(name_leader)
            if tr.find_all('td')[0].find('i').text=='Дата регистрации:':
                date_registr=tr.find_all('td')[1].text
                print(date_registr)
            if tr.find_all('td')[0].find('i').text=='Статус:':
                status=tr.find_all('td')[1].text
                print(status)
            if tr.find_all('td')[0].find('i').text=='ИНН / КПП:':
                INN=tr.find_all('td')[1].text.strip().split('/')[0]
                KPP = tr.find_all('td')[1].text.strip().split('/')[1]
                print(INN)
                print(KPP)
        data=[name_company,name_leader,date_registr,status,INN,KPP]
        print(data)
        write_data_excel(data)
    except Exception as e:
        print(e)
        print('------>>>Exception !!!')
        return None

def write_data_excel(row_need_finish):
    wb = Workbook()
    ws = wb.active
    #for row in row_need_finish:
    ws.append(row_need_finish)
    wb.save("test.xlsx")

def main():
    # Здесь подставляем в цикле ссыкли на компании

    #url ='https://www.list-org.com/company/4868135'
    url='https://www.list-org.com/company/982773'
    html=get_html(url)
    data_from_page(html)

if __name__ == '__main__':
    main()