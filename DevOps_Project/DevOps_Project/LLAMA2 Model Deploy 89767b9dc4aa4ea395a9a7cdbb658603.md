# LLAMA2 Model Deploy

### **LLAMA2 Deploy on AWS**

```
# Launch the notebook instance of type "ml.g5.12xlarge",
# For Instance 7$ per hour service quota increase needs to be requested
# While creating a notebook instance give IAM and S3 permissions
# AWS Sagemaker automatically creates the IAM Role and required S3 Buckets
# Once the notebook instance is running launch the Jupiter notebook and start executing below instructions
```

```bash
# Check sagemaker installed

pip list | grep sagemaker
```

```python
# Cross-check the assigned IAM role and the default region where the notebook instance has been launched.

import sagemaker
import boto3
import numpy
import scipy

# Declare the SageMaker execution role variable globally
sagemaker_execution_role = None

# set up sagemaker session
def setup_sagemaker_session(default_bucket=None):
    """
    Setup SageMaker session and get IAM execution role
    Params:
    - default_bucket: Default bucket name to use for the session
    Returns:
    - session: SageMaker session object
    - role_arn: ARN of the IAM execution role
    """
    global sagemaker_execution_role
    session = sagemaker. Session(default_bucket=default_bucket)

    try:
        sagemaker_execution_role = sagemaker.get_execution_role()
    except ValueError:
        iam = boto3. client('iam')
        sagemaker_execution_role = iam.get_role(RoleName='sagemaker_execution_role') ['Role']['Arn']

    return session, sagemaker_execution_role

def mask_account_id(account_id):
    return '*' * len(account_id)

def main():
    sagemaker_session_bucket = None

    session, sagemaker_execution_role = setup_sagemaker_session(default_bucket=sagemaker_session_bucket)

    # Mask with ****
    account_id = sagemaker_execution_role.split(':') [4]
    masked_account_id = mask_account_id(account_id)
    masked_role = sagemaker_execution_role. replace(account_id, masked_account_id)

    print(f"SageMaker role ARN: {masked_role}")
    print(f"SageMaker session region: {session.boto_region_name}")

if __name__ == "__main__":
    main()
```

```python
from sagemaker.huggingface import get_huggingface_llm_image_uri

# Fetch Docker image URI for the Hugging Face DLC:
# 1. backend name
# 2. Hugging Face LDC verison

llm_image = get_huggingface_llm_image_uri(
    "huggingface",
    version="0.9.3"
)

# Log the docker image URI
print(f"llm image uri: {llm_image}")
```

```python
import json

# confirm instance requirements are met:
with open('/opt/ml/metadata/resource-metadata.json') as f:
    metadata = json.load(f)
    print(metadata['ResourceName'])
```

```python
import json
import sagemaker
import boto3
from sagemaker.huggingface import HuggingFaceModel, get_huggingface_llm_image_uri

try:
	role = sagemaker.get_execution_role()
except ValueError:
	iam = boto3.client('iam')
	role = iam.get_role(RoleName='sagemaker_execution_role')['Role']['Arn']

# Hub Model configuration. https://huggingface.co/models
hub = {
	'HF_MODEL_ID':'meta-llama/Llama-2-7b-chat-hf',
	'SM_NUM_GPUS': json.dumps(1),
	'HUGGING_FACE_HUB_TOKEN': '<REPLACE WITH YOUR TOKEN>'
}

assert hub['HUGGING_FACE_HUB_TOKEN'] != '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', "You have to provide a token."

# create Hugging Face Model Class
huggingface_model = HuggingFaceModel(
	image_uri=get_huggingface_llm_image_uri("huggingface",version="1.1.0"),
	env=hub,
	role=role, 
)

# deploy model to SageMaker Inference
predictor = huggingface_model.deploy(
	initial_instance_count=1,
	instance_type="ml.g5.12xlarge",
	container_startup_health_check_timeout=300,
  )
  
# send request
predictor.predict({
	"inputs": "My name is Julien and I like to",
})
```

