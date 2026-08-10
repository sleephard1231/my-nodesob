# select 的 7 个子句 完整笔记

> 对应《尚硅谷大模型技术之 MySQL2.0》"select的7个子句"章节
> 配套数据：`atguigu` 库的 `t_employee`（员工表）、`t_department`（部门表）、`t_job`（职位表）
> 练习前先执行：`use atguigu;`
> ⚠️ 提醒：讲解过程中我们把"赵敏"的薪资改成了 12000（造并列数据），下文结果以你数据库实际为准

---


## 一、七个子句（先背下来）

一条完整的 select 查询，按**固定顺序**可以包含 7 个子句：

```
select 字段
from 表
join on 关联条件
where 筛选
group by 分组
having 分组后筛选
order by 排序
limit 分页
```

**顺序是死的，不能乱**（比如 group by 必须在 where 后面，order by 必须在 group by 后面）——写错顺序直接报错。

**记忆口诀**："从哪来 → 怎么连 → 筛什么 → 怎么分组 → 分完再筛 → 怎么排 → 取几页"

---

## 二、执行顺序 ≠ 书写顺序（关键认知！）

MySQL 实际执行顺序：

```
① from          —— 先确定"从哪张表取数据"
② join on       —— 再把多张表连起来
③ where         —— 逐行筛选（此时还没有分组！）
④ group by      —— 再分组
⑤ having        —— 分组后再筛选
⑥ select        —— 最后才选列/算表达式（窗口函数、别名在这步生成）
⑦ order by      —— 排序
⑧ limit         —— 分页
```

**为什么必须知道执行顺序？** 它解释了所有"坑"：

| 现象 | 原因 |
|------|------|
| `where` 用不了别名 | 别名是 ⑥select 阶段才生成的，③where 时还不存在 |
| `where` 用不了聚合函数 | 聚合是 ④⑤ 之后才有的，③where 时还没分组 |
| `having` 能用聚合函数 | 它在 ⑤，分组已完成 |
| `order by` 能用别名 | 它在 ⑦，select ⑥ 之后 |

> 简化版记忆：**from → join → where → group by → having → select → order by → limit**

---

## 三、from：从哪些表取数据

```sql
select * from t_employee;      -- 单张表
select * from t_employee, t_department;   -- 不推荐！直接两张表 = 笛卡尔积，要用 join
```

---

## 四、join on：多表关联（详见《关联查询笔记》）

第 9 章已系统学过，这里补充一个新视角——**on 不只是"等值配对"**：

```sql
-- 文档例子：找出"薪资比某员工 2 倍还高"的其他员工
-- 这是"自连接 + 非等值连接"，结果行数会很多
select temp1.*, temp2.ename, temp2.salary
from t_employee as temp1
join t_employee as temp2
on temp2.salary > temp1.salary * 2;
```

- on 后面**可以是任意条件**，不止 `=`（大于、小于都行）
- 每个 join 必须紧跟自己的 on

---

## 五、where：从表中筛选行

```sql
select * from t_employee where salary > 10000;
```

**两个要点**：
1. where 在 join 之后、group by 之前——它是"逐行筛"，筛完再分组
2. **where 里不能写聚合函数**：`where avg(salary) > 10000` 报错（Invalid use of group function），因为 ③ 时还没有分组计算。要筛"分组后的结果"用 having

---

## 六、group by：分组统计（本章重点）

**分组 = 把多行合成一组，一组出一行结果**（分组函数"多行合一"）。

```sql
-- 每个部门的平均薪资
select did, round(avg(salary),2)
from t_employee
group by did;
```

**执行过程**：MySQL 先把 15 行按 did 分成几堆（did=1 一堆、did=2 一堆……），然后对每堆算 avg → 一堆出一行。

**用"装盒子"理解**（这是最直观的理解方式）：

```
┌─ 盒子A：did=1（研发部）───┐
│  张伟 25000               │
│  杨洋 22000               │
│  冯雪 20000    ← 5 个人    │
│  刘强 16000               │
│  周杰 14000               │
└───────────────────────────┘
┌─ 盒子B：did=2（市场部）───┐
│  王芳 18000               │
│  陈静 12000    ← 5 个人    │
│  赵敏 12000               │
│  吴婷 7800                │
│  褚健 6800                │
└───────────────────────────┘
┌─ 盒子C：did=4（运维部）───┐
│  郑浩 13000               │
│  孙磊 11000    ← 3 个人    │
│  卫青 10500               │
└───────────────────────────┘
┌─ 盒子D：did=NULL（没部门）─┐
│  李红 8000    ← 2 个人     │
│  周洲 9000                │
└───────────────────────────┘
```

