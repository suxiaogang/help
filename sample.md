# 从这里开始写你的文档
sample.md 是编辑器的默认起点，也是一个语法速查示例。
想改默认内容，直接编辑本文件即可，不用动 markdown.html。

## 段落与强调
直接写字就是段落，回车即换行。**加粗**、*斜体*、~~删除线~~、`行内代码`，
还可以 [插入链接](https://example.com)。

## 列表
- 无序列表项
  - 子项（前面缩两个空格）
- [ ] 任务项：未完成
- [x] 任务项：已完成

1. 有序列表第一步
2. 有序列表第二步

## 引用
> 危机即转机。
> > 引用里还能再套一层引用。

## 链接
[链接文字Github](https://github.com)

## 图片
![图片说明](https://placehold.co/240x160.png)

## 代码
```bash
echo "hello, $USER"
git status --short
```

### MySQL 常用语句:
```sql
CREATE DATABASE my_sample_database CHARACTER SET 'utf8' COLLATE 'utf8_general_ci';
use my_sample_database;

/* 增加一列 */: 
ALTER TABLE `table_name` ADD `new_column` INT(20) NOT NULL AFTER `old_column`;

/* 批量替换https */
UPDATE k_product SET pic = replace(pic, 'http://images.example.com','https://images.example.com');

/* 删除表 */
DROP TABLE IF EXISTS `Cart`;
DROP TABLE IF EXISTS `Item`;

/* 新建表 */
CREATE TABLE `Item` (
  `item_id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `item_desc` varchar(20) NOT NULL,
  `item_price` decimal(10,0) NOT NULL,
  PRIMARY KEY (`item_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

/* 新建表(含外键关系) */
CREATE TABLE `Cart_Items` (
  `cart_id` int(11) unsigned NOT NULL,
  `item_id` int(11) unsigned NOT NULL,
  PRIMARY KEY (`cart_id`,`item_id`),
  CONSTRAINT `fk_cart` FOREIGN KEY (`cart_id`) REFERENCES `Cart` (`cart_id`),
  CONSTRAINT `fk_item` FOREIGN KEY (`item_id`) REFERENCES `Item` (`item_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

## 表格
| 快捷键 | 功能 |
| --- | --- |
| ⌘B | 加粗 |
| ⌘I | 斜体 |
| ⌘K | 插入链接 |

---

## 用法备忘（这节可整段删掉）
- 从某一篇真实的 .md 文件开始：右上角「打开文件」，或直接把文件拖进编辑器
- 写完用页面底部「下载 .md」保存，或「复制 Markdown」粘回原文件
- 中间的分隔线可以拖动调整两栏比例，编辑器会记着这次的宽度
