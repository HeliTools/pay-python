python 支付整合开发包
=====================

轻量级支付方式整合集成，实现支付与业务完全剥离，快速简单完成支付模块的开发

特性
----

1. 屏蔽支付方式之间接入API和数据结构的差异，统一API和数据结构
2. 支持支付类型横向扩展
3. 统一异常处理

支持支付方式及功能
------------------

支付方式
~~~~~~~~

-  支付宝(\ ``pay_type=ali_pay``)
-  微信支付(\ ``pay_type=wx_pay``)

通用功能
~~~~~~~~

-  `电脑网站支付 <#trade_page_pay>`__
-  `手机网站支付 <#trade_wap_pay>`__
-  `APP支付 <#trade_app_pay>`__
-  `异步通知校验 <#parse_and_verify_result>`__
-  `交易查询 <#trade_query>`__
-  `交易取消 <#trade_cancel>`__
-  `退款 <#trade_refund>`__
-  `退款查询 <#trade_refund_query>`__

平台特有功能
~~~~~~~~~~~~

-  `微信JS支付 <#trade_js_pay>`__
-  `微信企业付款到零钱 <#enterprise_pay>`__

使用说明
--------

核心说明
~~~~~~~~

-  配置(dict)

.. code:: shell

    ALIPAY_CONFIG = {
        'pay_type': 'ali_pay', # 必填 区分支付类型
        'app_id': 'xxx', #必填 应用id
        'private_key_path': 'xxx', #必填 私钥
        'public_key_path': 'xxx',#必填 公钥
        'notify_url': 'xxx',# 异步回调地址
        'sign_type': 'RSA2',  # 签名算法 RSA 或者 RSA2
        'debug': False, # 是否是沙箱模式
    }

    WECHAT_CONFIG = {
        'pay_type': 'wx_pay', # 必填 区分支付类型
        'app_id': 'xxx',  # 必填,应用id
        'mch_key': 'xxx',  # 必填,商户平台密钥
        'mch_id': 'xxx',  # 必填,微信支付分配的商户号
        'app_secret': 'xxx', # 应用密钥
        'notify_url': 'xxx'# 异步回调地址
        'api_cert_path': 'xxx', # API证书
        'api_key_path': 'xxx' # API证书 key
    }

其中
``pay_type``\ 为本项目所需，用来区分支付类型，其余为对应支付方式所需配置参数，具体参考对应支付方式对应的官方文档。

-  `Pay <https://github.com/adisonhuang/pay-python/blob/master/pay/pay.py>`__

**支付网关，支付方式分配和转发入口**

-  `PayOrder <https://github.com/adisonhuang/pay-python/blob/master/pay/pay_order.py>`__

**统一封装支付订单信息，主要用于支付下单** 生成统一订单例子

.. code:: python

    order = PayOrder.Builder().subject('商品标题') .out_trade_no('商品订单号').total_fee('商品费用').build()

通过\ ``Builder模式+链式调用``\ 灵活组合通用参数和特殊参数
更多参数说明参见\ `源码 <https://github.com/adisonhuang/pay-python/blob/master/pay/pay_order.py>`__

-  `PayResponse <https://github.com/adisonhuang/pay-python/blob/master/pay/pay_response.py>`__

是\ **统一封装支付返回业务信息，主要用于支付查询**

生成统一回单例子

.. code:: python

    response = PayResponse.Builder().trade_no('平台订单号').out_trade_no('商家订单号').build()

通过\ ``Builder模式+链式调用``\ 灵活组合通用参数和特殊参数
更多参数说明参见\ `源码 <https://github.com/adisonhuang/pay-python/blob/master/pay/pay_response.py>`__

demo
~~~~

.. code:: python

    ALIPAY_CONFIG = {
        'pay_type': 'ali_pay', # 必填 区分支付类型
        'app_id': 'xxx', #必填 应用id
        'private_key_path': 'xxx', #必填 私钥
        'public_key_path': 'xxx',#必填 公钥
        'notify_url': 'xxx',# 异步回调地址
        'sign_type': 'RSA2',  # 签名算法 RSA 或者 RSA2
        'debug': False, # 是否是沙箱模式
    }
    # 额外参数，某些支付方式有些选填的参数在PayOrder并没有封装，可以自行传递
    extra_params= {
        'xxx':'xxx'
        'xxx':'xxx'
        'xxx':'xxx'
    }
    order = PayOrder.Builder().subject('商品标题') .out_trade_no('商品订单号').total_fee('商品费用').build()
    pay = Pay(ALIPAY_CONFIG) # 传入对应支付方式配置
    order_res= pay.trade_page_pay(order,extra_params)# 传入对应订单和额外参数（要是需要）

功能说明
~~~~~~~~

电脑网站支付[trade\_page\_pay]
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    pay = Pay(ALIPAY_CONFIG) # 传入对应支付方式配置
    order_res= pay.trade_page_pay(order)# 传入对应订单

手机网站支付[trade\_wap\_pay]
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    pay = Pay(ALIPAY_CONFIG) # 传入对应支付方式配置
    order_res= pay.trade_wap_pay(order)# 传入对应订单

APP支付[trade\_app\_pay]
^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    pay = Pay(ALIPAY_CONFIG) # 传入对应支付方式配置
    order_res= pay.trade_app_pay(order)# 传入对应订单

异步通知校验[parse\_and\_verify\_result]
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    # 传入对应支付方式配置
    pay = Pay(WECHAT_CONFIG)
    # 传入对应支付方式返回的原始数据，校验成功会返回解析成json数据
    data = pay.parse_and_verify_result(req_xml)

微信JS支付[trade\_js\_pay]
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    # 传入对应支付方式配置
    pay = Pay(WECHAT_CONFIG)
    # 传入对应订单
    data = pay.trade_js_pay(order)

微信企业付款到零钱[enterprise\_pay]
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    # 传入对应支付方式配置
    pay = Pay(WECHAT_CONFIG)
    # 传入对应订单
    data = pay.enterprise_pay(order)

交易查询[trade\_query]
^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    # 传入对应支付方式配置
    pay = Pay(WECHAT_CONFIG)
    # 传入对应回单信息
    data = pay.trade_query(response)

交易取消[trade\_cancel]
^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    # 传入对应支付方式配置
    pay = Pay(WECHAT_CONFIG)
    # 传入对应回单信息
    data = pay.trade_cancel(response)

退款[trade\_refund]
^^^^^^^^^^^^^^^^^^^

.. code:: python

    # 传入对应支付方式配置
    pay = Pay(WECHAT_CONFIG)
    # 传入对应回单信息
    data = pay.trade_refund(response)

退款查询[trade\_refund\_query]
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    # 传入对应支付方式配置
    pay = Pay(WECHAT_CONFIG)
    # 传入对应回单信息
    data = pay.trade_refund_query(response)

贡献
----

本项目目前支持的支付方式和API还不多，欢迎你给本项目提pull
request，扩展新的的支付接口，同时如果你有好的意见或建议，也欢迎给本项目提issue

声明：
------

本项目主要目标的是支付整合，统一支付API和数据结构，在具体支付模块的接入实现参考了一些开源项目

支付宝模块基于\ `python-alipay-sdk <https://github.com/fzlee/alipay>`__

微信模块基于\ `wx\_pay\_python <https://github.com/Jolly23/wx_pay_python>`__
