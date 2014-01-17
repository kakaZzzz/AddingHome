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
    
###提取post内容脚本
    
    1. 连接addinghome库里的wp_posts（ID）和wp_terms_relationships（object_id）表
    2. 找到term_taxonomy_id为x、y、z（可能更多）的具体条目
    3. 把结果新增到adding_base库的app_events_for_all表里
        ID->/blog/archives/ID->hash
        post_title->title
        post_content->截取30个字符（可调）->description
    4. app_events_for_all表里增加一个post_id字段，第3步操作里，如果表里有相同id则修改，反之新增

###日程表增强版

    1. 新的接口名"schedule_new"，请求http://addinghome.com/api/schedule_new?p=2013-12-30&t=2014-1-28&w=3+4
    2. 其他功能同日程表，但是同日期下，remind=1的条目排在前面
    3. w3里数据条目，增加空日期项，即只有“data”，其他都为空，按顺序排列
    4. 提取包含今天的提醒项，具体值来自url中“t”减去“p”的值，里面的每项查询条件是remind=1 && data <= today && expire >= today，取出后，插入到w3、w4的合适位置上，并跟重复的提醒项去重；
        若t参数的日期不在w3、w4范围内，数据抛弃；
        如果没有t参数，则不做这步运算
    5. 最后输出json格式
        {
        	"w3": {
        		"resultCount": 3,
        		"results": [
        			{
        				"data": "2013-12-25",
        				"image": "",
        				"hash": "",
                        "title": "",
        				"description": "",
        				"expire": "",
        				"icon": "",
        				"bg": "",
        				"remind": 0
        			},
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
    6. 简单来看，一个w3的数据项是
    
        t=1.17
        
        1.16 空
        1.16 提醒A（1.16~1.18）
        1.16 资讯A
        
        1.17 空
        1.17 提醒A
        1.17 提醒B（1.17~1.18）
        
        1.18 空
        1.18 资讯B
        
        1.19 空
        
        1.20 空
        
        1.21 空
        
        1.22 空
