


东明：
1、提交核保
2、提核后自动核保通过的，异步上传表格中需要的影像资料，用户是无感的。
3、提核后保司要求上传影响资料的，看我们是否有，有的直接再次提核，缺少的，跳转到影响资料上传页面，提示用户少了什么，保司要求的已经存在的，直接页面返显即可。
4、核保成功后立即缴费支付页面
5、定时任务查询投保单状态人工核保通过处理
6、支付成功的保司回调的处理  和 我们主动查询状态的处理
7、撤销订单
8、删除订单
9、刷新状态

哈哈哈


计税分类：

代收税，免税，减税，不代收税，已完税，纳税并补税，不征；默认代收税





开发测试环境nacos地址：  http://192.168.160.52:8848/nacos/

登录账号：  用户名： nacos 密码： nacos



motor-insurance-channel-guoren  test5 回调地址：  http://36.110.105.37:9120

测试5环境每个项目的域名:

test5.insurance-channel-guoren.xs.sit
test5.motor-insurance-admin-ui.xs.sit  
test5.motor-insurance-admin.xs.sit 
test5.motor-insurance-app.xs.sit  
test5.motor-insurance-platform.xs.sit

测试环境 骑E的日志：

192.168.160.15
用户：tomcat
密码：xs-sit



数据库：
192.168.160.106/motor_insurance
 username: motor_rw
 password: motor



data:image/jpeg;base64,



测试：

15226588842

eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiLpqpFFIiwic3ViIjoiMTEifQ.97wRKny5pXKo_c1KaH1RkpldS677_V694jpgCta1eIQ





测试进度：正常
今日完成：
测试了保费计算之前的接口

明日计划：
1、提交核保接口测试
2、支付接口测试



表结构设计：

