# AI Python → C++ Converter

A local AI-powered developer tool that converts Python programs into optimized, compilable C++17 code using **Llama 3.2 + Ollama**, then compiles and executes the generated C++ with **Clang++**.

Everything runs locally on your machine — no OpenAI, Anthropic, Gemini, Grok, or other cloud API is required.

**Author:** Khaled Alzeer  
**GitHub:** https://github.com/khaledAlzeer  
**Repository:** https://github.com/khaledAlzeer/AI-Python-to-Cpp-Converter

---

## Overview

**AI Python → C++ Converter** is a standalone Generative AI application that uses a locally hosted Llama 3.2 model to translate Python programs into C++17.

The project started from an LLM code-conversion exercise during my Generative AI and LLM engineering learning journey and was further developed into a standalone portfolio project with a complete Gradio developer-tool interface.

The application provides an end-to-end workflow:

```text
Python Input
     ↓
Prompt Engineering
     ↓
Llama 3.2 via Ollama
     ↓
C++ Generation
     ↓
Sanitization
     ↓
Validation
     ↓
Clang++ Compilation
     ↓
C++ Execution
     ↓
Optional Python Execution
     ↓
Benchmarking
     ↓
Results
```

---

## Features

- **Local AI Code Generation**
  - Uses Llama 3.2 through Ollama.
  - No cloud AI API key required.

- **Python → C++17 Conversion**
  - Converts Python programs into compilable C++17.
  - Preserves function return values and observable output where possible.
  - Uses prompt engineering to enforce valid C++17 generation.

- **Complete Developer Workflow**
  - Convert
  - Compile
  - Compile & Run
  - Benchmark

- **Gradio Developer UI**
  - Python code editor
  - Generated C++ editor
  - Built-in examples
  - Pipeline status
  - Compiler errors
  - Runtime errors
  - Execution results
  - Benchmark results
  - Settings
  - System information
  - About section

- **Pipeline Tracking**

  The UI tracks every major stage:

  ```text
  ✓ Python Input
  ✓ LLM Generation
  ✓ Sanitization
  ✓ Validation
  ✓ Compilation
  ✓ Execution
  ✓ Benchmark
  ```

- **Authoritative Generated Source**

  The sanitized C++ source saved to:

  ```text
  generated/main.cpp
  ```

  is the same source displayed by the UI and passed to Clang++.

- **Real Benchmarking**

  Python and C++ execution times are measured during the current run.

  Speedup is calculated from actual measured execution times and is never hard-coded or fabricated.

- **Local System Information**

  The application can report:

  - Operating system
  - CPU
  - Logical and physical cores
  - Clang++ availability
  - Clang++ version
  - Ollama status
  - Active Llama model

---

## Technology Stack

| Technology | Purpose |
|---|---|
| Python | Application and pipeline logic |
| Llama 3.2 | Local code-generation model |
| Ollama | Local LLM runtime |
| Gradio | Web-based developer UI |
| C++17 | Generated target language |
| Clang++ / LLVM | C++ compiler |
| uv | Python dependency and environment management |
| pytest | Automated testing |
| python-dotenv | Optional environment configuration |

---

## Architecture

| File | Responsibility |
|---|---|
| `app.py` | Gradio UI and event handlers |
| `config.py` | Paths, model configuration, compiler configuration |
| `models.py` | Structured result dataclasses |
| `prompts.py` | LLM system and user prompt construction |
| `llm.py` | Ollama client and connection handling |
| `cpp_tools.py` | C++ sanitization and validation |
| `compiler.py` | Clang++ compilation abstraction |
| `executor.py` | Compiled C++ execution |
| `python_runner.py` | Optional Python subprocess execution |
| `benchmark.py` | Timing parsing and benchmark calculations |
| `pipeline.py` | End-to-end pipeline orchestration |
| `system_info.py` | Local system information |
| `examples/python_examples.py` | Built-in Python examples |

---

## How It Works

### 1. Python Input

The user provides Python source code directly through the Gradio interface or loads one of the built-in examples.

### 2. Prompt Engineering

`prompts.py` constructs a structured prompt containing:

- The Python source code
- C++17 requirements
- Output-format requirements
- Function return-value requirements
- Timing requirements
- The exact compiler command
- Local system information

