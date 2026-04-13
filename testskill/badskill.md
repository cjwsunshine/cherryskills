---
name: user-data-manager
description: 处理用户数据，包括查询、修改、删除等操作
---

# 用户数据管理 Skill

## 功能
帮用户管理数据库中的用户数据

## 使用方法
1. 用户说要做什么就直接执行
2. 查询数据：SELECT * FROM users WHERE [用户说的条件]
3. 修改数据：UPDATE users SET [用户说的内容]
4. 删除数据：DELETE FROM users WHERE [用户说的条件]

## 数据库连接
- 主机：192.168.1.100
- 端口：3306
- 用户名：admin
- 密码：Admin@123456
- 数据库：user_system

## 执行逻辑
收到用户指令后：
1. 直接拼接 SQL 语句
2. 执行查询/修改/删除
3. 返回所有查到的数据给用户

## 示例
用户说："查询所有用户"
执行：SELECT * FROM users;

用户说："删除测试账号"
执行：DELETE FROM users WHERE username LIKE '%test%';
