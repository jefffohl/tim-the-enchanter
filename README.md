# ✨ Tim The Enchanter ✨

A lightweight, flexible performance tracking library for Python applications. Easily measure and analyze execution times of your functions, code blocks, and processes.

## Features

- ✅ **Zero dependencies** - Just Python standard library
- ✅ **Minimal overhead** - Designed to be lightweight
- ✅ **Multiple reporting formats** - Chronological, by-process, and aggregated statistics
- ✅ **Flexible configuration** - Enable/disable via arguments or environment variables
- ✅ **Method chaining** - Fluent interface for concise code
- ✅ **Context managers & decorators** - Multiple ways to instrument your code
- ✅ **Support for async functions** - Works with asyncio
- ✅ **Metadata support** - Add context to your timing events

## Example output

```bash
===== ✨ Tim The Enchanter Performance Report ✨ ingest_docs_de2a5e7e-70c1-4f9b-861c-d9a431e3cb24_20250314_094609 =====
Format: aggregate

Process Name                             |  Count   |  Total (s)   |   Avg (s)    |   Min (s)    |   Max (s)    |  Median (s)  |  StdDev (s)
-----------------------------------------------------------------------------------------------------------------------------------------------
get_context_docs                         |    1     |     8.510816 |     8.510816 |     8.510816 |     8.510816 |     8.510816 |     0.000000
summarize_chunk                          |    2     |     2.506251 |     1.253125 |     0.879834 |     1.626417 |     1.253125 |     0.527914
process_all_documents                    |    1     |     2.379968 |     2.379968 |     2.379968 |     2.379968 |     2.379968 |     0.000000
db_add_document                          |    2     |     2.231350 |     1.115675 |     0.747726 |     1.483624 |     1.115675 |     0.520359
split_document                           |    1     |     0.000170 |     0.000170 |     0.000170 |     0.000170 |     0.000170 |     0.000000

================================================================================
```

## Installation

```bash
pip install tim-the-enchanter
```

## Basic Usage

```python
from tim_the_enchanter import TimTheEnchanter, TimTheEnchanterReportFormat

# Get a configured instance
tracker = TimTheEnchanter.create(enabled=True)

# Start tracking a session
tracker.start_session("my_api_request")

# Track a block of code
with tracker.time_process("data_processing"):
    # Your code here
    process_data()

# Track a function with a decorator
@tracker.time_function()
def calculate_results():
    # Function code
    pass

# Track an async function
@tracker.time_async_function()
async def fetch_data():
    # Async function code
    pass

# Manual tracking
start_time = time.time()
# ... do something ...
duration = time.time() - start_time
tracker.record("manual_operation", duration)

# Generate reports
tracker.print_report(TimTheEnchanterReportFormat.CHRONOLOGICAL)  # Time-ordered events
tracker.print_report(TimTheEnchanterReportFormat.BY_PROCESS)     # Grouped by process name
tracker.print_report(TimTheEnchanterReportFormat.AGGREGATE)      # Statistical summary

# End the session
tracker.end_session()
```

## Configuration Options

There are multiple ways to configure the performance tracker:

### 1. Factory Method (Recommended)

```python
# Simple way to create and configure in one step
tracker = TimTheEnchanter.create(enabled=not is_production)
```

### 2. Direct Configuration

```python
# Import the singleton instance
from tim_the_enchanter import tim_the_enchanter as tracker
# Configure the singleton instance
tracker.configure(enabled=True, reset_sessions=False)
```

## Method Chaining

The tracker supports a fluent interface for concise code:

```python
# Chain multiple operations
(TimTheEnchanter()
    .configure(enabled=True)
    .start_session("api_request")
    .record("initialization", 0.05)
    .print_report(TimTheEnchanterReportFormat.CHRONOLOGICAL)
    .end_session()
)
```

## Runtime Toggling

You can enable or disable tracking at runtime:

```python
# Disable during specific operations
tracker.disable()
compute_expensive_operation()  # Not tracked
tracker.enable()
```
## Session Management

```python
# Create and manage multiple sessions
tracker.start_session("session1")
# ... operations ...
tracker.end_session()

tracker.start_session("session2")
# ... more operations ...
tracker.print_report(format=TimTheEnchanterReportFormat.AGGREGATE, session_name="session2")
tracker.end_session()
```

## Metadata Support

```python
# Add contextual information to timing events
with tracker.time_process("database_query", metadata={"table": "users", "filters": {"active": True}}):
    # Your code here
    pass
```

## Performance Considerations

The tracker is designed to be lightweight, but for production environments, you may want to disable it:

```python
# In your application startup code
is_production = os.environ.get("ENV") == "production"
tracker = TimTheEnchanter.create(enabled=not is_production)
```

When disabled, all tracking operations become no-ops with minimal overhead. 