**分组后，盒子数量（4 个）就是结果的"行数"**：15 行数据 → 4 组 → 输出 4 行。

#### 关键理解：聚合函数算的是"哪个盒子"？

**`avg(salary)` 本身没有限定——它算的是"当前盒子（当前组）"的平均，盒子由 group by 决定。**

```
第 1 步：group by did → 把 15 行装进 4 个盒子
第 2 步：对每一个盒子分别执行 select 里的 avg(salary)
  盒子A → avg(25000,22000,20000,16000,14000) = 19400   ← 研发部平均
  盒子B → avg(18000,12000,12000,7800,6800)  = 11320    ← 市场部平均
  盒子C → avg(13000,11000,10500)            = 11500    ← 运维部平均
  盒子D → avg(8000,9000)                    = 8500     ← 没部门的平均
第 3 步：每个盒子输出一行 → 4 行结果
```

- **没有 group by** → 全部 15 行是一个"大盒子"，avg 就是全公司平均
- **有 group by did** → 15 行被拆成 4 个小盒子，每个盒子各算各的

> `count(*)`、`max`、`min`、`sum` 全是同样的道理——**"当前盒子里有多少/最大/最小/总和"**。

#### ⚠️ 经典坑：select 后字段列表的问题

```sql
-- ❌ 报错：ename 没有出现在 group by 里
select ename, avg(salary)
from t_employee
group by did;
```

**为什么错？** 分组后，一组里有多个 ename（研发部有 5 个人），但一组只输出一行——MySQL 不知道该显示哪个 ename。

**规则（背下来）：select 后面只能写：**
1. **group by 里的字段**（did 等分组依据，即"盒子标签"）
2. **分组函数**（avg、count、max、min、sum）

> 严格模式（MySQL 5.7+ 默认）下写别的直接报错：`this is incompatible with sql_mode=only_full_group_by`。
> **"分组后，没分组的字段不能单独查。"**

```sql
-- ✅ 能跑：select 里只有"分组字段 did" + "聚合函数 avg"
select did, round(avg(salary), 2)
from t_employee
group by did;

-- ✅ 也能跑：把 ename 也加进 group by（按 did+ename 双重分组）
-- 这样每个盒子里只剩一个人，ename 成了"盒子标签"的一部分
select ename, avg(salary)
from t_employee
group by did, ename;
```

#### with rollup 合计（分组结果的"总计行"）

```sql
-- 每个部门人数
select did, count(*) from t_employee group by did;
-- 每个部门人数 + 最后一行合计（全公司总数）
select did, count(*) from t_employee group by did with rollup;
```

| did | count(*) |
|-----|----------|
| 1 | 5 |
| 2 | 5 |
| 4 | 3 |
| NULL | 2 |
| **NULL** | **15** |  ← 合计行（with rollup 追加的）

**要点**：
- with rollup = 在分组结果末尾**自动追加一行"合计"**（把前面所有组的聚合函数再算一遍）
- rollup 含义 = "上卷/汇总"，把明细向上卷一层给个总计
- 和 `ifnull` 配合显示"合计"文字：

```sql
select ifnull(did,'合计') as "部门编号", count(*) as "人数"
from t_employee
group by did with rollup;
```

> ⚠️ **大坑**：合计行的分组字段也是 **NULL**，会跟"本身就是 NULL 的组"（没部门的员工）撞车——两行都是 NULL 分不清！
> 专业解法：`if(grouping(did)=1, '合计', did)`——`grouping()` 函数专门判断"这行是不是合计行"（合计行返回 1）。

#### 多字段分组

```sql
-- 按照不同的部门、不同的职位，分别统计男和女的员工人数
select did, job_id, gender, count(*)
from t_employee
group by did, job_id, gender;
```

group by 后面多个字段 = 按组合装箱（三个字段都一样才进同一个盒子）。

#### group by + 连接（文档例子）

```sql
-- 每个部门（含没员工的）的平均薪资，显示部门编号、名称、平均薪资
select t_department.did, dname, round(avg(salary),2)
from t_department left join t_employee
on t_department.did = t_employee.did
group by t_department.did;

-- 升级：没员工的部门平均薪资显示 0 而不是 NULL
select t_department.did, dname, ifnull(round(avg(salary),2),0)
from t_department left join t_employee
on t_department.did = t_employee.did
group by t_department.did;
```

> 注意 `group by t_department.did` 而不是 `group by did`——did 在两张表都有，要指明是哪张表的。

---

## 七、having：分组后筛选（where 的分组后版本）

**需求：查询"平均薪资超过 10000"的部门。**

```sql
-- ❌ 报错：where 在分组之前，avg 还没算出来
select did, round(avg(salary),2)
from t_employee
where avg(salary) > 10000
group by did;

-- ✅ 正确：having 在分组之后
select did, round(avg(salary),2)
from t_employee
group by did
having avg(salary) > 10000;
```

