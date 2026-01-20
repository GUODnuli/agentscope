### AgentScope 代码评审指南

你需要进行**严格的代码评审**。每条要求带有优先级标记：

- **[MUST]** 必须满足，否则 PR 会被拒绝  
- **[SHOULD]** 强烈建议遵守  
- **[MAY]** 可选建议  

---

### 1. 代码质量

#### [MUST] 延迟加载（Lazy Loading）

- 第三方库依赖应在**使用处就地导入**，避免在文件顶部集中导入。  
- 这里的「**第三方库**」指的是 **未包含在 `pyproject.toml` 中 `dependencies` 变量**里的库。  
- 对于基类导入，应使用工厂模式，示例：

```python
def get_xxx_cls() -> "MyClass":
    from xxx import BaseClass

    class MyClass(BaseClass):
        ...

    return MyClass
```

#### [SHOULD] 代码简洁性

在理解代码意图之后，检查是否可以进行优化：

- 避免不必要的临时变量  
- 合并重复的代码块  
- 优先复用已有的工具函数（utility functions）  

#### [MUST] 封装规范

- `src/agentscope` 目录下的所有 Python 文件都应使用 `_` 前缀命名，并通过 `__init__.py` 进行统一对外暴露控制。  
- 框架内部使用且不需要暴露给用户的类和函数，**必须**使用 `_` 前缀命名。  

---

### 2. [MUST] 代码安全

- 禁止硬编码 API key / token / 密码。  
- 必须使用环境变量或配置文件来管理敏感信息。  
- 检查是否存在调试信息和临时凭据。  
- 检查是否存在注入攻击风险（例如 SQL 注入、命令注入、代码注入等）。  

---

### 3. [MUST] 测试与依赖

- 新功能必须**提供单元测试**。  
- 新增依赖需要添加到 `pyproject.toml` 中对应的配置段。  
- 非核心场景的依赖**不应**加入最小依赖列表（minimal dependency list）。  

---

### 4. 代码规范

#### [MUST] 注释规范

- **统一使用英文注释**。  
- 所有类 / 方法都必须有完整的 docstring，并**严格遵循**以下模板：

```python
def func(a: str, b: int | None = None) -> str:
    """{description}

    Args:
        a (`str`):
            The argument a
        b (`int | None`, optional):
            The argument b

    Returns:
        `str`:
            The return str
    """
```

- 对于特殊内容，使用 reStructuredText 语法，例如：

```python
class MyClass:
    """xxx

    `Example link <https://xxx>`_

    .. note:: Example note

    .. tip:: Example tip

    .. important:: Example important info

    .. code-block:: python

        def hello_world():
            print("Hello world!")

    """
```

---

#### [MUST] Pre-commit 检查

- **严格检查**：在大多数情况下，应修改代码以通过检查，而不是跳过检查。  
- **禁止在文件级别跳过检查**。  
- 唯一允许跳过的场景：**agent 类的 system prompt 参数**（为了避免 `\n` 的格式化问题）。  

---

### 5. Git 规范

#### [MUST] PR 标题

- 必须遵循 **Conventional Commits** 规范。  
- 标题必须使用前缀：`feat` / `fix` / `docs` / `ci` / `refactor` / `test` 等。  
- 格式：`feat(scope): description`  
- 示例：`feat(memory): add redis cache support`  
