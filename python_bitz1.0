#!/usr/bin/python
#  -*-  coding:utf-8  -*-
import tkinter as tk
from tkinter import messagebox  # import this to fix messagebox error
from tkinter import Label, Button, END
import pickle
import requests 
import json
import hashlib
import time
import random
import threading

window = tk.Tk()
window.title('刷单小工具')
window.geometry('800x800+100+100')

# welcome image
canvas = tk.Canvas(window, height=200, width=500)
image_file = tk.PhotoImage(file='welcome.gif')
image = canvas.create_image(0,0, anchor='nw', image=image_file)
canvas.pack(side='top')

# user information
tk.Label(window, text='api_key ').place(x=50, y= 150)
tk.Label(window, text='tradepwd ').place(x=50, y= 190)
tk.Label(window, text='secret_key ').place(x=50, y= 230)
tk.Label(window, text='sell_number ').place(x=50, y= 270)
tk.Label(window, text='buy_number ').place(x=300, y= 270)

var_api_key = tk.StringVar()
entry_api_key = tk.Entry(window, textvariable=var_api_key)
entry_api_key.place(x=130, y=150)
var_tradepwd = tk.StringVar()
entry_tradepwd = tk.Entry(window, textvariable=var_tradepwd, show='*')
entry_tradepwd.place(x=130, y=190)
var_secret_key = tk.StringVar()
entry_secret_key = tk.Entry(window, textvariable=var_secret_key, width=50)
entry_secret_key.place(x=130, y=230)
var_number1 = tk.StringVar()
entry_number1 = tk.Entry(window, textvariable=var_number1)
entry_number1.place(x=130, y=270)
var_number2 = tk.StringVar()
entry_number2 = tk.Entry(window, textvariable=var_number2)
entry_number2.place(x=380, y=270)
rn = random.randint(100000, 999999)
ran = str(rn)


#获取牌价
#ticker=requests.get(url='https://api.bit-z.com/api_v1/ticker?coin=cam_eth')
#t=json.loads(ticker.text)
#print(t) 

def as_num(x):
    y='{:.8f}'.format(x) # 8f表示保留8位小数点的float型
    return(y)

#获取交易深度
def getdepth():
	depth=requests.get(url='https://api.bit-z.com/api_v1/depth?coin=cam_eth') 
	depthpy=json.loads(depth.text)
	asks=depthpy["data"]["asks"][-1][0]
	bids=depthpy["data"]["bids"][0][0]
	#print('asks: ', asks)
	#print('bids: ', bids)
	return asks   

def ticker():
	ticker=requests.get(url='https://api.bit-z.com/api_v1/ticker?coin=cam_eth')
	t=json.loads(ticker.text)
	text_config.insert(END,'行情信息-- 买1：'+str(t["data"]["buy"])+' 卖1：'+str(t["data"]["sell"])+'\n')
	#depth=requests.get(url='https://api.bit-z.com/api_v1/depth?coin=cam_eth') 
	#depthpy=json.loads(depth.text)
	#print(depthpy)


def curlmd5(src):
    m = hashlib.md5()
    m.update(src.encode('UTF-8'))
    return m.hexdigest()

def price():
	asksMin=float(getdepth())*100000000
	#print(asksMin)
	#price=
	mid=random.randint(asksMin-500,asksMin)
	midprice=mid/100000000
	price=as_num(midprice)
	#print(price)
	return price

#priceBuy=price()


#买进
def buy(api_key,buy_number,priceBuy,nowtime,tradepwd,secret_key):
	#priceBuy=price()

	srcBuy='api_key={}&coin=cam_eth&nonce={}&number={}&price={}&timestamp={}&tradepwd={}&type=in{}'.format(api_key,ran,buy_number,priceBuy,nowtime,tradepwd,secret_key)
	srcBuymd5=curlmd5(srcBuy)
	#print(srcBuy)
	bodybuy={'sign':srcBuymd5,'api_key':api_key,'coin':'cam_eth','nonce':ran,'number':buy_number,'price':priceBuy,'timestamp':nowtime,'tradepwd':tradepwd,'type':'in'}
	#print('buybody: \n',bodybuy)
	buy=requests.post(url='https://api.bit-z.com/api_v1/tradeAdd', data=bodybuy)
	buyinfo=json.loads(buy.text)
	#text_config.insert('insert','srcBuy: '+srcBuy+'\n' )
	text_buy.insert(1.0,buyinfo)
	#print('this is buy info : \n',buyinfo)
	return 

