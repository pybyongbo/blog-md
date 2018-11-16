---
title: Async和Await用法以及Promise的理解
tags:
  - web前端(微信小程序)
  - 工作笔记
categories: web技术(JS异步操作)
abbrlink: 3de22925
date: 2018-09-08 10:33:53
---

异步操作是 Javascript 编程的麻烦事,麻烦到一直有人提出各种各样的方案,试图来解决这个问题,从最早的回调函数,到`Promise`,到 ES7 的`async`和`await`,每次都有改进,但又让人觉得不彻底,他们都有额外的复杂性,都需要理解抽象底层运行机制.

ES7 引入的 async/await 是 Javascript 异步编程的一个重大改进.提供了在不阻塞主线程的情况下使用同步代码异步访问资源的能力,async可以让一个方法变成异步,await必须用在爱上一年从内部,下面结合自己实际项目中遇到的问题,和自己对它的一些理解,将从不同的实例说明 async/await 的用法:

<!-- more -->

### async/await 的好处

async/await 给我们带来的重要好处是同步编程风格.看下下面的例子:

```javascript
// async/await
async getBooksByAuthorwithAwait(authorId) {

  const books = await bookModel.fetchAll(); //接口获取数据方法

  return books.fillter(b=>{b.authorId === authorId})

}
//Promise

getBooksByAuthorwithAwait(authorId) {
  return bookModel.fetchAll().then(books=>books.fillter(b=>{
      b.authorId === authorId
  }))
}
```

很显然,async/await 比 Promise 更容易理解,如果忽略掉 await 关键字,代码看起来与其他任意一门同步语言一样.

另外一个好处是 async 关键字,尽管看起来不是很明显,它申明 getBooksByAuthorwithAwait() 函数的返回值的是一个 Promise,因此调用者可以安全的调用

getBooksByAuthorwithAwait().then(...) 或 await getBooksByAuthorwithAwait().比如像下面这样的代码:

```javascript
getBooksByAuthorWithPromise(authorId) {

  if(!authorId) {
    return null
  }

  return bookModel.fetAll().then(books=>books.fillter(b=>b.authorId === authorId))
}
```

在上面的代码中,getBooksByAuthorWithPromise 可能返回一个 Promise(正常情况) 或 null(异常情况),在这种情况下,调用者无法安全的调用.then().而如果使用 async 声明,则不会出现这种情况.

async/await 是一种改进,但它不过是一种语法糖,他不会完全改变我们的编程风格.从本质上讲,异步函数仍然是 Promise,在正确的使用异步函数之前,你必须了解 Promise,更糟糕的是,大部分时间需要同时使用 Promise 和异步函数.

虽然 await 可以让你的代码看起来像是同步的,但请记住,它仍然是异步的.

在做公司项目 SAAS 系统的时候,就遇到了相关的问题.编辑 SKU 信息,进行保存的操作.要判断是否有重复的 sku 信息,然后前端进行操作提示.然而自己对基础知识理解的不透彻,导致折腾了好一会儿(基于 Vue+ElemeUI 框架).

先看下实际的页面效果吧,我截了个图,此处表单因为多处使用,分离成了一个 vue 的组件引用.

项目截图:
![编辑sku保存](/uploadimg/saas_1.png "编辑sku保存")

点击保存方法:

