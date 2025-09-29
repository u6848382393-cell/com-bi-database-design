# COM_BI 数据库表关系图

## 📊 数据库实体关系图 (ERD)

```mermaid
erDiagram
    %% ===== 元数据管理层 =====
    my_data {
        bigint id PK
        bigint experiment_id FK
        varchar table_name
        text fields
        tinyint type
        varchar source
        bigint created_operator
        timestamp created_time
        bigint updated_operator
        timestamp updated_time
    }
    
    built_in_data {
        bigint id PK  
        bigint experiment_id FK
        varchar name
        tinyint industry
        tinyint source
        tinyint sensitivity_level
        tinyint access_permissions
        varchar describe
        varchar file
        int number
        bigint created_operator
        timestamp created_time
        bigint updated_operator
        timestamp updated_time
    }

    %% ===== 分析工具层 =====
    component {
        bigint id PK
        bigint experiment_id FK
        bigint created_operator
        timestamp created_time
        timestamp updated_time
        int type
        varchar name
        text content
    }
    
    large_screen {
        bigint id PK
        bigint experiment_id FK
        bigint created_operator
        timestamp created_time
        timestamp updated_time
        varchar name
        text content
        text component_ids FK
    }
    
    ai_history {
        bigint id PK
        bigint experiment_id FK
        bigint created_operator
        char type
        text history
    }

    %% ===== 用户数据样例表 =====
    user_data_sample {
        int pk_id PK
        bigint user_id
        varchar name
        varchar gender
        varchar phone_number
        varchar source_link
        varchar status
        varchar updated_by
        tinyint number_type
        mediumint number_type2
        decimal decimal_field
    }
    
    %% ===== 电商数据样例表 =====
    ecommerce_data_sample {
        int pk_id PK
        varchar order_number
        varchar brand
        varchar store
        varchar person_in_charge
        varchar product
        varchar color
        varchar customer_purchase_type
        smallint sales_volume
        decimal unit_price
        decimal sales_amount
        varchar is_discount
        smallint discount_amount
        decimal actual_unit_price
        decimal actual_payment
        varchar cost
        varchar profit
        tinyint customer_gender
        varchar customer_age
        varchar membership_status
        varchar is_page_visited
        tinyint page_visit_duration
        varchar transaction_status
        varchar product_status
        varchar recipient_name
        varchar recipient_phone
        varchar shipping_address
        varchar delivery_province
        varchar delivery_address
        varchar logistics_company
        varchar waybill_number
        varchar shipping_method
        varchar payment_time
        varchar estimated_arrival_time
        varchar actual_arrival_time
        varchar courier_feedback
        varchar is_returned
        varchar refund_reason
        varchar customer_satisfaction
        varchar date_of_birth
        varchar category
        varchar material
    }

    %% ===== 用户画像数据样例表 =====
    user_profile_sample {
        int pk_id PK
        smallint user_id
        varchar gender
        varchar phone_number
        varchar name
        varchar credit_card_number
        tinyint user_age
        decimal used_quota
        decimal balance
        date birthday
        varchar channel
        varchar has_credit_card
        varchar is_installment
        mediumint total_quota
        mediumint monthly_income
        varchar credit_card_level
        mediumint loan_amount
        varchar has_house
        varchar has_car
        tinyint number_of_children
        tinyint referrer_id
        smallint credit_score
        varchar activity_level
        varchar consumption_preference
        varchar city
        varchar occupation
        int total_assets
        mediumint annual_consumption_total
        tinyint credit_card_overdue_times
        tinyint recommended_friends_count
        smallint recent_consumption_interval
        tinyint annual_consumption_frequency
        decimal average_consumption_amount
    }

    %% ===== 关系定义 =====
    
    %% experiment_id 关联所有表
    my_data ||--o{ built_in_data : "experiment_id"
    my_data ||--o{ component : "experiment_id"  
    my_data ||--o{ large_screen : "experiment_id"
    my_data ||--o{ ai_history : "experiment_id"
    
    %% component 与 large_screen 关系
    component ||--o{ large_screen : "component_ids"
    
    %% my_data 通过 table_name 关联动态数据表
    my_data ||--o{ user_data_sample : "table_name"
    my_data ||--o{ ecommerce_data_sample : "table_name"
    my_data ||--o{ user_profile_sample : "table_name"
```

## 🏗️ 数据库架构说明

### 📋 表结构分类

| 分类 | 表名 | 功能描述 |
|------|------|----------|
| **元数据管理** | `my_data` | 数据集注册中心，管理所有动态数据表 |
| | `built_in_data` | 内置数据集库 |
| **分析工具** | `component` | 可复用分析组件 |
| | `large_screen` | 数据大屏展示 |
| | `ai_history` | AI分析历史记录 |
| **业务数据** | `*_my_data` | 动态创建的业务数据表（20+张） |

### 🔗 核心关联关系

1. **实验维度关联**
   ```
   experiment_id → 串联所有业务表
   ```

2. **数据表动态关联**
   ```
   my_data.table_name → 指向具体的动态数据表
   命名规则: {user_id}_{experiment_id}_my_data
   ```

3. **组件组合关系**
   ```
   large_screen.component_ids → 关联多个 component.id
   ```

### 📊 数据类型分布

#### 🧑‍💼 用户数据类型 (7张表)
- 基础用户信息：姓名、性别、手机号
- 来源渠道和状态管理
- 数字字段和时间字段

#### 🛒 电商数据类型 (13张表)
- **订单信息**: 订单号、品牌、店铺、负责人
- **商品信息**: 商品名、颜色、品类、材质
- **销售数据**: 销量、单价、销售额、成本、利润
- **客户信息**: 性别、年龄、会员状态、购买类型
- **物流信息**: 发货地址、物流公司、运单号、送达时间
- **交易状态**: 支付时间、交易状态、退货信息、满意度

#### 💳 用户画像类型 (1张表)  
- **基础信息**: 姓名、性别、年龄、生日、城市、职业
- **金融信息**: 信用卡、额度、余额、贷款、信用评分
- **资产信息**: 房产、车辆、子女、总资产
- **行为信息**: 消费偏好、活跃度、消费频次、推荐好友

### 🎯 业务应用场景

1. **📈 数据分析实验平台**
   - 多用户、多实验并行支持
   - 数据集版本管理和血缘追踪

2. **🛍️ 电商业务分析**
   - 订单分析、客户行为分析
   - 商品销售分析、物流效率分析
   - 营销效果评估

3. **👤 用户画像建模**
   - 金融用户信用评估
   - 消费行为预测
   - 风险控制分析

4. **📊 数据可视化**
   - 组件化图表构建
   - 交互式大屏展示
   - AI辅助分析

### 🔧 技术特点

- ✅ **高度结构化**: 统一的字段设计和命名规范
- ✅ **业务完整性**: 覆盖电商全链路数据
- ✅ **实验友好**: 支持数据科学实验和A/B测试
- ✅ **可视化就绪**: 内置组件化可视化支持
- ✅ **AI集成**: 支持AI分析历史记录和追踪

---

> 💡 **使用提示**: 这个数据库设计特别适合构建商业智能(BI)平台，支持灵活的数据分析和可视化需求。通过experiment_id的设计，可以很好地支持多租户和实验管理。
