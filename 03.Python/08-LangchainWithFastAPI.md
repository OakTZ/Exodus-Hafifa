# 08 – FastAPI + LangChain Integration

In this part you will turn your LangChain client into a web service so anyone can hit an HTTP endpoint to get LLM responses.

## Questions

1. How do you include your `LangChainClient` into a FastAPI dependency?  
2. What Pydantic model would you use to accept a `prompt` JSON payload?  
3. How do you handle errors or timeouts from the LLM call?  
4. How can you document your API so users see the prompt schema automatically?

## Tasks

* In `app/main.py`, import and instantiate `LangChainClient` as a dependency.  
* Define a Pydantic model:
  ```python
  class PromptRequest(BaseModel):
      prompt: str
  ```
* Create a POST endpoint `/generate/` that:
  - accepts `PromptRequest`  
  - calls `LangChainClient.generate()`  
  - returns `{ "response": "<LLM output>" }`  
* Add proper error handling (e.g., return 504 on timeout).   
