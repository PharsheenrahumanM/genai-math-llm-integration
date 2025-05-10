## Integration of a Mathematical Calulations with a Chat Completion System using LLM Function-Calling

### AIM:
To design and implement a Python function for calculating the volume of a cylinder, integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM).

### PROBLEM STATEMENT:

### DESIGN STEPS:

#### STEP 1: Load environment variables and set up the OpenAI API key for authentication.

#### STEP 2: Implement cal_cylinder_vol(radius, height), which computes and returns the volume in JSON format.

#### STEP 3: Send a user’s request to the OpenAI chat model (ChatCompletion.create) along with function metadata.

### PROGRAM:
~~~
import os
import math
import openai
import json
from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
openai.api_key = os.environ['OPENAI_API_KEY']
def cal_cylinder_vol(radius, height):
    volume = math.pi*(math.pow(radius,2))*height
    return json.dumps({"radius": radius, "height": height, "volume": volume})
functions = [
    {
        "name": "cal_cylinder_vol",
        "description": "Calculate the volume of a cylinder given its radius and height.",
        "parameters": {
            "type": "object",
            "properties": {
                "radius": {"type": "number", "description": "Radius of the cylinder in meters"},
                "height": {"type": "number", "description": "Height of the cylinder in meters"}
            },
            "required": ["radius", "height"]
        }
    }
]
    
r = float(input("Enter the radius of the cylinder: "))
h = float(input("Enter the height of the cylinder: "))

messages = [{"role": "user", "content": f"What is the volume of a cylinder with radius {r} and height {h} ?"}]

response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=messages,
    functions=functions
)
response_message = response["choices"][0]["message"]

if "function_call" in response_message:
    args = json.loads(response_message["function_call"]["arguments"])
    result = cal_cylinder_vol(**args)  

    messages.append({
        "role": "function",
        "name": "cal_cylinder_vol",
        "content": result,
    })

    final_response = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=messages,
    )

print(final_response["choices"][0]["message"]["content"])
~~~
### OUTPUT:
![image](https://github.com/user-attachments/assets/bdd3584b-7f1e-45ae-b6b2-fe19e2ab2c26)

### RESULT:The code successfully enables LLM-driven cylinder volume calculation via function calling.