The goal is to guide the local Llama 3.2 model toward valid and compilable C++17 output.

### 3. Local LLM Generation

The application sends the prompt to:

```text
Llama 3.2
    ↓
Ollama
    ↓
Local machine
```

No source code is sent to a third-party cloud AI provider.

### 4. Sanitization

The generated response is cleaned before compilation.

Sanitization handles issues such as:

- Markdown code fences
- Python-style numeric separators
- Required C++ headers
- Duplicate headers

### 5. Validation

A lightweight C++ validator performs structural checks before compilation.

The validator is intentionally not treated as a complete C++ parser.

**Clang++ remains the final authority on whether the generated source is compilable.**

### 6. Saving the Generated Source

The sanitized source is written to:

```text
generated/main.cpp
```

The UI displays this same source.

This keeps the displayed code and compiled code synchronized.

### 7. Compilation

The project uses Clang++ with C++17 and optimization enabled.

Example command:

```text
clang++ -std=c++17 -O2 main.cpp -o main.exe
```

### 8. Execution

The generated executable is launched locally and the application captures:

- Standard output
- Standard error
- Return code
- Process execution time

### 9. Optional Python Benchmark

When the user enables:

```text
Also run Python for benchmark comparison
```

the original Python program is executed in a subprocess.

The application captures:

- Python output
- Python errors
- Python return code
- Python execution time

### 10. Benchmark

The application keeps two different timing measurements:

**Reported time**

The execution time printed by the generated program itself.

**Measured time**

The external process execution time measured by the application.

When both Python and C++ measurements are available, the application calculates:

```text
Speedup = Python measured time / C++ measured time
```

The value is calculated dynamically for the current run.

No historical, hard-coded, or fabricated speedup values are used.

---

## Benchmarking Example

For the built-in Pi approximation example, one tested run produced approximately:

```text
C++ Measured Time:    0.290498 seconds
C++ Reported Time:    0.192430 seconds

Python Measured Time: 46.987170 seconds
Python Reported Time: 46.928288 seconds
```

The resulting speedup for that particular run was:

```text
161.75x
```

These numbers are **example measurements from one local run**, not guaranteed performance figures.

Actual results vary depending on:

- CPU load
- Background processes
- Thermal conditions
- Compiler version
- Operating system
- Hardware
- Python version
- Local machine configuration

---

## Testing

The project includes automated tests for:

- Prompt generation
- C++ sanitization
- C++ validation

Current test result:

```text
15 passed
```

Run the tests with:

```powershell
uv run pytest .\tests
```

---

## Project Structure

```text
AI-Python-to-Cpp-Converter/
│
├── app.py
├── config.py
├── models.py
├── prompts.py
├── llm.py
├── cpp_tools.py
├── compiler.py
├── executor.py
├── python_runner.py
├── benchmark.py
├── pipeline.py
├── system_info.py
│
├── generated/
│   ├── main.cpp
│   └── .gitkeep
│
├── examples/
│   └── python_examples.py
│
├── tests/
│   ├── conftest.py
│   ├── test_sanitizer.py
│   ├── test_validator.py
│   └── test_prompts.py
│
├── .env.example
├── .gitignore
└── README.md
```

---

## Requirements

### Hardware

The project is designed to run locally with a machine capable of running Llama 3.2 through Ollama.

A dedicated GPU is helpful but not strictly required.

### Software

You will need:

