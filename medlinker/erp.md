# ERP对接文档
## 对码
### 对码原则
* 规则1：本位码+批准文号+通用名+规格+生产厂家
* 规则2：批准文号+通用名+规格+生产厂家
* 规则3：通用名+规格+生产厂家
### 对码字段
| 字段名        | 中文列名   |  数据类型  | 是否必填 | 备注 |
| :--------:   | :-----:  | :----:  | :---------: | :-------: |
| genre      | 药学分类  |   无符号整型     | 否 | 1-中成药，2-西药 |
| standard_code        |   药品本位码   |   varchar(256)   | 否 | 多个用英文逗号隔开 |
| otc_type      | 是否处方药 | 整型 | 否 | -1未知，0-处方药，1-非处方药 |
| medicare_category_id | 医保类别 | 无符号整型 | 否 | 0-非医保、1-甲、2-乙、3-丙、4-民族药 |
| generic_name | 药品通用名 | varchar(100) | 是 | - |
| trade_name | 商品名称 | varchar(100) | 否 | - |
| en_name | 英文名 | varchar(200) | 否 | - |
| specification | 规格 | varchar(256) | 否 | - |
| formulation | 剂型 | varchar(20) | 否 | - |
| approval_number | 批准文号 | varchar(20) | 是 | - |
| product_type | 产品类型 | 无符号整型 | 否 | 等同cfda中的产品类型 ：0-未知，1-中成药，2-化学药品，3-生物制品，4-体外化学诊断试剂，5-药用>辅料，6-进口分包装药 |
| manufacturer_title | 生产单位 | varchar(100) | 是 | - |
| manufacturer_address | 生产地址 | varchar(200) | 否 | - |
| manufacturer_phone | 联系方式 | varchar(50) | 否 | - |
| approval_date | 批准日期 | 无符号整型 | 否 | 格式:yyyyMMdd |
| restrain_type | 限制分类 | 无符号整型 | 否 | - |
| minimum_sale | 起售数量 | 无符号整型 | 否 | - |
| sale_unit | 售卖单位 | varchar(8) | 否 | - |
| spu_sort | 自定义排序 | 无符号整型 | 否 | 自定义排序权重，越大越前 |
| spu_status | 状态 | 整型 | 否 | -1-无效，0-未知（默认），1-有效 |
| keywords | 搜索关键词 | varchar(400) | 否 | 由 通用名、通用名全拼、通用名拼音首字母、商品名、商品名全拼、商品名拼音首字母、生产厂家、标签 组成，之间用英文逗号隔开 |
| shelf_life | 有效期 | varchar(128) | 否 | - |
| tags | 标签 | text | 否 | - |
| indication | 功能主治/适应症 | text | 否 | - |
| ingredient | 成份 | text | 否 | - |
| usage | 用法用量 | text | 是 | - |
| pharmacological | 药理作用 | text | 否 | - |
| pharmacokinetics | 药动学 | text | 否 | - |
| drug_interaction | 药物相互作用 | text | 否 | - |
| adverse_reaction | 不良反应 | text | 否 | - |
| contraindication | 禁忌 | text | 否 | - |
| special_crowd_notice | 特殊人群用药 | text | 否 | - |
| consideration | 注意事项 | text | 否 | - |
| storage | 贮藏 | text | 否 | - |

## API文档
### 鉴权方式
对于ERP发起的接口，使用医联签发的token发起http调用。
在http header中使用medlinker_erp_token字段提交token。

> 注意: 此token不可泄露
### 协议格式
使用https协议作为交互协议。
其中http状态码作为协议握手状态码，如果http状态码为200则会继续检查下面响应格式中的status字段(逻辑状态码)。
#### 请求格式
请求格式按照约定的json格式提交数据
#### 响应格式
```
{
    "code": 0, // 0 响应正常 非0 则对应错误码
    "message": "error message", // code不为0时展示
    "data": ""              // 数据字段，保存的是下面响应格式的字符串，如果没有，则为空字符串
}
```
### 货品主数据对码
#### 请求格式
```
{
    "genre": 1,
    "standard_code": "1234",
    "otc_type": 1,
    "medicare_category_id": 1,
    "generic_name": "药品通用名",
    "trade_name": "商品名称",
    "en_name": "英文名称",
    "specification": "规格",
    "formulation": "剂型",
    "approval_number": "批准文号",
    "product_type": 1,
    "manufacturer_title": "生产单位",
    "manufacturer_address": "生产地址",
    "manufacturer_phone": "联系方式",
    "approval_date": 20161117,
    "restrain_type": 1,
    "minimum_sale": 10,
    "sale_unit": "售卖单位",
    "spu_sort": 1234,
    "spu_status": 1,
    "keywords": "搜索关键字",
    "shelf_life": "2019-12-31",
    "tags": "标签",
    "indication": "功能主治/适应症",
    "ingredient": "成份",
    "usage": "用法用量",
    "pharmacological": "药理作用",
    "pharmacokinetics": "药动学",
    "drug_interaction": "药物相互作用",
    "adverse_reaction": "不良反应",
    "contraindication": "禁忌",
    "special_crowd_notice": "特殊人群用药",
    "consideration": "注意事项",
    "storage": "贮藏",

    "goods_id": 1111,                       // erp内的货品id
    "erp_specification": "规格字符串"       // erp货品规格
}
```
#### 响应格式
```
{
}
```
### 库存同步
#### 请求格式
```
{
    "erp_store_id": "1234",             // erp内的药店id            类型: 字符串
    "goods_id": 1111,                   // erp内货品id              类型: 整型
    "entry_goods_id": 2222,             // erp内独立货品id          类型: 整型
    "stock": 10000                      // 库存                     类型: 整型
}
```
#### 响应格式
```
{
}
```
### 零售价格同步
#### 请求格式
```
{
    "erp_store_id": "1234",             // erp内的药店id            类型: 字符串
    "goods_id": 1111,                   // erp内货品id              类型: 整型
    "entry_goods_id": 2222,             // erp内独立货品id          类型: 整型
    "sale_price": 10000                 // 销售价格(单位: 分)       类型: 整型
}
```
#### 响应格式
```
{
}
```

### 药店首营
#### 请求格式
```
{
    "erp_store_id": "1234",             // erp内的药店id            类型: 字符串
    "goods_id": 1111,                   // erp内货品id              类型: 整型
    "entry_goods_id": 2222,             // erp内独立货品id          类型: 整型
    "sale_price": 10000                 // 销售价格(单位: 分)       类型: 整型
    "stock": 10000                      // 库存                     类型: 整型
}
```
#### 响应格式
```
{
}
```