def sell(api_key,sell_number,priceBuy,nowtime,tradepwd,secret_key):
    
    #print(asksMin)
    #priceSell=price()
    srcSell='api_key={}&coin=cam_eth&nonce={}&number={}&price={}&timestamp={}&tradepwd={}&type=out{}'.format(api_key,ran,sell_number,priceBuy,nowtime,tradepwd,secret_key)
    srcSellmd5=curlmd5(srcSell)
    #print(srcSell)
    sell=requests.post(url='https://api.bit-z.com/api_v1/tradeAdd', data={'sign':srcSellmd5,'api_key':api_key,'coin':'cam_eth','nonce':ran,'number':sell_number,'price':priceBuy,'timestamp':nowtime,'tradepwd':tradepwd,'type':'out'})
    sellinfo=json.loads(sell.text)
    #text_config.insert('insert','srcSell: '+srcSell+'\n' )
    #text_sell.insert(END,)
    #text_sell.insert(END,priceBuy)
    text_sell.insert(END,'价格：'+str(priceBuy)+' 数量：'+str(sell_number)+' 卖出：'+str(sellinfo["msg"]))
    #print('this is sell info : \n',sellinfo)
    return 


def sellbutton():
	global is_start
	api_key = var_api_key.get()
	tradepwd = var_tradepwd.get()
	secret_key = var_secret_key.get()
	sell_number = random.randint(10, 20)
	i=0
	while is_start:
		nowt=int(time.time())
		nowtime=str(nowt)
		sellprice=price()
		i += 1
		text_sell.insert(END,'第' )
		text_sell.insert(END,i )
		text_sell.insert(END,'次--' )

		sell(api_key,sell_number,sellprice,nowtime,tradepwd,secret_key)
		time.sleep(1)
	text_sell.insert('insert','卖出已停止！'+'\n' )


