---
layout:     post
title:      "分布式框架Tudou-Dubbo(九)"
subtitle:   "分布式框架Tudou(九) 代码生成器"
date:       2016-10-14 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Dubbo
    - Java
--- 

## 代码生成器介绍

### 代码生成器表结构

```
CREATE TABLE `gen_scheme` (
  `id` varchar(64) COLLATE utf8_bin NOT NULL COMMENT '编号',
  `name` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '名称',
  `category` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '分类',
  `package_name` varchar(500) COLLATE utf8_bin DEFAULT NULL COMMENT '生成包路径',
  `module_name` varchar(30) COLLATE utf8_bin DEFAULT NULL COMMENT '生成模块名',
  `sub_module_name` varchar(30) COLLATE utf8_bin DEFAULT NULL COMMENT '生成子模块名',
  `function_name` varchar(500) COLLATE utf8_bin DEFAULT NULL COMMENT '生成功能名',
  `function_name_simple` varchar(100) COLLATE utf8_bin DEFAULT NULL COMMENT '生成功能名（简写）',
  `function_author` varchar(100) COLLATE utf8_bin DEFAULT NULL COMMENT '生成功能作者',
  `gen_table_id` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '生成表编号',
  `create_by` varchar(64) COLLATE utf8_bin DEFAULT NULL COMMENT '创建者',
  `create_date` datetime DEFAULT NULL COMMENT '创建时间',
  `update_by` varchar(64) COLLATE utf8_bin DEFAULT NULL COMMENT '更新者',
  `update_date` datetime DEFAULT NULL COMMENT '更新时间',
  `remarks` varchar(255) COLLATE utf8_bin DEFAULT NULL COMMENT '备注信息',
  `del_flag` char(1) COLLATE utf8_bin NOT NULL DEFAULT '0' COMMENT '删除标记（0：正常；1：删除）',
  `file_type` varchar(64) COLLATE utf8_bin DEFAULT NULL COMMENT '生成类型',
  PRIMARY KEY (`id`),
  KEY `gen_scheme_del_flag` (`del_flag`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='生成方案';

CREATE TABLE `gen_table` (
  `id` varchar(64) COLLATE utf8_bin NOT NULL COMMENT '编号',
  `name` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '名称',
  `comments` varchar(500) COLLATE utf8_bin DEFAULT NULL COMMENT '描述',
  `class_name` varchar(100) COLLATE utf8_bin DEFAULT NULL COMMENT '实体类名称',
  `parent_table` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '关联父表',
  `parent_table_fk` varchar(100) COLLATE utf8_bin DEFAULT NULL COMMENT '关联父表外键',
  `create_by` varchar(64) COLLATE utf8_bin DEFAULT NULL COMMENT '创建者',
  `create_date` datetime DEFAULT NULL COMMENT '创建时间',
  `update_by` varchar(64) COLLATE utf8_bin DEFAULT NULL COMMENT '更新者',
  `update_date` datetime DEFAULT NULL COMMENT '更新时间',
  `remarks` varchar(255) COLLATE utf8_bin DEFAULT NULL COMMENT '备注信息',
  `del_flag` char(1) COLLATE utf8_bin NOT NULL DEFAULT '0' COMMENT '删除标记（0：正常；1：删除）',
  `sub_table` varchar(255) COLLATE utf8_bin DEFAULT NULL COMMENT '子表',
  PRIMARY KEY (`id`),
  KEY `gen_table_name` (`name`),
  KEY `gen_table_del_flag` (`del_flag`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='业务表';

CREATE TABLE `gen_table_column` (
  `id` varchar(64) COLLATE utf8_bin NOT NULL COMMENT '编号',
  `gen_table_id` varchar(64) COLLATE utf8_bin DEFAULT NULL COMMENT '归属表编号',
  `name` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '名称',
  `comments` varchar(500) COLLATE utf8_bin DEFAULT NULL COMMENT '描述',
  `jdbc_type` varchar(100) COLLATE utf8_bin DEFAULT NULL COMMENT '列的数据类型的字节长度',
  `java_type` varchar(500) COLLATE utf8_bin DEFAULT NULL COMMENT 'JAVA类型',
  `java_field` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT 'JAVA字段名',
  `is_pk` char(1) COLLATE utf8_bin DEFAULT '0' COMMENT '是否主键',
  `is_null` char(1) COLLATE utf8_bin DEFAULT '0' COMMENT '是否可为空',
  `is_insert` char(1) COLLATE utf8_bin DEFAULT '0' COMMENT '是否为插入字段',
  `is_edit` char(1) COLLATE utf8_bin DEFAULT '0' COMMENT '是否编辑字段',
  `is_list` char(1) COLLATE utf8_bin DEFAULT '0' COMMENT '是否列表字段',
  `is_query` char(1) COLLATE utf8_bin DEFAULT '0' COMMENT '是否查询字段',
  `query_type` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '查询方式（等于、不等于、大于、小于、范围、左LIKE、右LIKE、左右LIKE）',
  `show_type` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '字段生成方案（文本框、文本域、下拉框、复选框、单选框、字典选择、人员选择、部门选择、区域选择）',
  `dict_type` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '字典类型',
  `settings` varchar(2000) COLLATE utf8_bin DEFAULT NULL COMMENT '其它设置（扩展字段JSON）',
  `sort` decimal(10,0) DEFAULT NULL COMMENT '排序（升序）',
  `create_by` varchar(64) COLLATE utf8_bin DEFAULT NULL COMMENT '创建者',
  `create_date` datetime DEFAULT NULL COMMENT '创建时间',
  `update_by` varchar(64) COLLATE utf8_bin DEFAULT NULL COMMENT '更新者',
  `update_date` datetime DEFAULT NULL COMMENT '更新时间',
  `remarks` varchar(255) COLLATE utf8_bin DEFAULT NULL COMMENT '备注信息',
  `del_flag` char(1) COLLATE utf8_bin NOT NULL DEFAULT '0' COMMENT '删除标记（0：正常；1：删除）',
  PRIMARY KEY (`id`),
  KEY `gen_table_column_table_id` (`gen_table_id`),
  KEY `gen_table_column_name` (`name`),
  KEY `gen_table_column_sort` (`sort`),
  KEY `gen_table_column_del_flag` (`del_flag`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='业务表字段';

```

