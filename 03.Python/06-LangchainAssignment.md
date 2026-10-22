# 06-a – LangChain Assignment

In this part you will create a Python project that integrates with LangChain to talk to a locally-hosted LLM (we recommend using LLaMA via Ollama).

## Objectives

1. Research how to install and run LLaMA locally using [ollama.com](https://ollama.com).  
2. Structure your code into clear modules (no “everything in one file!”).  
3. Implement a `LangChainClient` class that uses the official LangChain library to send prompts and receive responses.  
4. Write clean, well-commented code.  
5. Produce:
   - a `/src` folder with your modules  
   - a `requirements.txt` with fully-pinned versions  
   - an `.env` file loaded via `python-dotenv`, excluding any environment variables from your code  

## Tasks

* Create a `src/langchain_client.py` module containing a `LangChainClient` class:
  - initializer loads model name, endpoint, etc. from environment  
  - a `generate(prompt: str) -> str` method that returns the LLM response
* Create a `requirements.txt` with pinned versions, e.g.:
  ```
  langchain==0.3.25
  python-dotenv==1.1.0
  ollama==0.4.9
  ...and anything else that you use in your code
  ```
* Provide an example script `run_demo.py` that uses `LangChainClient` to send a sample prompt  
* Add a `.env.example` (don’t include real environment variables!)

# 06-b - Standards

Skim the official PEP 8 style guide: https://peps.python.org/pep-0008/

and answer the following questions:

1. What is the recommended indentation style in Python, and why should you avoid mixing tabs and spaces?
2. What is the maximum recommended line length  for code lines, and what is the suggested limit for docstrings or comments?
3. Which naming conventions should be used for:
    - functions and variables?
    - classes?
    - constants?
4. How should you format binary operators (like +, =, >=) with spaces? And what are the rules for whitespace inside parentheses or brackets?
5. In what order and format should imports appear in your code? Describe grouping and spacing rules.
6. What is recommended regarding the use of comments and docstrings? (Think about updating, formatting, and placement.)
7. Name two linters and one autoformatter that are commonly used to enforce PEP 8 compliance.

## Tasks

* Make sure the code you're writing complies with PEP 008 standards. 😎
