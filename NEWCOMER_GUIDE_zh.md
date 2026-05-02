# MyBatis 3 新人入门导览

## 1. 代码库整体结构（先看这个）

- `src/main/java/org/apache/ibatis`：核心框架实现代码。
  - `session/`：`SqlSessionFactory`、`SqlSession` 等入口 API。
  - `builder/`：解析配置与 Mapper（XML、注解）。
  - `mapping/`：`MappedStatement`、`ResultMap`、`BoundSql` 等元数据模型。
  - `executor/`：执行 SQL、处理缓存/批量/结果集。
  - `scripting/`：动态 SQL、`LanguageDriver`。
  - `plugin/`：拦截器扩展机制（MyBatis 插件）。
  - `type/`：`TypeHandler` 与 JDBC/Java 类型转换。
  - `transaction/`：事务抽象和 JDBC/Managed 实现。
- `src/main/resources`：XSD/DTD 等配置约束文件。
- `src/test/java` 与 `src/test/resources`：大量回归测试和样例 SQL/Mapper，理解行为最重要的“活文档”。
- `src/site`：官方文档站点内容（多语言），和用户视角的功能说明一致。

## 2. 新人必须掌握的主流程

建议按“配置 -> 构建 -> 执行 -> 映射”来理解：

1. 读取 `mybatis-config.xml` 与 mapper XML/注解。
2. 解析后写入 `Configuration`，产生 `MappedStatement` 等元信息。
3. `SqlSession` 调用语句，进入 `Executor` 体系。
4. `StatementHandler` 组装 JDBC `Statement`、设置参数。
5. `ResultSetHandler` 把结果映射为对象（含 `ResultMap` / 自动映射）。
6. 一级/二级缓存与事务边界共同决定最终数据库交互行为。

## 3. 阅读源码建议顺序（高收益）

1. `org.apache.ibatis.session`（用户 API 入口）。
2. `org.apache.ibatis.builder`（配置如何变成可执行元数据）。
3. `org.apache.ibatis.mapping`（核心对象模型）。
4. `org.apache.ibatis.executor` + `executor/statement` + `executor/resultset`（执行链路）。
5. `org.apache.ibatis.scripting`（动态 SQL）。
6. `org.apache.ibatis.plugin`（扩展点）。
7. `org.apache.ibatis.type`（类型转换细节）。

## 4. 测试与调试建议

- 先跑单测再改代码；优先读你改动附近的测试类。
- 遇到复杂行为（构造器映射、嵌套结果映射、延迟加载、插件拦截）时，先从 `src/test/resources/org/apache/ibatis/submitted` 找最小复现。
- 动态 SQL 问题可重点观察 `BoundSql` 与参数映射内容。

## 5. 后续学习路径（3 个阶段）

### 阶段 A（1~2 周）：建立框架心智模型
- 跑通一次最小 demo（配置、Mapper、增删改查）。
- 对照调用链打断点：`SqlSession` -> `Executor` -> `StatementHandler` -> `ResultSetHandler`。

### 阶段 B（2~4 周）：吃透“映射与执行”
- 专题学习 `ResultMap`、构造器映射、嵌套查询/嵌套结果。
- 理解缓存键生成、一级/二级缓存失效时机。
- 学会写一个简单 `Interceptor`。

### 阶段 C（持续）：具备维护与扩展能力
- 阅读 `src/test/resources/org/apache/ibatis/submitted` 里的历史问题回归测试。
- 针对一个问题先补测试、再修复、最后重构。
- 关注版本变更对 Java 版本、依赖和测试矩阵的影响。

## 6. 常见误区

- 把 MyBatis 当成“自动 ORM”：MyBatis 是 SQL 映射框架，SQL 语义仍由开发者负责。
- 只看文档不看测试：很多边界行为只在测试中更直观。
- 忽略 `TypeHandler`：大量“字段映射异常”本质是类型处理问题。

## 7. 给新人的实操清单

- [ ] 本地执行 `./mvnw -q -DskipTests compile` 确认构建环境。
- [ ] 跑一个 `session` 包相关测试并能解释调用路径。
- [ ] 跑一个 `submitted` 子目录中的回归测试并说明它在防止什么回归。
- [ ] 新增一个最小 Mapper + 对应测试（含 XML 或注解）。
- [ ] 写一页自己的“调用链笔记”（类名 + 职责 + 关键断点）。

---

如果你是第一次维护 MyBatis，最关键的不是“背 API”，而是把**配置如何变成 `MappedStatement`，以及它如何被 `Executor` 执行**这条主链路吃透。