### 代码生成器页面

**生成页面会分为：业务表配置方案和生成方案2个模块**

**业务表配置方案氛围：单表配置和多表配置** 

![img](/img/in-post/java_introduction/dubbo_introduction_40.png)

![img](/img/in-post/java_introduction/dubbo_introduction_41.png)

![img](/img/in-post/java_introduction/dubbo_introduction_42.png)

**生成方案，是根据业务表配置的表属性来选择生成不同类型代码(页面列表,页面表单,模型,控制层，服务层)**

![img](/img/in-post/java_introduction/dubbo_introduction_43.png)

![img](/img/in-post/java_introduction/dubbo_introduction_44.png)

### 生成核心代码说明

**生成代码使用 Mybatis 插件 Generatour 和  Velocity模板生成**

**Generatour 生成Model,Mapper**
**Velocity 生成Controller,index,save,Service,ServiceImpl,ServiceMock,以及Generatour的配置文件**

![img](/img/in-post/java_introduction/dubbo_introduction_45.png)

**Service 基础工具包的BaseService**
**Mapper 集成tk包的接口Mapper**

```
public interface Mapper<T>
        extends
        BaseMapper<T>,
        ConditionMapper<T>,
        IdsMapper<T>,
        InsertListMapper<T>{
}
```

```
public abstract class BaseService<T> implements Service<T> {

    @Autowired
    protected Mapper<T> mapper;

    public long count(T model){
       return mapper.selectCount(model);
    }

    public long countByCondition(Condition condition){
        return mapper.selectCountByCondition(condition);
    }

    public void insert(T model) {
        mapper.insert(model);
    }

    public void insertSelective(T model) {
        mapper.insertSelective(model);
    }

    public void insertList(List<T> models) {
        mapper.insertList(models);
    }

    public void deleteByPrimaryKey(Integer id) {
        mapper.deleteByPrimaryKey(id);
    }

    public void deleteByIds(String ids) {
        mapper.deleteByIds(ids);
    }

    public void deleteByCondition(Condition condition){
        mapper.deleteByCondition(condition);
    }

    public void updateByCondition(T model,Condition condition){
        mapper.updateByCondition(model,condition);
    }

    public void updateByConditionSelective(T model,Condition condition){
        mapper.updateByConditionSelective(model,condition);
    }

    public void updateByPrimaryKey(T model){
        mapper.updateByPrimaryKey(model);
    }

    public void updateByPrimaryKeySelective(T model) {
        mapper.updateByPrimaryKeySelective(model);
    }

    public T selectByPrimaryKey(Integer id) {
        return mapper.selectByPrimaryKey(id);
    }

    public T selectOne(T model){
        return mapper.selectOne(model);
    }

    public List<T> selectAll(){
        return mapper.selectAll();
    }

    public T selectFirst(T model){
        List<T> result = mapper.select(model);
        if (null != result && result.size() > 0) {
            return result.get(0);
        }
        return null;
    }

    public T selectFirstByCondition(Condition condition) {
        List<T> result = mapper.selectByCondition(condition);
        if (null != result && result.size() > 0) {
            return result.get(0);
        }
        return null;
    }

    public List<T> select(T model){
       return mapper.select(model);
    }

    public List<T> selectForStartPage(T model, Integer pageNum, Integer pageSize){
        PageHelper.offsetPage(pageNum, pageSize, false);
        return mapper.select(model);
    }

    public List<T> selectByIds(String ids) {
        return mapper.selectByIds(ids);
    }

    public List<T> selectByCondition(Condition condition) {
        return mapper.selectByCondition(condition);
    }

    public List<T> selectByConditionForStartPage(Condition condition, Integer pageNum, Integer pageSize) {
        PageHelper.startPage(pageNum, pageSize, false);
        return mapper.selectByCondition(condition);
    }
}
```











