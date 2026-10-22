# 07 – FastAPI Basics

In this part you will learn how to build a simple HTTP API using FastAPI.

## Questions

1. How do you install FastAPI and Uvicorn?  
2. What decorator is used to define a GET endpoint?  
3. How do you read query parameters vs. path parameters?  
4. What is Pydantic used for in FastAPI?  
5. How do you run your app locally with automatic reload?

## Tasks

* Initialize a new FastAPI app in `app/main.py`.  
* Define a GET endpoint at `/hello` that returns `{"message": "Hello, FastAPI!"}`.  
* Define a GET endpoint at `/add/{a}/{b}` that reads path parameters `a` and `b` (ints), returns their sum.  
* Create a Pydantic model `Item(name: str, price: float)` and a POST endpoint `/items/` to accept and return the item.  
* Update your `requirements.txt` to include:
  ```
  fastapi==0.115.12
  uvicorn==0.34.2
  ...and anything else that you use in your code
  ```
* Provide a README with instructions:
  ```
  pip install -r requirements.txt
  uvicorn app.main:app --reload
  ```
* Test with [Postman](https://pst-dl.getpostman.com/app/download/start) (or any other tool of your choice)
