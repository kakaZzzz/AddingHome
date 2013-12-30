AddingHome
==========

##目录结构

    code    -- 网站程序，包括php/js/css/html代码，和开源框架
    config  -- 配置文件，包括nginx/php配置，以及从网站逻辑中分离出的配置
    shell   -- 脚本，包括部署测试/开发环境、重启服务等

##功能开发

###日程表

    1. 请求http://addinghome.com/schedule.php?p=2013-12-30&m=3+4
    2. 根据p和m参数，分析出具体日期范围
        ——从周到天数转换：(n-1)*7+1~n*7
            ——例如：第3周15~21，第4周22~28
        ——从2013年12月30日开始算，算出其后第15天到21天（孕期第3周），和第22天到28天（孕期第4周）
    3. 从数据库中查找第15到第28天的事件
        ——adding_base/app_event_for_all
    4. 最后输出json格式
        {
        	"m3": {
        		"resultCount": 2,
        		"results": [
        			{
        				"data": "2013-12-25",
        				"eventType": 1,
        				"hash": "articl=1234&page=2",
                                        "title": "吃苹果",
        				"description": "丫今儿该吃苹果了",
        				"expire": "2013-12-30",
        				"icon": "default.ico",
        				"bg": "0xfff",
        				...
        			},
        			{...}
        		]
        	},
        	"m4": {...}
        }