```python
import json
import boto3

# SageMaker runtime client
sagemaker_runtime = boto3.client('sagemaker-runtime')

# Define the endpoint name
endpoint = 'huggingface-pytorch-tgi-inference-2023-08-30-09-02-12-956'

def build_llama2_prompt(message):
    startPrompt = "<s> [INST]
    endPrompt = " [/INST]"
    conversation = []
    for index, message in enumerate(message):
        if message["role"] == "system" and index == 0:
            conversation.append (f" << SYS>>\n{message['content']}\n << /SYs>>\n\n")
            elif message["role"] == "user":
                conversation.append(message["content"].strip())
            else:
                conversation.append(f" [/INST] {message['content'].strip()}</s><s>[INST] ")
        return startPrompt + "".join(conversation) + endPrompt

# What we define
messages = [
    {
        "role": "system",
        "content": "You are a nonprofit advocate and champion. Your goal is to help entrepreneurs and movers."
    }
]

# What a user requests
instruction = "What does the world need more of right now?"
messages.append({"role": "user", "content": instruction})
prompt = build_llama2_prompt(messages)

# Define the input data
input_data = {
    "inputs": prompt,
    "parameters": {
        "do_sample": True,
        "top_p": 0.6,
        "temperature": 0.9,
        "top_k": 50,
        "max_new_tokens": 512,
        "repetition_penalty": 1.03,
        "stop": ["</s>"]
    }
}

input_data_json = json.dumps(input_data)

# Set the content type header
content_type = "application/json"

# Perform insference using the model
response = sagemaker_runtime.invoke_endpoint(
    EndpointName = endpoint_name,
    ContentType = content_type,
    Body = input_data_json.encode('utf-8')
}

# Read and decode the response
response_body = response ['Body' ]. read().decode('utf-8')
response_json = json. loads (response_body)

# Extract and print the generated response
generated_text = response_json [0] ['generated_text']
print(generated_text[len(prompt): ])

```

[https://youtu.be/Kq597DYdMEE](https://youtu.be/Kq597DYdMEE)

### LLAMA2 Deploy on Linux

```bash
# Raise the download request from [https://llama.meta.com/](https://llama.meta.com/)

# Clone the repository on local system

git clone [https://github.com/facebookresearch/codellama.git](https://github.com/facebookresearch/codellama.git)

# Update repository, install python3 and pip

sudo apt update; sudo apt install python3; sudo apt install python3-pip

# While downloading put the URL received over the email, and using pip install the dependencies listed in requirement file

cd /home/private/codellama/; ./download.sh; sudo pip install -e .

# Intensive hardware configuration required for torchrun alternately we can use llama.cpp 

torchrun --nproc_per_node 1 example_completion.py \
    --ckpt_dir CodeLlama-7b/ \
    --tokenizer_path CodeLlama-7b/tokenizer.model \
    --max_seq_len 128 --max_batch_size 4
```

```bash
# llama.cpp is to enable LLM inference with minimal setup and state-of-the-art performance on a wide variety of hardware - locally and in the cloud.

# Clone the repository on the local system 

# To build llama.cpp we have to use make

git clone [https://github.com/ggerganov/llama.cpp.git](https://github.com/ggerganov/llama.cpp.git); cd /home/private/llama.cpp/; make

# Copy the CodeLlama-7b model under llama.cpp models directory

cp -prv /home/private/codellama/CodeLlama-7b /home/private/llama.cpp/models/

# Use the below command to convert the CodeLlama-7b model in a more lightweight ggml format to run

python3 [convert.py](http://convert.py/) /home/private/llama.cpp/models/CodeLlama-7b/

# Run the quantized model

/home/private/llama.cpp/main -m /home/private/llama.cpp/models/CodeLlama-7b/ggml-model-f16.gguf -n 128 -ins

# quantize the model to 4-bits (using Q4_K_M method)

/home/private/llama.cpp/quantize /home/private/llama.cpp/models/CodeLlama-7b/ggml-model-f16.gguf /home/private/llama.cpp/models/CodeLlama-7b/ggml-model-q4_0.bin q4_0

# Run the quantized model

/home/private/llama.cpp/main -m /home/private/llama.cpp/models/CodeLlama-7b/ggml-model-q4_0.bin -n 128 -ins
```

![Untitled](Untitled.png)

[https://youtu.be/Ed7SH2y9Sos](https://youtu.be/Ed7SH2y9Sos)