```javascript
//保存sku信息方法(要区分新增保存还是编辑保存操作,新增前端操作数组，编辑保存调后端接口。)
async saveskuinfoData() {
      const sukItemSend = Object.assign({}, this.$refs.skuInfo.skuForm);
      sukItemSend.productId = this.$route.query.productId;
      this.$refs.skuInfo.editproskuRule();
      if (this.$refs.skuInfo.formFlag) {
        //验证表单通过
        // sukItemSend.skuId ===0 表示新增添加sku,不等于0表示编辑sku;
        this.submitskuDisabled = true;

        if (this.isSkuExist(sukItemSend) && sukItemSend.skuId === 0) {
          this.$message({
            message: "请不要重复添加相同SKU信息",
            type: "warning"
          });
          this.submitskuDisabled = false;
          return false;

        } else if (sukItemSend.skuId !== 0 && await this.isValidateIsExist(sukItemSend))  {
          //编辑sku保存情况 ,此处写了await,等待异步this.isValidateIsExist(sukItemSend)方法结果有返回了,在执行下面的代码,如果 saveskuinfoData 方法前不加 async 编辑器会报错
          // this.isValidateIsExist(sukItemSend).then(res=>{
          //     console.log(res)
          // });

          this.$message({
            message: '请不要重复添加相同SKU信息',
            type: "warning"
          });
          this.submitskuDisabled = false;
          return false;

        } else {
          updateSku(sukItemSend).then(response => {
            this.handleEditSkuinfoDialogClose();
            //新增方法修改只取商品详情里面的sku信息列表数据
            this.submitskuDisabled = false;
            this.updateproskuinfo(this.$route.query.productId);
          });
        }
      }
    },

//调接口判断是否有重复的sku,异步方法

async isValidateIsExist(sku) {
      //掉接口返回是否有重复sku方法
      let response =  await validateIsExist({
          id: sku.id,
          numUnit: sku.numUnit,
          packNum: sku.packNum,
          packUnit: sku.packUnit,
          productId: sku.productId,
          sellType: sku.sellType,
          tradeType: sku.tradeType
        })
        if(response.status === 200) {

            if(response.data.code === 1) {
                return true;
            } else {
                return false;
            }
        } else {

          this.$message({
            message: response.data.msg,
            type: "error"
          });

        }

    },
```

点击编辑操作,基本思路流程图说明:
![编辑流程图](/uploadimg/note1.png "弹框信息流程")

或者直接在编辑保存的情况下,调用 判断 sku 是否有重复的方法 (异步),也是一样的.提示信息可以用后端返回的提示语.

```javascript
saveskuinfoData() {
      const sukItemSend = Object.assign({}, this.$refs.skuInfo.skuForm);
      sukItemSend.productId = this.$route.query.productId;
      this.$refs.skuInfo.editproskuRule();
      if (this.$refs.skuInfo.formFlag) {
        //验证表单通过,sukItemSend.skuId ===0 表示新增添加sku,不等于0表示编辑sku;
        this.submitskuDisabled = true;
        if (this.isSkuExist(sukItemSend) && sukItemSend.skuId === 0) {
          this.$message({
            message: "请不要重复添加相同SKU信息",
            type: "warning"
          });
          this.submitskuDisabled = false;
          return false;

        } else if (sukItemSend.skuId !== 0 )  { //编辑sku保存情况 ,判断是否有重复的

          //直接调用 sku是否有重复的方法 (后端接口异步请求,此处没有用到async/await)
          this.isValidateIsExist(sukItemSend);

        } else {
          updateSku(sukItemSend).then(response => {
            this.handleEditSkuinfoDialogClose();
            //新增方法修改只取商品详情里面的sku信息列表数据
            this.submitskuDisabled = false;
            this.updateproskuinfo(this.$route.query.productId);
          });
        }
      }
    },
    async isValidateIsExist(sku) {
      //掉接口返回是否有重复sku方法
      let response =  await validateIsExist({
          id: sku.id,
          numUnit: sku.numUnit,
          packNum: sku.packNum,
          packUnit: sku.packUnit,
          productId: sku.productId,
          sellType: sku.sellType,
          tradeType: sku.tradeType
        })

        if(response.status === 200) {

            if(response.data.code === 1) { //有重复sku信息提示

                 this.$message({
                  message: response.data.msg,
                  type: "warning"
                });
                this.submitskuDisabled = false;
                return false;


            } else { //没有重复的sku,更新操作

                updateSku(sku).then(response => {
                  this.handleEditSkuinfoDialogClose();
                  //新增方法修改只取商品详情里面的sku信息列表数据
                  this.submitskuDisabled = false;
                  this.updateproskuinfo(this.$route.query.productId);
                });

            }
        } else {

          this.$message({
            message: response.data.msg,
            type: "error"
          });

        }

    },
```

### 其他模块引用方法

看了一下项目中,其他小伙伴的模块,都在大量的使用新语法,自己也要多用起来.统一代码风格,向大佬们靠齐.哈哈 😝

项目截图:
![其他模块](/uploadimg/saaa_2.png "其他模块")

通过此次问题的总结和归纳,也加深了自己对异步的一个理解,以及它的一个流程.新的东西流行起来,一定有它的优点和好处.所以自己也要不断的学习.

ES7 引入的 async/await 关键字绝对是对 Javascript 异步编程的重大改进.它让代码更易于阅读和调试.然而要正确的使用它们,必须了解 Promise.它们不过是语法糖,本质上仍然是 Promise.
