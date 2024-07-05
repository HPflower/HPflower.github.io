---
title: Python简单实现新大陆云平台API调用
date: 2024-7-6 2:19:00 +0800
categories: [Blog, Python]
tags: [Python,新大陆云平台,API]
---


# 前言
比完赛后浅浅的记录一下，万一以后用到了呢 (想来在这里应该不会有别人能看到吧？)

# 内容
```
'''
Author: HPflower
Date: 2024-07-06 01:42:04
LastEditors: HPflower
LastEditTime: 2024-07-06 02:16:00
Description:  新大陆云平台API调用库
'''

import requests
import json

'''
description: 从传感器返回数据中提取值，支持单个/多个传感器中同时提取值(可能有潜在的bug,请谨慎使用)
param {*} Response
return {*}
'''
def Get_Value_From_SensorData(Response):
    Value = []
    try:
        for Index in range(len(Response['ResultObj'])):
            Value.append(Response['ResultObj'][Index]['Value'])
        return Value
    except:
        Value.append(Response['ResultObj']['Value'])
        return Value



'''
description: 获取新大陆云平台的 APIToken
param {*} Account
param {*} Password
return {*}
'''
def Login(Account, Password):
    api_url = "http://api.nlecloud.com/Users/Login"
    data = {
        "Account": Account,
        "Password": Password
    }
    login_response = requests.post(
        url=api_url,
        data=data,
    )
    login_data = json.loads(login_response.text)
    return login_data['ResultObj']['AccessToken']



'''
description: 获取某个项目的信息
param {*} API_Token
param {*} Project_ID
return {*}
'''
def Project_Info_Get(API_Token, Project_ID):
    headers = {
        "AccessToken": API_Token
    }
    api_url = f"http://api.nlecloud.com/Projects/{Project_ID}"
    response = requests.get(
        url=api_url,
        headers=headers
    )
    Data = json.loads(response.text)
    return Data



'''
description: 获取某个设备的信息
param {*} API_Token
param {*} Device_ID
return {*}
'''
def Device_Info_Get(API_Token, Device_ID):
    headers = {
        "AccessToken": API_Token
    }
    api_url = f"http://api.nlecloud.com/Devices/{Device_ID}"
    response = requests.get(
        url=api_url,
        headers=headers
    )
    Data = json.loads(response.text)
    return Data



'''
description: 获取单个传感器的信息
param {*} API_Token
param {*} Device_ID
param {*} Sensor_Tag
return {*}
'''
def Sensor_Info_Get(API_Token,Device_ID,Sensor_Tag):
    headers = {
        "AccessToken": API_Token
    }
    api_url = f"http://api.nlecloud.com/devices/{Device_ID}/Sensors/{Sensor_Tag}"
    response = requests.get(
        url=api_url,
        headers=headers
    )
    Data = json.loads(response.text)
    return Data



'''
description: 获取多个传感器信息
param {*} API_Token
param {*} Device_ID
param {array} Sensor_Tag
return {*}
'''
def Sensor_Info_Get_Vaguely(API_Token,Device_ID,*Sensor_Tag):
    headers = {
        "AccessToken": API_Token
    }
    if Sensor_Tag:
        params = {
            "apiTags": ",".join(Sensor_Tag)
        }
    api_url = f"http://api.nlecloud.com/devices/{Device_ID}/Sensors"
    response = requests.get(
        url=api_url,
        headers=headers,
        params=params
    )
    Data = json.loads(response.text)
    return Data



'''
description: 查询某个策略的信息
param {*} API_Token
param {*} Device_ID
return {*}
'''
def Strategy_Info_Get(API_Token, Device_ID):
    headers = {
        "AccessToken": API_Token
    }
    api_url = "http://api.nlecloud.com/Strategys"
    #params = {
    #    "strategyId":Device_ID,
    #}
    response = requests.get(    #params=params 此处可添加策略ID来查询指定策略，但是没在云平台找到相关ID号
        url=api_url,
        headers=headers
    )
    data = response.json()
    return data



'''
description: 使用命令去控制设备
param {*} API_Token
param {*} Device_ID
param {*} Target_Tag
return {*}
'''
def Send_Cmd_or_Control_Device(API_Token,Device_ID,Target_Tag,Data):
    api_url = "http://api.nlecloud.com/Cmds"
    headers = {
        "AccessToken": API_Token
    }
    params = {
        "deviceId":Device_ID,
        "apiTag": Target_Tag
    }
    post = requests.post(
        url = api_url,
        headers = headers,
        json = Data,
        params = params
    )
    # 返回响应数据
    return post.text



#example:
#API_Token = Login(账号,密码)
#print(Send_Cmd_or_Control_Device(API_Token,969983,"m_fan",1))
#print(Device_Info_Get(API_Token,969983))
#print(Project_Info_Get(API_Token, 909323))
#print(Sensor_Info_Get(API_Token,969983,"m_temperature"))
#print(Strategy_Info_Get(API_Token,969983))
#print(Get_Value_From_SensorData(Sensor_Info_Get(API_Token,969983,"m_temperature")))
#print(Get_Value_From_SensorData(Sensor_Info_Get_Vaguely(API_Token,969983,"m_temperature","m_humidity")))
```