```mysql
CREATE TABLE insurance_variety_config (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `insurance_code` varchar(32) NOT NULL COMMENT '保险公司代码',
  `risk_code` varchar(64) NOT NULL COMMENT '险种代码',
  `risk_name` varchar(64) NOT NULL COMMENT '险种名称',
  `sum_amount` decimal(12,2) DEFAULT 0 COMMENT '总保额',
  `remark` varchar(256) DEFAULT NULL COMMENT '备注',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='险种配置表';

CREATE TABLE insurance_category_config (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `variety_config_id` int(11) NOT NULL COMMENT '险种配置id',
  `risk_code` varchar(64) NOT NULL COMMENT '险种代码',
  `kind_code` varchar(64) NOT NULL COMMENT '险别代码',
  `kind_name` varchar(64) NOT NULL COMMENT '险别名称',
  `sum_amount` decimal(12,2) DEFAULT 0 COMMENT '保额',
  `remark` varchar(256) DEFAULT NULL COMMENT '备注',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='险别配置表';

CREATE TABLE motor_order_base_info (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `proposal_no` varchar(64) NOT NULL COMMENT '投保单号',
  `order_type` tinyint NOT NULL DEFAULT 1 COMMENT '订单类型: 1 线上订单  2 线下订单',
  `vehicle_info_id` int(11) NOT NULL COMMENT '车辆信息id',
  `insurance_code` varchar(32) NOT NULL COMMENT '保险公司代码',
  `place_user_id` int(11) NOT NULL COMMENT '出单员id',
  `business_belong_id` int(11) NOT NULL COMMENT '业务归属id',
  `insurance_biz_no` varchar(64) DEFAULT NULL COMMENT '保司业务订单号uuid',
  `business_belong_mobile` varchar(32) NOT NULL COMMENT '业务归属手机号',
  `business_belong_name` varchar(32) NOT NULL COMMENT '业务归属姓名',
  `business_belong_channel` varchar(32) NOT NULL COMMENT '业务归属渠道',
  `invoice_type` tinyint DEFAULT NULL COMMENT '发票类型: 1 电子发票  2 纸质发票',
  `invoice_title` tinyint DEFAULT NULL COMMENT '发票抬头: 1 投保人  2 被保人',
  `issue_province` varchar(16) DEFAULT NULL COMMENT '投保地省',
  `issue_city` varchar(16) DEFAULT NULL COMMENT '投保地市',
  `sum_premium` decimal(12,2) DEFAULT 0 COMMENT '总保费',
  `order_status` tinyint NOT NULL COMMENT '订单状态：1：待审核  2：核保通过  3：核保失败 4：待人工审核 5：保单生效 6：关闭 7：删除',
  `remark` varchar(256) DEFAULT NULL COMMENT '备注',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime DEFAULT NULL COMMENT '更新时间',
  `version` int(11) NOT NULL DEFAULT '0' COMMENT '版本号',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='订单基本信息表';

CREATE TABLE motor_order_insurance_variety (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `vehicle_info_id` int(11) NOT NULL COMMENT '车辆信息id',
  `proposal_no` varchar(64) NOT NULL COMMENT '投保单号',
  `insurance_proposal_no` varchar(64) DEFAULT NULL COMMENT '保险公司投保单号',
  `policy_no` varchar(64) DEFAULT NULL COMMENT '保险公司保单号',
  `risk_code` varchar(64) NOT NULL COMMENT '险种代码',
  `sum_premium` decimal(12,2) DEFAULT 0 COMMENT '总保费',
  `underwrite_status` tinyint DEFAULT 1 COMMENT '核保状态 1：待核保  2：核保通过  3：待人工审核 4：核保失败',
  `underwrite_date` datetime DEFAULT NULL COMMENT '核保时间',
  `underwrite_content` varchar(256) DEFAULT NULL COMMENT '核保意见',
  `electronic_policy_download_address` varchar(128) DEFAULT NULL COMMENT '电子保单下载地址',
  `start_date` datetime NOT NULL COMMENT '起保日期',
  `end_date` datetime NOT NULL COMMENT '终保日期',
  `sign_bill_date` datetime DEFAULT NULL COMMENT '签单日期（当天操作日期）',
  `remark` varchar(256) DEFAULT NULL COMMENT '备注',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime DEFAULT NULL COMMENT '更新时间',
  `version` int(11) NOT NULL DEFAULT '0' COMMENT '版本号',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='订单险种表';

CREATE TABLE motor_order_insurance_variety_extension (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `insurance_variety_id` int(11) NOT NULL COMMENT '订单险种id',
  `special_agreement` json DEFAULT NULL COMMENT '特别约定',
  `remark` varchar(256) DEFAULT NULL COMMENT '备注',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime DEFAULT NULL COMMENT '更新时间',
  `version` int(11) NOT NULL DEFAULT '0' COMMENT '版本号',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='订单险种扩展表';

CREATE TABLE motor_order_insurance_category (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `insurance_variety_id` int(11) NOT NULL COMMENT '订单险种id',
  `risk_code` varchar(64) NOT NULL COMMENT '险种代码',
  `kind_code` varchar(64) NOT NULL COMMENT '险别代码',
  `kind_name` varchar(64) NOT NULL COMMENT '险别名称',
  `sum_amount` decimal(12,2) DEFAULT 0 COMMENT '保额',
  `single_seat_amount` decimal(12,2) DEFAULT 0 COMMENT '每座保额',
  `benchmark_premium` decimal(12,2) DEFAULT 0 COMMENT '基准保费',
  `standard_premium` decimal(12,2) DEFAULT 0 COMMENT '标准保费',
  `premium` decimal(12,2) DEFAULT 0 COMMENT '保费',
  `remark` varchar(256) DEFAULT NULL COMMENT '备注',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime DEFAULT NULL COMMENT '更新时间',
  `version` int(11) NOT NULL DEFAULT '0' COMMENT '版本号',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='订单险别表';

CREATE TABLE motor_payment_apply (
    `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
    `proposal_no` varchar(64) NOT NULL COMMENT '投保单号',
    `payment_channel` tinyint NOT NULL COMMENT '支付渠道  1 二维码支付  2 支付宝  3  微信',
    `trade_amount` decimal(12,2) DEFAULT 0 COMMENT '支付金额',
    `payment_url` text DEFAULT NULL COMMENT '支付链接',
    `trade_status` tinyint DEFAULT 0 COMMENT '支付状态  0 待支付  1 支付完成  2 支付失败',
    `payment_end_time` datetime NOT NULL COMMENT '支付截止时间',
    `remark` varchar(256) DEFAULT NULL COMMENT '备注',
    `create_time` datetime NOT NULL COMMENT '创建时间',
    `update_time` datetime DEFAULT NULL COMMENT '更新时间',
    `version` int(11) NOT NULL DEFAULT '0' COMMENT '版本号',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='支付申请表';




INSERT INTO motor_platform_attribute_mapping(insurance_code, mapping_type, standard_value, third_party_value, value_des, create_time, update_time, version) VALUES ('PCIC', 1, '5', '0', '上传资料', now(), now(), 0);
INSERT INTO motor_platform_attribute_mapping(insurance_code, mapping_type, standard_value, third_party_value, value_des, create_time, update_time, version) VALUES ('PCIC', 1, '2', '1', '核保通过', now(), now(), 0);
INSERT INTO motor_platform_attribute_mapping(insurance_code, mapping_type, standard_value, third_party_value, value_des, create_time, update_time, version) VALUES ('PCIC', 1, '3', '2', '核保失败', now(), now(), 0);
INSERT INTO motor_platform_attribute_mapping(insurance_code, mapping_type, standard_value, third_party_value, value_des, create_time, update_time, version) VALUES ('PCIC', 1, '2', '3', '核保通过', now(), now(), 0);
INSERT INTO motor_platform_attribute_mapping(insurance_code, mapping_type, standard_value, third_party_value, value_des, create_time, update_time, version) VALUES ('PCIC', 1, '4', '9', '待人工审核', now(), now(), 0);

```



