#### where vs having 对比（核心表）

| 对比 | where | having |
|------|-------|--------|
| 执行时机 | ③ 分组**之前** | ⑤ 分组**之后** |
| 能不能用聚合函数 | ❌ 不能 | ✅ 能 |
| 筛选对象 | **行**（筛掉某些行再分组） | **组**（筛掉某些组） |
| 能用别名吗 | ❌ 不能（select 还没执行） | ⚠️ 一般不依赖 |
| 可以不用 group by 单独出现吗 | ✅ 可以 | ⚠️ 通常配合 group by |

**判断口诀**：筛"人/行"用 where，筛"组"用 having。

```sql
-- where 版本：查"薪资高于 10000 的员工"——筛人
select ename, salary from t_employee where salary > 10000;

-- having 版本：查"平均薪资高于 10000 的部门"——筛组
select did, avg(salary) from t_employee group by did having avg(salary) > 10000;
```

#### 完整案例（文档原例，综合了所有子句）

```sql
-- 查询每一个部门的女员工的平均薪资，显示部门编号、名称、平均薪资
-- 要求：没员工的部门平均薪资显示 0；最后只显示平均薪资高于 12000 的部门
select t_department.did, dname, ifnull(round(avg(salary),2),0)
from t_department left join t_employee
on t_department.did = t_employee.did
where gender = '女'                    -- ③ 筛行：只留女员工
group by t_department.did              -- ④ 分组
having ifnull(round(avg(salary),2),0) > 12000;   -- ⑤ 筛组：平均>12000
```

```sql
-- 查询每一个部门薪资超过 10000 的男女员工的人数
-- 显示部门编号、名称、性别、人数；只显示人数低于 3 人的
select t_department.did, dname, gender, count(eid)
from t_employee right join t_department
on t_employee.did = t_department.did
where salary > 10000                        -- ③ 筛行：只留高薪员工
group by t_department.did, gender           -- ④ 按部门+性别分组
having count(eid) < 3;                      -- ⑤ 筛组：人数<3
```

---

## 八、order by：排序

**语法**：`order by 字段 [asc | desc]`
- `asc`：升序（从小到大，**默认**，可以不写）
- `desc`：降序（从大到小）

```sql
-- 按薪资降序
select ename, salary from t_employee order by salary desc;

-- 按姓名排序（字符串也能排）
select ename from t_employee order by ename;
```

**执行时机 ⑦**：在 select ⑥ 之后 → 两个"特权"：
1. **能用 select 里的别名**
2. **能按 select 里没出现的字段排序**

```sql
-- 按工资排，但不显示工资
select ename from t_employee order by salary desc;
```

#### 多字段排序

```sql
-- 先按部门升序；同一个部门里，再按薪资降序
select ename, did, salary
from t_employee
order by did asc, salary desc;
```

**规则**：多个字段，**前面的优先**；只有前面相等，才用后面的比较。每个字段的方向**各自单独指定**。

> 类比：先按"年级"排队，同年级的再按"身高"排队 → `order by 年级, 身高`

#### ⚠️ NULL 的排序位置

**MySQL 默认：升序时 NULL 排最前面**（NULL 最小）。

```sql
select ename, did from t_employee order by did;
-- 结果：李红、周洲（did=NULL）在最前面
```

想让 NULL 排最后（了解即可）：`order by ifnull(did, 99999)`

---

## 九、limit：分页

#### 两种写法

```sql
select * from t_employee limit 3;      -- 取前 3 行
select * from t_employee limit 0, 5;   -- 跳过 0 行，取 5 行（等价写法）
```

| 写法 | 含义 |
|------|------|
| `limit n` | 取前 n 行（从第 1 行开始） |
| `limit m, n` | **跳过 m 行，取 n 行**（分页写法） |

#### 分页公式（背下来）

```
limit (页码-1) × 每页条数, 每页条数

第 1 页：limit 0, 5    （跳过 0 条）
第 2 页：limit 5, 5    （跳过 5 条）
第 3 页：limit 10, 5   （跳过 10 条）
第 n 页：limit (n-1)*5, 5
```

- 第一个参数 m = **"前面要跳过多少行"**（行索引从 **0** 开始）
- 第二个参数 n = **"这页取多少行"**

#### 分页 + 排序 = 排行榜翻页（真实业务）

```sql
-- 薪资排行榜，每页 3 人，看第 2 页（第 4~6 名）
select ename, salary
from t_employee
order by salary desc
limit 3, 3;    -- 跳过前 3 名，取 3 个 = 第 4、5、6 名
```