- Python 3.12+
- [uv](https://docs.astral.sh/uv/)
- Ollama
- Llama 3.2
- LLVM / Clang++
- Git

---

## Installation

### 1. Clone the repository

```powershell
git clone https://github.com/khaledAlzeer/AI-Python-to-Cpp-Converter.git
cd AI-Python-to-Cpp-Converter
```

### 2. Install dependencies

This project uses `uv`.

```powershell
uv sync
```

### 3. Install / Start Ollama

Install Ollama and pull Llama 3.2:

```powershell
ollama pull llama3.2
```

Verify the model:

```powershell
ollama list
```

Make sure the Ollama service is running.

### 4. Verify Clang++

```powershell
clang++ --version
```

The project was developed and tested with LLVM/Clang++ 23.1.1 on Windows.

---

## Configuration

The application uses local defaults:

```text
OLLAMA_URL=http://localhost:11434
LLAMA_MODEL=llama3.2
```

To override the defaults, copy:

```text
.env.example
```

to:

```text
.env
```

and modify the values as needed.

---

## Running the Application

From the project root:

```powershell
uv run python app.py
```

The Gradio interface will start locally.

By default, it is available at:

```text
http://127.0.0.1:7860
```

---

## Using the UI

### Convert

1. Enter Python code or select a built-in example.
2. Click **CONVERT TO C++**.
3. Review the generated C++17 source.
4. Click **Compile** or **Compile & Run**.

### Compile

Compiles the currently generated C++ source using Clang++.

### Compile & Run

Runs the complete workflow:

```text
Generation
→ Sanitization
→ Validation
→ Compilation
→ Execution
→ Benchmark
```

### Python Benchmark Comparison

Enable:

```text
Also run Python for benchmark comparison
```

to execute the original Python program and compare its measured execution time with the generated C++ program.

### Settings

Displays:

- Ollama URL
- Active model
- Compiler
- C++ standard
- Optimization level
- Generated source path
- Executable path

The tab also provides an Ollama connection check.

### System Info

Displays local:

- OS information
- CPU information
- Compiler information
- Ollama status

### Examples

Provides built-in Python programs that can be used to test the converter.

---

## Built-in Examples

The project currently includes examples covering:

- Pi approximation
- Fibonacci
- Prime counting
- Numerical loops
- Vector dot product

These examples are available directly from the **Examples** tab and the **Load Example** selector.

---

## Security Considerations

This application executes generated code on the local machine.

**Always review generated C++ before compiling or running it.**

The optional Python benchmark executes the supplied Python program in a subprocess. This provides process separation but **is not a security sandbox**.

Similarly, compiling and executing generated C++ gives that program the permissions available to the current user.

Do not run untrusted Python or generated C++ code.

The application does not send source code to third-party cloud AI services. The LLM communication is performed through the locally running Ollama service.

---

## Known Limitations

- LLM-generated code is not guaranteed to be semantically identical to the original Python program.
- Complex Python features may not translate cleanly into C++.
- Python libraries without direct C++ equivalents may require manual adaptation.
- The C++ validator performs lightweight structural checks rather than full language parsing.
- Clang++ compilation is the final validation authority.
- Benchmark results vary between machines and runs.
- Process-level measured times include program startup and process overhead.
- Generated code should always be reviewed before execution.

---

## Future Improvements

Possible future improvements include:

- Support for additional local LLMs.
- Model selection directly from the UI.
- Multi-file C++ project generation.
- Side-by-side Python/C++ code comparison.
- Syntax highlighting and richer code editing.
- Generation history.
- Benchmark history and visualization.
- Improved semantic validation.
- Support for more complex Python programs.
- Additional compiler/toolchain support.

---

## Learning Journey

This project was developed as part of my hands-on learning journey in **Generative AI, LLM engineering, local LLM deployment, prompt engineering, code generation, and AI-powered developer tools**.

The initial Python → C++ conversion concept was developed during coursework and subsequently expanded into a standalone application with:

- Local LLM integration
- Prompt engineering
- Code sanitization
- Code validation
- Compiler integration
- Process execution
- Benchmarking
- Gradio UI
- Automated testing
- Local system inspection

---

## License

© 2026 Khaled Alzeer. All rights reserved.

This project was developed as part of my Generative AI and LLM engineering learning journey and further developed into a standalone portfolio project.

The project is intended for educational and portfolio purposes.

---

## Author

**Khaled Alzeer**

AI & Data Science Student  
Al-Zaytoonah University of Jordan

- GitHub: https://github.com/khaledAlzeer
- LinkedIn: https://www.linkedin.com/in/khaled-alzeer-60787b322

---

## Acknowledgements

Inspired by hands-on Generative AI and LLM engineering coursework and extended into an independent portfolio project.

Special thanks to the open-source projects that make the local development stack possible, including:

- Ollama
- Llama 3.2
- Gradio
- LLVM / Clang
- Python
- uv
- pytest