def dealbutton():
	global deal_start
	api_key = var_api_key.get()
	tradepwd = var_tradepwd.get()
	secret_key = var_secret_key.get()
	sell_number = random.randint(10, 20)
	buy_number = sell_number
	#if (sell_number=='' or buy_number==''):
	#	tk.messagebox.showerror(title='配置错误',message='Error, 请输入卖出、买进数量')
	#else:		
	i=0
	while deal_start:
		nowt=int(time.time())
		nowtime=str(nowt)
		dealprice=price()
		sell_number = random.randint(10, 20)
		buy_number = sell_number
		i += 1
		text_sell.insert(END,'第' )
		text_sell.insert(END,i )
		text_sell.insert(END,'次--' )

		srcBuy='api_key={}&coin=cam_eth&nonce={}&number={}&price={}&timestamp={}&tradepwd={}&type=in{}'.format(api_key,ran,buy_number,dealprice,nowtime,tradepwd,secret_key)
		srcBuymd5=curlmd5(srcBuy)
		
		#print('buybody: \n',bodybuy)

		srcSell='api_key={}&coin=cam_eth&nonce={}&number={}&price={}&timestamp={}&tradepwd={}&type=out{}'.format(api_key,ran,sell_number,dealprice,nowtime,tradepwd,secret_key)
		srcSellmd5=curlmd5(srcSell)
		sell=requests.post(url='https://api.bit-z.com/api_v1/tradeAdd', data={'sign':srcSellmd5,'api_key':api_key,'coin':'cam_eth','nonce':ran,'number':sell_number,'price':dealprice,'timestamp':nowtime,'tradepwd':tradepwd,'type':'out'})
		since = time.time()	
		sellinfo=json.loads(sell.text)
		text_sell.insert(END,'价格：'+str(dealprice)+' 数量：'+str(sell_number)+' 卖出：'+str(sellinfo["msg"])+' id:'+str(sellinfo["data"]["id"]))
		buy=requests.post(url='https://api.bit-z.com/api_v1/tradeAdd', data={'sign':srcBuymd5,'api_key':api_key,'coin':'cam_eth','nonce':ran,'number':buy_number,'price':dealprice,'timestamp':nowtime,'tradepwd':tradepwd,'type':'in'})
		time_elapsed = time.time() - since

		buyinfo=json.loads(buy.text)
		text_sell.insert(END,'买入：'+str(buyinfo["msg"])+' id:'+str(buyinfo["data"]["id"])+'\n')
		print('The code run {:.0f}m {:.0f}s'.format(time_elapsed // 60, time_elapsed % 60))
		time.sleep(1)
		order()
	text_sell.insert(END,'卖出已停止！'+'\n' )

def config():
	api_key = var_api_key.get()
	tradepwd = var_tradepwd.get()
	secret_key = var_secret_key.get()
	sell_number = var_number1.get()
	buy_number = var_number2.get()
	#text_config.insert(1.0,'api_key: '+api_key+'\n' )
	#text_config.insert(2.0,'secret_key: '+secret_key+'\n' )
	#text_config.insert(3.0,'number: '+sell_number+'\n' )
	if (sell_number=='' or buy_number==''):
		tk.messagebox.showerror(title='配置错误',message='Error, 请输入卖出、买进数量')
	else:
		nowt=int(time.time())
		nowtime=str(nowt)
		priceBuy=price()
		sell(api_key,sell_number,priceBuy,nowtime,tradepwd,secret_key)
		buy(api_key,buy_number,priceBuy,nowtime,tradepwd,secret_key)




def order():
	api_key = var_api_key.get()
	secret_key = var_secret_key.get()
	nowt=int(time.time())
	nowtime=str(nowt)
	srcOrders='api_key={}&coin=cam_eth&nonce={}&timestamp={}{}'.format(api_key,ran,nowtime,secret_key)
	srcOrdersmd5=curlmd5(srcOrders)
	body={'api_key':api_key,'coin':'cam_eth','nonce':ran ,'timestamp': nowtime,'sign': srcOrdersmd5}
	openOrders=requests.post(url='https://api.bit-z.com/api_v1/openOrders', data=body) 
	ordersinfo=json.loads(openOrders.text)
	#print('this is my orders : ', ordersinfo["data"])
	orderdata=ordersinfo["data"]
	if len(orderdata)==0:
		text_config.insert(END,'订单信息：交易已完成，此时无订单'+'\n')
	else:
		for i in range(len(orderdata)):
			text_config.insert(END,'订单信息--'+'id:'+str(orderdata[i]["id"])+' 总数量：'+str(orderdata[i]["number"])+' 剩余数量：'+str(orderdata[i]["numberover"])+' 类型：'+str(orderdata[i]["flag"])+'\n')

def balance():
	api_key = var_api_key.get()
	secret_key = var_secret_key.get()
	nowt=int(time.time())
	nowtime=str(nowt)
	srcBalances='api_key={}&nonce={}&timestamp={}{}'.format(api_key,ran,nowtime,secret_key)
	srcBalancesmd5=curlmd5(srcBalances)
	balances=requests.post(url='https://api.bit-z.com/api_v1/balances', data={'api_key':api_key,'nonce':ran ,'timestamp': nowtime,'sign': srcBalancesmd5}) 
	balancesinfo=json.loads(balances.text)
	#print('this is my eth : ',balancesinfo["data"]["eth"])
	#print('this is my eth_over : ',balancesinfo["data"]["eth_over"])
	#print('this is my eth_lock : ',balancesinfo["data"]["eth_lock"])
	#print('this is my cam : ',balancesinfo["data"]["cam"])
	#print('this is my cam_over : ',balancesinfo["data"]["cam_over"])
	#print('this is my cam_lock : ',balancesinfo["data"]["cam_lock"])
	text_config.insert(END,'My eth:'+str(balancesinfo["data"]["eth"])+'     My eth_lock: '+str(balancesinfo["data"]["eth_lock"])+'\n')
	text_config.insert(END,'My cam:'+str(balancesinfo["data"]["cam"])+'   My eth_lock: '+str(balancesinfo["data"]["cam_lock"])+'\n')



#撤销委托单
def selltradeCancel():
	api_key = var_api_key.get()
	secret_key = var_secret_key.get()
	
	#sellid=691261630	
	while 1:
		nowt=int(time.time())
		nowtime=str(nowt)
		srcOrders='api_key={}&coin=cam_eth&nonce={}&timestamp={}{}'.format(api_key,ran,nowtime,secret_key)
		srcOrdersmd5=curlmd5(srcOrders)
		body={'api_key':api_key,'coin':'cam_eth','nonce':ran ,'timestamp': nowtime,'sign': srcOrdersmd5}
		openOrders=requests.post(url='https://api.bit-z.com/api_v1/openOrders', data=body) 
		ordersinfo=json.loads(openOrders.text)		  		
		orderdata=ordersinfo["data"]
		if len(orderdata)>0:
			sellid=ordersinfo["data"][0]["id"] 
			srcTradeCancel='api_key={}&id={}&nonce={}&timestamp={}{}'.format(api_key,sellid,ran,nowtime,secret_key)
			srcTradeCancelmd5=curlmd5(srcTradeCancel)
			tradeCancel=requests.post(url='https://api.bit-z.com/api_v1/tradeCancel', data={'api_key':api_key,'id':sellid,'nonce':ran ,'timestamp': nowtime,'sign': srcTradeCancelmd5}) 
			tradeCancelinfo=json.loads(tradeCancel.text)
			print('this is my selltradeCancelinfo : ', tradeCancelinfo)
			text_config.insert(END,'撤销信息: '+str(tradeCancelinfo)+'\n')
		else:
			text_config.insert(END,'撤销完成！ '+'\n')
			break

def startsell():
    global is_start
    is_start=True
    thread=threading.Thread(target=sellbutton)
    thread.start()


def stopsell():
    global is_start
    is_start=False


def startdeal():
    global deal_start
    deal_start=True
    threadDeal=threading.Thread(target=dealbutton)
    threadDeal.start()


def stopdeal():
    global deal_start
    deal_start=False

def startcancel():
    threadCancel=threading.Thread(target=selltradeCancel)
    threadCancel.start()




but_startdeal = tk.Button(window,text='开始交易',command=startdeal)
but_startdeal.place(x=140, y=310)
but_stopdeal = tk.Button(window,text='停止交易',command=stopdeal)
but_stopdeal.place(x=210, y=310)
but_balance = tk.Button(window,text='我的资产',command=balance)
but_balance.place(x=280, y=310)
but_balance = tk.Button(window,text='我的交易',command=order)
but_balance.place(x=350, y=310)
but_balance = tk.Button(window,text='最新信息',command=ticker)
but_balance.place(x=420, y=310)
text_config = tk.Text(window,height=15,width=95)
text_config.place(x=80,y=350)


#but_sell = tk.Button(window,text='卖出',command=sellbutton)
#but_sell.place(x=100, y=425+130)

#but_sell = tk.Button(window,text='开始卖出',command=startsell)
#but_sell.place(x=140, y=425+130)
#but_sell = tk.Button(window,text='停止卖出',command=stopsell)
#but_sell.place(x=210, y=425+130)
but_sell = tk.Button(window,text='撤销卖出',command=startcancel)
but_sell.place(x=280, y=425+130)

#tk.Label(window, text='卖出信息 ').place(x=180, y= 425)
text_sell = tk.Text(window,height=10,width=95)
text_sell.place(x=80,y=460+130)


#tk.Label(window, text='买入信息 ').place(x=170, y= 430+130+105)
#text_buy = tk.Text(window,height=5,width=85)
#$text_buy.place(x=90,y=460+130+100)



#ticker=requests.get(url='https://api.bit-z.com/api_v1/ticker?coin=cam_eth')
#t=json.loads(ticker.text)
#print(t) 



#tradeCancel()

button = Button(window, text='QUIT',command=window.quit,activeforeground='white',activebackground='red')  #创建按钮，command为回调函数
button.pack(side='bottom') #fill=tkinter.X表示横向拉伸完全

window.mainloop()


#print('啊哈哈哈')
