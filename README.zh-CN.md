# UtcPay Elements

[English](./README.md)|简体中文

![GitHub release version](https://img.shields.io/github/release/UtcPayProtocol/utcpay-elements)
![GitHub release downloads](https://img.shields.io/github/downloads/UtcPayProtocol/utcpay-elements/total)
![GitHub License](https://img.shields.io/github/license/UtcPayProtocol/utcpay-elements)

将UtcPay支付页面嵌入到您的网站或应用程序中的JavaScript SDK。

## 快速开始

从GitHub获取最新的[发布版本](https://github.com/UtcPayProtocol/utcpay-elements/releases/latest)，下载并解压缩utcpay-elemets.zip文件，将解压出的内容放到您的项目目录中。

我们提供ESM和UMD两种模块化的构建版本，您可以根据自己的项目需要选择合适的版本。

UtcPay Elements SDK需要配合UtcPay商户API使用，您可以在[UtcPay Docs](https://doc.utcpay.com/)中查看如何[创建收单订单](https://doc.utcpay.com/merchant/payment#%E5%88%9B%E5%BB%BA%E6%94%B6%E5%8D%95%E8%AE%A2%E5%8D%95)。

### ESM

如果您的项目使用ESM模块化，可以直接引入`utcpay-elements.js`文件和css文件。ESM同时支持原生HTML和React、Vue等框架。

```html
<!doctype html>
<html lang="en">

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Your website title</title>
  <script type="module">
    // 引入utcpay-elements.js
    import UtcPayElements from './utcpay-elements.js';
    // 解析paymentUrl中的apiSign, paymentUrl是从UtcPay商户API中创建收单订单时返回的paymentUrl
    const paymentUrl = '/payment?apiSign=gKLedHpHBzAmVMNq1cVgox7QGrwg%2FYH8Igp0%2Fv1GX3c%3D';
    const encondedApiSign = paymentUrl.split('=')[1];
    const apiSign = decodeURIComponent(encondedApiSign);
    // 初始化配置
    const Payment = new UtcPayElements({
      apiSign: apiSign,
      // 挂载点的id
      root: 'payment',
      // 是否使用沙盒环境
      sandbox: true,
    });
    // 挂载
    Payment.mount();
    // 如果要更新apiSign，可以调用updateApiSign方法
    // let newApiSign = '...';
    // Payment.updateApiSign(newApiSign);
  </script>
  <!-- 引入utcpay-elements.css -->
  <link rel="stylesheet" href="./utcpay-elements.css">
</head>

<body>
  <!-- 提供一个挂载点 -->
  <div id="payment"></div>
</body>

</html>
```

### UMD

如果您的项目使用UMD模块化，可以直接引入`utcpay-elements.umd.cjs`文件和css文件。

```html
<!doctype html>
<html lang="en">

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Your website title</title>
  <!-- 引入utcpay-elements.umd.cjs -->
  <script src="./utcpay-elements.umd.cjs"></script>
  <!-- 引入utcpay-elements.css -->
  <link rel="stylesheet" href="./utcpay-elements.css">
</head>

<body>
  <!-- 提供一个挂载点 -->
  <div id="payment"></div>
  <!-- 脚本需要在挂载点加载之后执行 -->
  <script>
    // 解析paymentUrl中的apiSign, paymentUrl是从UtcPay商户API中创建收单订单时返回的paymentUrl
    const paymentUrl = '/payment?apiSign=gKLedHpHBzAmVMNq1cVgox7QGrwg%2FYH8Igp0%2Fv1GX3c%3D';
    const encondedApiSign = paymentUrl.split('=')[1];
    const apiSign = decodeURIComponent(encondedApiSign);
    // 初始化配置
    const UtcPay = new UtcPayElements({
      apiSign: apiSign,
      // 挂载点的id
      root: 'payment',
      // 是否使用沙盒环境
      sandbox: true,
    });
    // 挂载
    UtcPay.mount();
    // 如果要更新apiSign，可以调用updateApiSign方法
    // let newApiSign = '...';
    // Payment.updateApiSign(newApiSign);
  </script>
</body>

</html>
```

## 注意事项

- 如果用户没有安装Web3钱包，将无法完成支付。
- 如果用户没有在规定时间内完成支付，将被引导返回前一页以便重新下单或者选择支付方式。
- 如果用户成功支付，将被引导跳转到创建订单时指定的`redirectionUrl`。同时UtcPay将会向您指定的`notifyUrl`发送支付结果。您也可以通过轮询商户API的订单信息接口来获取支付结果。

## API

### UtcPayElements类

#### 构造函数

```typescript
new UtcPayElements(options: UtcPayElementsOptions)
```

创建一个 UtcPayElements 实例。

#### 参数

- `options`：`UtcPayElementsOptions`类型，初始化配置。

`UtcPayElementsOptions`类型定义如下：

| 参数名        | 类型          | 必填 | 默认值 | 描述                                                                                                                                                                                                                                                      |
| ------------- | ------------- | ---- | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| apiSign       | string        | 是   | 无     | 从UtcPay商户API中创建收单订单时返回的paymentUrl中解析出的apiSign，每一笔订单的apiSign都是唯一的                                                                                                                                                           |
| root          | string        | 是   | 无     | HTML挂载点的id                                                                                                                                                                                                                                            |
| sandbox       | boolean       | 否   | false  | 是否使用沙盒环境，如果为true，将使用沙盒环境，否则使用正式环境                                                                                                                                                                                            |
| customTheme   | ThemeOverride | 否   | 无     | 自定义主题，如果不传，将使用默认主题，具体可定义的内容参考下文                                                                                                                                                                                            |
| customCKTheme | any           | 否   | 无     | UtcPay Elements使用了[ConnectKit](https://docs.family.co/connectkit)来连接以太坊钱包，您可以自定义ConnectKit组件的主题，如果不传，将使用默认主题，文档可参考[customTheme](https://docs.family.co/connectkit/api-reference#connectkitprovider-customtheme) |

#### 方法

##### mount

```typescript
mount(): void
```

挂载支付页面。

##### updateApiSign

```typescript
updateApiSign(newApiSign: string): void
```

更新apiSign并刷新支付页面。

## 主题定制

### 简单定制

通过设置`customTheme`参数，可以简单定制支付页面的主题。

例如：
```typescript
const Payment = new UtcPayElements({
  apiSign,
  root: 'payment',
  customTheme: {
    colorPrimary: '#2D20E8',
    colorBackground: '#C7E8E6',
    colorText: '#232CA0',
    fontFamily: 'Comic Sans MS',
    fontSizeBase: '14px',
    spacingUnit: '3px',
    borderRadiusBase: '0px',
    colorBorder: '#8ABEF0',
  },
})
```

所有可定制的主题属性如下（属性值的类型需符合CSS规范，类型为字符串或数字）：

| 属性名                 | 默认值                                                     | 描述                                                                                     |
| ---------------------- | ---------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| fontFamily             | 'Inter', system-ui, 'Avenir', Helvetica, Arial, sans-serif | 字体，可能需要另外提供字体文件                                                           |
| fontSizeBase           | 16px                                                       | 基础字体大小，会覆盖HTML根节点的字体大小                                                 |
| fontSizeSm             | 0.875rem                                                   | 小号字体大小，用于元素内部和次要文字                                                     |
| fontSizeLg             | 1.25rem                                                    | 大号字体大小，用于标题等内容                                                             |
| lineHeightNone         | 1                                                          | 1倍行高                                                                                  |
| lineHeightTight        | 1.25                                                       | 1.25倍行高，用于紧凑的内容                                                               |
| lineHeightNormal       | 1.5                                                        | 1.5倍行高，用于正常显示的文本                                                            |
| spacingUnit            | 0.25rem                                                    | 间距单位，各元素内外边距的值都是间距单位的倍数，使用到的倍数有0，1，2，3，4，5，6，8，10 |
| borderWidth            | 1px                                                        | 边框宽度                                                                                 |
| borderRadiusBase       | 0.25rem                                                    | 基础圆角，最小尺寸                                                                       |
| borderRadiusMd         | borderRadiusBase的2.5倍                                    | 中等圆角，用于大尺寸按钮等元素                                                           |
| borderRadiusLg         | borderRadiusBase的5倍                                      | 大圆角，用于模态窗等元素                                                                 |
| borderRadiusPill       | borderRadiusBase的200倍                                    | 胶囊圆角，用于胶囊按钮等元素                                                             |
| colorPrimary           | #206bc4                                                    | 主题色或品牌色                                                                           |
| colorPrimaryLight      | rgba(192, 218, 247, 0.28)                                  | 主题色的浅色，用于元素背景或hover状态                                                    |
| colorBackground        | #ffffff                                                    | 背景色                                                                                   |
| colorText              | #182433                                                    | 文字颜色                                                                                 |
| colorTextSecondary     | #6673b2                                                    | 次要文字颜色                                                                             |
| colorDanger            | #d63939                                                    | 危险色                                                                                   |
| colorDangerLight       | #fbebeb                                                    | 危险色的浅色                                                                             |
| colorWarning           | #f76707                                                    | 警告色                                                                                   |
| colorWarningLight      | #fefoe6                                                    | 警告色的浅色                                                                             |
| colorSuccess           | #2fb344                                                    | 成功色                                                                                   |
| colorSuccessLight      | #eaf7ec                                                    | 成功色的浅色                                                                             |
| colorInputShadow       | #accaed                                                    | 输入框聚焦时的阴影颜色                                                                   |
| colorModalShadow       | rgba(131, 186, 219, 0.2)                                   | 模态窗的阴影颜色                                                                         |
| colorBackdrop          | rgba(131, 186, 219, 0.2)                                   | 模态窗的遮罩背景色                                                                       |
| colorBorder            | #eaeaea                                                    | 边框颜色                                                                                 |
| modalZIndex            | 500                                                        | 模态窗的z-index                                                                          |
| colorBackgroundDark    | #000000                                                    | 深色模式下的背景色                                                                       |
| colorTextDark          | #f6f6f6                                                    | 深色模式下的文字颜色                                                                     |
| colorTextSecondaryDark | #858585                                                    | 深色模式下的次要文字颜色                                                                 |
| colorBorderDark        | #686868                                                    | 深色模式下的边框颜色                                                                     |

### 深度定制

您可以通过修改`utcpay-elements.css`文件或者覆盖相关样式来深度定制支付页面的主题。

## 许可证

MIT
