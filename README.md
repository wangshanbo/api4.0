# api4.0
云合同API4.0对接文档
# 引用云合同 SDK JS文件

请勿下载yht.js到本地引用，当yht.js进行版本迭代后可能会产生不可预知的bug。
在你的页面引入yht.js，同时，如果你的页面编码不是UTF-8，请添加charset="utf-8"属性。
对接时必须引入jQuery!
``` <script type="text/javascript" charset="utf-8" src="https://api.yunhetong.com/api_page/api/yht.js"></script> ```

# 手机端引用:

``` <script type="text/javascript" charset="utf-8" src="https://api.yunhetong.com/api_page/api/m/yht.js"></script> ```

# 云合同初始化和Token管理

API4.0 对签名和合同的操作请求需要带上Token并对Token进行校验，对token超时、为空，返回码为400时，系统回调请求 Token 服务。

Token 的初始化：

```
var tokenUnableListener = function (obj) { //当token不合法时，SDK会回调此方法
    $.ajax({
      type: 'POST',
      url: 'token', //第三方服务器获取token的URL，云合同SDK无法提供
      cache: false,
      dataType: 'json',
      data: {}, //第三方获取token需要的参数
      beforeSend: function (xhr) {
        xhr.setRequestHeader('Content-Type', 'application/json;charset=UTF-8');
      },
      success: function (data, textStatus, request) {
        YHT.setToken(request.getResponseHeader("token")); //重新设置token  //从请求头获取token
        YHT.do(obj); //调用此方法，会继续执行上次未完成的操作
      },
      error: function (data) {
        alert(data);
      }
    });
  }
  YHT.init("AppID", tokenUnableListener); //必须初始化YHT
  ```
  
  # 云合同合同管理
  云合同合同管理模块是指第三方 App 对自己合同进行操作的功能，方法调用后跳转到云合同API合同详情页面。
  
  ```
   $.ajax({
    type: 'POST',
    async: false, //请使用同步
    url: 'contract', //第三方服务器获取合同ID的URL
    cache: false,
    dataType: 'json',
    data: {},
    success: function (data, textStatus, jqXHR) {
      var contractId = '';
      
      //合同查看方法
      YHT.queryContract(   
        function successFun(url) {
          window.open(url);
        },
        function failFun(data) {
          alert(data);
        },
        contractId
      );
      
      //合同签署页面
      YHT.signContract(
        function successFun(url) {
          window.open(url);
        },
        function failFun(data) {
          console.log(data);
        },
        contractId
      );
      
      //前置绘制签名页面
      YHT.dragSignF(
        function successFun(url) {
          window.open(url);
        },
        function failFun(data) {
          console.log(data);
        }
      );
    },
    error: function (data) {

    }
  });
  ```
  | 名称         |描述   			  |  类型     |参数             |
  | ------------ | --------------  | --------- |:---------------:
  | successFun   | 请求成功回调函数  | function | url : 跳转地址  |
  | failFun      | 请求失败回调函数  | function | data : 错误信息 |
  | contractId   | 合同ID号         | string   |                |
  
  回调参数传递后，可以通过消息回调获取。
  
