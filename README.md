AddingHome
==========

##目录结构

    code    -- 网站程序，包括php/js/css/html代码，和开源框架
    config  -- 配置文件，包括nginx/php配置，以及从网站逻辑中分离出的配置
    shell   -- 脚本，包括部署测试/开发环境、重启服务等

##功能开发

###日程表

    1. 请求http://addinghome.com/api/schedule?p=2013-12-30&w=3+4
    2. 根据p和m参数，分析出具体日期范围
        ——从周到天数转换：(n-1)*7+1~n*7
            ——例如：第3周15~21，第4周22~28
        ——从2013年12月30日开始算，算出其后第15天到21天（孕期第3周），和第22天到28天（孕期第4周）
    3. 从数据库中查找第15到第28天的事件（原始数据存的是第n天）
        ——adding_base/app_event_for_all
    4. 最后输出json格式
        {
        	"w3": {
        		"resultCount": 2,
        		"results": [
        			{
        				"data": "2013-12-25",
        				"image": "addinghome.jpg",
        				"hash": "/20131230/1/",
                        "title": "吃苹果",
        				"description": "丫今儿该吃苹果了",
        				"expire": "2013-12-30",
        				"icon": "default.ico",
        				"bg": "0xfff",
        				"remind": 0
        			},
        			{...}
        		]
        	},
        	"w4": {...}
        }

###保存IOS设备token

    1. 请求http://addinghome.com/api/iostoken?t=740f4707bebcf74f9b7c25d48e3358945f6aa01da5ddb387462c7eaf61bb78ad
    2. token长度为64字节
    3. 保存到数据库adding_base/app_ios_devices
    
    备注:保存数据成功返回 0； 保存失败返回 1； token长度不符则返回 2； 
    
###只访问Blog的文章内容
    请调用:http://www.addinghome.com/blog/app/文章id 
    例如：http://www.addinghome.com/blog/app/1
