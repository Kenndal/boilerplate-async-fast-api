## **Role**

You are an expert in:

- pytest
- pytest-asyncio
- asynchronous testing patterns for FastAPI and SQLAlchemy
- refactoring large test suites safely and mechanically

Your job is to refactor **ONLY the test suite**, so that all tests correctly support the repository’s new asynchronous FastAPI + SQLAlchemy codebase.

---

## **Primary Objective**

Rewrite the entire test suite so that it is fully compatible with asyncio, specifically:

### **1. Convert test functions to async (where required)**

- Replace `def test_*` with `async def test_*` when testing async code
- Add `@pytest.mark.asyncio` or use pytest-asyncio auto mode

### **2. Fix client/test client usage**

Depending on library:

- Replace sync `TestClient` with `AsyncClient` (e.g. httpx)
- Use async context managers where required

### **3. Update fixtures**

Convert fixtures interacting with async code to:

- `@pytest_asyncio.fixture`
- `async def fixture(...)`

### **4. Update mocking**

Mock async functions correctly:

- Use `AsyncMock` instead of `Mock` where needed
- Preserve mock behavior exactly as before

---

## **Strict Constraints**

### ❗ **DO NOT modify application code**

- Not a single line of Python outside `tests/` may be changed
- Not even small improvements or cleanups

### ❗ **DO NOT change what tests verify**

- No changes to assertions
- No changes in expected values
- No changes in test scope or test scenarios
- No removing or adding tests

### ❗ **NO additional logic**

- Do not refactor test structure
- Do not rewrite test flow
- Do not add new helper functions unless strictly necessary for async compatibility
- Do not simplify or “improve” anything

### ❗ **Behavioral equivalence must be 100%**

Every test after conversion must check exactly the same thing as before — only using async-compatible patterns.

---

## Allowed Changes

### ✔ Convert tests to async

### ✔ Update fixtures to async

### ✔ Replace sync test clients with async test clients

### ✔ Replace sync DB session fixtures with async sessions

### ✔ Use correct async mocks (`AsyncMock`)

### ✔ Use `await` before async actions

### ✔ Update test setup/teardown if required for async context

---

## **Process**

### **1. Analyze the entire test suite**

Identify:

- tests calling async endpoints
- fixtures returning database sessions
- fixtures using sync DB/engine/session
- mocks of async functions
- sync test clients

### **2. Perform mechanical async refactor**

Transform only what is necessary to make tests run.

### **3. Ensure pytest compatibility**

Use one of:

- `@pytest.mark.asyncio`
- pytest-asyncio auto mode (if configured)
- async fixtures

### **4. Maintain file structure**

Do NOT reorganize, split, or merge test files.

---

## **Requirements for the Output**

For every modified test file:

- Provide a patch-like diff, or
- Provide a fully updated file if cleaner

The output **must contain only the updated test code** or patches — nothing else.

---

## Examples of allowed transformations

### **Test function**

```python
# before
def test_get_item(client):
    resp = client.get("/items/1")
    assert resp.status_code == 200

# after
@pytest.mark.asyncio
async def test_get_item(async_client):
    resp = await async_client.get("/items/1")
    assert resp.status_code == 200

```

### **Fixture**

```python
# before
@pytest.fixture
def db_session():
    session = Session()
    yield session
    session.close()

# after
@pytest_asyncio.fixture
async def db_session(async_sessionmaker):
    async with async_sessionmaker() as session:
        yield session

```

### **Mock**

```python
# before
mock_repo.get_item = MagicMock(return_value=item)

# after
mock_repo.get_item = AsyncMock(return_value=item)

```

---

## **Final Instruction**

Refactor **ONLY the tests** so that they correctly support asynchronous FastAPI + SQLAlchemy code.

Do NOT modify application code.

Do NOT add new functionality.

Do NOT change what tests assert or validate.

Only add the minimal changes required to make tests asynchronous and compatible with the new async codebase.

Produce the updated test files or diffs now.

---