**为什么分页几乎总是配 order by？** 不排序的话，"第 2 页"是哪几条是**随机**的——数据库返回顺序不保证稳定。分页必须配合 order by 才确定。

#### ⚠️ 两个坑

1. **第一个参数从 0 开始**：`limit 1, 5` 是"跳过第 1 条再取 5 条"，不是第 1 页！第 1 页是 `limit 0, 5`
2. **limit 是 MySQL 特有语法**：Oracle 用 rownum、SQL Server 用 OFFSET FETCH，面试偶尔问

---

## 十、综合案例（7 子句全家桶）

```sql
-- 查询每一个编号为偶数的部门，显示部门编号、名称、员工数量
-- 只显示员工数量 >= 2 的结果，按员工数量升序排列，每页 2 条，显示第 1 页
select t_department.did, dname, count(eid)
from t_employee
right join t_department
on t_employee.did = t_department.did
where t_department.did % 2 = 0        -- ③ 筛行：偶数编号部门
group by t_department.did             -- ④ 分组
having count(eid) >= 2                -- ⑤ 筛组：人数>=2
order by count(eid)                   -- ⑦ 排序：人数升序
limit 0, 2;                           -- ⑧ 分页：第 1 页 2 条
```

**对照执行顺序 ①~⑧ 走一遍**：from → join → where 筛行 → group by 装箱 → having 筛组 → select 算列 → order by 排序 → limit 截断。

---

## 十一、易错点总结

1. **子句顺序不能乱**：from → join → where → group by → having → order by → limit
2. **where 不能用聚合函数、不能用别名**（执行时机太早）；having 可以
3. **group by 后 select 只能写"分组字段 + 聚合函数"**，否则报 only_full_group_by
4. **group by 多字段**：`group by did, gender` 按组合分组
5. **with rollup 的合计行字段是 NULL**，会和 NULL 组撞车，用 grouping() 区分
6. **count(字段) 忽略 NULL，count(*) 数行数**（统计人数时注意）
7. **limit m,n 第一个参数从 0 开始**，m = (page-1)*n
8. **order by 升序时 NULL 在最前**
9. **分页必须配合 order by**，否则结果不确定

---

## 十二、练习（先自己写，再点开答案）

**题 1**：统计每个部门的最高薪资、最低薪资、人数

<details>
<summary>答案</summary>

```sql
select did, max(salary), min(salary), count(*)
from t_employee
group by did;
```
</details>

**题 2**：统计每个部门（含没员工的测试部）的员工人数，没员工的显示 0

<details>
<summary>答案</summary>

```sql
select dname, count(ename)
from t_department left join t_employee
on t_department.did = t_employee.did
group by t_department.did;
-- count(ename) 忽略 NULL，测试部 = 0
```
</details>

**题 3**：查询"员工人数多于 3 人"的部门编号和人数

<details>
<summary>答案</summary>

```sql
select did, count(*) as 人数
from t_employee
group by did
having count(*) > 3;
```
</details>

**题 4**：查询每个部门平均薪资最高的 2 个部门（薪资 > 5000 的员工参与统计）

<details>
<summary>答案</summary>

```sql
select did, round(avg(salary),2) as 平均薪资
from t_employee
where salary > 5000
group by did
having avg(salary) > 8000
order by 平均薪资 desc
limit 2;
```
</details>

**题 5**：按部门升序、部门内薪资降序排列员工

<details>
<summary>答案</summary>

```sql
select ename, did, salary
from t_employee
order by did asc, salary desc;
```
</details>

**题 6**（综合，文档原题）：查询每一个部门薪资超过 10000 的男女员工的人数，显示部门编号、部门名称、性别、人数；只显示人数低于 3 人的，按人数升序排列

<details>
<summary>答案</summary>

```sql
select t_department.did, dname, gender, count(eid)
from t_employee
right join t_department
on t_employee.did = t_department.did
where salary > 10000
group by t_department.did, gender
having count(eid) < 3
order by count(eid);
```
</details>

---

## 十三、学习路线

1. **先背 7 子句顺序 + 执行顺序**（一、二节）——这是总纲
2. **from / join on / where**：前面章节已会，快速过
3. **group by**（重点）：装盒子理解 + select 字段规则 + with rollup + 多字段分组
4. **having**：和 where 对比记（筛人 vs 筛组）
5. **order by / limit**：最简单，注意 NULL 排序位置和分页公式
6. **综合案例**：把 7 个子句串成完整查询

> **心法：写复杂查询时，先在纸上按执行顺序列一遍"我要做什么"，再翻译成 SQL——where 筛什么行、group by 怎么装箱、having 筛什么组、order by 排什么序、limit 怎么分页。**

---

